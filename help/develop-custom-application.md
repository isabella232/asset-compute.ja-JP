---
title: の開発 [!DNL Asset Compute Service]。
description: を使用してカスタムアプリケーションを作成します [!DNL Asset Compute Service]。
translation-type: tm+mt
source-git-commit: 127895cf1bab59546f9ba0be2b3b7a935627effb
workflow-type: tm+mt
source-wordcount: '1496'
ht-degree: 0%

---


# カスタムアプリケーションの開発 {#develop}

カスタムアプリケーションの開発を開始する前に、以下を実行します。

* すべての [前提条件が満たされていることを確認します](/help/understand-extensibility.md#prerequisites-and-provisioning) 。
* [必要なソフトウェアツールをインストールします](/help/setup-environment.md#create-dev-environment)。
* 「環境の [設定](setup-environment.md) 」を参照して、カスタムアプリケーションを作成する準備ができていることを確認してください。

## カスタムアプリの作成 {#create-custom-application}

AdobeI/O CLI [がローカルにインストールされていることを確認します](https://github.com/adobe/aio-cli) 。

1. カスタムアプリケーションを作成するには、Fireflyアプリケーションを [作成します](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#4-bootstrapping-new-app-using-the-cli)。 これを行うには、ターミナル `aio app init <app-name>` でを実行します。

   まだログインしていない場合は、このコマンドを使用すると、ブラウザに対して、 [Adobe開発者コンソール](https://console.adobe.io/) (Adobe ID)へのサインインを求めるプロンプトが表示されます。 cliからのサインインの詳細につ [いては](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli) 、ここを参照してください。

   Adobeでは、ログインすることを推奨します。 問題が発生した場合は、指示に従って、ログイン [せずにアプリを作成します](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#42-developer-is-not-logged-in-as-enterprise-organization-user)。

1. ログイン後、CLIのプロンプトに従って、 、 、 、 、の順に選択 `Organization`し、アプリケーション `Project``Workspace` に使用します。 環境の [設定時に作成したプロジェクトとワークスペースを選択します](setup-environment.md)。

   ```sh
   $ aio app init <app-name>
   Retrieving information from Adobe I/O Console..
   ? Select Org My Adobe Org
   ? Select Project MyFireflyProject
   ? Select Workspace myworkspace
   create console.json
   ```

1. プロンプトが表示され `Which Adobe I/O App features do you want to enable for this project?`たら、少なくとも次を選択しま `Actions`す。

   ```bash
   ? Which Adobe I/O App features do you want to enable for this project?
   select components to include (Press <space> to select, <a> to toggle all, <i> to invert selection)
   ❯◉ Actions: Deploy Runtime actions
   ◯ Events: Publish to Adobe I/O Events
   ◯ Web Assets: Deploy hosted static assets
   ◯ CI/CD: Include GitHub Actions based workflows for Build, Test and Deploy
   ```

1. プロンプトが表示され `Which type of sample actions do you want to create?`たら、以下を必ず選択してください `Adobe Asset Compute Worker`。

   ```bash
   ? Which type of sample actions do you want to create?
   Select type of actions to generate
   ❯◉ Adobe Asset Compute Worker
   ◯ Generic
   ```

1. プロンプトの残りの部分に従って、Visual Studioコード（またはお気に入りのコードエディター）で新しいアプリケーションを開きます。 カスタムアプリケーションのスキャフォールドとサンプルコードが含まれています。

   Fireflyアプリの [主なコンポーネントについては、ここを参照してください](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application)。

   テンプレートアプリケーションは、アプリケーションレンディションのアップロード、ダウンロード、編成にアドビの [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk#asset-compute-sdk) (Asset Compute SDK)を利用するので、開発者はカスタムアプリケーションロジックを実装するだけで済みます。 フォルダー内の `actions/<worker-name>``index.js` ファイルでは、カスタムアプリケーションコードを追加する場所が指定されています。

カスタムアプリの例とアイデアについては、 [カスタムアプリの例](#try-sample) （英語）を参照してください。

### 追加資格情報 {#add-credentials}

アプリケーションの作成時にログインすると、Fireflyの秘密鍵証明書のほとんどがENVファイルに収集されます。 ただし、開発者ツールを使用するには、追加の資格情報が必要です。

<!-- TBD: Check if manual setup of credentials is required.
Manual set up of credentials is removed from troubleshooting and best practices page. Link was broken.
If you did not log in, refer to our troubleshooting guide to [set up credentials manually](troubleshooting.md).
-->

#### 開発者ツールストレージ資格情報 {#developer-tool-credentials}

カスタムアプリを実際に使用してテストする開発者ツールには、テストファイルをホストしたり、アプリケーションで生成されたレンディションを受け取って表示したりするクラウドストレージコンテナが [!DNL Asset Compute service] 必要です。

>[!NOTE]
>
>これは、Cloud Serviceとしてのクラウドストレージとは別 [!DNL Adobe Experience Manager] のものです。 これは、アセット計算開発者ツールを使用した開発およびテストにのみ適用されます。

サ [ポート対象のクラウドストレージコンテナにアクセスできることを確認してください](https://github.com/adobe/asset-compute-devtool#prerequisites)。 このコンテナは、必要に応じて異なるプロジェクトの複数の開発者が共有できます。

#### ENVファイル追加への資格情報 {#add-credentials-env-file}

Firefly追加プロジェクトのルートにあるENVファイルの開発者ツールに対する次の資格情報。

1. Firefly追加プロジェクトにサービスを追加する際に作成された秘密鍵ファイルの絶対パス：

   ```conf
   ASSET_COMPUTE_PRIVATE_KEY_FILE_PATH=
   ```

1. 追加S3またはAzureストレージの資格情報です。 1つのクラウドストレージソリューションへのアクセスのみが必要です。

   ```conf
   # S3 credentials
   S3_BUCKET=
   AWS_ACCESS_KEY_ID=
   AWS_SECRET_ACCESS_KEY=
   AWS_REGION=
   
   # Azure Storage credentials
   AZURE_STORAGE_ACCOUNT=
   AZURE_STORAGE_KEY=
   AZURE_STORAGE_CONTAINER_NAME=
   ```

## アプリケーションの実行 {#run-custom-application}

Asset Compute Developer Toolを使用してアプリケーションを実行する前に、 [秘密鍵証明書を適切に設定します](#developer-tool-credentials)。

開発者ツールでアプリケーションを実行するには、 `aio app run` commandを使用します。 これにより、ローカルマシン上に開発ツールを展開し、Adobe I/O Runtimeと開始にアクションを展開します。 このツールは、開発中にアプリケーションリクエストをテストするために使用します。 レンディションリクエストの例を次に示します。

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg"
    }
]
```

>[!NOTE]
>
>コマンドで `--local` フラグを使用しないでく `run` ださい。 カスタムアプリケーションおよびアセット計算開発者ツールでは使用できま [!DNL Asset Compute] せん。 カスタムアプリケーションは、開発者のローカルマシンで実行され [!DNL Asset Compute Service] ているアクションにアクセスできない場合に呼び出されます。

アプリケーションのテストとデバッグ [方法については](test-custom-application.md) 、ここを参照してください。 カスタムアプリケーションの開発が完了したら、カスタムアプリケーションを [デプロイします](deploy-custom-application.md)。

## Adobeが提供するサンプルアプリケーションを試してみます。 {#try-sample}

次に、カスタムアプリケーションの例を示します。

* [労働者基本の](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic)
* [労働者動物画像](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-animal-pictures)

### テンプレートカスタムアプリケーション {#template-custom-application}

worker [-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic) は、テンプレートアプリケーションです。 ソースファイルをコピーするだけでレンディションが生成されます。 このアプリのコンテンツは、aioアプリの作成時に選択したテンプレート `Adobe Asset Compute` になります。

アプリケーションファイルは、を [`worker-basic.js`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) 使用してソースファイル [`asset-compute-sdk`](https://github.com/adobe/asset-compute-sdk#overview) をダウンロードし、各レンディション処理を調整して、結果のレンディションをクラウドストレージにアップロードします。

アプリケーションコード内で [`renditionCallback`](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) 定義されるのは、すべてのアプリケーション処理ロジックを実行する場所です。 でのレンディションコールバックは、ソースファイルの内容をレンディションファイルに `worker-basic` 単純にコピーします。

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

exports.main = worker(async (source, rendition) => {
    // copy source to rendition to transfer 1:1
    await fs.copyFile(source.path, rendition.path);
});
```

## 外部APIの呼び出し {#call-external-api}

アプリケーションコードでは、外部API呼び出しを行って、アプリケーションの処理を支援できます。 外部APIを呼び出すアプリケーションファイルの例を以下に示します。

```javascript
exports.main = worker(async function (source, rendition) {

    const response = await fetch('https://adobe.com', {
        method: 'GET',
        Authorization: params.AUTH_KEY
    })
});
```

例えば、はウィキメディアから [`worker-animal-pictures`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L46) の静的URLへの取得要求を行い、ライブラリを使用し [`node-httptransfer`](https://github.com/adobe/node-httptransfer#node-httptransfer) ます。

<!-- TBD: Revisit later to see if this note is required.
>[!NOTE]
>
>For extra authorization for these API calls, see [custom authorization checks](#custom-authorization-checks).
-->

### カスタムパラメーターを渡す {#pass-custom-parameters}

カスタム定義パラメータは、レンディションオブジェクトに渡すことができます。 これらは、アプリケーション内で [`rendition` 手順に従って参照できます](https://github.com/adobe/asset-compute-sdk#rendition)。 レンディションオブジェクトの例を次に示します。

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg",
        "my-custom-parameter": "my-custom-parameter-value"
    }
]
```

カスタムパラメーターにアクセスするアプリケーションファイルの例を次に示します。

```javascript
exports.main = worker(async function (source, rendition) {

    const customParam = rendition.instructions['my-custom-parameter'];
    console.log('Custom paramter:', customParam);
    // should print out `Custom parameter: "my-custom-parameter-value"`
});
```

このパラメータ `example-worker-animal-pictures` は、どのファイルをウィキメディアから取得す [`animal`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L39) るかを決定するためにカスタムパラメータを渡します。

## 認証と認証のサポート {#authentication-authorization-support}

デフォルトでは、アセット計算カスタムアプリケーションには、認証と認証でFireflyアプリケーションのチェックが付属しています。 これは、 `require-adobe-auth` 注釈をに設定すると有効 `true` になり `manifest.yml`ます。

### 他のAdobeAPIへのアクセス {#access-adobe-apis}

<!-- TBD: Revisit this section. Where do we document console workspace creation?
-->

セットアップで追加作成した [!DNL Asset Compute] コンソールワークスペースへのAPIサービス。 これらのサービスは、によって生成されたJWTアクセストークンの一部で [!DNL Asset Compute Service]す。 トークンと他の秘密鍵証明書は、application actionオブジェクト内でアクセスでき `params` ます。

```javascript
const accessToken = params.auth.accessToken; // JWT token for Technical Account with entitlements from the console workspace to the API service
const clientId = params.auth.clientId; // Technical Account client Id
const orgId = params.auth.orgId; // Experience Cloud Organization
```

### サードパーティ製システムの資格情報を渡す {#pass-credentials-for-tp}

他の外部サービスの資格情報を処理するには、これらをアクションのデフォルトパラメーターとして渡します。 これらは自動的に送信中に暗号化されます。 詳しくは、『Runtime開発者ガイド』の「アクションの [作成](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/creating_actions.md)」を参照してください。 次に、展開時に環境変数を使用して設定します。 これらのパラメーターは、アクション内の `params` オブジェクトからアクセスできます。

の内部にデフォルトのパラメータ `inputs` ーを設定し `manifest.yml`ます。

```yaml
packages:
  __APP_PACKAGE__:
    actions:
      worker:
        function: 'index.js'
        runtime: 'nodejs:10'
        web: true
        inputs:
           secretKey: $SECRET_KEY
        annotations:
          require-adobe-auth: true
```

この `$VAR` 式は、という名前の環境変数から値を読み取り `VAR`ます。

開発時に、呼び出しシェルから設定された変数に加えて、ENVファイルから環境変数を `aio` 自動的に読み取るために、ローカルENVファイルに値を設定できます。 この例では、ENVファイルは次のようになります。

```CONF
#...
SECRET_KEY=secret-value
```

実稼働環境での展開では、GitHubアクションでのシークレットの使用など、CIシステムで環境変数を設定できます。 最後に、アプリケーション内のデフォルトのパラメーターにアクセスします。次のようにします。

```javascript
const key = params.secretKey;
```

## アプリケーションのサイズ調整 {#sizing-workers}

アプリケーションは、Adobe I/O Runtimeのコンテナで実行され、 [制限は](https://www.adobe.io/apis/experienceplatform/runtime/docs.html#!adobedocs/adobeio-runtime/master/guides/system_settings.md) 、 `manifest.yml`次のコマンドを使用して設定できます。

```yaml
    actions:
      myworker:
        function: /actions/myworker/index.js
        limits:
          timeout: 300000
          memorySize: 512
          concurrency: 1
```

Asset Computeアプリケーションが行う処理の量が多いので、最適なパフォーマンス（バイナリアセットを扱うのに十分な大きさ）と効率(未使用のコンテナメモリに起因するリソースの浪費を防ぐ)の上限を調整する必要が高くなります。

ランタイムでのアクションのデフォルトのタイムアウトは1分ですが、制限値（ミリ秒）を設定することで増やすことができ `timeout` ます。 大きなファイルを処理する場合は、この時間を長くしてください。 ソースのダウンロード、ファイルの処理、レンディションのアップロードにかかる合計時間を考慮します。 アクションがタイムアウトした場合、つまり、指定されたタイムアウト制限より前にアクティベーションが返されない場合、Runtimeはコンテナを破棄し、再利用しません。

自然別のアセット計算アプリケーションは、ネットワークとディスクのI/Oがバインドされる傾向があります。 ソースファイルを最初にダウンロードする必要があり、処理は多くの場合I/O負荷が高くなり、その後、結果として生じるレンディションが再びアップロードされます。

アクションコンテナが使用できるメモリは、MB単位で指定 `memorySize` されます。 現在のところ、これはコンテナがCPUにアクセスする量も定義し、最も重要なのは、Runtimeの使用コストの主要な要素です(コンテナが多いほどコストがかかります)。 処理に必要なメモリやCPUの量が多い場合は、大きい値を指定しますが、コンテナが大きいほど全体のスループットが低くなるので、リソースを浪費しないように注意してください。

また、この `concurrency` 設定を使用して、コンテナ内のアクションの同時実行性を制御することもできます。 これは、（同じアクションの）1つのコンテナが同時に受け取るアクティベーションの数です。 このモデルでは、アクションコンテナは、Node.jsサーバーが複数の同時要求を受け取るのと同様で、その制限を超えません。 設定しなかった場合、Runtimeのデフォルトは200です。これは、小さなFireflyアクションに適していますが、ローカル処理とディスクアクティビティの負荷が高い場合、通常はAsset Computeアプリケーションには大きすぎます。 導入に応じて、一部のアプリケーションは同時アクティビティでは動作しない場合があります。 アセット計算SDKを使用すると、異なる一意のアクティベーションーにファイルを書き込むことで、フォルダーが確実に区切られます。

およびの最適な数を見つけるには、アプリケーションをテスト `concurrency` し `memorySize`ます。 コンテナが大きい=メモリ制限が大きいと、同時実行性が高くなる可能性がありますが、トラフィック量が少ない場合は無駄になる可能性もあります。

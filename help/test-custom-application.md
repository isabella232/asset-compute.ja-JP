---
title: ' [!DNL Asset Compute Service]  カスタムアプリケーションのテストとデバッグ。'
description: ' [!DNL Asset Compute Service]  カスタムアプリケーションのテストとデバッグ。'
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '788'
ht-degree: 100%

---


# カスタムアプリケーションのテストとデバッグ {#test-debug-custom-worker}

## カスタムアプリケーションのユニットテストの実行 {#test-custom-worker}

[Docker Desktop](https://www.docker.com/get-started) をコンピューターにインストールします。カスタムワーカーをテストするには、アプリケーションのルートで次のコマンドを実行します。

```bash
$ aio app test
```

<!-- TBD
To run tests for a custom application, run `adobe-asset-compute test-worker` command in the root of the custom application application application.

Document interactively running `adobe-asset-compute` commands `test-worker` and `run-worker`.
-->

これにより、以下で説明するように、プロジェクト内の Asset Compute アプリケーションアクションに対するカスタムユニットテストフレームワークが実行されます。これは `package.json` ファイル内の設定を通じて接続されます。また、Jest などの JavaScript ユニットテストをおこなうこともできます。`aio app test` では両方を実行します。

[aio-cli-plugin-asset-compute](https://github.com/adobe/aio-cli-plugin-asset-compute#install-as-local-devdependency) プラグインは、カスタムアプリケーションに開発依存コンポーネントとして組み込まれているので、ビルド／テストシステムにインストールする必要はありません。

### アプリケーションユニットテストフレームワーク {#unit-test-framework}

Asset Compute アプリケーションのユニットテストフレームワークを使用すると、コードを書かずにアプリケーションをテストできます。アプリケーションのソースからレンディションへのファイル原則を利用します。テストソースファイル、オプションパラメーター、期待されるレンディション、カスタム検証スクリプトを含んだテストケースを定義するには、特定のファイルおよびフォルダー構造を設定する必要があります。デフォルトでは、レンディションのバイト等価性が比較されます。さらに、単純な JSON ファイルを使用して外部 HTTP サービスのモックを容易に作成できます。

### テストの追加 {#add-tests}

テストは、AIO プロジェクトのルートレベルにある `test` フォルダーに含まれている必要があります。各アプリケーションのテストケースは、パス `test/asset-compute/<worker-name>` 内にテストケースごとに 1 つのフォルダーとして配置する必要があります。

```yaml
action/
manifest.yml
package.json
...
test/
  asset-compute/
    <worker-name>/
        <testcase1>/
            file.jpg
            params.json
            rendition.png
        <testcase2>/
            file.jpg
            params.json
            rendition.gif
            validate
        <testcase3>/
            file.jpg
            params.json
            rendition.png
            mock-adobe.com.json
            mock-console.adobe.io.json
```

いくつかの例については、[カスタムアプリケーションの例](https://github.com/adobe/asset-compute-example-workers/)を参照してください。以下に詳細を説明します。

### テスト出力 {#test-output}

カスタムアプリケーションの詳細なテスト出力（ログなど）は、`aio app test` の出力で示されているように、Firefly アプリケーションのルートの `build` フォルダーにあります。

### 外部サービスのモック作成 {#mock-external-services}

テストケースで `mock-<HOST_NAME>.json` ファイルを定義すると、アクション内で外部サービス呼び出しのモックを作成できます。ここで、HOST_NAME はモックを作成するホストです。ユースケースの例としては、S3 を個別に呼び出すアプリケーションがあります。新しいテスト構造は次のようになります。

```json
test/
  asset-compute/
    <worker-name>/
      <testcase3>/
        file.jpg
        params.json
        rendition.png
        mock-<HOST_NAME1>.json
        mock-<HOST_NAME2>.json
```

モックファイルは、JSON 形式の http 応答です。詳しくは、[こちらのドキュメント](https://www.mock-server.com/mock_server/creating_expectations.html)を参照してください。モック作成対象のホスト名が複数ある場合は、複数の `mock-<mocked-host>.json` ファイルを定義します。以下は、`mock-google.com.json` という名前の、`google.com` のサンプルモックファイルです。

```json
[{
    "httpRequest": {
        "path": "/images/hello.txt"
        "method": "GET"
    },
    "httpResponse": {
        "statusCode": 200,
        "body": {
          "message": "hello world!"
        }
    }
}]
```

`worker-animal-pictures` サンプルには、やり取りするウィキメディアサービスの[モックファイル](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/test/asset-compute/worker-animal-pictures/simple-test/mock-upload.wikimedia.org.json)が含まれています。

#### テストケース間でのファイルの共有 {#share-files-across-test-cases}

複数のテスト間で `file.*`、`params.json`、`validate` のいずれかのスクリプトを共有する場合は、相対的なシンボリックリンクを使用することをお勧めします。これらは Git でサポートされます。共有ファイルには異なる名前をつけることもあるので、必ず一意の名前を付けてください。以下の例では、いくつかの共有ファイルとテスト自体のファイルを組み合わせています。

```json
tests/
    file-one.jpg
    params-resize.json
    params-crop.json
    validate-image-compare

    jpg-png-resize/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-resize.json
        rendition.png
        validate    - symlink: ../validate-image-compare

    jpg2-png-crop/
        file.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate    - symlink: ../validate-image-compare

    jpg-gif-crop/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate
```

### 予期されるエラーのテスト {#test-unexpected-errors}

エラーテストケースには、予期される `rendition.*` ファイルを含めず、予期される `errorReason` を `params.json` ファイル内で定義してください。

エラーテストケースの構造：

```json
<error_test_case>/
    file.jpg
    params.json
```

エラー理由を含んだパラメーターファイル：

```javascript
{
    "errorReason": "SourceUnsupported",
    // ... other params
}
```

エラー理由の一覧と説明については、[Asset Compute のエラー理由](https://github.com/adobe/asset-compute-commons#error-reasons)を参照してください。

## カスタムアプリケーションのデバッグ {#debug-custom-worker}

次の手順は、Visual Studio Code を使用したカスタムアプリケーションのデバッグ方法を示しています。ライブログの確認、ブレークポイントのヒット、コードのステップスルー、ローカルコード変更のライブ再読み込みをアクティベーションのたびにおこなうことができます。

これらの手順の多くは通常、初期設定では `aio` で自動的に実行されます。詳しくは、[Firefly ドキュメント](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md)の「Debugging the Application」（アプリケーションのデバッグ）の節を参照してください。現時点では、以下の手順には回避策が含まれています。

1. GitHub の最新の [wskdebug](https://github.com/apache/openwhisk-wskdebug) とオプションの [ngrok](https://www.npmjs.com/package/ngrok) をインストールします。

   ```shell
   npm install -g @openwhisk/wskdebug
   npm install -g ngrok --unsafe-perm=true
   ```

1. ユーザー設定 JSON ファイルに追加します。古い VS Code デバッガーが引き続き使用されます。新しいデバッガーには wskdebug（`"debug.javascript.usePreview": false`）に関して[いくつかの問題](https://github.com/apache/openwhisk-wskdebug/issues/74)があります。
1. `aio app run` で開いているアプリのインスタンスをすべて閉じます。
1. `aio app deploy` を使用して最新のコードをデプロイします。
1. `npx adobe-asset-compute devtool` を使用して、Asset Compute 開発者ツールのみ実行します。ツールを開いたままにします。
1. VS Code エディターで、次のデバッグ設定を `launch.json` に追加します。

   ```json
   {
     "type": "node",
     "request": "launch",
     "name": "wskdebug worker",
     "runtimeExecutable": "wskdebug",
     "args": [
       "PROJECT-0.0.1/__secured_worker",           // Replace this with your ACTION NAME
       "${workspaceFolder}/actions/worker/index.js",
       "-l",
       "--ngrok"
     ],
     "localRoot": "${workspaceFolder}",
     "remoteRoot": "/code",
     "outputCapture": "std",
     "timeout": 30000
   }
   ```

   `aio app deploy` の出力から ACTION NAME を取得します。例えば、`Your deployed actions -> TypicalCoffeeCat-0.0.1/__secured_worker` のような情報です。

1. 実行／デバッグ設定から `wskdebug worker` を選択し、再生アイコンを押します。**[!UICONTROL Debug Console]** ウィンドウに **[!UICONTROL Ready for activations]** と表示されるまで待ちます。

1. 開発者ツールで「**[!UICONTROL run]**」をクリックします。実行中のアクションが VS Code エディターに表示され、ログの表示が開始されます。

1. コードにブレークポイントを設定し、再実行すると、ブレークポイントがヒットします。

コードの変更内容はすべてリアルタイムで読み込まれ、次回アクティベーションがおこなわれるとすぐに有効になります。

>[!NOTE]
>
>カスタムアプリケーションでは、リクエストごとに 2 つのアクティベーションが存在します。最初のリクエストは、SDK コード内で自分自身を非同期的に呼び出す Web アクションです。2 つ目のアクティベーションは、コードにヒットするものです。

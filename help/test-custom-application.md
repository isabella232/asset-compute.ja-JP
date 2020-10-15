---
title: カスタムアプリケーションのテストと [!DNL Asset Compute Service] デバッグを行います。
description: カスタムアプリケーションのテストと [!DNL Asset Compute Service] デバッグを行います。
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '788'
ht-degree: 0%

---


# カスタムアプリケーションのテストとデバッグ {#test-debug-custom-worker}

## カスタムアプリケーションの単体テストを実行する {#test-custom-worker}

コンピューターに [Docker Desktop](https://www.docker.com/get-started) をインストールします。 カスタムワーカーをテストするには、アプリケーションのルートで次のコマンドを実行します。

```bash
$ aio app test
```

<!-- TBD
To run tests for a custom application, run `adobe-asset-compute test-worker` command in the root of the custom application application application.

Document interactively running `adobe-asset-compute` commands `test-worker` and `run-worker`.
-->

これにより、以下に説明するように、プロジェクト内のアセット計算アプリケーションアクションに対するカスタム単体テストフレームワークが実行されます。 ファイル内の設定を介して接続され `package.json` ます。 また、JestなどのJavaScriptの単体テストを行うこともできます。 `aio app test` 両方を実行します。

aio-cli-plugin-asset-compute [](https://github.com/adobe/aio-cli-plugin-asset-compute#install-as-local-devdependency) プラグインは、カスタムアプリケーションアプリケーションに開発依存性として組み込まれているので、ビルド/テストシステムにインストールする必要がありません。

### 申し込みユニットテストフレームワーク {#unit-test-framework}

Asset Computeアプリケーション単体テストフレームワークを使用すると、コードを記述しなくてもアプリケーションをテストできます。 アプリケーションのソースからレンディションへのファイルの原則に依存します。 テストソースファイル、オプションのパラメーター、期待されるレンディション、カスタム検証スクリプトを使用してテストケースを定義するには、特定のファイルとフォルダー構造を設定する必要があります。 デフォルトでは、レンディションはバイトの等価性のために比較されます。 さらに、外部HTTPサービスは、単純なJSONファイルを使用して簡単にモックできます。

### 追加テスト {#add-tests}

テストは、AIOプロジェクトのルートレベルの `test` フォルダー内で行う必要があります。 各アプリケーションのテストケースは、各テストケースに対して1つのフォルダーを含むパス `test/asset-compute/<worker-name>`に置く必要があります。

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

カスタムアプリケーションの [例を見てみましょう](https://github.com/adobe/asset-compute-example-workers/) 。 以下に詳細を示します。

### Test output {#test-output}

カスタムアプリケーションのログを含む詳細なテスト出力は、出力で示すように、Fireflyアプリケーションのルートにある `build` フォルダーで確認でき `aio app test` ます。

### 外部サービスのモック {#mock-external-services}

テストケースで `mock-<HOST_NAME>.json` ファイルを定義すると、アクション内の外部サービスの呼び出しをモックできます。この場合、HOST_NAMEはモックするホストです。 使用例としては、S3を個別に呼び出すアプリケーションがあります。 新しいテスト構造は次のようになります。

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

モックファイルは、JSON形式のhttp応答です。 For more information, see [this documentation](https://www.mock-server.com/mock_server/creating_expectations.html). 複数のホスト名をモックする場合は、複数の `mock-<mocked-host>.json` ファイルを定義します。 次に、 `google.com``mock-google.com.json`名前の付いた

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

この例には、相互作用するウィキメディアサービスの `worker-animal-pictures` モックファイル [](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/test/asset-compute/worker-animal-pictures/simple-test/mock-upload.wikimedia.org.json) が含まれています。

#### テストケース間でファイルを共有 {#share-files-across-test-cases}

複数のテストでスクリプトを共有する場合は、相対的なシンボリックリンクを使用す `file.*`るこ `params.json``validate` とをお勧めします。 gitでサポートされます。 共有ファイルには、異なる名前を付ける場合があるので、一意の名前を付けてください。 次の例では、いくつかの共有ファイルとそれ自体のファイルを混合し、一致させるテストが行われています。

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

エラーテストの場合は、予期された `rendition.*` ファイルが含まれていないことが必要です。また、 `errorReason``params.json` ファイル内で期待されるファイルを定義する必要があります。

エラーテストケース構造：

```json
<error_test_case>/
    file.jpg
    params.json
```

エラーの原因のあるパラメーターファイル：

```javascript
{
    "errorReason": "SourceUnsupported",
    // ... other params
}
```

「 [アセット計算エラーの原因」の完全なリストと説明を参照してください](https://github.com/adobe/asset-compute-commons#error-reasons)。

## カスタムアプリケーションのデバッグ {#debug-custom-worker}

次の手順は、Visual Studioコードを使用したカスタムアプリケーションのデバッグ方法を示しています。 ライブログの確認、ブレークポイントのヒット、コードのステップスルー、および各アクティベーションでのローカルコード変更のライブ再読み込みが可能です。

これらの手順の多くは通常、初期設定の状態で自動化され `aio` ます。詳しくは、『Firefallyドキュメント』の「アプリケーションのデバッグ [](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md)」の節を参照してください。 現時点では、次の手順に回避策を示します。

1. GitHubとオプションの [ngrokから最新のwskdebug](https://github.com/apache/openwhisk-wskdebug) をインストールし [ます](https://www.npmjs.com/package/ngrok)。

   ```shell
   npm install -g @openwhisk/wskdebug
   npm install -g ngrok --unsafe-perm=true
   ```

1. を追加ユーザー設定のJSONファイルに追加します。 古いVSコードデバッガを使い続けますが、新しいデバッガにはwskdebugに関する [いくつかの問題があります](https://github.com/apache/openwhisk-wskdebug/issues/74) 。 `"debug.javascript.usePreview": false`.
1. で開いているアプリのインスタンスをすべて閉じ `aio app run`ます。
1. を使用して最新のコードをデプロイし `aio app deploy`ます。
1. を使用して、Asset compute Devtoolのみを実行し `npx adobe-asset-compute devtool`ます。 開けたまま。
1. VSコードエディターで、次のデバッグ設定を `launch.json`

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

   の出力からアクション名を取得し `aio app deploy`ます。 見える `Your deployed actions -> TypicalCoffeeCat-0.0.1/__secured_worker`。

1. 実行/デバッグ `wskdebug worker` の設定からを選択し、再生アイコンを押します。 [ **[!UICONTROL デバッグコンソール]** ]ウィンドウに[アクティベーションの **[!UICONTROL 準備完了]** ]と表示されるまで、開始するまで待ちます。

1. 開発ツールで **[!UICONTROL 実行]** (run)をクリックします。 VSコードエディターで実行中のアクションとログ開始が表示されます。

1. コードにブレークポイントを設定し、再実行すると、コードがヒットします。

コードの変更はすべてリアルタイムで読み込まれ、次のアクティベーションが発生するとすぐに有効になります。

>[!NOTE]
>
>カスタムアプリケーションのリクエストごとに2つのアクティベーションが存在します。 最初のリクエストは、SDKコード内で非同期に呼び出すWebアクションです。 2つ目のアクティベーションは、コードをヒットしたものです。

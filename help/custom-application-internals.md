---
title: カスタムアプリケーションの動作を理解する。
description: カスタムアプリケ [!DNL Asset Compute Service] ーションの内部作業を参照してください。
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '774'
ht-degree: 0%

---


# カスタムアプリケーションの内部 {#how-custom-application-works}

次の図を参照して、デジタルアセットがクライアントによるカスタムアプリケーションで処理される場合のエンドツーエンドのワークフローを理解してください。

![カスタムアプリケーションワークフロー](assets/customworker.png)

*図：を使用してアセットを処理する手順 [!DNL Asset Compute Service]です。*

## 登録 {#registration}

クライアントは、Adobeアセット計算用のAdobeI/Oイベントを受け取るためのジャーナルURLを設定して取得するため [`/register`](api.md#register)[`/process`](api.md#process-request) に、に対する最初の要求の前に1回呼び出す必要があります。

```sh
curl -X POST \
  https://asset-compute.adobe.io/register \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY"
```

JavaScriptライブラリは、NodeJSアプリケーションで使用して、登録、処理、非同期イベント処理から必要なすべての手順を処理できます。 [`@adobe/asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) 必要なヘッダーの詳細については、「 [認証と認証](api.md)」を参照してください。

## 処理中 {#processing}

クライアントが [処理リクエストを送信します](api.md#process-request) 。

```sh
curl -X POST \
  https://asset-compute.adobe.io/process \
  -H "x-ims-org-id: $ORG_ID" \
  -H "x-gw-ims-org-id: $ORG_ID" \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "x-api-key: $API_KEY" \
  -d "<RENDITION_JSON>
```

クライアントは、事前に署名されたURLを使用してレンディションを正しくフォーマットする必要があります。 JavaScriptライブラリは、URLに事前署名するためにNodeJSアプリケーションで使用できます。 [`@adobe/node-cloud-blobstore-wrapper`](https://github.com/adobe/node-cloud-blobstore-wrapper#presigned-urls) 現在、ライブラリはAzure BlobストレージとAWS S3コンテナのみをサポートしています。

処理要求は、AdobeI/Oイベント `requestId` をポーリングするのに使用できる値を返します。

カスタムアプリケーション処理リクエストの例を以下に示します。

```json
{
    "source": "https://www.adobe.com/some-source-file.jpg",
    "renditions" : [
        {
            "worker": "https://my-project-namespace.adobeioruntime.net/api/v1/web/my-namespace-version/my-worker",
            "name": "rendition1.jpg",
            "target": "https://some-presigned-put-url-for-rendition1.jpg",
        }
    ],
    "userData": {
        "my-asset-id": "1234567890"
    }
}
```

は、カスタムアプリケーションにカスタムアプリケーションレンディション要求を [!DNL Asset Compute Service] 送信します。 Project Fireflyの保護されたWebアクションURLである、指定されたアプリケーションURLへのHTTPPOSTを使用します。 すべてのリクエストで、データのセキュリティを最大限に高めるためにHTTPSプロトコルが使用されます。

カスタムアプリケーションが [使用するAsset Compute SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) （アセット計算SDK）は、HTTPPOSTリクエストを処理します。 また、ソースのダウンロード、レンディションのアップロード、I/Oイベントの送信、エラー処理も処理されます。

<!-- TBD: Add the application diagram. -->

### Application code {#application-code}

カスタムコードは、ローカルで使用可能なソースファイル(`source.path`)を受け取るコールバックを提供する必要があります。 は、アセット処理要求 `rendition.path` の最終結果を配置する場所です。 カスタムアプリケーションは、コールバックを使用して、ローカルで使用可能なソースファイルを、渡された名前(`rendition.path`)を使用してレンディションファイルに変換します。 カスタムアプリケーションは、レンディションを作成す `rendition.path` るにはに書き込む必要があります。

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

// worker() is the entry point in the SDK "framework".
// The asynchronous function defined is the rendition callback.
exports.main = worker(async (source, rendition) => {

    // Tip: custom worker parameters are available in rendition.instructions.
    console.log(rendition.instructions.name); // should print out `rendition.jpg`.

    // Simplest example: copy the source file to the rendition file destination so as to transfer the asset as is without processing.
    await fs.copyFile(source.path, rendition.path);
});
```

### ソースファイルのダウンロード {#download-source}

カスタムアプリケーションは、ローカルファイルのみを扱います。 ソースファイルのダウンロードは、 [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk)（アセット計算SDK）で処理されます。

### レンディションの作成 {#rendition-creation}

SDKは、各レンディションに対して非同期 [レンディションコールバック関数](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) を呼び出します。

このコールバック関数は、 [source](https://github.com/adobe/asset-compute-sdk#source) オブジェクトと [rendition](https://github.com/adobe/asset-compute-sdk#rendition) オブジェクトにアクセスできます。 は `source.path` 既に存在し、ソースファイルのローカルコピーへのパスです。 は、処理 `rendition.path` 済みのレンディションを保存する必要があるパスです。 disableSourceDownloadフラグが設定されていない限り [、アプリケーションは完全にを使用する必要があり](https://github.com/adobe/asset-compute-sdk#worker-options-optional)`rendition.path`ます。 そうしないと、SDKはレンディションファイルを特定または識別できず、失敗します。

この例は、カスタムアプリケーションの構造を説明し、焦点を絞るために過剰に簡略化されています。 アプリケーションは、ソースファイルをレンディションのコピー先にコピーするだけです。

レンディションコールバックパラメーターについて詳しくは、「 [Asset Compute SDK API](https://github.com/adobe/asset-compute-sdk#api-details)」を参照してください。

### レンディションをアップロード {#upload-rendition}

各レンディションが作成され、が指定したパスを持つファイルに保存された後 `rendition.path`、 [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk#adobe-asset-compute-worker-sdk) （AWSまたはAzure）は、各レンディションをクラウドストレージ（AWSまたはAzure）にアップロードします。 カスタムアプリケーションは、受信要求に同じアプリケーションURLを指す複数のレンディションが含まれている場合に限り、同時に複数のレンディションを取得します。 クラウドへのアップロードストレージは、各レンディションの後、および次のレンディションのコールバックを実行する前に実行されます。

の動作 `batchWorker()` は異なります。これは、実際にはすべてのレンディションが処理され、すべてのレンディションが処理された後にアップロードされるからです。

## AdobeI/Oイベント {#aio-events}

SDKは、レンディションごとにAdobeI/Oイベントを送信します。 これらのイベントは、タイプ `rendition_created` または結果 `rendition_failed` に応じて異なります。 イベントについて詳しくは、 [Asset Compute asynchronousイベント](api.md#asynchronous-events) （非同期を計算）を参照してください。

## AdobeI/Oイベントの受信 {#receive-aio-events}

クライアントは、消費ロジックに従って [AdobeI/Oイベントジャーナル](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#/Journaling) をポーリングします。 最初のジャーナルURLは、 `/register` API応答で提供されるURLです。 イベントは、イベントに存在 `requestId` し、で返されるのと同じを使用して識別でき `/process`ます。 すべてのレンディションには、別々のイベントがあり、レンディションがアップロード（または失敗）されるとすぐに送信されます。 一致するイベントを受け取ると、クライアントは結果のレンディションを表示したり、処理したりできます。

JavaScriptライブラリ [`asset-compute-client`](https://github.com/adobe/asset-compute-client#usage) では、ジャーナルのポーリングを簡単に行うことができます。この方法を使用して、すべてのイベントを取得するこ `waitActivation()` とができます。

```javascript
const events = await assetCompute.waitActivation(requestId);
await Promise.all(events.map(event => {
    if (event.type === "rendition_created") {
        // get rendition from cloud storage location
    }
    else if (event.type === "rendition_failed") {
        // failed to process
    }
    else {
        // other event types
        // (could be added in the future)
    }
}));
```

ジャーナルイベントの取得方法について詳しくは、「 [AdobeI/OイベントAPI](https://www.adobe.io/apis/experienceplatform/events/ioeventsapi.html#!adobedocs/adobeio-events/master/events-api-reference.yaml)」を参照してください。

<!-- TBD:
* Illustration of the controls/data flow.
* Basic overview, in text and not code, of how an application works.
-->

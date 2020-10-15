---
title: '[!DNL Asset Compute Service] HTTP API.'
description: '[!DNL Asset Compute Service] HTTP APIを使用してカスタムアプリケーションを作成します。'
translation-type: tm+mt
source-git-commit: 18e97e544014933e9910a12bc40246daa445bf4f
workflow-type: tm+mt
source-wordcount: '2931'
ht-degree: 3%

---


# [!DNL Asset Compute Service] HTTP API {#asset-compute-http-api}

APIの使用は開発目的に限られます。 このAPIは、カスタムアプリケーションの開発時にコンテキストとして提供されます。 [!DNL Adobe Experience Manager] を使用するCloud Serviceは、APIを使用して処理情報をカスタムアプリケーションに渡します。 詳しくは、アセットのマイクロサービスの [使用および処理プロファイルを参照してください](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)。

>[!NOTE]
>
>[!DNL Asset Compute Service] は、Cloud Serviceとしてのみ使用 [!DNL Experience Manager] できます。

HTTP APIの任意のクライアントは、次の高レベルのフローに従う必要があります。 [!DNL Asset Compute Service]

1. クライアントがIMS組織でプロジ [!DNL Adobe Developer Console] ェクトとしてプロビジョニングされる。 各クライアント(システムまたは環境)は、イベントデータフローを分離するために、独自のプロジェクトを必要とします。

1. クライアントは、 [JWT（サービスアカウント）認証を使用して、テクニカルアカウントのアクセストークンを生成します](https://www.adobe.io/authentication/auth-methods.html)。

1. クライアントは1回だけ呼び出 [`/register`](#register) してジャーナルURLを取得します。

1. クライアントは、レンディション [`/process`](#process-request) を生成する各アセットに対して呼び出します。 呼び出しは非同期的です。

1. クライアントは定期的にジャーナルをポーリングしてイベント [を受け取り](#asynchronous-events)ます。 レンディションが正常に処理された(`rendition_created` イベントタイプ)とき、またはエラー(`rendition_failed` イベントタイプ)が発生した場合に、要求された各レンディションのイベントを受け取ります。

@adobe/asset-compute-client [](https://github.com/adobe/asset-compute-client) モジュールを使用すると、Node.jsコードでAPIを簡単に使用できます。

## 認証と認証 {#authentication-and-authorization}

すべてのAPIでアクセストークン認証が必要です。 リクエストでは、次のヘッダーを設定する必要があります。

1. `Authorization` adobe開発者コンソールプロジェクトから [](https://www.adobe.io/authentication/auth-methods.html) JWT交換経由で受け取った、テクニカルアカウントトークンであるbearerトークンを持つヘッダー。 ス [コープ](#scopes) は以下のとおりです。

<!-- TBD: Change the existing URL to a new path when a new path for docs is available. The current path contains master word that is not an inclusive term. Logged ticket in AIO's GitHub repo to get a new URL.
-->

1. `x-gw-ims-org-id` ヘッダーにIMS組織IDを割り当てます。

1. `x-api-key` を、 [!DNL Adobe Developers Console] プロジェクトのクライアントIDに置き換えます。

### 範囲 {#scopes}

アクセストークンに対して次のスコープを確認します。

* `openid`
* `AdobeID`
* `asset_compute`
* `read_organizations`
* `event_receiver`
* `event_receiver_api`
* `adobeio_api`
* `additional_info.roles`
* `additional_info.projectedProductContext`

これらを行うには、 [!DNL Adobe Developer Console] プロジェクトが、 `Asset Compute`、、 `I/O Events`および `I/O Management API` サービスを登録する必要があります。 個々のスコープの分類は次のとおりです。

* 基本
   * scopes: `openid,AdobeID`

* アセット計算
   * metascope: `asset_compute_meta`
   * scopes: `asset_compute,read_organizations`

* AdobeI/Oイベント
   * metascope: `event_receiver_api`
   * scopes: `event_receiver,event_receiver_api`

* AdobeI/O管理API
   * metascope: `ent_adobeio_sdk`
   * scopes: `adobeio_api,additional_info.roles,additional_info.projectedProductContext`

## 登録 {#register}

サービスに登録されている一意の [!DNL Asset Compute service] プロジェクトの各クライアントは、処理要求を行う前に [!DNL Adobe Developer Console] 登録する必要があります [](#register-request) 。 登録ステップは、レンディション処理から非同期イベントを取得するために必要な固有のイベントジャーナルを返します。

ライフサイクルの最後に、クライアントは [登録解除できます](#unregister-request)。

### 登録要求 {#register-request}

このAPI呼び出しは、 [!DNL Asset Compute] クライアントを設定し、イベントジャーナルURLを提供します。 これは理想的な操作であり、各クライアントに対して1回だけ呼び出す必要があります。 このメソッドを再度呼び出して、ジャーナルURLを取得できます。

| パラメーター | 値 |
|--------------------------|------------------------------------------------------|
| 方法 | `POST` |
| パス | `/register` |
| ヘッダー `Authorization` | すべての [認証関連のヘッダー](#authentication-and-authorization)。 |
| ヘッダー `x-request-id` | オプションで、システム間の処理要求の固有のエンドツーエンド識別子に対して、クライアントが設定できます。 |
| リクエスト本文 | 空にする必要があります。 |

### レスポンスの登録 {#register-response}

| パラメーター | 値 |
|-----------------------|------------------------------------------------------|
| MIME タイプ | `application/json` |
| ヘッダー `X-Request-Id` | リクエストヘッダーと同じか、一意に生成された `X-Request-Id` ヘッダーと同じです。 複数のシステムやサポートの要求を識別するために使用します。 |
| 応答本文 | 、 `journal`および `ok` /またはの `requestId` フィールドを持つJSONオブジェクト。 |

HTTPステータスコードは次のとおりです。

* **200件の成功**:リクエストが成功したとき。 このURLには、(成功した場合にイベントタイプとして、失敗した場合に)トリガーされる非同期処理の結果に関して通知される `journal` URLが含まれ `/process` ます。このURLは、非同期処理の結果に関して通知され `rendition_created``rendition_failed` るURLです。

   ```json
   {
       "ok": true,
       "journal": "https://api.adobe.io/events/organizations/xxxxx/integrations/xxxx/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
       "requestId": "1234567890"
   }
   ```

* **401 Unauthorized**:要求に有効な [認証がない場合に発生します](#authentication-and-authorization)。 無効なアクセストークンや無効なAPIキーなどがあります。

* **403 Forbidden**:要求に有効な [認証がない場合に発生します](#authentication-and-authorization)。 例えば、有効なアクセストークンが考えられますが、AdobeDeveloper Consoleプロジェクト（テクニカルアカウント）が、必要なすべてのサービスに登録されているわけではありません。

* **429リクエストが多すぎます**:このクライアントまたはその他の方法でシステムが過負荷の場合に発生します。 クライアントは [指数バックオフで再試行する必要があります](https://en.wikipedia.org/wiki/Exponential_backoff)。 身体が空だ。
* **4xxエラー**:その他のクライアントエラーが発生し、登録に失敗した場合。 通常、以下のようなJSON応答が返されますが、これはすべてのエラーで保証されているわけではありません。

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **5xxエラー**:他のサーバー側のエラーが発生し、登録に失敗した場合に発生します。 通常、以下のようなJSON応答が返されますが、これはすべてのエラーで保証されているわけではありません。

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

### 登録解除要求 {#unregister-request}

このAPI呼び出しは、 [!DNL Asset Compute] クライアントの登録を解除します。 この後は、を呼び出すことはできません `/process`。 未登録のクライアントまたは未登録のクライアントに対するAPI呼び出しを使用すると、 `404` エラーが返されます。

| パラメーター | 値 |
|--------------------------|------------------------------------------------------|
| 方法 | `POST` |
| パス | `/unregister` |
| ヘッダー `Authorization` | すべての [認証関連のヘッダー](#authentication-and-authorization)。 |
| ヘッダー `x-request-id` | オプションで、システム間の処理要求の固有のエンドツーエンド識別子に対して、クライアントが設定できます。 |
| リクエスト本文 | 空白。 |

### 応答の登録解除 {#unregister-response}

| パラメーター | 値 |
|-----------------------|------------------------------------------------------|
| MIME タイプ | `application/json` |
| ヘッダー `X-Request-Id` | リクエストヘッダーと同じか、一意に生成された `X-Request-Id` ヘッダーと同じです。 複数のシステムやサポートの要求を識別するために使用します。 |
| 応答本文 | とフィールドを含むJSONオブジェクト `ok` です `requestId` 。 |

ステータスコードは次のとおりです。

* **200件の成功**:登録とジャーナルが見つかり、削除されたときに発生します。

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **401 Unauthorized**:要求に有効な [認証がない場合に発生します](#authentication-and-authorization)。 無効なアクセストークンや無効なAPIキーなどがあります。

* **403 Forbidden**:要求に有効な [認証がない場合に発生します](#authentication-and-authorization)。 例えば、有効なアクセストークンが考えられますが、AdobeDeveloper Consoleプロジェクト（テクニカルアカウント）が、必要なすべてのサービスに登録されているわけではありません。

* **404 Not found**:指定された資格情報に対して現在の登録がない場合に発生します。

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **429リクエストが多すぎます**:システムが過負荷の場合に発生します。 クライアントは [指数バックオフで再試行する必要があります](https://en.wikipedia.org/wiki/Exponential_backoff)。 身体が空だ。

* **4xxエラー**:その他のクライアントエラーが発生し、登録解除に失敗した場合に発生します。 通常、以下のようなJSON応答が返されますが、これはすべてのエラーで保証されているわけではありません。

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **5xxエラー**:他のサーバー側のエラーが発生し、登録に失敗した場合に発生します。 通常、以下のようなJSON応答が返されますが、これはすべてのエラーで保証されているわけではありません。

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

## プロセス要求 {#process-request}

この `process` 操作では、要求の手順に基づいて、ソースアセットを複数のレンディションに変換するジョブが送信されます。 完了(イベントタイプ `rendition_created`)またはエラー(イベントタイプ `rendition_failed`)に関する通知は、イベントジャーナルに送信されます。このユーザーは、何回かの [要求を行う前に1回](#register)`/process` /登録を使用して取得する必要があります。 間違って形成されたリクエストは、400エラーコードで即座に失敗します。

バイナリは、AmazonAWS S3事前署名URLやAzure BlobストレージSAS URLなどのURLを使用して参照され、アセット(URL)の読み取りとレンディション( `source``GET``PUT` URL)の書き込みの両方に使用されます。 クライアントは、これらの事前署名済みURLの生成を担当します。

| パラメーター | 値 |
|--------------------------|------------------------------------------------------|
| 方法 | `POST` |
| パス | `/process` |
| MIME タイプ | `application/json` |
| ヘッダー `Authorization` | すべての [認証関連のヘッダー](#authentication-and-authorization)。 |
| ヘッダー `x-request-id` | オプションで、システム間の処理要求の固有のエンドツーエンド識別子に対して、クライアントが設定できます。 |
| リクエスト本文 | 以下に説明するように、プロセスリクエストのJSON形式にする必要があります。 処理するアセットと生成するレンディションに関する手順を示します。 |

### プロセス要求JSON {#process-request-json}

のリクエスト本文 `/process` は、次の高レベルスキーマを持つJSONオブジェクトです。

```json
{
    "source": "",
    "renditions" : []
}
```

以下のフィールドを使用できます。

| 名前 | 型 | 説明 | 例 |
|--------------|----------|-------------|---------|
| `source` | `string` | 処理するソースアセットのURL。 要求されたレンディション形式(例： `fmt=zip`)をクリックします。 | `"http://example.com/image.jpg"` |
| `source` | `object` | 処理するソースアセットの説明。 以下の「 [ソースオブジェクトフィールドの説明](#source-object-fields) 」を参照してください。 要求されたレンディション形式(例： `fmt=zip`)をクリックします。 | `{"url": "http://example.com/image.jpg", "mimeType": "image/jpeg" }` |
| `renditions` | `array` | ソースファイルから生成するレンディション。 各レンディションオブジェクトは、 [レンディションの指示をサポートします](#rendition-instructions)。 必須. | `[{ "target": "https://....", "fmt": "png" }]` |

URL `source` として表示さ `<string>` れるURLか、フィールドを追加したURL `<object>` のいずれかを指定できます。 次のバリエーションが似ています。

```json
"source": "http://example.com/image.jpg"
```

```json
"source": {
    "url": "http://example.com/image.jpg"
}
```

### ソースオブジェクトフィールド {#source-object-fields}

| 名前 | 型 | 説明 | 例 |
|-----------|----------|-------------|---------|
| `url` | `string` | 処理するソースアセットのURL。 必須. | `"http://example.com/image.jpg"` |
| `name` | `string` | ソースアセットファイル名。 MIMEタイプが検出されない場合は、名前のファイル拡張子が使用される場合があります。 URLパス内のファイル名や、バイナリリソースの `content-disposition` ヘッダー内のファイル名よりも優先されます。 初期設定は「file」です。 | `"image.jpg"` |
| `size` | `number` | ソースアセットファイルのサイズ（バイト単位）。 バイナリリソースの `content-length` ヘッダーよりも優先されます。 | `10234` |
| `mimetype` | `string` | ソースアセットファイルのMIMEタイプ。 バイナリリソースの `content-type` ヘッダーよりも優先されます。 | `"image/jpeg"` |

### 完全な `process` リクエストの例 {#complete-process-request-example}

```json
{
    "source": "https://www.adobe.com/content/dam/acom/en/lobby/lobby-bg-bts2017-logged-out-1440x860.jpg",
    "renditions" : [{
            "name": "image.48x48.png",
            "target": "https://some-presigned-put-url-for-image.48x48.png",
            "fmt": "png",
            "width": 48,
            "height": 48
        },{
            "name": "image.200x200.jpg",
            "target": "https://some-presigned-put-url-for-image.200x200.jpg",
            "fmt": "jpg",
            "width": 200,
            "height": 200
        },{
            "name": "cqdam.xmp.xml",
            "target": "https://some-presigned-put-url-for-cqdam.xmp.xml",
            "fmt": "xmp"
        },{
            "name": "cqdam.text.txt",
            "target": "https://some-presigned-put-url-for-cqdam.text.txt",
            "fmt": "text"
    }]
}
```

## プロセスの応答 {#process-response}

リクエストは、基本的なリクエストの検証に基づいて、成功または失敗を即座に返します。 `/process` 実際のアセット処理は非同期で実行されます。

| パラメーター | 値 |
|-----------------------|------------------------------------------------------|
| MIME タイプ | `application/json` |
| ヘッダー `X-Request-Id` | リクエストヘッダーと同じか、一意に生成された `X-Request-Id` ヘッダーと同じです。 複数のシステムやサポートの要求を識別するために使用します。 |
| 応答本文 | とフィールドを含むJSONオブジェクト `ok` です `requestId` 。 |

ステータスコード：

* **200件の成功**:リクエストが正常に送信された場合。 応答JSONには次が含まれ `"ok": true`ます。

   ```json
   {
       "ok": true,
       "requestId": "1234567890"
   }
   ```

* **400無効な要求**:リクエストの形式が正しくない場合。例えば、リクエストJSONに必須フィールドがない場合などです。 応答JSONには次が含まれ `"ok": false`ます。

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **401 Unauthorized**:要求に有効な [認証がない場合](#authentication-and-authorization)。 無効なアクセストークンや無効なAPIキーなどがあります。
* **403 Forbidden**:要求に有効な [認証がない場合](#authentication-and-authorization)。 例えば、有効なアクセストークンが考えられますが、AdobeDeveloper Consoleプロジェクト（テクニカルアカウント）が、必要なすべてのサービスに登録されているわけではありません。
* **429リクエストが多すぎます**:このクライアントまたは一般的にシステムが過負荷の場合。 クライアントは [指数バックオフで再試行できます](https://en.wikipedia.org/wiki/Exponential_backoff)。 身体が空だ。
* **4xxエラー**:その他のクライアントエラーが発生した場合。 通常、以下のようなJSON応答が返されますが、これはすべてのエラーで保証されているわけではありません。

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

* **5xxエラー**:他のサーバー側のエラーが発生した場合。 通常、以下のようなJSON応答が返されますが、これはすべてのエラーで保証されているわけではありません。

   ```json
   {
       "ok": false,
       "requestId": "1234567890",
       "message": "error message"
   }
   ```

ほとんどのクライアントは、401や403などの設定上の問題や400のような無効なリクエストを [除き](https://en.wikipedia.org/wiki/Exponential_backoff) 、エラー時に ** 指数的バックオフを使用して完全に同じリクエストを再試行する傾向があります。 429応答を介した通常のレート制限に加え、一時的なサービスの停止または制限により、5xxエラーが発生する場合があります。 その後、しばらくしてから再試行することをお勧めします。

すべてのJSON応答（存在する場合）に、ヘッダーと同じ値 `requestId` の `X-Request-Id` が含まれます。 常に存在するので、ヘッダーから読み取ることをお勧めします。 また、 `requestId` は、としてのリクエストの処理に関連するすべてのイベントで返され `requestId`ます。 クライアントは、この文字列の形式を想定してはなりません。この文字列は不透明な文字列識別子です。

## 後処理のオプトイン {#opt-in-to-post-processing}

「 [アセット計算SDK](https://github.com/adobe/asset-compute-sdk) 」は、基本的な画像後処理オプションのセットをサポートしています。 カスタムワーカーは、レンディションオブジェクトのフィールドオプトインをに設定することで、明示的に後処理を行うことができ `postProcess``true`ます。

次の使用例がサポートされています。

* 切り抜き.w、切り抜き.h、切り抜き.x、切り抜き.yで制限が定義された長方形にレンディションを切り抜きます。これは、レンディションオブジェクト `instructions.crop` でによって定義されます。
* 幅、高さまたはその両方を使用して画像のサイズを変更します。 レンディションオブジェクト `instructions.width` のおよび `instructions.height` で定義されます。 幅または高さのみを使用してサイズを変更する場合は、値を1つだけ設定します。 コンピューティングサービスは、縦横比を保持します。
* JPEG画像の画質を設定します。 これは、レンディションオブジェクト `instructions.quality` でによって定義されます。 最も良いクォリティはで示され、値を小さくすると品質が低下します。 `100`
* インターレース画像を作成します。 これは、レンディションオブジェクト `instructions.interlace` でによって定義されます。
* DPIを設定して、ピクセルに適用するスケールを調整して、デスクトップパブリッシング用にレンダリングサイズを調整します。 dpi解像度を変更するた `instructions.dpi` めに、レンディションオブジェクトで定義されます。 ただし、異なる解像度で同じサイズになるように画像のサイズを変更する場合は、 `convertToDpi` 手順に従います。
* 指定したターゲット解像度(DPI)で、レンダリングされる幅または高さが元の画像と同じになるように画像のサイズを変更します。 これは、レンディションオブジェクト `instructions.convertToDpi` でによって定義されます。

## 透かしアセット {#add-watermark}

「 [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk) 」は、PNG、JPEG、TIFFおよびGIF画像ファイルへの透かしの追加をサポートしています。 透かしは、レンディション上の `watermark` オブジェクトのレンディションの指示に従って追加されます。

レンディションの後処理中に透かしが行われます。 アセットに透かしを付けるには、レンディションオブジェクトのフィールドをに設定して [、カスタムワーカー](#opt-in-to-post-processing) が後処理を選択 `postProcess``true`します。 ワーカーがオプトインを行わない場合、透かしオブジェクトが要求のレンディションオブジェクトに設定されている場合でも、透かしは適用されません。

## レンディションの手順 {#rendition-instructions}

これらは、 `renditions` /processの [アレイで使用できるオプションです](#process-request)。

### 共通フィールド {#common-fields}

| 名前 | 型 | 説明 | 例 |
|-------------------|----------|-------------|---------|
| `fmt` | `string` | レンディションターゲット形式は、テキスト抽出 `text` 用、およびXMPメタデータをxmlとして抽出 `xmp` するための形式にも使用できます。 「 [サポートされる形式」を参照](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html) | `png` |
| `worker` | `string` | カス [タムアプリケーションのURL](develop-custom-application.md)。 URLである必要があり `https://` ます。 このフィールドが存在する場合、レンディションはカスタムアプリケーションによって作成されます。 その他のレンディション設定フィールドは、カスタムアプリケーションで使用されます。 | `"https://1234.adobeioruntime.net`<br>`/api/v1/web`<br>`/example-custom-worker-master/worker"` |
| `target` | `string` | 生成されたレンディションのアップロード先URL。HTTPPUTを使用します。 | `http://w.com/img.jpg` |
| `target` | `object` | 生成されたレンディション用の事前署名済みURLアップロード情報をマルチパートで提供します。 これは、 [AEM/Oak Direct Binary Upload](https://jackrabbit.apache.org/oak/docs/features/direct-binary-access.html) 、この [マルチパートアップロード動作を使用する場合に使用します](http://jackrabbit.apache.org/oak/docs/apidocs/org/apache/jackrabbit/api/binary/BinaryUpload.html)。<br>フィールド:<ul><li>`urls`:文字列の配列。事前署名された各パーツURLに1つずつ割り当てられます。</li><li>`minPartSize`:1つのパーツに使用する最小サイズ= url</li><li>`maxPartSize`:1つのパーツに使用する最大サイズ= url</li></ul> | `{ "urls": [ "https://part1...", "https://part2..." ], "minPartSize": 10000, "maxPartSize": 100000 }` |
| `userData` | `object` | オプションで、クライアントによって制御され、レンディションイベントにそのまま渡される予約済み領域。 クライアントがカスタム情報を追加してレンディションイベントを識別できるようにします。 クライアントはいつでも自由に変更できるので、カスタムアプリケーションでは変更したり、依存関係に依存したりしてはいけません。 | `{ ... }` |

### レンディション固有のフィールド {#rendition-specific-fields}

現在サポートされているファイル形式のリストについては、 [サポートされているファイル形式を参照してください](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html)。

| 名前 | 型 | 説明 | 例 |
|-------------------|----------|-------------|---------|
| `*` | `*` | カスタムアプリケーションが [理解できる高度なカスタムフィールドを追加できます](develop-custom-application.md) 。 |  |
| `embedBinaryLimit` | `number` バイト単位 | この値を設定し、レンディションのファイルサイズがこの値より小さい場合、レンディションの生成が完了すると送信されるイベントにレンディションが埋め込まれます。 埋め込みできる最大サイズは32 KB（32 x 1024バイト）です。 レンディションのサイズが上限を超える場合、レンディションはクラウドストレージの場所に配置され、イベントに埋め込まれません。 `embedBinaryLimit` | `3276` |
| `width` | `number` | ピクセル単位の幅. 画像レンディションの場合のみ。 | `200` |
| `height` | `number` | 高さ（ピクセル単位） 画像レンディションの場合のみ。 | `200` |
|  |  | 次の場合、縦横比は常に維持されます。 <ul> <li> との両方 `width` を指定 `height` した場合、画像は縦横比を維持しながらサイズに合わせて調整されます </li><li> 指定され `width` た画像のみ `height` または唯一のものを指定した場合、結果の画像は縦横比を維持したまま、対応する寸法を使用します</li><li> も指定し `width` なかった場合 `height` は、元の画像のピクセルサイズが使用されます。 ソースのタイプに応じて異なります。 PDFファイルなど、一部の形式では、デフォルトサイズが使用されます。 最大サイズ制限を設定できます。</li></ul> |  |
| `quality` | `number` | jpeg画質は、 ～の範囲で指定 `1` し `100`ます。 画像レンディションにのみ適用できます。 | `90` |
| `xmp` | `string` | XMPメタデータの書き戻しでのみ使用され、指定したレンディションに書き戻すには、base64エンコードされたXMPが使用します。 |  |
| `interlace` | `bool` | に設定して、インターレースのPNG、GIFまたはプログレッシブJPEGを作成し `true`ます。 他のファイル形式には影響しません。 |  |
| `jpegSize` | `number` | JPEGファイルの約サイズ（バイト単位）。 これはすべての `quality` 設定を上書きします。 他の形式には影響しません。 |  |
| `dpi` | `number` か `object` のどちらかにする必要があります。 | xおよびyのDPIを設定します。 簡単にするために、xとyの両方に使用する1つの数値に設定することもできます。画像自体には影響しません。 | `96` か `{ xdpi: 96, ydpi: 96 }` のどちらかにする必要があります。 |
| `convertToDpi` | `number` か `object` のどちらかにする必要があります。 | xおよびy DPIは、物理サイズを維持しながら値を再サンプリングします。 簡単にするために、xとyの両方に使用する1つの数値に設定することもできます。 | `96` か `{ xdpi: 96, ydpi: 96 }` のどちらかにする必要があります。 |
| `files` | `array` | ZIPアーカイブに含めるファイルのリスト(`fmt=zip`)。 各エントリは、URL文字列または次のフィールドを持つオブジェクトにすることができます。<ul><li>`url`:ファイルをダウンロードするURL</li><li>`path`:このパスの下のファイルをZIPに格納</li></ul> | `[{ "url": "https://host/asset.jpg", "path": "folder/location/asset.jpg" }]` |
| `duplicate` | `string` | ZIPアーカイブの重複処理(`fmt=zip`) デフォルトでは、ZIP内の同じパスに格納された複数のファイルでエラーが発生します。 を `duplicate``ignore` に設定すると、最初のアセットのみが保存され、残りは無視されます。 | `ignore` |
| `watermark` | `object` | 透かしに関する手順が含まれ [ます](#watermark-specific-fields)。 |  |

### 透かし固有のフィールド {#watermark-specific-fields}

透かしとしてPNG形式が使用されます。

| 名前 | 型 | 説明 | 例 |
|-------------------|----------|-------------|---------|
| `scale` | `number` | との間の透かしの尺度 `0.0` です `1.0`。 `1.0` は、透かしの元のスケール(1:1)が設定され、値を小さくすると透かしのサイズが小さくなることを意味します。 | 値は、元のサイズの半分を `0.5` 意味します。 |
| `image` | `url` | 透かしに使用するPNGファイルのURL。 |  |

## 非同期イベント {#asynchronous-events}

レンディションの処理が完了した場合、またはエラーが発生した場合は、イベントが [AdobeI/Oイベントジャーナルに送信されます](https://www.adobe.io/apis/experienceplatform/events/documentation.html#!adobedocs/adobeio-events/master/intro/journaling_api.md)。 クライアントは、 [/registerを通じて提供されるジャーナルURLをリッスンする必要があります](#register)。 ジャーナル応答は、各イベントに対して1つのオブジェクトから成る `event` 配列を含み、その配列の中の `event` フィールドは、実際のイベントペイロードを含む。

のすべてのイベントのAdobeI/Oイベントタイプ [!DNL Asset Compute Service] は、で `asset_compute`す。 ジャーナルは、このイベントタイプのみに自動的に登録され、AdobeI/Oイベントタイプに基づいてフィルタする必要はありません。 サービス固有のイベントタイプは、イベントの `type` プロパティで使用できます。

### イベントタイプ {#event-types}

| イベント | 説明 |
|---------------------|-------------|
| `rendition_created` | 正常に処理およびアップロードされた各レンディションに対して送信されます。 |
| `rendition_failed` | 処理またはアップロードに失敗した各レンディションに対して送信されます。 |

### イベント属性 {#event-attributes}

| 属性 | 型 | イベント | 説明 |
|-------------|----------|---------------|-------------|
| `date` | `string` | `*` | JavaScript [Date.toISOString()で定義される、シンプルな拡張](https://en.wikipedia.org/wiki/ISO_8601) ISO-8601 [形式でイベントが送信された場合のタイムスタンプ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString)。 |
| `requestId` | `string` | `*` | ヘッダーと同じ、に対する元のリクエストのリクエ `/process`ストID `X-Request-Id` 。 |
| `source` | `object` | `*` | リクエ `source` ストの `/process` 内容。 |
| `userData` | `object` | `*` | 設定され `userData` ている場合は、 `/process` 要求のレンディションのこと。 |
| `rendition` | `object` | `rendition_*` | 渡される対応するレンディションオブジェクト `/process`。 |
| `metadata` | `object` | `rendition_created` | レンディションの [メタデータ](#metadata) プロパティ。 |
| `errorReason` | `string` | `rendition_failed` | レンディションの失敗 [の理由](#error-reasons) （存在する場合） |
| `errorMessage` | `string` | `rendition_failed` | レンディションの失敗に関する詳細を示すテキスト（存在する場合）。 |

### メタデータ {#metadata}

| プロパティ | 説明 |
|--------|-------------|
| `repo:size` | レンディションのサイズ（バイト単位）。 |
| `repo:sha1` | レンディションのsha1ダイジェスト。 |
| `dc:format` | レンディションのMIMEタイプ。 |
| `repo:encoding` | テキストベースの形式の場合のレンディションの文字セットエンコーディング。 |
| `tiff:ImageWidth` | レンディションの幅（ピクセル単位）。 画像レンディションにのみ存在します。 |
| `tiff:ImageLength` | レンディションの長さ（ピクセル単位）。 画像レンディションにのみ存在します。 |

### エラーの原因 {#error-reasons}

| 理由 | 説明 |
|---------|-------------|
| `RenditionFormatUnsupported` | 要求されたレンディション形式は、指定されたソースではサポートされていません。 |
| `SourceUnsupported` | この種類はサポートされているにもかかわらず、特定のソースはサポートされていません。 |
| `SourceCorrupt` | ソースデータが壊れています。 空のファイルが含まれます。 |
| `RenditionTooLarge` | で指定された事前署名済みURLを使用してレンディションをアップロードできませんで `target`した。 実際のレンディションサイズは、でメタデータとして使用でき `repo:size` 、クライアントは、適切な数の事前署名済みURLを使用してこのレンディションを再処理できます。 |
| `GenericError` | その他の予期しないエラー。 |

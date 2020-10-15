---
title: Introduction to the [!DNL Asset Compute Service].
description: '[!DNL Asset Compute Service] は、複雑さを軽減し、拡張性を向上させる、クラウドネイティブのアセット処理サービスです。'
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '332'
ht-degree: 2%

---


# 概要 [!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] は、デジタルアセットを処理するためのスケーラブルで拡張可能なサービス [!DNL Adobe Experience Cloud] です。 画像、ビデオ、ドキュメントおよびその他のファイル形式を、サムネール、抽出したテキストおよびメタデータ、アーカイブなど、様々なレンディションに変換できます。

開発者は、カスタムアセットアプリケーション（カスタムワーカーとも呼ばれます）をプラグインして、カスタム使用例に対処できます。 サービスは [!DNL Adobe I/O] ランタイムで動作します。 Node.jsで書かれたヘッドレスアプリを通じて拡張でき [!DNL Project Firefly] ます。 画像操作を実行するための外部APIの呼び出しや、サポートの活用など、カスタム操作を実行でき [!DNL Adobe Sensei] ます。

[!DNL Project Firefly] は、カスタムwebアプリケーションを [!DNL Adobe I/O] 実行時に構築およびデプロイして、Adobe Experience Cloudのソリューションを拡張するフレームワークです。 カスタムアプリケーションを作成する場合、開発者は [!DNL React Spectrum] (AdobeのUIツールキット)を活用し、マイクロサービスを作成し、カスタムイベントを作成し、APIを調整できます。 Project Fireflyの [ドキュメントを参照してください](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html)。

>[!NOTE]
>
>現在、はをCloud Service [!DNL Asset Compute Service] としてのみ使用 [!DNL Experience Manager] できます。 管理者は、を呼び出して処理用にアセットを渡すこ [!DNL Asset Compute Service] とのできる処理プロファイルを作成します。 詳しくは、アセットマイクロサービスと処理プロファイルの [使用を参照してください](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)。

## サポートされる使用例 [!DNL Asset Compute Service] {#possible-use-cases-benefits}

[!DNL Asset Compute Service] は、基本的な画像処理など、一般的なビジネス用途のいくつかをサポートしています。Adobeアプリケーション固有のコンバージョン複雑なビジネス要件を調整するカスタムアプリケーションの作成も可能です。

Webサービスを使用して、様々なファイルタイプのサムネールを生成できます。 [!DNL Asset Compute] サ [ポートされるファイル形式では、高品質な画像レンダリングが可能](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html)。 カスタム設定でサポートされる [使用例を参照してください](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html#custom-config)。

>[!NOTE]
>
>サービスはアセットストレージを提供しません。 ユーザーはこの情報を提供し、クラウドストレージ内のソースファイルとレンディションファイルの場所への参照を提供します。

<!-- TBD: Should this be mentioned in the docs?

|Asset Compute Service does not do this|Expectations from implementing client|
|---|---|
| Binary uploads or API-based asset ingestion. | Use other methods to ingest assets. |
| Store binaries or any persisted data across processing requests.| Each request is independent so treat it as a standalone request by sharing binary and processing instructions. |
| Store any configurations such as processing rules or settings for a user or an organization's account. | Add processing request to each request/instruction. |
| Direct event handling of asset creation events from storage systems and processing completed notifications, and errors. | Use Adobe I/O Events and other methods. |

-->

>[!MORELIKETHIS]
>
>* [Adobe Experience Managerの資産マイクロサービスをCloud Serviceとした資産処理の概要](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html)。
>* [Project Fireflyのドキュメント](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html)。
>* [処理でサポートされるファイル形式](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html)。
>* [Asset Compute Serviceのリリースノート](release-notes.md)


<!-- **TBD:**
* Clarify the service can only be used within AEM as Cloud Service. The docs provided as context for custom application developers. Not to be used as a standalone service.
  ** and API as that plays a role in custom applications (accepting standard params, invoking Nui itself in the future, etc. (this is an outlook))

* link to aem as cloud service docs on asset ingestion and customization with processing profiles.
-->

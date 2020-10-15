---
title: 拡張について理解します [!DNL Asset Compute Service]。
description: カスタムアセット処理を実行するた [!DNL Asset Compute Service] めの機能を拡張するタイミングと方法。
translation-type: tm+mt
source-git-commit: 54afa44d8d662ee1499a385f504fca073ab6c347
workflow-type: tm+mt
source-wordcount: '275'
ht-degree: 3%

---


# 拡張機能の概要 {#introduction-to-extensibilty}

形式への変換や画像のサイズ変更など、多くのレンディションの要件は、Cloud Service [内の [!DNL Experience Manager] 処理プロファイル](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html)が対処します。 より複雑なビジネス要件には、組織のニーズに合ったカスタム作成ソリューションが必要になる場合があります。 [!DNL Asset Compute Service] は、の「処理プロファイル」から呼び出されるカスタムアプリケーションを作成することで拡張でき [!DNL Experience Manager]ます。 これらのカスタムアプリケーションは、 [サポートされる使用例に応じて異なります](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)。

>[!NOTE]
>
>[!DNL Asset Compute Service] は、Cloud Serviceとしてのみ使用 [!DNL Experience Manager] できます。

カスタムアプリケーションは、ヘッドレスな [Project Firefly](https://github.com/AdobeDocs/project-firefly) Appです。 カスタムアプリケーション [!DNL Asset Compute Service] を使用した拡張は、 [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk) 、Project Firefly開発者ツールを使用して簡単に行えます。 これにより、開発者はビジネスロジックに集中できます。 カスタムアプリケーションの作成は、プレーンサーバーレスのAdobe I/O Runtimeアクションを作成するのと同じくらい簡単です。 これは、単一のNode.js JavaScript関数です。 基本的な [カスタムアプリケーションの例](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) 。

## 前提条件とプロビジョニング要件 {#prerequisites-and-provisioning}

次の前提条件を満たしていることを確認します。

* Project Fireflyツールがコンピューターにインストールされている。
* 組織 [!DNL Experience Cloud] 。 詳細は [こちら](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials)。
* エクスペリエンス組織では、Cloud Serviceが有効になっ [!DNL Experience Manager] ている必要があります。
* [!DNL Adobe Experience Cloud] 組織は、 [!DNL Project Firefly] 開発者プレビュープログラムの一部です。 アクセスの適用 [方法を参照してください](https://github.com/AdobeDocs/project-firefly/blob/master/overview/getting_access.md)。
* 開発者の組織の開発者ロールまたは管理者権限を確認します。
* [AdobeI/O CLI](https://github.com/adobe/aio-cli) がローカルにインストールされていることを確認します。

<!-- TBD for later:

* What all accesses and licenses are required?
* What all permissions are required to create, debug, and deploy custom applications?
* How do developers get access and provision the required apps?
* What is repository management?
* Anything on security and data transfer?
* What about handling personal or sensitive information?
* Custom application SLA is dependent on SLAs of various services it depends on.
* Document how the devs can get to know the KPIs of their custom applications. The KPIs are dependent on the performance at Adobe's side, amongst other things.
-->

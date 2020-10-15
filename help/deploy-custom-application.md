---
title: ' [!DNL Asset Compute Service] Deploycustomアプリケーション。'
description: ' [!DNL Asset Compute Service] Deploycustomアプリケーション。'
translation-type: tm+mt
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: tm+mt
source-wordcount: '201'
ht-degree: 8%

---


# カスタムアプリケーションのデプロイ {#deploy-custom-application}

アプリケーションをデプロイするには、 [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy) コマンドを使用します。 ターミナルで、このコマンドはカスタムアプリケーションにアクセスするためのURLを表示します。 URLは次の形式で指定し `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`ます。

アプリケーションを再デプロイせずに同じURLを取得するには、 [`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action) コマンドを使用します。

このURLをExperience Managerの [処理プロファイルでCloud Serviceとして使用し](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html) 、Cloud Service [!DNL Experience Manager] との統合を行います。

FireflyプロジェクトとWorkspaceが、アクションを使用するCloud Service環境 [!DNL Experience Manager] として対応していることを確認します。 開発、ステージング、実稼動用に異なる環境があります。 環境を検証するには、FireflyアプリケーションのルートにあるENVファイル内で定義されている `AIO_runtime_*` 秘密鍵証明書を確認します。 例えば、ワークスペースにデプロイする場合、 `Stage` は次の形式 `AIO_runtime_namespace` になり `xxxxxx_xxxxxxxxx_stage`ます。 Cloud Service実稼動環境 [!DNL Experience Manager] として統合するには、FireflyワークスペースのアプリケーションURLを使用し `Production` ます。

>[!CAUTION]
>
>重要な [!DNL Experience Manager] 環境で個人用ワークスペースを使用しないでください。

>[!MORELIKETHIS]
>
>* [Experience Manager内の環境をCloud Serviceとして理解し、管理します](https://docs.adobe.com/content/help/ja-JP/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html)。


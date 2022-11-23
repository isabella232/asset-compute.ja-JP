---
title: ' [!DNL Asset Compute Service]  カスタムアプリケーションのデプロイ'
description: ' [!DNL Asset Compute Service]  カスタムアプリケーションのデプロイ。'
exl-id: a68d4f59-8a8f-43b2-8bc6-19320ac8c9ef
source-git-commit: 129651ba432b75703bc27baa7081da60302f828d
workflow-type: tm+mt
source-wordcount: '184'
ht-degree: 85%

---

# カスタムアプリケーションのデプロイ {#deploy-custom-application}

アプリケーションをデプロイするには、[aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy) コマンドを使用します。ターミナルでこのコマンドを実行すると、カスタムアプリケーションにアクセスするための URL が表示されます。URL は `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]` の形式です。

アプリケーションを再デプロイせずに同じ URL を取得するには、[`aio app get-url`](https://github.com/adobe/aio-cli#aio-app-get-url-action) コマンドを使用します。

この URL を [Adobe  [!DNL Experience Manager]  as a  [!DNL Cloud Service] の処理プロファイル](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html?lang=ja)で使用すると、アプリケーションを Adobe [!DNL Experience Manager] as a [!DNL Cloud Service] と統合できます。

App Builder プロジェクトとワークスペースが [!DNL Experience Manager] as a [!DNL Cloud Service] 環境でアクションを使用します。 開発、ステージング、実稼動用の環境は異なります。環境が正しいか確かめるには、Firefly アプリケーションのルートにある ENV ファイル内で定義されている `AIO_runtime_*` 資格情報を確認します。例えば、`Stage` ワークスペースにデプロイする場合、`AIO_runtime_namespace` は `xxxxxx_xxxxxxxxx_stage` の形式です。Adobe [!DNL Experience Manager] as a [!DNL Cloud Service] の実稼動環境と統合するには、Firefly の `Production` ワークスペースのアプリケーション URL を使用します。

>[!CAUTION]
>
>重要な Adobe [!DNL Experience Manager] 環境で個人用ワークスペースを使用しないでください。

>[!MORELIKETHIS]
>
>* [Adobe  [!DNL Experience Manager]  as a  [!DNL Cloud Service] の環境の理解と管理](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html?lang=ja)。


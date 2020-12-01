---
title: ' [!DNL Asset Compute Service]  カスタムアプリケーションのデプロイ。'
description: ' [!DNL Asset Compute Service]  カスタムアプリケーションのデプロイ。'
translation-type: tm+mt
source-git-commit: 78c1246f5fc42006013701a6cf4d375a1d8c9fd8
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 86%

---


# カスタムアプリケーションのデプロイ {#deploy-custom-application}

アプリケーションをデプロイするには、[aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy) コマンドを使用します。ターミナルでこのコマンドを実行すると、カスタムアプリケーションにアクセスするための URL が表示されます。URL は `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]` の形式です。

アプリケーションを再デプロイせずに同じ URL を取得するには、[`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action) コマンドを使用します。

 [!DNL Experience Manager] の[処理プロファイルのURLを [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html)として使用し、[!DNL Cloud Service]として[!DNL Experience Manager]とアプリケーションを統合します。

Firefly プロジェクトとワークスペースが、アクションを使用する Adobe [!DNL Experience Manager] as a 環境に対応していることを確認します。[!DNL Cloud Service]開発、ステージング、実稼動用の環境は異なります。環境が正しいか確かめるには、Firefly アプリケーションのルートにある ENV ファイル内で定義されている `AIO_runtime_*` 資格情報を確認します。例えば、`Stage` ワークスペースにデプロイする場合、`AIO_runtime_namespace` は `xxxxxx_xxxxxxxxx_stage` の形式です。Adobe [!DNL Experience Manager][!DNL Cloud Service] as a の実稼動環境と統合するには、Firefly の `Production` ワークスペースのアプリケーション URL を使用します。

>[!CAUTION]
>
>重要な Adobe [!DNL Experience Manager] 環境で個人用ワークスペースを使用しないでください。

>[!MORELIKETHIS]
>
>* [環境 [!DNL Experience Manager] を理解し、管理します [!DNL Cloud Service]](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html)。


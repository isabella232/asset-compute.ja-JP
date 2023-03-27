---
title: ' [!DNL Asset Compute Service] に必要な開発環境の設定'
description: カスタムコードの作成とテストを開始するための [!DNL Asset Compute Service] の開発環境の設定。
exl-id: 91c12889-01d8-4757-9bdd-f73c491cd9d5
source-git-commit: 2dde177933477dc9ac2ff5a55af1fd2366e18359
workflow-type: ht
source-wordcount: '357'
ht-degree: 100%

---

# 開発環境の設定 {#create-dev-environment}

[!DNL Asset Compute Service] に対応した開発をおこなえるように環境を設定するには、次の要件と手順に従います。

1. [!DNL Adobe Developer App Builder] の[アクセス権と資格情報を取得](https://developer.adobe.com/app-builder/docs/getting_started/#acquire-access-and-credentials)します。

1. 必須ツールなど、[ローカル環境を設定](https://developer.adobe.com/app-builder/docs/getting_started/#local-environment-set-up)します。

1. スムーズに開発に着手するために役立つツールは次のとおりです。

   * [Git](https://git-scm.com/)
   * [Docker Desktop](https://www.docker.com/get-started)
   * [NodeJS](https://nodejs.org)（v14 LTS、奇数バージョンはお勧めしません）と [NPM](https://www.npmjs.com)。OSX HomeBrew のユーザーは、`brew install node` を実行して両方をインストールできます。それ以外の場合は、[NodeJS ダウンロードページ](https://nodejs.org/ja/)からダウンロードします
   * NodeJS に適した IDE。[Visual Studio Code（VS Code）](https://code.visualstudio.com)をお勧めします。デバッガーでサポートされている IDE です。その他の任意の IDE をコードエディターとして使用できますが、高度な使用方法（デバッガーなど）はまだサポートされていません
   * 最新の [[!DNL Adobe I/O] CLI](https://github.com/adobe/aio-cli)（`aio`）をインストールします

   <!-- - install using `npm install -g @adobe/aio-cli@7.1.0` -->

1. 必ず[前提条件](/help/understand-extensibility.md#prerequisites-and-provisioning)を満たすようにしてください。

<!--
>[!NOTE]
>
>For now, use [!DNL Adobe I/O] CLI v7.1.0 of and do not use [!DNL Adobe I/O] CLI v8.
-->

## App Builder プロジェクトの設定 {#create-App-Builder-project}

1. [!DNL Experience Cloud] 組織のシステム管理者または開発者の役割を確認します。この設定は、システム管理者が [Admin Console](https://adminconsole.adobe.com/overview) でおこないます。

1. [Adobe Developer Console](https://console.adobe.io/) にログオンします。[!DNL Experience Manager] as a [!DNL Cloud Service] 統合と同じ [!DNL Experience Cloud] 組織に属していることを確認します。Adobe Developer Console について詳しくは、[コンソールのドキュメント](https://www.adobe.io/apis/experienceplatform/console/docs.html)を参照してください。

1. [App Builder プロジェクトを作成します](https://developer.adobe.com/app-builder/docs/getting_started/first_app/)。**[!UICONTROL Create new project]**／**[!UICONTROL Project from template]** をクリックします。「App Builder」を選択します。`Production` と `Stage` の 2 つのワークスペースを持つ新しい App Builder プロジェクトが作成されます。必要に応じて、ワークスペース（例：`Development`）を追加します。

1. この App Builder プロジェクトで、ワークスペースを選択し、Asset Compute に必要なサービスを購読します。**Add to Project**／**API** をクリックし、`Asset Compute`、`IO Events`、`IO Events Management` の各サービスを追加します。最初の API を追加すると、秘密鍵の作成を促すメッセージが表示されます。開発者ツールでカスタムアプリケーションをテストするには、この鍵が必要なので、この情報をコンピューターに保存します。

## 次の手順 {#next-step}

これで環境が設定されたので、[カスタムアプリケーションを作成](develop-custom-application.md)する準備が整いました。

<!-- More ideas:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)

TBD: When aio-cli v8 bugs are resolved, update the AIO CLI install command to remove v7.x reference and instruct users to use the latest version. See CQDOC-18346.

-->

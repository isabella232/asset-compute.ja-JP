---
title: ' [!DNL Asset Compute Service] に必要な開発環境の設定。'
description: カスタムコードの作成とテストを開始するための  [!DNL Asset Compute Service]  の開発環境の設定。
translation-type: ht
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: ht
source-wordcount: '376'
ht-degree: 100%

---


# 開発環境の設定 {#create-dev-environment}

[!DNL Asset Compute Service] に対応した開発をおこなえるように環境を設定するには、次の要件と手順に従います。

1. Project Firefly の[アクセス権と資格情報を取得](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials)します。

1. 必須ツールなど、[ローカル環境を設定](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#local-environment-set-up)します。

1. スムーズに開発に着手するために役立つツールは次のとおりです。

   * [Git](https://git-scm.com/)
   * [Docker Desktop](https://www.docker.com/get-started)
   * [NodeJS](https://nodejs.org)（v10～v12 LTS、奇数バージョンはお勧めしません）と [NPM](https://www.npmjs.com)。OSX HomeBrew のユーザーは、`brew install node` を実行して両方をインストールできます。それ以外の場合は、[NodeJS ダウンロードページ](https://nodejs.org/ja/)からダウンロードします。
   * NodeJS に適したIDE。[Visual Studio Code（VS Code）](https://code.visualstudio.com)をお勧めします。デバッガーでサポートされている IDE です。その他の任意の IDE をコードエディターとして使用できますが、高度な使用方法（デバッガーなど）はまだサポートされていません。
   * [AIO CLI](https://github.com/adobe/aio-cli)（`aio`）- `npm install -g @adobe/aio-cli` を使用してインストールします。

1. 必ず[前提条件](/help/understand-extensibility.md#prerequisites-and-provisioning)を満たすようにしてください。

## Firefly プロジェクトの設定 {#create-firefly-project}

1. エクスペリエンス組織でのシステム管理者ロールまたは開発者ロールのアクセス権を取得します。これは、システム管理者が [Admin Console](https://adminconsole.adobe.com/overview) で設定できます。

1. [Adobe Developer Console](https://console.adobe.io/) にログオンします。AEM as a Cloud Service 統合と同じ Adobe Experience Cloud 組織に属していることを確認します。Adobe Developer Console について詳しくは、[コンソールのドキュメント](https://www.adobe.io/apis/experienceplatform/console/docs.html)を参照してください。

1. [Firefly プロジェクトを作成](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md)します。**[!UICONTROL Create new project]**／**[!UICONTROL Project from template]**&#x200B;をクリックします。「Project Firefly」を選択します。`Production` と `Stage` の 2 つのワークスペースを持つ新しい Firefly プロジェクトが作成されます。必要に応じて、ワークスペース（例：`Development`）を追加します。

1. この Firefly プロジェクトで、ワークスペースを選択し、Asset Compute に必要なサービスをサブスクライブします。**Add to Project**／**API** をクリックし、`Asset Compute`、`IO Events`、`IO Events Management` の各サービスを追加します。最初の API を追加すると、秘密鍵の作成を促すメッセージが表示されます。開発者ツールでカスタムアプリケーションをテストするには、この鍵が必要なので、この情報をコンピューターに保存します。

## 次の手順 {#next-step}

これで環境が設定されたので、[カスタムアプリケーションを作成](develop-custom-application.md)する準備が整いました。

<!-- TBD items for later:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)
-->

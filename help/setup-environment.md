---
title: 必要な開発環境を設定します [!DNL Asset Compute Service]。
description: カスタムコードの作成とテスト [!DNL Asset Compute Service] を開始に対して行う開発者環境の設定。
translation-type: tm+mt
source-git-commit: 1c2a1dc41296bf26c432c51b5afa20cb07a4c5c5
workflow-type: tm+mt
source-wordcount: '376'
ht-degree: 3%

---


# 開発者環境の設定 {#create-dev-environment}

の開発を可能にする設定を作成するに [!DNL Asset Compute Service]は、次の要件と手順に従います。

1. [Project Fireflyのアクセス権と資格情報を取得します](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials) 。

1. [ローカル環境](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#local-environment-set-up) 、および必要なツールを設定します。

1. スムーズな開発を始めるために役立つツールは以下のとおりです。

   * [Git](https://git-scm.com/).
   * [Docker Desktop](https://www.docker.com/get-started)。
   * [NodeJS](https://nodejs.org) （v10 ～ v12 LTS、奇数バージョンは推奨されません）と [NPM](https://www.npmjs.com)。 OSX HomeBrewのユーザは、両方をインストール `brew install node` できます。 それ以外の場合は、 [NodeJSダウンロードページからダウンロードします](https://nodejs.org/en/)。
   * NodeJSに適したIDEです。デバッガでサポートされているIDEなので、 [Visual Studioコード（VSコード）](https://code.visualstudio.com) を使用することをお勧めします。 他のIDEをコードエディタとして使用できますが、高度な使用方法（デバッガなど）はまだサポートされていません。
   * [AIO CLI](https://github.com/adobe/aio-cli) (`aio`) — を使用してインストール `npm install -g @adobe/aio-cli`します。

1. 必ず [前提条件を満たしてください](/help/understand-extensibility.md#prerequisites-and-provisioning)。

## Fireflyプロジェクトの設定 {#create-firefly-project}

1. エクスペリエンス組織でシステム管理者または開発者ロールのアクセス権を付与されます。 これは、 [Admin Consoleのシステム管理者が設定できます](https://adminconsole.adobe.com/overview)。

1. [Adobeデベロッパーコンソールにログオンします](https://console.adobe.io/)。 Cloud Service統合とAEMと同じAdobe Experience Cloud組織に属していることを確認します。 Adobe開発者コンソールについて詳しくは、 [コンソールのドキュメントを参照してください](https://www.adobe.io/apis/experienceplatform/console/docs.html)。

1. [Fireflyプロジェクトを作成します](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md)。 新規プロジェクト **[!UICONTROL を作成]** /テンプレートから **[!UICONTROL プロジェクトを選択]**。 [蛍]を選択します。 次の2つのワークスペースを持つ新しいFireflyプロジェクトが作成されます。 `Production` と `Stage`。 必要に応じて、追加追加のワークスペース( `Development`例：)。

1. Fireflyプロジェクトで、ワークスペースを選択し、アセット計算に必要なサービスを登録します。 Project **追加** / **API** をクリックし、 `Asset Compute`、、 `IO Events`、および `IO Events Management` サービスを追加します。 最初のAPIを追加すると、秘密鍵の作成を促すメッセージが表示されます。 開発者ツールでカスタムアプリケーションをテストするには、このキーが必要なので、この情報をお使いのコンピューターに保存してください。

## 次の手順 {#next-step}

環境の設定が完了したら、カスタムアプリケーションを [作成する準備が整いました](develop-custom-application.md)。

<!-- TBD items for later:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)
-->

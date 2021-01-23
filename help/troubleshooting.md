---
title: トラブルシューティング [!DNL Asset Compute Service]
description: ' [!DNL Asset Compute Service] を使用したカスタムアプリケーションのトラブルシューティングとデバッグ。'
translation-type: tm+mt
source-git-commit: 95e384d2a298b3237d4f93673161272744e7f44a
workflow-type: tm+mt
source-wordcount: '288'
ht-degree: 91%

---


# トラブルシューティング {#troubleshoot}

Asset Compute Service のトラブルシューティングに役立つ一般的なトラブルシューティングヒントを以下にいくつか示します。

* JavaScript アプリケーションが起動時にクラッシュしないようにします。このようなクラッシュは、通常、ライブラリや依存コンポーネントが見つからないことが原因で発生します。
* インストールするすべての依存コンポーネントがアプリケーションの `package.json` ファイルで参照されるようにします。
* 失敗時のクリーンアップに起因する可能性のあるエラーが、元の問題を隠す独自のエラーを発生させないようにします。

* 新しい [!DNL Asset Compute Service] 統合で初めて開発者ツールを起動した場合、Asset Compute イベントジャーナルの設定が完全でない可能性があり、最初の処理リクエストが失敗する場合があります。ジャーナルが設定されるまでしばらく待ってから、別のリクエストを送信します。
* Asset Compute の `/register` リクエストまたは `/process` リクエストの送信でエラーが発生した場合は、必要なすべての API が プロジェクトとワークスペース（Asset Compute、 イベント、 イベント管理、ランタイム）に追加されていることを確認します。[!DNL Adobe I/O][!DNL Adobe I/O][!DNL Adobe I/O][!DNL Adobe I/O]

## [!DNL Adobe I/O] CLI {#login-via-aio-cli}を使用してログインする

[!DNL Adobe Developer Console][ CLI を通じて [!DNL Adobe I/O]  ](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli) にログインするときに問題が発生した場合は、カスタムアプリケーションの開発、テスト、デプロイに必要な資格情報を手動で追加します。

1. [Adobe Developer Console](https://console.adobe.io/) で Firefly プロジェクトとワークスペースに移動し、右上隅にある「**[!UICONTROL Download]**」をクリックします。このファイルを開き、この JSON をコンピューター上の安全な場所に保存します。

1. Firefly アプリケーションの ENV ファイルに移動します。

1. 追加[!DNL Adobe I/O]ランタイム資格情報。 ダウンロードしたJSONから[!DNL Adobe I/O]ランタイム資格情報を取得します。 資格情報は `project.workspace.services.runtime` の配下にあります。追加`AIO_runtime_XXX`変数内の[!DNL Adobe I/O]ランタイム資格情報：

   ```json
   AIO_runtime_auth=
   AIO_runtime_namespace=
   ```

1. 手順 1でダウンロードした JSON の絶対パスを追加します。

   ```json
       ASSET_COMPUTE_INTEGRATION_FILE_PATH=
   ```

1. 開発者ツールに必要な残りの[必須資格情報](develop-custom-application.md)を設定します。

<!-- TBD for later:
Add any best practices for developers in this section:
* Any items to take care of when creating projects.
* Any naming conventions, reserved keywords, etc.?
* Any terms that can become a source of confusion later based on our OOTB naming.

* If required, add limitations for custom applications and spin those off as best practices.
* Do NOT borrow any content from https://git.corp.adobe.com/nui/nui/blob/master/doc/worker_api.md. It is outdated and irrelevant for 3rd party custom applications.
-->

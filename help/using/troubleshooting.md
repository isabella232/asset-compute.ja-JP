---
title: ' [!DNL Asset Compute Service] に関連するトラブルシューティング'
description: ' [!DNL Asset Compute Service] を使用したカスタムアプリケーションのトラブルシューティングとデバッグ。'
exl-id: 017fff91-e5e9-4a30-babf-5faa1ebefc2f
source-git-commit: 5257e091730f3672c46dfbe45c3e697a6555e6b1
workflow-type: tm+mt
source-wordcount: '291'
ht-degree: 100%

---

# トラブルシューティング {#troubleshoot}

Asset Compute Service のトラブルシューティングに役立つ一般的なトラブルシューティングヒントを以下にいくつか示します。

* JavaScript アプリケーションが起動時にクラッシュしないようにします。このようなクラッシュは、通常、ライブラリや依存コンポーネントが見つからないことが原因で発生します。
* インストールするすべての依存コンポーネントがアプリケーションの `package.json` ファイルで参照されるようにします。
* 失敗時のクリーンアップに起因する可能性のあるエラーが、元の問題を隠す独自のエラーを発生させないようにします。

* 新しい [!DNL Asset Compute Service] 統合で初めて開発者ツールを起動する際、Asset Compute イベントジャーナルの設定が完全でない場合は、最初の処理リクエストが失敗する場合があります。ジャーナルが設定されるまでしばらく待ってから、別のリクエストを送信します。
* Asset Compute の `/register` リクエストまたは `/process` リクエストの送信でエラーが発生した場合は、必要なすべての API が [!DNL Adobe I/O] プロジェクトとワークスペース（Asset Compute、[!DNL Adobe I/O] イベント、[!DNL Adobe I/O] イベント管理、[!DNL Adobe I/O] Runtime）に追加されていることを確認します。

## [!DNL Adobe I/O] CLI を使用してログインする  {#login-via-aio-cli}

[ [!DNL Adobe I/O] CLI を通じて](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#3-signing-in-from-cli)、[!DNL Adobe Developer Console] にログインする際に問題が発生した場合は、カスタムアプリケーションの開発、テストおよびデプロイに必要な資格情報を手動で追加します。

1. [Adobe Developer Console](https://console.adobe.io/) で Adobe Developer App Builder プロジェクトとワークスペースに移動し、右上隅にある「**[!UICONTROL ダウンロード]**」を押します。このファイルを開き、この JSON をコンピューター上の安全な場所に保存します。

1. Adobe Developer App Builder アプリケーションの ENV ファイルに移動します。

1. [!DNL Adobe I/O] Runtime 資格情報を追加します。ダウンロードした JSON から [!DNL Adobe I/O] Runtime の資格情報を取得します。資格情報は `project.workspace.services.runtime` の配下にあります。[!DNL Adobe I/O] Runtime の資格情報を `AIO_runtime_XXX` 変数に追加します。

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

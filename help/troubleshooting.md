---
title: トラブルシューティング [!DNL Asset Compute Service].
description: を使用したカスタムアプリケーションのトラブルシューティングとデバッグ [!DNL Asset Compute Service]。
translation-type: tm+mt
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: tm+mt
source-wordcount: '306'
ht-degree: 1%

---


# トラブルシューティング {#troubleshoot}

Asset Compute Serviceのトラブルシューティングに役立つ一般的なトラブルシューティングのヒントをいくつか示します。

* 起動時にJavaScriptアプリケーションがクラッシュしないことを確認します。 このようなクラッシュは、通常、見つからないライブラリや依存関係に関連しています。
* インストールするすべての依存関係がアプリケーションの `package.json` ファイルで参照されていることを確認します。
* 失敗時のクリーンアップに起因する可能性のあるエラーが、元の問題を隠す独自のエラーを生成しないようにします。

* 新しい [!DNL Asset Compute Service] 統合で初めて開発者ツールを起動すると、「アセット計算イベント」ジャーナルが完全に設定されていない場合があるので、最初の処理リクエストが失敗する場合があります。 ジャーナルが設定されるまでしばらく待ってから、別の要求を送信します。
* アセット計算 `/register``/process` または要求の送信でエラーが発生した場合は、必要なすべてのAPIがAdobeのI/Oプロジェクトとワークスペース(Asset Compute、IOイベント、IOイベント管理、Runtime)に追加されていることを確認してください。

## AdobeI/O CLIを使用したログインの問題 {#login-via-aio-cli}

AdobeI/O CLI [!DNL Adobe Developer Console] を [](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli)使用したログインで問題が発生した場合は、カスタムアプリケーションの開発、テスト、展開に必要な資格情報を手動で追加します。

1. AdobeデベロッパーコンソールのFireflyプロジェクトとワークスペースに移動し [、右上隅の](https://console.adobe.io/) 「ダウンロード **** 」を押します。 このファイルを開き、このJSONをマシン上の安全な場所に保存します。

1. Firefly ApplicationのENVファイルに移動します。

1. Adobe I/O Runtime追加の資格情報 ダウンロードしたJSONからAdobe I/O Runtimeの資格情報を取得します。 The credentials are under `project.workspace.services.runtime`. 変追加数内のI/Oランタイム資格情報： `AIO_runtime_XXX`

   ```json
   AIO_runtime_auth=
   AIO_runtime_namespace=
   ```

1. 手順1追加のダウンロード済みJSONの絶対パス：

   ```json
       ASSET_COMPUTE_INTEGRATION_FILE_PATH=
   ```

1. 開発者ツールに必要な残りの [必要な資格情報を設定します](develop-custom-application.md) 。

<!-- TBD for later:
Add any best practices for developers in this section:
* Any items to take care of when creating projects.
* Any naming conventions, reserved keywords, etc.?
* Any terms that can become a source of confusion later based on our OOTB naming.

* If required, add limitations for custom applications and spin those off as best practices.
* Do NOT borrow any content from https://git.corp.adobe.com/nui/nui/blob/master/doc/worker_api.md. It is outdated and irrelevant for 3rd party custom applications.
-->

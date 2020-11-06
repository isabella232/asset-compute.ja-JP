---
title: ' [!DNL Asset Compute Service] のリリースノート。'
description: ' [!DNL Asset Compute Service] の新機能、機能強化、既知の問題。'
translation-type: ht
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: ht
source-wordcount: '193'
ht-degree: 100%

---


# [!DNL Asset Compute Service] のリリースノート {#release-notes}

[!DNL Asset Compute Service] の最新リリースは 2020 年 7 月 30 日にリリースされました。

<!--

To test your custom applications with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). Currently, Adobe supports Azure Blob Storage and AWS S3.

>[!NOTE]
>
>Cloud storage access is only required for using the developer tool. You can still create, test and deploy custom applications with out using the developer tool.
-->

## 新機能 {#what-is-new}

これは、[!DNL Asset Compute Service] の最初のリリースです。デジタルアセットを処理するためのスケーラブルかつ拡張可能な [!DNL Adobe Experience Cloud] サービスです。画像、ビデオ、ドキュメントなどのファイル形式を、サムネール、抽出テキストとメタデータ、アーカイブなど、様々なレンディションに変換できます。

現在、[!DNL Asset Compute Service] は Adobe [!DNL Experience Manager] as a Cloud Service でのみ使用できます。

## 制限事項と既知の問題 {#known-limitations}

[開発者ツール](https://github.com/adobe/asset-compute-devtool)でカスタムアプリケーションをテストするには、[クラウドストレージコンテナ](https://github.com/adobe/asset-compute-devtool#prerequisites)にアクセスする必要があります。

* クラウドストレージ（[!DNL Experience Manager] BLOB ストアとは別物）へのアクセスは、開発者ツールにのみ必要です。開発者ツールを使用しなくても、カスタムアプリケーションの作成、テスト、デプロイは可能です。
* 複数の開発者が異なるプロジェクトで使用する共有コンテナにできます。

## 貢献 {#contribute-open-source}

[!DNL Asset Compute Service] の拡張機能は、拡張機能開発者からの貢献を歓迎するオープン開発モデルの下、[github.com/adobe](https://github.com/adobe) で開発されています。カスタムアプリケーションの開発、作成、テスト、デプロイに関連するコンポーネントはすべてオープンソースです。Compute Service への貢献の方法と場所については、[こちら](contribute-to-compute-service.md)を参照してください。

<!-- **TBD:**
* Are we versioning the releases?
* Is there any compatibility information to be added? With Project Firefly versions, or AEMaaCS releases, or other offerings/integrations such as InDesign Server?
-->

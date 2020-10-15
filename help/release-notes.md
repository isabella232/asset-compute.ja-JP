---
title: のリリースノート [!DNL Asset Compute Service]。
description: の新機能、拡張機能および既知の問題 [!DNL Asset Compute Service]。
translation-type: tm+mt
source-git-commit: 68d910cd092fccb599c361f24daff80460129e1c
workflow-type: tm+mt
source-wordcount: '193'
ht-degree: 2%

---


# リリースノート [!DNL Asset Compute Service] {#release-notes}

の最新リリース [!DNL Asset Compute Service] は2020年7月30日にリリースされます。

<!--

To test your custom applications with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). Currently, Adobe supports Azure Blob Storage and AWS S3.

>[!NOTE]
>
>Cloud storage access is only required for using the developer tool. You can still create, test and deploy custom applications with out using the developer tool.
-->

## What is new {#what-is-new}

は、の最初のリリースで [!DNL Asset Compute Service]す。 これは、デジタルアセットを処理するためのスケーラブルで拡張可能なサービス [!DNL Adobe Experience Cloud] です。 画像、ビデオ、ドキュメントおよびその他のファイル形式を、サムネール、抽出したテキストおよびメタデータ、アーカイブなど、様々なレンディションに変換できます。

現在、はCloud Service [!DNL Asset Compute Service] としてのみ使用 [!DNL Experience Manager] できます。

## 制限事項と既知の問題 {#known-limitations}

[開発者ツールを使用してカスタムアプリケーションをテストするには](https://github.com/adobe/asset-compute-devtool)、 [クラウドストレージコンテナにアクセスする必要があります](https://github.com/adobe/asset-compute-devtool#prerequisites)。

* クラウドストレージ( [!DNL Experience Manager] BLOBストアとは異なる)へのアクセスは、開発者ツールに対してのみ必要です。 開発者ツールを使用しなくても、カスタムアプリケーションの作成、テストおよびデプロイは引き続き可能です。
* 異なるプロジェクトの複数の開発者が使用する共有コンテナーを使用できます。

## 貢献 {#contribute-open-source}

[!DNL Asset Compute Service] 拡張機能は、拡張機能開発者の貢献を歓迎するgithub.com/adobe [のオープンな開発モデルに基づいて開発されています](https://github.com/adobe) 。 カスタムアプリケーションの開発、作成、テストおよびデプロイに関連するすべてのコンポーネントは、オープンソースです。 コンピュ [ーティングサービスに貢献する方法と場所を確認](contribute-to-compute-service.md)。

<!-- **TBD:**
* Are we versioning the releases?
* Is there any compatibility information to be added? With Project Firefly versions, or AEMaaCS releases, or other offerings/integrations such as InDesign Server?
-->

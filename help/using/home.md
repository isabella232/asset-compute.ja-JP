---
title: 「[!DNL Adobe Asset Compute Service] ユーザーガイド」
description: このドキュメントでは、導入、カスタムコードの開発、管理、デプロイ、トラブルシューティングの方法など、 [!DNL Asset Compute Service]  に関連するタスクについて説明しています。
exl-id: 5acf87d1-a391-4802-bfce-e367fc8564df
source-git-commit: 5257e091730f3672c46dfbe45c3e697a6555e6b1
workflow-type: ht
source-wordcount: '222'
ht-degree: 100%

---

# [!DNL Asset Compute Service] について

[!DNL Asset Compute Service] は、デジタルアセットを処理するためのスケーラブルかつ拡張可能な Adobe Experience Cloud サービスです。画像、ビデオ、ドキュメントなどのファイル形式を、サムネール、抽出したテキストとメタデータ、アーカイブなど、様々なレンディションに変換できます。開発者は、カスタムアプリケーション（カスタムワーカーとも呼ばれます）をプラグインして、カスタムユースケースに対処できます。これらのユースケースは [Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview) を使用して作成され、サーバーレスの [[!DNL Adobe I/O] Runtime](https://www.adobe.io/apis/experienceplatform/runtime.html) で動作します。

このドキュメントでは、カスタムコードの開発、管理、デプロイ、トラブルシューティングの方法など、[!DNL Asset Compute Service] に関連するトピックについて説明しています。[!DNL Asset Compute Service] の機能については、[概要](introduction.md)を参照してください。[サービスの内容](introduction.md#possible-use-cases-benefits)を確認してください。

[!DNL Asset Compute Service] は、多くのファイル形式の変換をサポートしており、多くの Adobe サービスと統合されています。詳しくは、[サポートされているファイル形式とサービス統合](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/file-format-support.html?lang=ja)の一覧を参照してください。

[ [!DNL Adobe Experience Manager]  as a  [!DNL Cloud Service] で利用可能なアセットマイクロサービス機能](https://experienceleague.adobe.com/docs/experience-manager-cloud-service/assets/asset-microservices-overview.html?lang=ja)および [!DNL Experience Manager] のマイクロサービスの使用方法については、概要を参照してください。

[!DNL Asset Compute Service] の拡張機能は、拡張機能開発者からの貢献を歓迎するオープン開発モデルの下、[github.com/adobe](https://github.com/adobe) で開発されています。カスタムアプリケーションの開発、作成、テスト、デプロイに関連するコンポーネントはすべてオープンソースです。Compute Service への貢献の方法と場所については、[こちら](contribute-to-compute-service.md)を参照してください。

<!--
Possible to record the below info here in this landing page to centralize the miscellaneous info about Asset Compute Service?
 List of dependencies and requirements SDK, CLI, Devtools, etc.? Or may be a link to the prerequisites.
 Introduction video when Tech Marketing team shares one.
-->

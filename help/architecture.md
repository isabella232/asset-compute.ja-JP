---
title: ' [!DNL Asset Compute Service] のアーキテクチャ'
description: ' [!DNL Asset Compute Service] の API、アプリケーション、SDK が連携して、クラウドネイティブなアセット処理サービスを提供する仕組み。'
exl-id: 658ee4b7-5eb1-4109-b263-1b7d705e49d6
source-git-commit: 0c5ab8ab230e3f033b804849a2c005351542155f
workflow-type: tm+mt
source-wordcount: '486'
ht-degree: 98%

---

# [!DNL Asset Compute Service] のアーキテクチャ {#overview}

[!DNL Asset Compute Service] は、サーバーレスの [!DNL Adobe I/O] Runtime プラットフォーム上に構築されています。アセットに Adobe Sensei コンテンツサービスを提供します。呼び出し側クライアント（Adobe [!DNL Experience Manager] as a [!DNL Cloud Service] のみサポート）には、アセットに必要な Adobe Sensei 生成情報が提供されます。返される情報は JSON 形式です。

[!DNL Asset Compute Service] は、[!DNL Project Adobe Developer App Builder] に基づいてカスタムアプリケーションを作成することにより拡張できます。これらのカスタムアプリケーションは [!DNL Project Adobe Developer App Builder] ヘッドレスアプリであり、カスタム変換ツールの追加や、画像操作を実行する外部 API の呼び出しなどのタスクをおこないます。

[!DNL Project Adobe Developer App Builder] は、[!DNL Adobe I/O] ランタイム上にカスタム Web アプリケーションを作成してデプロイするためのフレームワークです。カスタムアプリケーションを作成する場合、開発者は [!DNL React Spectrum]（アドビの UI ツールキット）を活用し、マイクロサービスを作成し、カスタムイベントを作成し、API をオーケストレーションすることができます。詳しくは、 [Adobe Developer App Builder のドキュメント](https://developer.adobe.com/app-builder/docs/overview).

アーキテクチャの基盤となるものは次のとおりです。

* アプリケーションのモジュール性 - 指定のタスクに必要なものだけを含むことで、アプリケーションを互いに切り離し軽量に保つことができます。

* [!DNL Adobe I/O] Runtime のサーバーレス概念は、非同期性、高いスケーラビリティ、分離、ジョブベースの処理など、多くの利点をもたらし、アセット処理に最適です。

* バイナリクラウドストレージには、ストレージへのフルアクセス権限がなくても、署名済みの URL 参照を使用して、アセットファイルやレンディションを個別に保存およびアクセスするために必要な機能が用意されています。クラウドストレージを使用した転送高速化、CDN キャッシュ、コンピューティングアプリケーションのコロケーションにより、待ち時間が短い最適なコンテンツアクセスが可能になります。AWS クラウドと Azure クラウドの両方がサポートされています。

![Asset Compute Service のアーキテクチャ](assets/architecture-diagram.png)

*図：[!DNL Asset Compute Service] のアーキテクチャと、Adobe [!DNL Experience Manager]、ストレージ、処理アプリケーションとの統合方法。*

アーキテクチャは次の部分で構成されます。

* **API およびオーケストレーションレイヤー**&#x200B;はソースアセットを複数のレンディションに変換するようにサービスに指示する（JSON 形式の）リクエストを受け取ります。これらのリクエストは非同期的に実行され、アクティベーション ID（別名「ジョブ ID」）が返されます。指示は完全に宣言的なもので、標準的なあらゆる処理作業（例：サムネール生成、テキスト抽出など）について、コンシューマーは目的の結果のみを指定し、特定のレンディションを処理するアプリケーションは指定しません。認証、分析、レート制限などの汎用 API 機能は、サービスの前面にある Adobe API Gateway を使用して処理され、[!DNL Adobe I/O] Runtime に転送されるすべてのリクエストを管理します。アプリケーションルーティングは、オーケストレーションレイヤで動的におこなわれます。クライアントでは特定レンディション用のカスタムアプリケーションを指定でき、カスタムアプリケーションにはカスタムパラメーターを含めることができます。アプリケーションの実行は、[!DNL Adobe I/O] Runtime では個別のサーバーレス関数になるので、完全に並列処理することができます。

* **アセットを処理するアプリケーション**&#x200B;は特定の種類のファイル形式またはターゲットレンディションに特化しています。概念的には、アプリケーションは Unix パイプの概念と似ています。入力ファイルが 1 つ以上の出力ファイルに変換されます。

* **[共通アプリケーションライブラリ](https://github.com/adobe/asset-compute-sdk)**&#x200B;はソースファイルのダウンロード、レンディションのアップロード、エラー報告、イベント送信および監視などの、一般的なタスクを処理します。このライブラリは、アプリケーションの開発を、サーバーレスのアイデアに従って、できるだけ簡易化し、ローカルファイルシステムの操作のみに制限できるように設計されています。

<!-- TBD:

* About the YAML file?
* minimize description to custom applications
* remove all internal stuff (e.g. Photoshop application, API Gateway) from text and diagram
* update diagram to focus on 3rd party custom applications ONLY
* Explain important transactions/handshakes?
* Flow of assets/control? See the illustration on the Nui diagrams wiki.
* Illustrations. See the SVG shared by Alex.
* Exceptions? Limitations? Call-outs? Gotchas?
* Do we want to add what basic processing is not available currently, that is expected by existing AEM customers?
-->

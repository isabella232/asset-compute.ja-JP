---
title: のアーキテクチャ [!DNL Asset Compute Service]。
description: HowAPI、アプリケーション、SDKが連携して、クラウドネイティブのアセット処理サービスを提供し [!DNL Asset Compute Service] ます。
translation-type: tm+mt
source-git-commit: 0fb256f7d9f83fbae564d9fd52ee6b2f34c5d7e9
workflow-type: tm+mt
source-wordcount: '496'
ht-degree: 0%

---


# アーキテクチャ [!DNL Asset Compute Service] {#overview}

は、サーバ [!DNL Asset Compute Service] ーレスのAdobe I/O Runtimeプラットフォーム上に構築されています。 アセットをAdobe Senseiコンテンツサービスでサポートします。 呼び出しクライアント(Cloud Service [!DNL Experience Manager] がサポートされている場合のみ)は、アセットに対して探したAdobe Sensei生成情報と共に提供されます。 返される情報はJSON形式です。

[!DNL Asset Compute Service] は、に基づくカスタムアプリケーションを作成することで拡張でき [!DNL Project Firefly]ます。 これらのカスタムアプリはヘッドレスアプリで、カスタムコンバージョンツールの追加や外部APIの呼び出しなどのタスクを行って画像操作を実行します。 [!DNL Project Firefly]

[!DNL Project Firefly] は、カスタムwebアプリケーションを構築し、 [!DNL Adobe I/O] 実行時にデプロイするフレームワークです。 カスタムアプリケーションを作成する場合、開発者は [!DNL React Spectrum] (AdobeのUIツールキット)を活用し、マイクロサービスを作成し、カスタムイベントを作成し、APIを調整できます。 Project Fireflyの [ドキュメントを参照してください](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html)。

アーキテクチャの基盤は、次のものです。

* アプリケーションのモジュラリティは、特定のタスクに必要なものだけを含むので、アプリケーションを相互に切り離し、軽量に保つことができます。

* Adobe I/O Runtimeのサーバレスコンセプトは多くの利点をもたらす。非同期、高い拡張性、分離、ジョブベースの処理。アセット処理に最適です。

* Binary Cloudストレージには、署名済みのURL参照を使用して、ストレージへのフルアクセス権限を必要とせずに、アセットファイルやレンディションを個別に保存およびアクセスするために必要な機能が用意されています。 クラウドストレージを使用して、転送の高速化、CDNキャッシュ、コンピューティングアプリケーションの配置の調整を行うことで、最適な低遅延コンテンツアクセスを実現します。 AWSクラウドとAzureクラウドの両方がサポートされます。

![資産計算サービスのアーキテクチャ](assets/architecture-diagram.png)

*図：のアーキテクチャ [!DNL Asset Compute Service] と、それとの統合方法、 [!DNL Experience Manager]ストレージ、処理アプリケーション。*

このアーキテクチャは、次の部分で構成されます。

* **APIおよびオーケストレーションレイヤーは** 、（JSON形式の）要求を受け取ります。この要求は、ソースアセットを複数のレンディションに変換するようにサービスに指示します。 これらのリクエストは非同期的に実行され、アクティベーションID（「ジョブID」）で返されます。 手順は完全に宣言的なもので、すべての標準処理作業(サムネール生成、テキスト抽出など)のコンシューマーは目的の結果のみを指定しますが、特定のレンディションを処理するアプリケーションは指定しません。 認証、分析、レート制限などの汎用API機能は、サービスの前にあるAdobeAPI Gatewayを使用して処理され、I/Oランタイムに送信されるすべての要求を管理します。 アプリケーションルーティングは、オーケストレーションレイヤによって動的に行われます。 カスタムアプリケーションは、クライアントが特定のレンディションに対して指定し、カスタムパラメーターを含めることができます。 I/Oランタイムでは、アプリケーションの実行はサーバを持たない別の機能であるため、完全に並列処理できます。

* **特定の種類のファイル形式またはターゲットレンディションに特化したアセットを処理するアプリケーション** 。 概念上、アプリケーションはUnixパイプの概念と似ています。入力ファイルが1つ以上の出力ファイルに変換されます。

* **共通アプリケーションライブラリ [は](https://github.com/adobe/asset-compute-sdk)** 、ソースファイルのダウンロード、レンディションのアップロード、エラーレポート、イベント送信と監視など、一般的なタスクを処理します。 これは、アプリケーションの開発を、サーバレスのアイデアに従って、できるだけ簡単にし、ローカルファイルシステムの操作に制限できるように設計されています。

<!-- TBD:

* About the YAML file?
* See [https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#5-anatomy-of-a-project-firefly-application).

* minimize description to custom applications
* remove all internal stuff (e.g. Photoshop application, API Gateway) from text and diagram
* update diagram to focus on 3rd party custom applications ONLY
* Explain important transactions/handshakes?
* Flow of assets/control? See the illustration on the Nui diagrams wiki.
* Illustrations. See the SVG shared by Alex.
* Exceptions? Limitations? Call-outs? Gotchas?
* Do we want to add what basic processing is not available currently, that is expected by existing AEM customers?
-->

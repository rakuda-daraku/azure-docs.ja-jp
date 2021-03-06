---
title: "サービス マップと System Center Operations Manager の統合 | Microsoft Docs"
description: "サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする Operations Management Suite のソリューションです。 この記事では、Operations Manager でサービス マップを使用して、分散アプリケーション ダイアグラムを自動作成する方法について説明します。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0b710c338be3a2c2fde6bba43173f7c5f480e357
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---

# <a name="service-map-integration-with-system-center-operations-manager"></a>サービス マップと System Center Operations Manager の統合
  > [!NOTE]
  > この機能はプライベート プレビュー段階にあるため、運用システムでは使用しないでください。
  > 
  
Operations Management Suite のサービス マップでは、Windows および Linux システムのアプリケーション コンポーネントが自動的に検出され、サービス間の通信がマップされます。 サービス マップを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして、思うように表示することができます。 サービス マップには、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続が表示されます。その際、エージェントのインストール以外の構成は必要ありません。 詳細については、[サービス マップのドキュメント](operations-management-suite-service-map.md)を参照してください。

このサービス マップと System Center Operations Manager との統合を利用すると、サービス マップの動的な依存関係に基づいて、Operation Manager で分散アプリケーション ダイアグラムを自動作成できます。

## <a name="prerequisites"></a>前提条件
* サーバー セットを管理する Operations Manager 管理グループ。
* サービス マップ ソリューションが有効化された Operations Management Suite ワークスペース。
* Operations Manager で管理され、サービス マップにデータを送信する (1 つ以上の) サーバー セット。 Windows および Linux サーバーがサポートされています。
* Operations Management Suite ワークスペースに関連付けられている Azure サブスクリプションにアクセスできるサービス プリンシパル。 詳細については、「[サービス プリンシパルの作成](#creating-a-service-principal)」を参照してください。

## <a name="install-the-service-map-management-pack"></a>サービス マップ管理パックのインストール
Operations Manager とサービス マップの統合を有効にするには、Microsoft.SystemCenter.ServiceMap 管理パック バンドル (Microsoft.SystemCenter.ServiceMap.mpb) をインポートします。 このバンドルには、次の管理パックが含まれています。
* Microsoft サービス マップ アプリケーション ビュー
* Microsoft System Center サービス マップ Internal
* Microsoft System Center サービス マップ
* Microsoft System Center サービス マップ

## <a name="configure-the-service-map-integration"></a>サービス マップ統合の構成
サービス マップ管理パックをインストールすると、**[管理]** ウィンドウの **[Operations Management Suite]** の下に、**[サービス マップ]** という名前の新しいノードが表示されます。 

サービス マップの統合を構成するには、次の操作を行います。

1. 構成ウィザードを開くには、**[サービス マップの概要]** ウィンドウの **[ワークスペースの追加]** をクリックします。  

    ![[サービス マップの概要] ウィンドウ](media/oms-service-map/scom-configuration.png)

2. **[接続構成ツール]** ウィンドウで、サービス プリンシパルのテナント名または ID、アプリケーション ID (ユーザー名またはクライアント ID とも呼ばれます)、およびパスワードを入力し、**[次へ]** をクリックします。 詳細については、「[サービス プリンシパルの作成](#creating-a-service-principal)」を参照してください。

    ![[接続構成ツール] ウィンドウ](media/oms-service-map/scom-config-spn.png)

3. **[Subscription Selection (サブスクリプションの選択)]** ウィンドウで、Azure サブスクリプション、Azure リソース グループ (Operations Management Suite ワークスペースが含まれるグループ)、Operations Management Suite ワークスペースを選択し、**[次へ]** をクリックします。

    ![Operations Manager 構成ワークスペース](media/oms-service-map/scom-config-workspace.png)

4. **[サーバーの選択]** ウィンドウで、Operations Manager とサービス マップの同期を行うサーバーを指定して、サーバー マップ サーバー グループを構成します。 **[サーバーの追加と削除]** をクリックします。   
    
    この統合によりサーバーの分散アプリケーション ダイアグラムを作成するには、サーバーが次の条件を満たす必要があります。

    * Operations Manager で管理されている。
    * サービス マップで管理されている。
    * サービス マップ サーバー グループに登録されている。

    ![Operations Manager 構成グループ](media/oms-service-map/scom-config-group.png)

5. 省略可能: Operations Management Suite と通信する管理サーバーのリソース プールを選択し、**[ワークスペースの追加]** をクリックします。

    ![Operations Manager 構成リソース プール](media/oms-service-map/scom-config-pool.png)

    Operations Management Suite ワークスペースの構成および登録には時間がかかる場合があります。 構成が完了すると、Operations Manager と、Operations Management Suite の最初のサービス マップの同期が開始されます。

    ![Operations Manager 構成リソース プール](media/oms-service-map/scom-config-success.png)

    >[!NOTE]
    >既定の同期間隔は 60 分に設定されています。 この同期間隔は、上書きを構成することで変更できます。 また、**[作成]** ウィンドウで、サーバーをサービス マップ サーバー グループに手動で追加することもできます。 それには、**[グループ]** を選択し、**サービス マップ サーバー グループ**を検索します。 そのサーバーのサーバー マップは次の同期サイクルで同期されます。この同期のタイミングは、構成された同期間隔に基づきます。

## <a name="monitor-service-map"></a>サービス マップの監視
Operations Management Suite ワークスペースが接続されると、Operations Manager コンソールの **[監視]** ウィンドウに、サービス マップという名前の新しいフォルダーが表示されます。

![Operations Manager の [監視] ウィンドウ](media/oms-service-map/scom-monitoring.png)

サービス マップ フォルダーには 3 つのノードがあります。
* **アクティブなアラート**: Operations Manager と Operations Management Suite のサービス マップ ソリューションの間の通信に関するアクティブなアラートの一覧が表示されます。

    >[!NOTE]
    >このアラートは、Operations Manager に表示される Operations Management Suite アラートではありません。

* **サーバー**: サービス マップと同期するように構成されている監視対象サーバーの一覧が表示されます。

    ![Operations Manager の [監視] の [サーバー] ウィンドウ](media/oms-service-map/scom-monitoring-servers.png)

* **サーバー依存関係ビュー**: サービス マップと同期されているサーバーの一覧が表示されます。 サーバーをクリックすると、その分散アプリケーション ダイアグラムを確認できます。

    ![Operations Manager の分散アプリケーション ダイアグラム](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>ワークスペースを編集または削除
構成済みワークスペースは、**[サービス マップ概要]** ウィンドウ (**[管理]** ウィンドウ > **[Operations Management Suite]**  >  **[サービス]**) で編集または削除できます。 現時点で構成できる Operations Management Suite ワークスペースは 1 つのみです。

![Operations Manager の [ワークスペースの編集] ウィンドウ](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>規則と上書きの構成
サービス マップから定期的に情報を取得するために、_Microsoft.SystemCenter.ServiceMapImport.Rule_ という規則が作成されます。 同期のタイミングを変更するには、この規則の上書きを構成します (**[作成]** ウィンドウ > **[規則]**  >  **[Microsoft.SystemCenter.ServiceMapImport.Rule]**)。

![Operations Manager の [Overrides properties (プロパティの上書き)] ウィンドウ](media/oms-service-map/scom-overrides.png)

* **Enabled**: 自動更新を有効または無効にします。 
* **IntervalMinutes**: 更新間隔をリセットします。 既定の間隔は 1 時間です。 サーバー マップの同期の頻度を上げるには、この値を変更します。
* **TimeoutSeconds**: 要求がタイムアウトになるまでの時間をリセットします。 
* **TimeWindowMinutes**: データに対するクエリの時間枠をリセットします。 既定の時間枠は 60 分です。 サービス マップで許可されている最大値は 60 分です。

## <a name="known-issues-and-limitations"></a>既知の問題と制限

現在の設計には次の問題と制限があります。
* **[作成]** ウィンドウでサーバーをサービス マップ サーバー グループに手動で追加できますが、そのサーバーのマップは、次の同期サイクルまでサービス マップと同期されません。 既定値は 60 分ですが、このタイミングは上書きできます。 
* 接続できる Operations Management Suite ワークスペースは 1 つだけです。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成
サービス プリンシパル作成に関する Azure の公式ドキュメントについては、次を参照してください。
* [PowerShell を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Azure Portal を使用してサービス プリンシパルを作成する](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>フィードバック
サービス マップやこのドキュメントについてフィードバックはありますか。 [User Voice ページ](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)を是非ご利用ください。このページでは、機能を提案したり、既存の提案に投票したりすることができます。


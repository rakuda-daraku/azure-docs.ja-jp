---
title: "Azure CLI で初めての関数を作成する | Microsoft Docs"
description: "Azure CLI を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。"
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 3dc0e1b26c95ac6583dd3b1068b36deb54f7ac5a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>Azure CLI での初めての関数の作成

このクイックスタート チュートリアルでは、Azure Functions を使用して最初の関数を作成する方法について説明します。 Azure CLI を使用して、関数をホストするサーバーレス インフラストラクチャである Function App を作成します。 関数コード自体は、GitHub サンプル レポジトリからデプロイされます。    

以下の手順は、Mac、Windows、または Linux コンピューターを使用して実行することができます。 

## <a name="prerequisites"></a>前提条件 

このサンプルを実行する前に、以下が必要です。

+ アクティブな [GitHub](https://github.com) アカウント。 
+ [Azure CLI がインストールされていること](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
+ 有効な Azure サブスクリプション

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure へのログイン

[az login](/cli/azure/#login) コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。 

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) でリソース グループを作成します。 Azure リソース グループとは、Function App、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、`myResourceGroup` という名前のリソース グループを作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

Functions は、関数に関する状態その他の情報を維持するために Azure Storage アカウントを使用します。 [az storage account create](/cli/azure/storage/account#create) コマンドを使用して作成したリソース グループ内にストレージ アカウントを作成します。

次のコマンドで、`<storage_name>` プレースホルダーをグローバルで一意な独自のストレージ アカウント名に置き換えます。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。

```azurecli
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

ストレージ アカウントの作成後に、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 Function App は、関数コードのサーバーレス実行の環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、および共有を容易にするためのロジック ユニットとして関数をグループ化できます。 Function App の作成には、[az functionapp create](/cli/azure/functionapp#create) コマンドを使用します。 

次のコマンドで、`<app_name>` プレースホルダーを独自の一意の Function App 名に、`<storage_name>` をストレージ アカウント名に置き換えます。 `<app_name>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
既定では、Function App は従量課金ホスティング プランで作成されます。つまり、リソースは関数の要求に応じて動的に追加され、関数が実行されている場合にのみ課金されます。 詳細については、「[Azure Functions の適切なサービス プランを選択する](functions-scale.md)」を参照してください。 

Function App が作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

これで Function App が作成されたので、GitHub サンプル レポジトリから実際の関数コードをデプロイできます。

## <a name="deploy-your-function-code"></a>関数コードをデプロイする  

新しい Function App で関数コードを作成する方法はいくつかあります。 このトピックでは、GitHub のサンプル レポジトリについて説明します。 前回と同様、次のコードで `<app_name>` プレースホルダーを作成済みの Function App の名前に置き換えます。 

```azurecli
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
デプロイ ソースの設定後に、次の例のような情報が Azure CLI に表示されます (読みやすくするために null 値は削除してあります)。

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>関数をテストする

cURL を使用して、Mac または Linux コンピューターで、または Windows で Bash を使用して、デプロイした関数をテストします。 次の cURL コマンドを実行します。`<app_name>` プレースホルダーを Function App の名前に置き換えます。 URL にクエリ文字列 `&name=<yourname>` を追加します。

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![ブラウザーに表示された関数の応答。](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

コマンド ラインで使用可能な cURL がない場合は、Web ブラウザーのアドレスに同じ URL を入力します。 再度、`<app_name>` プレースホルダーを Function App の名前に置き換え、URL にクエリ文字列 `&name=<yourname>` を追加して、要求を実行します。 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![ブラウザーに表示された関数の応答。](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 引き続きクイックスタートまたはチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 作業する予定がない場合は、次のコマンドを使用して、このクイックスタートで作成したすべてのリソースを削除してください。

```azurecli
az group delete --name myResourceGroup
```
確認を求められたら「`y`」と入力します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]


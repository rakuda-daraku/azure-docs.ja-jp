---
title: "Azure の Linux VM の仮想ハード ディスクを拡張する | Microsoft Docs"
description: "Azure CLI 2.0 を使用して、Linux VM の仮想ハード ディスクを拡張する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4a0b8254ec80576576afde7af34828025d1d2f0a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する方法
Azure の Linux 仮想マシン (VM) では、通常、オペレーティング システム (OS) の既定の仮想ハード ディスク サイズは 30 GB です。 [データ ディスクを追加](add-disk.md)して記憶域を追加できますが、OS ディスクまたは既存のデータ ディスクを拡張することもできます。 この記事では、Azure CLI 2.0 を使用して、Linux VM の管理ディスクを拡張する方法について詳しく説明します。 [Azure CLI 1.0](expand-disks-nodejs.md) を使用して、非管理対象 OS ディスクを拡張することもできます。

## <a name="expand-disk"></a>ディスクを拡張する
[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしていることを確認します。

以下のサンプルでは、パラメーター名を独自の値に置き換えてください。 たとえば、*myResourceGroup* と *myVM* といったパラメーター名にします。

1. 仮想ハード ディスクに対する操作は、実行中の VM では実行できません。 [az vm deallocate](/cli/azure/vm#deallocate) を使用して VM の割り当てを解除します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` では、コンピューティング リソースは解放されません。 コンピューティング リソースを解放するには、`az vm deallocate` を使用します。 仮想ハード ディスクを拡張するには、VM の割り当てを解除する必要があります。

2. [az disk list](/cli/azure/disk#list) を使用して、リソース グループに含まれる管理ディスクの一覧を表示します。 次の例では、*myResourceGroup* という名前のリソース グループに含まれる管理ディスクの一覧を表示します。

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    [az disk update](/cli/azure/disk#update) を使用して、必要なディスクを拡張します。 次の例では、*myDataDisk* という名前の管理ディスクのサイズを *200* GB に拡張します。

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > 管理ディスクを拡張すると、更新されたサイズが管理ディスクの最も近いサイズにマップされます。 管理ディスクの利用可能なサイズとレベルの表については、「[Azure Managed Disks の概要 - 価格と課金](../../storage/storage-managed-disks-overview.md#pricing-and-billing)」をご覧ください。

3. [az vm start](/cli/azure/vm#start) を使用して VM を起動します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM を起動します。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. 適切な資格情報を使用して VM に SSH 接続します。 OS ディスクのサイズが変更されたことを確認するには、`df -h` を使用します。 次の出力例は、データ ドライブ (*/dev/sdc1*) が 200 GB になったことを示しています。

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>次のステップ
追加の記憶域が必要な場合に、[Linux VM にデータ ディスクを追加](add-disk.md)します。 ディスク暗号化の詳細については、「[Azure CLI を使って Linux VM のディスクを暗号化する](encrypt-disks.md)」をご覧ください。


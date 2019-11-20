---
title: Azure 'da bir VHD 'nin anlık görüntüsünü oluşturma
description: Azure 'da bir VHD 'nin kopyasını bir yedekleme olarak veya sorun giderme sorunları için oluşturmayı öğrenin.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 15696469ca3861586617e9f418f8a55a7ea90467
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034790"
---
# <a name="create-a-snapshot"></a>Anlık görüntü oluşturma 

Yedekleme için bir işletim sistemi veya veri diskinin anlık görüntüsünü alın veya VM sorunlarını gidermek için. Anlık görüntü, bir VHD 'nin tam ve salt okunurdur kopyasıdır. 

## <a name="use-azure-cli"></a>Azure CLI kullanma 

Aşağıdaki örnek, [Cloud Shell](https://shell.azure.com/bash) kullanmanızı veya Azure CLI 'nın yüklü olmasını gerektirir.

Aşağıdaki adımlarda, **--Source-Disk** parametresiyle **az Snapshot Create** komutunu kullanarak anlık görüntünün nasıl yapılacağı gösterilmektedir. Aşağıdaki örnek, *Myresourcegroup* kaynak grubunda *myvm* adlı bir VM olduğunu varsayar.

[Az VM Show](/cli/azure/vm#az-vm-show)kullanarak disk kimliğini alın.

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

[Az Snapshot Create](/cli/azure/snapshot#az-snapshot-create)kullanarak *OSDisk-Backup* adlı bir anlık görüntü alın.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Anlık görüntüsünü bölge dayanıklı depolamada depolamak isterseniz, bunu [kullanılabilirlik bölgelerini](../../availability-zones/az-overview.md) destekleyen bir bölgede oluşturmanız ve **--SKU Standard_ZRS** parametresini eklemeniz gerekir.

[Az Snapshot List](/cli/azure/snapshot#az-snapshot-list)kullanarak anlık görüntülerin bir listesini görebilirsiniz.

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Azure portalı kullanma 

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol üst taraftan başlayarak **kaynak oluştur ' a** tıklayın ve **anlık görüntü**arayın. Arama sonuçlarından **anlık görüntü** ' i seçin.
3. **Anlık görüntü** dikey penceresinde **Oluştur**' a tıklayın.
4. Anlık görüntü için bir **ad** girin.
5. Var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu adı yazın. 
7. **Kaynak disk**için, anlık görüntü yapılacak yönetilen diski seçin.
8. Anlık görüntüyü depolamak için kullanılacak **hesap türünü** seçin. Yüksek performanslı bir SSD üzerinde depolanmış olması gerekmedikçe **Standart HDD** kullanın.
9. **Oluştur**’ tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

 Anlık görüntüden yönetilen bir disk oluşturarak ve ardından yeni yönetilen diski işletim sistemi diski olarak ekleyerek bir anlık görüntüden sanal makine oluşturun. Daha fazla bilgi için [bir Snapshot betiğinin VM oluşturma](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) bölümüne bakın.


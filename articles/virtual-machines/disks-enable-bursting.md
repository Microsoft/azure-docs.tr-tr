---
title: İsteğe bağlı disk patlaması 'nı etkinleştir
description: Yönetilen diskinizde istek üzerine disk patlaması etkinleştirin.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5110e580bada7bb1090b17d6df22a9354622e8e4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483155"
---
# <a name="enable-on-demand-bursting"></a>İsteğe bağlı patlaması etkinleştirin

Premium katı hal sürücüleri (SSD) iki adet kullanılabilir ikili model sahiptir; Kredi tabanlı patlama ve isteğe bağlı bir istek. Bu makalede, isteğe bağlı olarak nasıl geçiş yapılacağı ele alınmaktadır. İsteğe bağlı modeli kullanan diskler, başlangıçtaki sağlanan hedefleri aşacak şekilde veri bloğu oluşturabilir. İsteğe bağlı patlama, en fazla patlama hedefine kadar iş yükü için gereken sıklıkta gerçekleşir. İsteğe bağlı olarak, ek ücretler uygulanır.

Disk patlaması hakkında daha fazla bilgi için bkz. [yönetilen disk](disk-bursting.md)patlaması.

> [!IMPORTANT]
> Kredi tabanlı patlama kullanmak için bu makaledeki adımları izlemeniz gerekmez. Varsayılan olarak, kredi tabanlı burdıya uygun tüm disklerde etkindir.

İsteğe bağlı okeyi etkinleştirmeden önce aşağıdakileri anlayın:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Bölgesel kullanılabilirlik

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>başlarken

İsteğe bağlı burdıya Azure PowerShell modülü, Azure CLı veya Azure Resource Manager şablonlarıyla etkinleştirilebilir. Aşağıdaki örneklerde, isteğe bağlı alma özelliği etkinken yeni bir disk oluşturma ve var olan disklere isteğe bağlı olarak istek sağlama işlemlerinin nasıl yapılacağı ele alınmaktadır.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

İsteğe bağlı tekrar açma cmdlet 'leri, sürüm 5.5.0 ve az modülünün daha yeni sürümlerinde kullanılabilir. Alternatif olarak, [Azure Cloud Shell](https://shell.azure.com/)de kullanabilirsiniz.
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>İsteğe bağlı alarak boş bir veri diski oluşturun

İsteğe bağlı tekrar açma özelliğini etkinleştirmek için bir yönetilen diskin 512 GiB 'den büyük olması gerekir. `<myResourceGroupDisk>`Ve parametrelerini değiştirin ve `<myDataDisk>` sonra isteğe bağlı olarak BIR Premium SSD oluşturmak için aşağıdaki betiği çalıştırın:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Mevcut bir disk üzerinde isteğe bağlı olarak birlikte çalışmaya izin sağlayın

İsteğe bağlı tekrar açma özelliğini etkinleştirmek için bir yönetilen diskin 512 GiB 'den büyük olması gerekir. `<myResourceGroupDisk>`, Parametrelerini değiştirin `<myDataDisk>` ve var olan bir disk üzerinde isteğe bağlı olarak yeniden oluşturma özelliğini etkinleştirmek için bu komutu çalıştırın:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İsteğe bağlı tekrar açma cmdlet 'leri, sürüm 2.19.0 ve [Azure CLI modülünün](/cli/azure/install-azure-cli)daha yeni sürümlerinde kullanılabilir. Alternatif olarak, [Azure Cloud Shell](https://shell.azure.com/)de kullanabilirsiniz.

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>İsteğe bağlı bir veri diski oluşturma ve iliştirme

İsteğe bağlı tekrar açma özelliğini etkinleştirmek için bir yönetilen diskin 512 GiB 'den büyük olması gerekir. `<yourDiskName>`, `<yourResourceGroup>` Ve parametrelerini değiştirin ve `<yourVMName>` sonra isteğe bağlı olarak bir Premium SSD oluşturmak için aşağıdaki komutları çalıştırın:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Mevcut bir disk üzerinde isteğe bağlı olarak birlikte çalışmaya izin sağlayın-CLı

İsteğe bağlı tekrar açma özelliğini etkinleştirmek için bir yönetilen diskin 512 GiB 'den büyük olması gerekir. `<myResourceGroupDisk>`Ve parametrelerini değiştirin `<yourDiskName>` ve var olan bir disk üzerinde isteğe bağlı olarak yeniden oluşturma özelliğini etkinleştirmek için bu komutu çalıştırın:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

`2020-09-30`Disk API 'si ile, yeni oluşturulan veya var olan Premium SSD 'leri 512 GiB 'den büyük bir süre sonra isteğe bağlı olarak sağlayabilirsiniz. `2020-09-30`API yeni bir özellik sunmuştur `burstingEnabled` . Varsayılan olarak, bu özellik false olarak ayarlanır. Aşağıdaki örnek şablon, disk patlaması etkinken Orta Batı ABD bir 1TiB Premium SSD oluşturur:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Sonraki adımlar

Elde eden kaynaklarınız hakkında fikir edinme hakkında bilgi edinmek için bkz. [disk alma ölçümleri](disks-metrics.md).

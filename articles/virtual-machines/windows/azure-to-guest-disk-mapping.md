---
title: Azure disklerini Windows VM Konuk disklerine eşleme
description: Bir Windows VM 'nin Konuk disklerini daha az oluşturan Azure disklerini belirleme.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560999"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Azure disklerini Windows VM Konuk disklerine eşleme

Bir sanal makinenin Konuk disklerini geri yükleyen Azure disklerini belirlemeniz gerekebilir. Bazı senaryolarda, disk veya birim boyutunu bağlı Azure disklerinin boyutuyla karşılaştırabilirsiniz. SANAL makineye bağlı aynı boyutta birden çok Azure diskinin bulunduğu senaryolarda, veri disklerinin mantıksal birim numarasını (LUN) kullanmanız gerekir. 

## <a name="what-is-a-lun"></a>LUN nedir?

Mantıksal birim numarası (LUN), belirli bir depolama cihazını tanımlamak için kullanılan bir sayıdır. Her depolama cihazına, sıfırdan başlayan benzersiz bir sayısal tanımlayıcı atanır. Bir cihazın tam yolu, veri yolu numarası, hedef KIMLIK numarası ve mantıksal birim numarası (LUN) ile temsil edilir. 

Örneğin: ***Bus numarası 0, hedef kimliği 0, LUN 3***

Alıştırmada yalnızca LUN 'yi kullanmanız gerekir.

## <a name="finding-the-lun"></a>LUN bulma

LUN 'u bulmanın iki yöntemi vardır. Bu, [depolama alanları](/windows-server/storage/storage-spaces/overview) kullanıyor olmanız durumunda sizin seçtiğiniz bir işlem olur.

### <a name="disk-management"></a>Disk Yönetimi

Depolama havuzlarını kullanmıyorsanız, LUN 'U bulmak için [disk yönetimi](/windows-server/storage/disk-management/overview-of-disk-management) ' ni kullanabilirsiniz.

1. VM 'ye bağlanın ve disk yönetimi a ' yı açın. Başlat düğmesine sağ tıklayın ve "Disk Yönetimi" a ' yı seçin. Ayrıca `diskmgmt.msc` , aramaya başla kutusunu da yazabilirsiniz
1. Alt bölmede, disklerden birine sağ tıklayın ve "Özellikler" i seçin
1. LUN, "genel" sekmesindeki "konum" özelliğinde listelenecektir

### <a name="storage-pools"></a>Depolama havuzları

1. VM 'ye bağlanın ve Sunucu Yöneticisi açın
1. "Dosya ve depolama hizmetleri", "birimler", "depolama havuzları" seçeneğini belirleyin
1. Sunucu Yöneticisi sağ alt köşesinde bir "fiziksel diskler" bölümü olacaktır. Depolama havuzunu oluşturan disklerin yanı sıra her bir disk için LUN de listelenir.

## <a name="finding-the-lun-for-the-azure-disks"></a>Azure diskleri için LUN bulma

Azure portal, Azure CLı veya Azure PowerShell kullanarak bir Azure diskinin LUN 'unu bulabilirsiniz

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Azure portal bir Azure diskinin LUN 'unu bulma

1. Azure portal sanal makinelerinizin listesini göstermek için "sanal makineler" i seçin
1. Sanal makineyi seçin
1. "Diskler" i seçin
1. Eklenen diskler listesinden bir veri diski seçin.
1. Diskin LUN 'U, disk ayrıntısı bölmesinde görüntülenir. Burada görüntülenen LUN, Device Manager veya Sunucu Yöneticisi kullanılarak Konukta aranan LUN 'Lara göre açıklanmıştır.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Azure CLı veya Azure PowerShell kullanarak bir Azure diskinin LUN 'unu bulma

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---
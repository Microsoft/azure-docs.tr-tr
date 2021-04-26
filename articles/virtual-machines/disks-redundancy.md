---
title: Azure yönetilen diskler için artıklık seçenekleri
description: Bölgesel olarak yedekli depolama ve Azure yönetilen diskler için yerel olarak yedekli depolama hakkında bilgi edinin.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0882efeccfc8dc83686d75ab39b8364219c3b5f1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588097"
---
# <a name="redundancy-options-for-managed-disks"></a>Yönetilen diskler için artıklık seçenekleri

Azure yönetilen diskler, önizleme olarak, bölgesel olarak yedekli depolama (ZRS) ve yerel olarak yedekli depolama olmak üzere iki depolama artıklığı seçeneği sunar. ZRS, yönetilen diskler için yerel olarak yedekli depolama (LRS) dışında daha yüksek kullanılabilirlik sağlar. Ancak LRS disklerinin yazma gecikmesi ZRS disklerinden daha iyidir, çünkü LRS diskleri eşzamanlı olarak verileri tek bir veri merkezinde üç kopyaya yazar.

## <a name="locally-redundant-storage-for-managed-disks"></a>Yönetilen diskler için yerel olarak yedekli depolama

Yerel olarak yedekli depolama (LRS), verilerinizi seçili bölgedeki tek bir veri merkezi içinde üç kez çoğaltır. LRS, verilerinizi sunucu rafı ve sürücü hatalarıyla karşı korur. 

Doğal felaketler veya donanım sorunları nedeniyle ortaya çıkabilecek tüm bölge arızalarından LRS disklerini kullanarak uygulamanızı koruyabilmeniz için birkaç yol vardır:
- İki bölgeye eşzamanlı olarak veri yazabilme ve olağanüstü durum sırasında başka bir bölgeye otomatik yük devretmeyle SQL Server AlwaysOn gibi bir uygulama kullanın.
- LRS disklerinin sık kullanılan yedeklemelerini ZRS anlık görüntüleriyle alın.
- [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)aracılığıyla LRS diskleri için çapraz bölge olağanüstü durum kurtarmayı etkinleştirin. Ancak, çapraz bölge olağanüstü durum kurtarma sıfır kurtarma noktası hedefi (RPO) sağlamaz.

İş akışınız, bölgelerde uygulama düzeyinde zaman uyumlu yazma işlemlerini desteklemiyorsa veya uygulamanızın sıfır RPO 'ya uyması gerekiyorsa, ZRS diskler idealdir.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Yönetilen diskler için bölgesel olarak yedekli depolama (Önizleme)

Bölgesel olarak yedekli depolama (ZRS), Azure yönetilen diskinizi, seçili bölgedeki üç Azure kullanılabilirlik alanı üzerinden eşzamanlı olarak çoğaltır. Her kullanılabilirlik alanı bağımsız enerji, soğutma ve ağ altyapısına sahip olan ayrı bir fiziksel konumu ifade eder. 

ZRS diskleri, kullanılabilirlik bölgelerindeki hatalardan kurtarmanızı sağlar. Bir bölgenin tamamı kapalıysa, farklı bir bölgedeki bir sanal makineye ZRS diski eklenebilir. Ayrıca, SQL FCı, SAP ASCS/SCS veya GFS2 gibi kümelenmiş veya dağıtılmış uygulamalar için daha iyi kullanılabilirlik sağlamak üzere ZRS disklerini paylaşılan bir disk olarak da kullanabilirsiniz. Hem ZRS hem de [kullanılabilirlik alanları](../availability-zones/az-overview.md)avantajlarından yararlanmak için, farklı bölgelerdeki birincil ve ikincil VM 'lere paylaşılan bir ZRS diski iliştirebilirsiniz. Birincil bölgeniz başarısız olursa, [SCSI kalıcı ayırmasını](disks-shared-enable.md#supported-scsi-pr-commands)kullanarak ikincil VM 'ye hızlı bir şekilde yük devreolursunuz.

### <a name="limitations"></a>Sınırlamalar

Önizleme sırasında, yönetilen diskler için ZRS aşağıdaki kısıtlamalara sahiptir:

- Yalnızca Premium katı hal sürücüleri (SSD) ve standart SSD 'Ler ile desteklenir.
- Şu anda yalnızca EastUS2EUAP bölgesinde kullanılabilir.
- ZRS diskleri, API kullanılarak yalnızca Azure Resource Manager şablonlarıyla oluşturulabilir `2020-12-01` .

Önizlemeye [buradan](https://aka.ms/ZRSDisksPreviewSignUp)kaydolun.

### <a name="billing-implications"></a>Faturalandırma etkileri

Ayrıntılar için bkz. [Azure fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Diğer disk türleriyle karşılaştırma

Daha fazla yazma gecikmesi haricinde ZRS kullanan diskler LRS kullanan disklerle aynıdır. Aynı performans hedeflerine sahip olurlar. LRS ve ZRS diskleri arasındaki gecikmeyi karşılaştırmak için uygulamanızın iş yükünün benzetimini yapmak üzere [disk açısından bir sınama](disks-benchmarks.md) yapmanızı öneririz. 

### <a name="create-zrs-managed-disks"></a>ZRS yönetilen diskler oluşturma

`2020-12-01`BIR ZRS diski oluşturmak için Azure Resource Manager şablonunuz ile API 'yi kullanın.

#### <a name="create-a-vm-with-zrs-disks"></a>ZRS diskleriyle VM oluşturma

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Farklı bölgelerde bulunan VM 'lere bağlı bir paylaşılan ZRS diski ile VM oluşturma

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>ZRS diskleriyle bir sanal makine ölçek kümesi oluşturma

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Sonraki adımlar

- [ZRS diskleriyle BIR VM oluşturmak için bu örnek Azure Resource Manager şablonlarını](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)kullanın.

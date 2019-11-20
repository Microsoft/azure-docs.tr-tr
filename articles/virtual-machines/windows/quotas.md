---
title: Azure için vCPU kotaları
description: Azure için vCPU kotaları hakkında bilgi edinin.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 29eaad9835b74e9867454dffb35799a01bbb03e1
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073432"
---
# <a name="virtual-machine-vcpu-quotas"></a>Sanal makine vCPU kotaları

Sanal makineler ve sanal makine ölçek kümeleri için vCPU kotaları her bir bölgede her bir abonelik için iki katmanda düzenlenir. İlk katman toplam bölgesel vCPU sayısı ve ikinci katman D serisi vCPU 'Lar gibi çeşitli VM boyutu ailesi çekirdekleri. Yeni bir VM 'nin dağıtıldığı her zaman, sanal makine için vCPU 'Lar VM boyut ailesi için vCPU kotasını veya toplam bölgesel vCPU kotasını aşmamalıdır. Bu kotalardan biri aşılırsa, VM dağıtımına izin verilmez. Ayrıca, bölgedeki toplam sanal makine sayısı için bir kota de vardır. Bu kotaların her biri hakkındaki ayrıntılar, [Azure Portal](https://portal.azure.com) **abonelik** sayfasının **kullanım + kotalar** bölümünde görünebilir veya PowerShell kullanarak değerleri sorgulayabilirsiniz.

   
 
## <a name="check-usage"></a>Kullanımı denetleme

Kota kullanımınızı denetlemek için [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) cmdlet 'ini kullanabilirsiniz.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Çıktı şuna benzer olacaktır:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Ayrılmış VM Örnekleri
VM boyutu esnekliği olmayan tek bir aboneliğin kapsamına alınmış ayrılmış VM örnekleri, vCPU kotaları için yeni bir boyut ekler. Bu değerler, belirtilen boyutun abonelikte dağıtılabilir olması gereken örneklerinin sayısını anlatır. Kota, ayrılmış VM örneklerinin abonelikte dağıtılabilir olmasını sağlamak için kotanın ayrıldığından emin olmak üzere kota sisteminde bir yer tutucu olarak çalışır. Örneğin, belirli bir abonelikte 10 Standard_D1 ayrılmış VM örneği varsa, Standard_D1 ayrılmış VM örnekleri için kullanım sınırı 10 olur. Bu, Azure 'un, Standard_D1 örnekleri için kullanılmak üzere toplam bölgesel vCPU kotasında her zaman en az 10 vCPU olduğundan ve Standard_D1 örnekleri için kullanılacak standart D ailesi vCPU kotasında en az 10 vCPU olduğundan emin olmasına neden olur.

Tek bir aboneliği satın almak için kota artışı gerekiyorsa, aboneliğinizde [bir kota artışı isteyebilirsiniz](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) .

## <a name="next-steps"></a>Sonraki adımlar

Faturalandırma ve Kotalar hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).

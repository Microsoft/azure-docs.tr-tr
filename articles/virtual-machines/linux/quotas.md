---
title: Azure için vCPU kotaları
description: Azure için vCPU kotaları hakkında bilgi edinin.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 04ab96058650e6165a745b48f4c0fc213c2f9412
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035180"
---
# <a name="virtual-machine-vcpu-quotas"></a>Sanal makine vCPU kotaları

Sanal makineler ve sanal makine ölçek kümeleri için vCPU kotaları her bir bölgede her bir abonelik için iki katmanda düzenlenir. İlk katman toplam bölgesel vCPU sayısı ve ikinci katman D serisi vCPU 'Lar gibi çeşitli VM boyutu ailesi çekirdekleri. Yeni bir VM 'nin dağıtıldığı her zaman, sanal makine için vCPU 'Lar VM boyut ailesi için vCPU kotasını veya toplam bölgesel vCPU kotasını aşmamalıdır. Bu kotalardan biri aşılırsa, VM dağıtımına izin verilmez. Ayrıca, bölgedeki toplam sanal makine sayısı için bir kota de vardır. Bu kotaların her biri hakkındaki ayrıntılar, [Azure Portal](https://portal.azure.com) **abonelik** sayfasının **kullanım + kotalar** bölümünde görünebilir veya Azure CLI kullanarak değerleri sorgulayabilirsiniz.


## <a name="check-usage"></a>Kullanımı denetleme

[Az VM List-Usage](/cli/azure/vm)kullanarak kota kullanımınızı kontrol edebilirsiniz.

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

Çıktı aşağıdakine benzer görünmelidir:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Ayrılmış VM Örnekleri
VM boyutu esnekliği olmayan tek bir aboneliğin kapsamına alınmış ayrılmış VM örnekleri, vCPU kotaları için yeni bir boyut ekler. Bu değerler, belirtilen boyutun abonelikte dağıtılabilir olması gereken örneklerinin sayısını anlatır. Kota sisteminde Azure ayırmalarının abonelikte yer aldığından emin olmak için kotanın ayrılmış olduğundan emin olmak için kota sisteminde bir yer tutucu olarak çalışırlar. Örneğin, belirli bir aboneliğin 10 Standard_D1 ayırması varsa Standard_D1 ayırmaları için kullanım limiti 10 olur. Bu, Azure 'un, Standard_D1 örnekleri için kullanılmak üzere toplam bölgesel vCPU kotasında her zaman en az 10 vCPU olduğundan ve Standard_D1 örnekleri için kullanılacak standart D ailesi vCPU kotasında en az 10 vCPU olduğundan emin olmasına neden olur.

Tek bir aboneliği satın almak için kota artışı gerekiyorsa, aboneliğinizde [bir kota artışı isteyebilirsiniz](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) .

## <a name="next-steps"></a>Sonraki adımlar

Faturalandırma ve Kotalar hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).

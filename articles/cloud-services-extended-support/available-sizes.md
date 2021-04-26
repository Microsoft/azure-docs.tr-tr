---
title: Azure Cloud Services için kullanılabilir Boyutlar (genişletilmiş destek)
description: Azure Cloud Services (genişletilmiş destek) dağıtımları için kullanılabilir Boyutlar
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 1c1faf14eb101da41679f9f0596e1e750a3c6a51
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166269"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure Cloud Services için kullanılabilir Boyutlar (genişletilmiş destek)

Bu makalede Cloud Services (genişletilmiş destek) örnekleri için kullanılabilir sanal makine boyutları açıklanmaktadır.   

| SKU Ailesi |  ACU/çekirdek | 
|---|---|
| [A5-7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[AV2](../virtual-machines/av2-series.md) | 100 | 
|[TID](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160-190 * |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160-190 * |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230-260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230-260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160-190 * |
|[Acil](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[Olsun](../virtual-machines/h-series.md) | 290-300* | 

>[!NOTE]
> * işaretli ACU'lar, CPU frekansını artırmak ve performans artışı sağlamak için Intel® Turbo Boost teknolojisinden faydalanır. Performans artışının oranı VM boyutuna, iş yüküne ve aynı ana bilgisayarda çalışan iş yüklerine göre değişiklik gösterebilir.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Cloud Services için boyutları yapılandırın (genişletilmiş destek)

Hizmet tanımı dosyasındaki hizmet modelinin bir parçası olarak bir rol örneğinin sanal makine boyutunu belirtebilirsiniz. Rolün boyutu CPU çekirdeği sayısını, bellek kapasitesini ve yerel dosya sistemi boyutunu belirler.

Örneğin, Web rolü örnek boyutunu şu şekilde ayarlama `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Mevcut bir rolün boyutunu değiştirme

Mevcut bir rolün boyutunu değiştirmek için, hizmet tanımlama dosyasında (csdef) sanal makine boyutunu değiştirin, bulut hizmetinizi yeniden paketleyin ve yeniden dağıtın. 

## <a name="get-a-list-of-available-sizes"></a>Kullanılabilir boyutların bir listesini alın 

Kullanılabilir boyutların bir listesini almak için bkz. [kaynak SKU 'ları-Listele](/rest/api/compute/resourceskus/list) ve aşağıdaki filtreleri Uygula:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Sonraki adımlar 
- Cloud Services için [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin (genişletilmiş destek).
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [şablon](deploy-template.md) veya [Visual Studio](deploy-visual-studio.md)kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtın.

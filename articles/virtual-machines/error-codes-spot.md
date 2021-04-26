---
title: Azure spot sanal makineleri ve ölçek kümeleri örnekleri için hata kodları
description: Azure spot sanal makineleri ve ölçek kümesi örneklerini kullanırken görebileceğiniz hata kodları hakkında bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 6d43935365580233063deb0e523d222351c22d54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670613"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Azure spot sanal makineleri ve ölçek kümeleri için hata iletileri

Azure spot sanal makineleri ve ölçek kümelerini kullanırken alacağınız bazı olası hata kodları aşağıda verilmiştir.


| Anahtar | İleti | Açıklama |
|-----|---------|-------------|
| SkuNotAvailable | ' ' Kaynağı için istenen katman \<resource\> Şu anda ' ' \<location\> aboneliği için ' ' konumunda kullanılamıyor \<subscriptionID\> . Başka bir katman deneyin veya farklı bir konuma dağıtın. | Bu konumda VM veya ölçek kümesi örneğinizi oluşturmak için yeterli sayıda Azure spot sanal makine kapasitesi yok. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Çıkarma ilkesi yalnızca Azure spot sanal makinelerinde ayarlanabilir. | Bu VM bir Azure spot sanal makinesi olmadığından, çıkarma ilkesini ayarlayamazsınız. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Azure spot sanal makinesi kullanılabilirlik kümesi 'nde desteklenmiyor. | Bir Azure spot sanal makinesi kullanmayı veya bir VM 'yi bir kullanılabilirlik kümesinde kullanmayı seçmeniz gerekir, her ikisini de seçemezsiniz. |
| AzureSpotFeatureNotEnabledForSubscription  |  Abonelik, Azure spot sanal makine özelliği ile etkin değil. | Azure spot sanal makinelerini destekleyen bir abonelik kullanın. |
| VMPriorityCannotBeApplied  |  Belirtilen ' ' öncelik değeri, {0} {1} sanal makine oluşturulduğunda hiçbir öncelik belirtilmediğinden ' ' sanal makinesine uygulanamıyor. | VM oluşturulduğunda önceliği belirtin. |
| SpotPriceGreaterThanProvidedMaxPrice  |  {0}Belirtilen en büyük fiyat ' USD ', ' {1} {2} ' Azure spot sanal makine boyutu için geçerli olan ' USD ' nokta fiyatından düşük olduğundan ' ' işlemi gerçekleştirilemiyor {3} . | En yüksek fiyat ' ı seçin. Daha fazla bilgi için bkz. [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) veya [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)için fiyatlandırma bilgileri.|
| Maxpricevaluegeçersizdir  |  Geçersiz en yüksek fiyat değeri. En yüksek fiyat için desteklenen değerler-1 veya sıfırdan büyük bir ondalık. -1 ' in Max Price değeri, Azure spot sanal makinesinin fiyat nedenleriyle çıkarılmayacağını gösterir. | Geçerli bir en yüksek fiyat girin. Daha fazla bilgi için bkz. [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) veya [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)fiyatlandırması. |
| Maxpricechangenotallodilimforallocatedvms | ' ' Sanal makinesi {0} Şu anda ayrıldığınızda en yüksek fiyat değişikliğine izin verilmez. Serbest bırakın ve yeniden deneyin. | En fazla fiyata değişiklik yapabilmeniz için VM 'yi serbest bırakın. |
| MaxPriceChangeNotAllowed | En fazla fiyat değişikliğine izin verilmez. | Bu VM için en yüksek fiyatı değiştiremezsiniz. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure spot sanal makinesi bu API sürümü için desteklenmiyor. | API sürümünün 2019-03-01 olması gerekir. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure spot sanal makinesi bu VM boyutu için desteklenmiyor {0} . | Başka bir VM boyutu seçin. Daha fazla bilgi için bkz. [Azure spot sanal makineler](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  En yüksek fiyat yalnızca Azure spot sanal makineler için desteklenir. | Daha fazla bilgi için bkz. [Azure spot sanal makineler](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Kaynakları taşıma isteği bir Azure spot sanal makinesi içerir. Desteklenmez. Sanal makine kimliklerinin hata ayrıntılarına bakın. | Azure spot sanal makinelerini taşıyamazsınız. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Kaynakları taşıma isteği bir Azure spot sanal makine ölçek kümesi içerir. Desteklenmez. Sanal makine ölçek kümesi kimlikleri için hata ayrıntılarına bakın. | Azure spot sanal makine ölçek kümesi örneklerini taşıyamazsınız. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Azure spot sanal makinesi, VM düzenleme modu ile sanal makine ölçek kümesinde desteklenmez. | Azure spot sanal makine örneklerini kullanmak için Orchestration modunu sanal makine ölçek kümesine ayarlayın. |


**Sonraki adımlar** Daha fazla bilgi için bkz. [sanal makineleri spot](./spot-vms.md).
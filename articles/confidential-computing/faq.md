---
title: Azure gizli bilgi Işlem hakkında SSS
description: Azure gizli bilgi işlem hakkında sık sorulan soruların yanıtları.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: aa78d6495eeffd0ea733451e029f07413602ce0d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812741"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure gizli bilgi Işlem için sık sorulan sorular

Bu makalede, [Azure sanal makinelerinde gizli bilgi işlem iş yüklerini](overview.md)çalıştırmaya ilişkin en yaygın soruların yanıtları sağlanmaktadır.

Azure sorununuz bu makalede giderilmemişse, [MSDN ve Stack Overflow](https://azure.microsoft.com/support/forums/)Azure forumlarını ziyaret edin. Sorununuzu bu forumlara gönderebilir veya [ @AzureSupport Twitter 'da](https://twitter.com/AzureSupport)ilan edebilirsiniz. Ayrıca, bir Azure destek isteği de gönderebilirsiniz. Destek isteği göndermek için [Azure Desteği sayfasında](https://azure.microsoft.com/support/options/)destek al ' ı seçin.

## <a name="confidential-computing-virtual-machines"></a>Gizli Bilgi Işlem sanal makineleri <a id="vm-faq"></a>

**Azure 'da DCsv2 serisi VM 'Leri nasıl dağıtırım?**

Bir DCsv2 VM dağıtımı için bazı yollar şunlardır:
   - [Azure Resource Manager şablonu](../virtual-machines/windows/template-description.md) kullanma
   - [Azure Portal](https://portal.azure.com/#create/hub)
   - [Azure gizli bilgi işlem (sanal makine)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) marketi çözüm şablonunda. Market çözüm şablonu, bir müşteriyi desteklenen senaryolara (bölgeler, görüntüler, kullanılabilirlik, disk şifrelemesi) karşı sınırlandırılmasına yardımcı olur. 

**Tüm işletim sistemi görüntüleri Azure gizli bilgi işlem ile çalışacak mı?**

Hayır. Sanal makineler yalnızca Ubuntu Server 18,04, Ubuntu Server 20,04, Windows Server 2019 Datacenter ve Windows Server 2016 Datacenter ile 2. nesil işletim makinelerine dağıtılabilir. [Linux](../virtual-machines/generation-2.md) ve [Windows](../virtual-machines/generation-2.md) üzerinde gen 2 VM 'ler hakkında daha fazla bilgi edinin

**DCsv2 sanal makineler portalda gri renkte bulunur ve bir tane seçemiyorum**

VM 'nin yanındaki bilgi kabarcığa göre gerçekleştirilecek farklı eylemler vardır:
   -    **Unsupportedgeneration**: sanal makine görüntüsünün neslini "Gen2" olarak değiştirin.
   -    **NotAvailableForSubscription**: bölge, aboneliğiniz için henüz kullanılamıyor. Kullanılabilir bir bölge seçin.
   -    **InsufficientQuota**: [kotayı artırmak için bir destek isteği oluşturun](../azure-portal/supportability/per-vm-quota-requests.md). Ücretsiz deneme aboneliklerinin, gizli bilgi işlem VM 'Leri için kotası yoktur. 

**DCsv2 sanal makineler, Portal boyut Seçicisi 'nde arama yapmayı denediğimde gösterilmez**

[Kullanılabilir bir bölge](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)seçtiğinizden emin olun. Ayrıca, boyut seçicisindeki "tüm filtreleri temizle" yi seçtiğinizden emin olun. 

**Azure gizli bilgi işlem ile hızlandırılmış ağı etkinleştirebilir miyim?**

 Hayır. Hızlandırılmış ağ DC-Series veya DCsv2-Series sanal makinelerde desteklenmez. Her türlü gizli bilgi işlem sanal makine dağıtımı veya Azure Kubernetes hizmet kümesi dağıtımı için, gizli bilgi işlem sırasında çalışan her türlü gizli bilgi

**Azure ayrılmış ana bilgisayarını Bu makinelerle kullanabilir miyim?**

Evet. Azure adanmış konak desteği DCsv2 serisi sanal makineler. Azure adanmış ana bilgisayar, sanal makinelerinizi üzerinde çalıştırmak için tek kiracılı bir fiziksel sunucu sağlar. Kullanıcılar genellikle fiziksel güvenlik, veri bütünlüğü ve izleme gibi uyumluluk gereksinimlerini karşılamak için Azure ayrılmış ana bilgisayarını kullanır. 

**Bir Azure Resource Manager şablonu dağıtım hatası hatası alıyorum: "onaylanan standart DcsV2 ailesi çekirdek kotasının aşıldığı için Işlem tamamlanamadı"**

[Kotayı artırmak için bir destek Isteği oluşturun](../azure-portal/supportability/per-vm-quota-requests.md). Ücretsiz deneme aboneliklerinin, gizli bilgi işlem VM 'Leri için kotası yoktur. 

**DCsv2-Series ve DC-Series VM 'Ler arasındaki fark nedir?**

DC-Series VM 'Ler, Intel SGX ile daha az sayıda bellek, daha az şifreleme sayfa önbelleği (EPC) belleğine sahiptir ve yalnızca iki bölgede (ABD Doğu ve Standard_DC2s ve Standard_DC4s boyutlarda Avrupa Batı) kullanılabilir. Bu VM 'Lerin genel kullanıma açık hale getirilmesi planlanmamaktadır ve üretim kullanımı için önerilmez. Bu VM 'Leri dağıtmak için,  [gizli işlem DC-Series VM [Önizleme]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Market örneğini kullanın.

**DCsv2 sanal makineleri küresel olarak kullanılabilir mi?**

Hayır. Şu anda, bu sanal makineler yalnızca seçim bölgelerinde kullanılabilir. Kullanılabilir en son bölgeler için [ürünleri bölgelere göre sayfasına](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) göz atın. 

**Bu makinelerde hiper iş parçacığı kapalı mı?**

Tüm Azure gizli bilgi işlem kümeleri için hiper iş parçacığı devre dışı bırakıldı.

**DCsv2 sanal makinelerine açık şifreleme SDK 'sını Yükleme Nasıl yaparım??**
   
Azure veya şirket içi bir makineye OE SDK 'Yı yükleme yönergeleri için, [Open Enclave SDK GitHub](https://github.com/openenclave/openenclave)' da yer alan yönergeleri izleyin.
     
Ayrıca, işletim sistemine özgü yükleme yönergeleri için açık şifreleme SDK GitHub ' a bakabilirsiniz:
   - [Windows 'a OE SDK 'Yı yükler](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Ubuntu 18,04 ' ye OE SDK 'Yı kurma](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
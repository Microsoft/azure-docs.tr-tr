---
title: include dosyası
description: include dosyası
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 0930fa371500125c15cd969b9e9f4b7a2853612e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71175004"
---
## <a name="deployment-considerations"></a>Dağıtma konuları
* **Azure aboneliği** : birkaç işlem yoğunluğu yoğun örneği dağıtmak için, Kullandıkça Öde aboneliğine veya diğer satın alma seçeneklerine göz önünde bulundurun. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/) kullanıyorsanız, yalnızca sınırlı sayıda Azure işlem çekirdeği kullanabilirsiniz.

* **Fiyatlandırma ve kullanılabilirlik** -bu VM boyutları yalnızca standart fiyatlandırma katmanında sunulur. Azure bölgelerinde kullanılabilirlik için [bölgeye göre kullanılabilir ürünleri](https://azure.microsoft.com/global-infrastructure/services/) denetleyin. 
* **Çekirdek kotası** – Azure aboneliğinizdeki çekirdek kotasını varsayılan değerden artırmanız gerekebilir. Aboneliğiniz Ayrıca, H serisi de dahil olmak üzere belirli VM boyutu ailelerinde dağıtabileceğiniz çekirdek sayısını sınırlayabilir. Kota artışı istemek için, ücretsiz [bir çevrimiçi müşteri destek isteği açın](../articles/azure-supportability/how-to-create-azure-support-request.md) . (Varsayılan sınırlar, abonelik kategorime bağlı olarak değişebilir.)
  
  > [!NOTE]
  > Büyük ölçekli kapasite gereksinimleriniz varsa Azure desteği 'ne başvurun. Azure kotaları, kapasite garantisi değil kredi limitlerdir. Kotasından bağımsız olarak yalnızca kullandığınız çekirdekler için ücretlendirilirsiniz.
  > 
  > 
* **Sanal ağ** : yoğun işlem yoğunluklu örnekleri kullanmak Için bir Azure [sanal ağı](https://azure.microsoft.com/documentation/services/virtual-network/) gerekli değildir. Ancak, birçok dağıtım için, şirket içi kaynaklara erişmeniz gerekiyorsa, en az bir bulut tabanlı Azure sanal ağı veya siteden siteye bağlantı gerekir. Gerektiğinde, örnekleri dağıtmak için yeni bir sanal ağ oluşturun. Benzeşim grubundaki bir sanal ağa işlem yoğunluklu VM 'Lerin eklenmesi desteklenmez.
* **Yeniden boyutlandırma** – kendi özel donanımları nedeniyle, yalnızca aynı büyüklükte aile içindeki işlem yoğunluğu örnekleri yeniden boyutlandırabilirsiniz (H serisi veya Işlem yoğunluklu A serisi). Örneğin, bir h serisi VM 'yi yalnızca bir H serisi boyutundan diğerine yeniden boyutlandırabilirsiniz. Ayrıca, işlem yoğunluğu olmayan bir boyuttan işlem yoğunluğu yoğun bir boyut olarak yeniden boyutlandırılması desteklenmez.  

## <a name="rdma-capable-instances"></a>RDMA özellikli örnekler
Yoğun işlem yoğunluğu olan örneklerin (A8, A9, H16r, H16mr, HB ve HC) bir alt kümesi, uzak doğrudan bellek erişimi (RDMA) bağlantısı için bir ağ arabirimi özelliğidir. ' R ' ile ayrılmış, NC24rs konfigürasyonları (NC24rs_v2 ve NC24rs_v3) gibi seçili N serisi boyutlar da RDMA özellikli. Bu arabirim, diğer VM boyutlarının kullanabildiği standart Azure ağ arabirimine ek niteliğindedir. 
  
Bu arabirim, RDMA özellikli örneklerin bir InfiniBand (ıB) ağı üzerinden iletişim kurmasını, HB, HC, H16r, H16mr ve RDMA özellikli N serisi sanal makineler için de EDR ücretleri ve A8 ve A9 sanal makineler için QDR ücretleri ile çalışma olanağı sağlar. Bu RDMA özellikleri, bazı Ileti geçirme arabirimi (MPı) uygulamalarının ölçeklenebilirliğini ve performansını artırabilir. Hız hakkında daha fazla bilgi için bu sayfadaki tablolardaki ayrıntılara bakın.

> [!NOTE]
> Azure 'da, ıB üzerinden IP yalnızca SR-ıOV etkinleştirilmiş VM 'lerde desteklenir (InfiniBand için SR-ıOV, şu anda HB ve HC). RDMA üzerinden RDMA, tüm RDMA özellikli örnekler için desteklenir.
>


---
title: 'Azure ExpressRoute: devreler ve eşleme'
description: Bu sayfada ExpressRoute bağlantı hattı ve yönlendirme etki alanları/eşleme hakkında genel bir bakış sağlanır.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 87fed1d2ac4f5fa85c01d7af10bec10c1412744f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202353"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute devreleri ve eşleme

ExpressRoute devreleri, bir bağlantı sağlayıcısı aracılığıyla şirket içi altyapınızı Microsoft 'a bağlama. Bu makale ExpressRoute devreleri ve yönlendirme etki alanlarını/eşlemeyi anlamanıza yardımcı olur. Aşağıdaki şekilde, WAN ve Microsoft arasındaki bağlantının mantıksal bir gösterimi gösterilmektedir.

![ExpressRoute bağlantı hattı, bağlantı sağlayıcısı aracılığıyla şirket içi altyapınızı Microsoft 'a nasıl bağlayabildiğini gösteren diyagram.](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azure genel eşlemesi kullanım dışı bırakılmıştır ve yeni ExpressRoute devreleri için kullanılamaz. Yeni devreler, Microsoft eşlemesini ve özel eşlemeyi destekler.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute bağlantı hatları

ExpressRoute devresi, bir bağlantı sağlayıcısı aracılığıyla şirket içi altyapınız ile Microsoft bulut hizmetleri arasındaki mantıksal bir bağlantıyı temsil eder. Birden fazla ExpressRoute devresine sipariş edebilirsiniz. Her devre aynı veya farklı bölgelerde olabilir ve farklı bağlantı sağlayıcıları aracılığıyla şirket içi olarak bağlanabilir.

ExpressRoute devreleri hiçbir fiziksel varlıkla eşlenmiyor. Devre, hizmet anahtarı (s-Key) olarak adlandırılan standart bir GUID tarafından benzersiz bir şekilde tanımlanır. Hizmet anahtarı, Microsoft, bağlantı sağlayıcısı ve sizin için değiş tokuş edilen tek bilgi parçasıdır. S anahtarı güvenlik amaçları için gizli dizi değildir. Bir ExpressRoute bağlantı hattı ile s anahtarı arasında bir 1:1 eşlemesi vardır.

Yeni ExpressRoute devreleri, iki bağımsız eşleme içerebilir: özel eşleme ve Microsoft eşlemesi. Mevcut ExpressRoute devreleri üç eşleme içerebilir: Azure genel, Azure özel ve Microsoft. Her eşleme bağımsız bir BGP oturumu çiftidir ve bunların her biri yüksek kullanılabilirlik için Redundantly yapılandırırlar. Bir ExpressRoute bağlantı hattı ve yönlendirme etki alanları arasında 1: N (1 <= N <= 3) eşleme var. ExpressRoute bağlantı hattı, ExpressRoute bağlantı hattı başına etkinleştirilmiş herhangi bir, iki veya üç peerte sahip olabilir.

Her devrenin sabit bir bant genişliğine (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 10 Gbps) sahiptir ve bir bağlantı sağlayıcısına ve bir eşleme konumuna eşlenir. Seçtiğiniz bant genişliği tüm devre eşlemelerine göre paylaşılır

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Kotalar, sınırlar ve sınırlamalar

Varsayılan kotalar ve sınırlar her ExpressRoute devresi için geçerlidir. Kotalar hakkında güncel bilgiler için [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md) sayfasına bakın.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute eşlemesi

ExpressRoute devresi ile ilişkili birden çok yönlendirme etki alanı/eşlemesi vardır: Azure genel, Azure özel ve Microsoft. Her eşleme, yüksek kullanılabilirlik için bir çift yönlendiriciyle (etkin-etkin veya yük paylaşım yapılandırması) aynı şekilde yapılandırılır. Azure Hizmetleri, IP adresleme düzenlerini göstermek için Azure *genel* ve *Azure özel* olarak kategorize edilir.

![Azure genel, Azure özel ve Microsoft eşlerinin bir ExpressRoute devresinde nasıl yapılandırıldığını gösteren diyagram.](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Azure özel eşlemesi

Bir sanal ağ içinde dağıtılan sanal makineler (IaaS) ve Cloud Services (PaaS) gibi Azure işlem hizmetleri, özel eşleme etki alanı aracılığıyla bağlanabilir. Özel eşleme etki alanı, Microsoft Azure ' ye çekirdek ağınızın güvenilen bir uzantısı olarak değerlendirilir. Çekirdek ağınız ve Azure sanal ağlarınız (sanal ağlar) arasında çift yönlü bağlantı kurabilirsiniz. Bu eşleme, sanal makinelere ve bulut hizmetlerine doğrudan kendi özel IP adreslerine bağlanmanızı sağlar.  

Özel eşleme etki alanına birden fazla sanal ağ bağlayabilirsiniz. Sınırlamalar ve sınırlamalar hakkında bilgi edinmek için [SSS sayfasını](expressroute-faqs.md) gözden geçirin. Limitlerde güncel bilgiler için [Azure aboneliği ve hizmet sınırları, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md) sayfasını ziyaret edebilirsiniz.  Yönlendirme yapılandırması hakkında ayrıntılı bilgi için [yönlendirme](expressroute-routing.md) sayfasına bakın.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft eşlemesi

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Microsoft çevrimiçi hizmetler (Microsoft 365 ve Azure PaaS Services) bağlantısı, Microsoft eşlemesi aracılığıyla oluşur. Microsoft eşleme yönlendirme etki alanı aracılığıyla WAN ve Microsoft bulut hizmetleri arasında çift yönlü bağlantıyı etkinleştik. Yalnızca size veya bağlantı sağlayıcınıza ait olan genel IP adreslerinden Microsoft bulut hizmetlerine bağlanmanız gerekir ve tüm tanımlı kurallara uymalısınız. Daha fazla bilgi için bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md) sayfası.

Desteklenen hizmetler, maliyetler ve yapılandırma ayrıntıları hakkında daha fazla bilgi için [SSS sayfasına](expressroute-faqs.md) bakın. Microsoft eşleme desteği sunan bağlantı sağlayıcılarının listesi hakkında bilgi için bkz. [ExpressRoute konumları](expressroute-locations.md) sayfası.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Eşleme karşılaştırması

Aşağıdaki tabloda üç eşleme karşılaştırılır:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

ExpressRoute devrenizin kapsamında bir veya daha fazla yönlendirme etki alanını etkinleştirebilirsiniz. Tek bir yönlendirme etki alanında birleştirmek istiyorsanız, tüm yönlendirme etki alanlarının aynı VPN 'e yerleştirilmelerini seçebilirsiniz. Ayrıca, diyagrama benzer şekilde farklı yönlendirme etki alanlarına da koyabilirsiniz. Önerilen yapılandırma, özel eşlemenin doğrudan çekirdek ağa bağlanması ve genel ve Microsoft eşleme bağlantılarının DMZ 'nize bağlı olması durumunda olur.

Her eşleme ayrı BGP oturumları gerektirir (her bir eşleme türü için bir çift). BGP oturum çiftleri, yüksek oranda kullanılabilir bir bağlantı sağlar. Katman 2 bağlantı sağlayıcıları aracılığıyla bağlanıyorsanız, yönlendirmeyi yapılandırma ve yönetme konusunda siz sorumlusunuz. ExpressRoute ayarlamaya yönelik [iş akışlarını](expressroute-workflows.md) inceleyerek daha fazla bilgi edinebilirsiniz.

## <a name="expressroute-health"></a><a name="health"></a>ExpressRoute durumu

ExpressRoute devreleri, [ağ performansı İzleyicisi](../networking/network-monitoring-overview.md) (NPM) kullanarak, sanal ağlar ve bant genişliği kullanımı için kullanılabilir.

NPM, Azure özel eşleme ve Microsoft eşleme sistem durumunu izler. Daha fazla bilgi için [gönderimize](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* Bir hizmet sağlayıcı bulun. Bkz. [ExpressRoute hizmet sağlayıcıları ve konumları](expressroute-locations.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).
* ExpressRoute bağlantınızı yapılandırın.
  * [ExpressRoute devreleri oluşturup yönetme](expressroute-howto-circuit-portal-resource-manager.md)
  * [ExpressRoute işlem hatları için yönlendirmeyi (eşleme) yapılandırma](expressroute-howto-routing-portal-resource-manager.md)
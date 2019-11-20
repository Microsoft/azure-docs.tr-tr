---
title: 'Azure ExpressRoute: devreler ve eşleme'
description: Bu sayfada, ExpressRoute devreleri ve Yönlendirme etki alanları/eşlemesi genel bir bakış sağlar.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: mialdrid
ms.openlocfilehash: f6673e114c249cb86c648155b889e925554e9458
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083629"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute bağlantı hatları ve eşleme

ExpressRoute bağlantı hatları, şirket içi altyapınızı Microsoft bağlantı sağlayıcısı aracılığıyla bağlanın. Bu makalede, ExpressRoute devreleri ve Yönlendirme etki alanları/eşlemesi anlamanıza yardımcı olur. Aşağıdaki şekilde WAN ile Microsoft arasında bağlantı mantıksal bir gösterimi gösterilmektedir.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azure genel eşlemesi kullanım dışı bırakılmıştır ve yeni ExpressRoute devreleri için kullanılamaz. Yeni devreler, Microsoft eşlemesini ve özel eşlemeyi destekler.  
>

## <a name="circuits"></a>ExpressRoute devreleri

Bir ExpressRoute bağlantı hattı, şirket içi altyapınızı ve bağlantı sağlayıcı üzerinden Microsoft bulut hizmetleri arasında mantıksal bağlantıyı temsil eder. Birden çok ExpressRoute bağlantı hattına sipariş edebilirsiniz. Her bağlantı hattı aynı veya farklı bölgelerde olabilir ve farklı bağlantı sağlayıcıları aracılığıyla şirket içinde bağlanabilir.

ExpressRoute bağlantı hatları için herhangi bir fiziksel varlık eşlemeyin. Bir bağlantı hattı GUID hizmet anahtarını (s-anahtar) adlandırılan bir standart tarafından benzersiz şekilde tanımlanır. Hizmet anahtarı yalnızca Microsoft, bağlantı sağlayıcısı ve siz arasında alınıp verilen bilgi parçasıdır. S-key, güvenlik nedenleriyle bir gizli dizi değil. S anahtar ile bir ExpressRoute bağlantı hattı arasında bir 1:1 eşleme vardır.

Yeni ExpressRoute bağlantı hatları, iki bağımsız eşlemeler içerebilir: özel eşleme ve Microsoft eşlemesi. Mevcut bir ExpressRoute bağlantı hatları üç eşlemenin içerebilir ancak: Azure genel, Azure özel ve Microsoft. Her eşleme bir BGP oturumu bağımsız, bunların nedenle yüksek kullanılabilirlik için yapılandırılmış her biri çiftidir. 1: n yoktur (1 < = N < = 3) bir ExpressRoute bağlantı hattı arasında eşleme ve Yönlendirme etki alanları. ExpressRoute devresi, herhangi bir, iki veya üç eşlemenin ExpressRoute bağlantı hattı etkin tamamını olabilir.

Her bağlantı hattı (50 MB/sn, 100 MB/sn, 200 MB/sn, 500 MB/sn, 1 GB/sn, 10 GB/sn) sabit bir bant genişliğine sahip ve bir bağlantı sağlayıcısı ve eşleme konumuna eşlenmiş. Seçtiğiniz bant genişliği tüm bağlantı hattı eşlemeler arasında paylaşılır.

### <a name="quotas"></a>Kotalar, sınırlar ve sınırlamalar

Her ExpressRoute bağlantı hattı için varsayılan kotaları ve sınırları geçerlidir. Başvurmak [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-subscription-service-limits.md) kotaları hakkında güncel bilgi sayfası.

## <a name="routingdomains"></a>ExpressRoute eşdüzey hizmet sağlama

ExpressRoute bağlantı hattına sahip birden fazla Yönlendirme etki alanları/ilişkili eşlemeleri: Azure genel, Azure özel ve Microsoft. Her eşleme yönlendiricileri çifti üzerinde aynı şekilde yapılandırıldığından (etkin-etkin ya da yük paylaşma yapılandırma) yüksek kullanılabilirlik için. Azure Hizmetleri olarak kategorilere ayrılmış *Azure genel* ve *Azure özel* IP adresi düzenlerini göstermek için.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Azure özel eşdüzey hizmet sağlama

Azure işlem Hizmetleri, yani sanal makineler (Iaas) ve bulut hizmetlerini (PaaS) ve bir sanal ağda dağıtılan üzerinden özel eşleme etki alanına bağlanabilir. Özel Eşleme etki alanı, çekirdek ağınızı Microsoft azure'da güvenilir bir uzantısı olarak kabul edilir. Çekirdek Ağ ve Azure sanal ağları (Vnet) arasında çift yönlü bağlantı ayarlayabilirsiniz. Bu eşleme, sanal makinelere bağlanmak ve bulut Hizmetleri doğrudan üzerinde özel IP adreslerini sağlar.  

Birden fazla sanal ağ özel eşleme etki alanına bağlanabilir. Gözden geçirme [SSS sayfasını](expressroute-faqs.md) sınırlar ve sınırlamalar hakkında bilgi için. Ziyaret ettiğiniz [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-subscription-service-limits.md) sınırları hakkında güncel bilgi sayfası.  Başvurmak [yönlendirme](expressroute-routing.md) sayfasına yönlendirme yapılandırması hakkında ayrıntılı bilgi için.

### <a name="microsoftpeering"></a>Microsoft eşlemesi

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Microsoft çevrimiçi hizmetler bağlantısı (Office 365 ve Azure PaaS hizmetleri) Microsoft eşlemesi üzerinden gerçekleşir. WAN ve Microsoft cloud services aracılığıyla Microsoft eşleme Yönlendirme etki alanı arasında çift yönlü bağlantı etkinleştiririz. Yalnızca, veya bağlantı sağlayıcınızdan ait genel IP adresleri üzerinden Microsoft bulut hizmetlerine bağlanmak ve tüm tanımlı kurallara uymalıdır. Daha fazla bilgi için [ExpressRoute önkoşulları](expressroute-prerequisites.md) sayfası.

Bkz: [SSS sayfasını](expressroute-faqs.md) daha fazla desteklenen hizmetlerle ilgili bilgiler, maliyetleri ve yapılandırma ayrıntıları. Bkz: [ExpressRoute konumları](expressroute-locations.md) Microsoft eşleme desteği sunan bağlantı sağlayıcılarının listesi hakkında bilgi için sayfa.

### <a name="publicpeering"></a>Azure ortak eşleme (yeni bağlantı hatları için kullanım dışı)

> [!Note]
> Azure genel eşleme, her BGP oturumuyla ilişkili 1 NAT IP adresine sahiptir. 2 ' den fazla NAT IP adresi için Microsoft eşlemesi ' ne geçin. Microsoft eşleme, kendi NAT ayırmalarınızı yapılandırmanıza ve seçmeli ön ek tanıtımları için yol filtrelerini kullanmanıza olanak sağlar. Daha fazla bilgi için bkz. [Microsoft eşlemesi 'Ne taşıma](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

Azure depolama, SQL veritabanları ve Web siteleri gibi hizmetleri genel IP adreslerinde sunulur. Özel VIP'ler genel eşleme Yönlendirme etki alanı aracılığıyla, bulut hizmetleri de dahil olmak üzere genel IP adreslerinde barındırılan hizmetler bağlanabilirsiniz. ÇEVRE ağınız için genel eşleme etki bağlama ve tüm Azure hizmetlerinde kendi genel IP adresleri için Internet üzerinden bağlanmak zorunda kalmadan WAN'ınızdan bağlanın.

Bağlantı, her zaman Microsoft Azure Hizmetleri için WAN'ınızdan başlatılır. Microsoft Azure Hizmetleri, ağınızda bu yönlendirme etki alanı aracılığıyla yönelik bağlantıları başlatmasını mümkün olmayacaktır. Genel eşdüzey hizmet sağlama etkinleştirildikten sonra tüm Azure hizmetlerine bağlanabilirsiniz. Biz size yolları tanıtmak Hizmetleri seçmeli olarak çekmek izin vermiyor.

Yalnızca gereksinim duyduğunuz yolları kullanmak için ağınızdaki özel yol filtreleri tanımlayabilirsiniz. Başvurmak [yönlendirme](expressroute-routing.md) sayfasına yönlendirme yapılandırması hakkında ayrıntılı bilgi için.

Desteklenen genel eşleme Yönlendirme etki alanı hizmetleri hakkında daha fazla bilgi için bkz. [SSS](expressroute-faqs.md).

## <a name="peeringcompare"></a>Eşleme karşılaştırma

Aşağıdaki tabloda, üç eşlemenin karşılaştırılır:

|  | **Özel eşdüzey hizmet sağlama** | **Microsoft eşlemesi** |  **Genel eşleme** (yeni bağlantı hatları için kullanım dışı) |
| --- | --- | --- | --- |
| **Maks. eşleme başına desteklenen # ön ekleri** |Varsayılan olarak, ExpressRoute Premium ile 10.000 4000 |200 |200 |
| **Desteklenen IP adresi aralıkları** |WAN içinde geçerli bir IP adresi. |Siz veya bağlantı sağlayıcınızdan ait genel IP adresleri. |Siz veya bağlantı sağlayıcınızdan ait genel IP adresleri. |
| **Gereksinim sayısı olarak** |Özel ve ortak AS numaraları. Ortak sahip olmanız kullanmayı seçerseniz bir sayı olarak. |Özel ve ortak AS numaraları. Ancak, genel IP adresleri sahipliğini kanıtlamaları gerekir. |Özel ve ortak AS numaraları. Ancak, genel IP adresleri sahipliğini kanıtlamaları gerekir. |
| **Desteklenen IP protokolleri**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Yönlendirme arabirimi IP adresleri** |RFC1918 ve genel IP adresleri |Yönlendirme kayıt defterleri için kaydettiğiniz genel IP adresleri. |Yönlendirme kayıt defterleri için kaydettiğiniz genel IP adresleri. |
| **MD5 karma destek** |Yes |Yes |Yes |

ExpressRoute bağlantı hattınızın parçası olarak, bir veya daha fazla Yönlendirme etki alanları sağlayabilir. Bunları tek bir yönlendirme etki alanına birleştirmek istiyorsanız aynı VPN'yi put tüm Yönlendirme etki alanlarını seçebilirsiniz. Sizin de bunları farklı yönlendirme etki alanları, diyagrama benzer yerleştirebilirsiniz. Önerilen yapılandırma özel eşdüzey hizmet sağlama doğrudan çekirdek ağa bağlı ve genel ve Microsoft eşleme bağlantıları için DMZ'NİZDE bağlı olduğu.

Her eşleme ayrı BGP oturumları (her eşleme türü için bir çift) gerektirir. BGP oturumu çiftleri yüksek oranda kullanılabilir bir bağlantı sağlar. Katman 2 bağlantı sağlayıcıları bağlanılıyorsa, yapılandırma ve yönlendirme yönetmek için sorumlu olursunuz. İnceleyerek daha fazla bilgi [iş akışları](expressroute-workflows.md) ExpressRoute ' ayarlamak için.

## <a name="health"></a>ExpressRoute durumu

ExpressRoute bağlantı hatları izlenen kullanılabilirlik, sanal ağlar ve bant genişliği kullanımı kullanarak bağlantısını için [Ağ Performansı İzleyicisi](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

NPM Azure özel eşleme ve Microsoft eşlemesi izler. Kullanıma sunduğumuz [sonrası](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) daha fazla bilgi için.

## <a name="next-steps"></a>Sonraki adımlar

* Bir hizmet sağlayıcı bulun. Bkz: [ExpressRoute hizmet sağlayıcıları ve konumları](expressroute-locations.md).
* Tüm önkoşulların sağlandığından emin olun. Bkz. [ExpressRoute önkoşulları](expressroute-prerequisites.md).
* ExpressRoute bağlantınızı yapılandırın.
  * [ExpressRoute devreleri oluşturup yönetme](expressroute-howto-circuit-portal-resource-manager.md)
  * [ExpressRoute işlem hatları için yönlendirmeyi (eşleme) yapılandırma](expressroute-howto-routing-portal-resource-manager.md)

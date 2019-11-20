---
title: 'Azure ExpressRoute: QoS gereksinimleri'
description: Bu sayfa, yapılandırma ve hizmet kalitesi yönetmeye yönelik ayrıntılı gereksinimler sağlanmıştır. Skype kurumsal iş/ses Hizmetleri ele alınmıştır.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.openlocfilehash: debc5d91478d0a5c3cc16c7b09f5713ba09b467e
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080094"
---
# <a name="expressroute-qos-requirements"></a>ExpressRoute QoS gereksinimleri
Skype Kurumsal’da farklı QoS davranışları gerektiren çeşitli iş yükleri vardır. ExpressRoute aracılığıyla ses hizmetleri kullanmayı planlıyorsanız, aşağıda açıklanan gereksinimlere uymanız gerekir.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS gereksinimleri yalnızca Microsoft eşlemeleri için geçerlidir. Azure ortak eşleme ve Azure özel eşlemesinde alınan ağ trafiğinizdeki DSCP değerleri 0’a ayarlanacaktır. 
> 
> 

Aşağıdaki tabloda, Microsoft ekipleri ve Skype Kurumsal tarafından kullanılan DSCP işaretlerinin bir listesi verilmiştir. Daha fazla bilgi için [Skype Kurumsal için QoS’i yönetme](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) bölümüne başvurun.

| **Trafik Sınıfı** | **İşleme (DSCP İşaretleme)** | **Microsoft ekipleri ve Skype Kurumsal iş yükleri** |
| --- | --- | --- |
| **Ses** |EF (46) |Skype/Microsoft ekipleri/Lync Voice |
| **Etkileşimli** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Uygulama paylaşımı | 
| **Varsayılan** |AF11 (10) |Dosya aktarımı |
| |CS0 (0) |Diğer |

* İş yükleri sınıflandırmanız ve doğru DSCP değerlerini işaretlemeniz gerekir. Ağınızda DSCP işaretlerini ayarlamak için [burada](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) sağlanan yönergeleri izleyin.
* Ağınızda çoklu QoS kuyruklarını yapılandırmanız ve desteklemeniz gerekir. Ses tek başına bir sınıf olmalıdır ve [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt)' de belirtilen EF düzeltmesini almalıdır. 
* Kuyruğa alma mekanizması, sıkışma algılama ilkesi ve trafik sınıfı başına bant genişliği ayırmayı siz belirleyebilirsiniz. Ancak, Skype Kurumsal iş yükleri için DSCP işaretlemesi korunmalıdır. AF31 (26) gibi yukarıda listelenmeyen DSCP işaretlerini kullanıyorsanız, paketi Microsoft'a göndermeden önce bu DSCP değerinin 0 olarak yeniden yazılması gerekir. Microsoft yalnızca yukarıdaki tabloda gösterilen DSCP değeriyle işaretlenen paketleri gönderir. 

## <a name="next-steps"></a>Sonraki adımlar
* [Yönlendirme](expressroute-routing.md) ve [NAT](expressroute-nat.md) için gereksinimlere bakın.
* ExpressRoute bağlantınızı yapılandırmak için aşağıdaki bağlantılara bakın.
  
  * [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-classic.md)
  * [Yönlendirmeyi yapılandırma](expressroute-howto-routing-classic.md)
  * [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-classic.md)


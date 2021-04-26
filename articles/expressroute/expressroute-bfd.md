---
title: "Azure ExpressRoute: BFD 'yi yapılandırma"
description: Bu makale, bir ExpressRoute devresine ait özel eşleme üzerinde BFD (çift yönlü Iletim algılama) yapılandırma hakkında yönergeler sağlar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511272"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute üzerinde BFD 'yi yapılandırma

ExpressRoute, hem özel hem de Microsoft eşlemesi üzerinden çift yönlü Iletme algılamayı (BFD) destekler. ExpressRoute üzerinden BFD 'yi etkinleştirdiğinizde, Microsoft Enterprise Edge (MSEE) cihazları ile ExpressRoute devrenizin yapılandırdığı yönlendiriciler (CE/PE) arasındaki bağlantı hatası algılamayı hızlandıracaksınız. ExpressRoute 'u Edge yönlendirme cihazlarınız veya Iş ortağı Edge yönlendirme cihazlarınız (yönetilen katman 3 bağlantı hizmeti ile karşılaşırsanız) üzerinde yapılandırabilirsiniz. Bu belge BFD gereksinimi boyunca size yol gösterir ve ExpressRoute üzerinde BFD 'yi nasıl etkinleştireceğinizi açıklar.

## <a name="need-for-bfd"></a>BFD gereksinimi

Aşağıdaki diyagramda, ExpressRoute bağlantı hattı üzerinde BFD 'yi etkinleştirme avantajı gösterilmektedir: [![1]][1]

ExpressRoute devresini katman 2 bağlantıları veya yönetilen katman 3 bağlantılarıyla etkinleştirebilirsiniz. Her iki durumda da ExpressRoute bağlantı yolunda birden fazla katman 2 cihaz varsa, yoldaki bağlantı hatalarının algılanmasının sorumluluğu, çok sayıda BGP oturumunda yer alır.

MSEE cihazlarında BGP Keep-canlı ve bekleme zamanı genellikle sırasıyla 60 ve 180 saniye olarak yapılandırılır. Bir bağlantı hatası oluştuğunda, bağlantı başarısızlığını tespit etmek ve diğer bağlantıya trafik değiştirmek üç dakikaya kadar sürebilir.

Uç eşleme cihazınızda daha düşük bir BGP canlı tutmayı ve basılı tutmayı yapılandırarak BGP zamanlayıcılarını kontrol edebilirsiniz. BGP zamanlayıcılar iki eşleme cihazı arasında aynı değilse BGP oturumu, daha düşük saat değerini kullanarak oluşturulur. BGP etkin tut, en az üç saniye, en fazla 10 saniye olarak bir bekleme süresi ayarlanabilir. Ancak, protokol işlem yoğunluğu olduğu için çok ısrarlı bir BGP zamanlayıcısını ayarlama önerilmez.

Bu senaryoda BFD yardımcı olabilir. BFD, alt/saniye bir zaman aralığında düşük ek yük hata algılaması sağlar. 


## <a name="enabling-bfd"></a>BFD 'yi etkinleştirme

BFD, tüm yeni oluşturulan ExpressRoute özel eşleme arabirimlerini MSEE altında varsayılan olarak yapılandırılır. Bu nedenle, BFD 'yi etkinleştirmek için yalnızca hem birincil hem de ikincil cihazlarınızda BFD 'yi yapılandırmanız gerekir. BFD 'yi yapılandırmak iki adımlı bir işlemdir. Arabirimdeki BFD 'yi yapılandırıp BGP oturumuna bağlarsınız.

Örnek bir CE/PE (Cisco IOS XE kullanılarak) yapılandırma aşağıda gösterilmiştir. 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>Zaten var olan bir özel eşleme altında BFD 'yi etkinleştirmek için; eşlemeyi sıfırlamanız gerekir. Bkz. [ExpressRoute eşayarlarını sıfırlama][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD Zamanlayıcı anlaşması

BFD eşleri arasında iki eşin daha yavaş olması iletim hızını belirlemektir. MSEE BFD iletim/alma aralıkları 300 milisaniyeye ayarlanır. Belirli senaryolarda, Aralık 750 milisaniyelik daha yüksek bir değerde ayarlanabilir. Daha yüksek bir değer yapılandırarak, bu aralıkların daha uzun olmasına zorlayabilir ancak daha kısa hale getirmek mümkün değildir.

>[!NOTE]
>Coğrafi olarak yedekli ExpressRoute devreleri yapılandırdıysanız veya yedekleme olarak siteden siteye IPSec VPN bağlantısı kullanıyorsanız. BFD 'nin etkinleştirilmesi ExpressRoute bağlantı hatasından sonra hızlı bir şekilde yük devretmeye yardımcı olur. 
>

## <a name="next-steps"></a>Sonraki Adımlar

Daha fazla bilgi veya yardım için aşağıdaki bağlantıları inceleyin:

- [ExpressRoute bağlantı hattını oluşturma ve değiştirme][CreateCircuit]
- [Bir ExpressRoute bağlantı hattı için yönlendirmeyi oluşturma ve değiştirme][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD bağlantı hatası kesinti süresi"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md
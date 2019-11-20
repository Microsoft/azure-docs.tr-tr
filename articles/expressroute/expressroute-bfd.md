---
title: "Azure ExpressRoute: BFD 'yi yapılandırma"
description: Bu makalede, özel eşleme üzerinden ExpressRoute bağlantı hattının BFD (iletme çift yönlü algılama) yapılandırma hakkında yönergeler sağlar.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 608b5e0011d4ed656ff61fec84a23f2fb22373b3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080791"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute üzerinden BFD yapılandırın

ExpressRoute, hem özel hem de Microsoft eşlemesi üzerinden çift yönlü Iletme algılamayı (BFD) destekler. ExpressRoute üzerinde BFD 'yi etkinleştirerek, Microsoft Enterprise Edge (MSEE) cihazları ile ExpressRoute devresini (CE/PE) sonlandıran yönlendiriciler arasında bağlantı hatası algılamayı yönlendirebilirsiniz. (Yönetilen Katman 3 bağlantısı hizmetiyle gittiyse) müşteri Edge'i yönlendirme cihazları veya iş ortağı uç yönlendirme cihazları üzerinden ExpressRoute sonlandırabilirsiniz. Bu belge ihtiyacını BFD ve ExpressRoute üzerinden BFD etkinleştirme konusunda size kılavuzluk eder.

## <a name="need-for-bfd"></a>BFD gereksinimini

ExpressRoute bağlantı hattı üzerinden BFD etkinleştirme avantajı Aşağıdaki diyagramda gösterilmiştir: [ ![1]][1]

Yönetilen Katman 3 bağlantılarını veya ExpressRoute bağlantı hattı Katman 2 bağlantıları ya da etkinleştirebilirsiniz. ExpressRoute bağlantı yolunda bir veya daha fazla katman 2 cihazlar varsa her iki durumda da, üstteki BGP ile bağlantı hataları algılama yolu sorumluluğunu arasındadır.

MSEE cihazlarda BGP keepalive ve durma süresini genellikle 60-180 saniye sırasıyla yapılandırılır. Bu nedenle, en fazla götürecek bir bağlantı hatası algılamak için üç dakika bağlantı hatası ve alternatif bağlantı trafiğini geçin.

Müşteri sınır eşleme cihazı daha düşük BGP keepalive ve durma süresini yapılandırarak BGP zamanlayıcılar kontrol edebilirsiniz. İki eşleme cihazlar arasında BGP zamanlayıcılar eşleşirse, eşler arasında BGP oturumu alt zamanlayıcı değeri kullanırsınız. BGP keepalive üç saniye ve onlarca saniye sırasına göre Durma süresini olabildiğince düşük olarak ayarlanabilir. Ancak, protokol işlem yoğun olduğundan BGP zamanlayıcıları kararlılığı daha az tercih edilir.

Bu senaryoda BFD yardımcı olabilir. BFD subsecond zaman aralığındaki düşük ek yük bağlantı hatası algılama sağlar. 


## <a name="enabling-bfd"></a>BFD etkinleştirme

Varsayılan olarak tüm yeni oluşturulan ExpressRoute özel eşlemesi arabirimlerde Msee'ler altında BFD yapılandırılır. Bu nedenle BFD 'yi etkinleştirmek için, her ikisi de her ikisi de (hem birincil hem de ikincil cihazlarınızda) BFD 'yi yapılandırmanız gerekir. BFD 'nin yapılandırması iki adımlı bir işlemdir: arabirimdeki BFD 'yi yapılandırmanız ve ardından BGP oturumuna bağlamanız gerekir.

Örnek bir CE/PE (Cisco IOS XE kullanılarak) yapılandırma aşağıda gösterilmiştir. 

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

>[!NOTE]
>Bir mevcut özel eşdüzey hizmet sağlama altında BFD etkinleştirmek için; eşleme sıfırlamak gerekir. Bkz. [ExpressRoute eşayarlarını sıfırlama][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD Zamanlayıcı anlaşması

BFD eşleri arasında iki eş daha yavaş aktarım hızını belirler. Msee BFD aktarım/alma aralıkları 300 milisaniye olarak ayarlanır. Belirli senaryolarda aralığı 750 milisaniye olarak daha yüksek bir değer ayarlanabilir. Yüksek değerler yapılandırarak, daha uzun olacak şekilde Bu aralıklar zorunlu kılabilirsiniz; Ancak, daha kısa değil.

>[!NOTE]
>Coğrafi olarak yedekli ExpressRoute devreleri yapılandırdıysanız veya yedekleme olarak siteden siteye IPSec VPN bağlantısı kullanıyorsanız; BFD 'nin etkinleştirilmesi ExpressRoute bağlantı hatasından sonra hızlı bir şekilde yük devretmeye yardımcı olur. 
>

## <a name="next-steps"></a>Sonraki Adımlar

Daha fazla bilgi veya Yardım için aşağıdaki bağlantıları kontrol edin:

- [ExpressRoute bağlantı hattı oluşturma ve değiştirme][CreateCircuit]
- [ExpressRoute devresi için yönlendirme oluşturma ve değiştirme][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD hızlandıran bağlantı hatası kesinti süresi"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering







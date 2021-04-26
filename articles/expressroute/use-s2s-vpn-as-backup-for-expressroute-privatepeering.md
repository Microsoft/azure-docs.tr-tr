---
title: S2S VPN 'i Azure ExpressRoute özel eşlemesi için yedekleme olarak kullanma | Microsoft Docs
description: Bu sayfa, S2S VPN ile Azure ExpressRoute özel eşlemesini yedeklemeye yönelik mimari öneriler sağlar.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207929"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>ExpressRoute özel eşlemesi için S2S VPN 'i yedekleme olarak kullanma

[ExpressRoute özel eşlemesi ile olağanüstü durum kurtarma Için tasarlama][DR-PP]başlıklı makalede, bir ExpressRoute özel eşleme bağlantısı için yedekleme bağlantısı çözümü gereksinimi ve coğrafi olarak yedekli ExpressRoute devrelerinin amaç için nasıl kullanılacağı ele alınmıştır. Bu makalede, bir ExpressRoute özel eşlemesi için bir yedekleme olarak siteden siteye (S2S) VPN 'yi nasıl kullanacağınızı ve koruyacağınızı düşünmemize izin verin. 

Coğrafi olarak yedekli ExpressRoute devrelerinin aksine, ExpressRoute-VPN olağanüstü durum kurtarma birleşimini yalnızca etkin-pasif modda kullanabilirsiniz. Pasif modda yedek ağ bağlantısı kullanmanın önemli bir zorluğu, pasif bağlantının genellikle birincil bağlantıyla birlikte başarısız olmasına neden olur. Pasif bağlantı hatalarının yaygın nedenlerinden biri etkin bakım olmamasıdır. Bu nedenle, bu makalede bir ExpressRoute özel eşlemesini yedekleyen S2S VPN bağlantısını doğrulamaya ve etkin bir şekilde korumaya odaklanalım.

>[!NOTE] 
>Belirli bir yol ExpressRoute ve VPN aracılığıyla tanıtıldığında Azure, ExpressRoute üzerinden yönlendirmeyi tercih eder.  
>

Bu makalede, hem Azure perspektifinden hem de müşteri tarafı ağ kenarı perspektifinden bağlantıyı nasıl doğrulayabildiğinizi görelim. Bunlardan herhangi birini doğrulama özelliği, Microsoft ağ varlıklarıyla eş olan müşteri tarafı ağ cihazlarını yönetip yönetmeyeceğinizi bağımsız olarak size yardımcı olur. 

## <a name="example-topology"></a>Örnek topoloji

Kurulumumuzda, bir ExpressRoute bağlantı hattı ve bir S2S VPN bağlantısı aracılığıyla Azure hub VNet 'e bağlı bir şirket içi ağa sahipsiniz. Azure hub VNet, aşağıdaki diyagramda gösterildiği gibi bir bağlı olan VNet 'e sırayla depolanır:

![1][1]

Kurulumda ExpressRoute bağlantı hattı, şirket içi bir "müşteri Edge" (CE) yönlendiricileri çiftinde sonlandırılır. Şirket içi LAN, lider modunda çalışan bir güvenlik duvarı çifti üzerinden CE yönlendiricilerine bağlanır. S2S VPN, güvenlik duvarları üzerinde doğrudan sonlandırılır.

Aşağıdaki tabloda, topolojinin anahtar IP önekleri listelenmektedir:

| **Varlık** | **Ön ek** |
| --- | --- |
| Şirket içi LAN | 10.1.11.0/25 |
| Azure hub VNet | 10.17.11.0/25 |
| Azure sanal ağ VNet | 10.17.11.128/26 |
| Şirket içi test sunucusu | 10.1.11.10 |
| Bağlı ağ VNet test sanal makinesi | 10.17.11.132 |
| ExpressRoute birincil bağlantı P2P alt ağı | 192.168.11.16/30 |
| ExpressRoute ikincil bağlantı P2P alt ağı | 192.168.11.20/30 |
| VPN Gateway birincil BGP eşi IP 'si | 10.17.11.76 |
| VPN Gateway ikincil BGP eşi IP 'si | 10.17.11.77 |
| Şirket içi güvenlik duvarı VPN BGP eş IP 'si | 192.168.11.88 |
| Birincil CE yönlendirici ı/f güvenlik duvarı IP 'si | 192.168.11.0/31 |
| Birincil CE yönlendirici IP 'ye yönelik güvenlik duvarı g/f | 192.168.11.1/31 |
| İkincil CE yönlendirici güvenlik duvarı IP 'si | 192.168.11.2/31 |
| İkincil CE yönlendirici IP 'ye yönelik güvenlik duvarı g/f | 192.168.11.3/31 |


Aşağıdaki tabloda topolojinin ASNs listelenmiştir:

| **Özerk sistem** | **ASN** |
| --- | --- |
| Şirket içi | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Sanal ağ GW (ExR) | 65515 |
| Sanal ağ GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Asymmetrity olmadan yüksek kullanılabilirlik

### <a name="configuring-for-high-availability"></a>Yüksek kullanılabilirlik için yapılandırma

[ExpressRoute ve siteden siteye birlikte mevcut bağlantıları yapılandırma][Conf-CoExist] , birlikte var olan ExpressRoute bağlantı HATTıNıN ve S2S VPN bağlantılarının nasıl yapılandırılacağını açıklar. [ExpressRoute ile yüksek kullanılabilirlik tasarlama konusunda][HA]anlatıldığı gibi, kurduğumuz ExpressRoute yüksek kullanılabilirliğini geliştirmek için, tüm uç noktalara kadar ağ yedekliliği (tek hata noktasını engeller) korur. Ayrıca, ExpressRoute devrelerinin birincil ve ikincil bağlantıları, şirket içi önekleri her iki bağlantıyla de aynı şekilde duyurarak etkin-etkin modda çalışacak şekilde yapılandırılmıştır. 

ExpressRoute devresinin birincil bağlantısı aracılığıyla birincil CE yönlendiricisinin şirket içi yol tanıtımı aşağıda gösterilmektedir (Junos komutları):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

ExpressRoute devresinin ikincil bağlantısı üzerinden ikincil CE yönlendiricisinin şirket içi yol tanıtımı aşağıda gösterilmektedir (Junos komutları):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Yedek bağlantının yüksek oranda kullanılabilirliğini artırmak için, S2S VPN etkin-etkin modda da yapılandırılır. Azure VPN ağ geçidi yapılandırması aşağıda gösterilmiştir. VPN yapılandırması VPN 'nin bir parçası olarak bkz. ağ geçidinin BGP eşi IP adresleri--10.17.11.76 ve 10.17.11.77--Ayrıca listelenir.

![2][2]

Şirket içi yol, VPN ağ geçidinin birincil ve ikincil BGP eşlerine güvenlik duvarları tarafından tanıtılabilir. Yol reklamları aşağıda gösterilmiştir (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>S2S VPN 'i etkin-etkin modda yapılandırmak, yalnızca olağanüstü durum kurtarma yedek ağ bağlantınız için yüksek kullanılabilirlik sağlar, ancak yedekleme bağlantısına daha yüksek performans sağlar. Diğer bir deyişle, etkin-etkin modda S2S VPN yapılandırması, birden çok temel tünel oluşturmayı zorgerektirdiğinden önerilir.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Simetrik trafik akışı için yapılandırma

Verilen şirket içi bir rota hem ExpressRoute hem de S2S VPN aracılığıyla tanıtıldığında Azure, ExpressRoute yolunu tercih edebiliyoruz. Azure 'un aynı ExpressRoute üzerinden S2S VPN yolunu tercih etmeye zorlamak için, VPN bağlantısı aracılığıyla daha fazla özel yol (daha büyük alt ağ maskesiyle daha uzun ön ek) duyurmanız gerekir. Buradaki amaç VPN bağlantılarını yalnızca yedekleme olarak kullanmaktır. Bu nedenle, Azure 'un varsayılan yol seçimi davranışı hedefimize göre çevrimiçi. 

Şirket içinden Azure 'a giden trafiğin aynı zamanda S2S VPN üzerinden ExpressRoute yolunu tercih ettiği bir sorumluluktur. Şirket içi kurulumumuzdaki CE yönlendiricilerinin ve güvenlik duvarlarının varsayılan yerel tercihi 100 ' dir. Bu nedenle, ExpressRoute özel eşayarları 100 ' den büyük olan yolların yerel tercihini yapılandırarak (150), Azure 'a hedeflenen trafiği, düzenli durumda ExpressRoute devresini tercih edebiliriz.

ExpressRoute devresinin birincil bağlantısını sonlandıran birincil CE yönlendiricisinin BGP yapılandırması aşağıda gösterilmiştir. IGP oturumu üzerinden tanıtılan yolların yerel tercihinin değeri 150 olacak şekilde yapılandırılır. Benzer şekilde, ExpressRoute bağlantı hattının ikincil bağlantısını sonlandıran ikincil CE yönlendiricisinin yerel tercihlerinin de 150 olarak yapılandırıldığından emin olunması gerekir.

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

Şirket içi güvenlik duvarları 'nin yönlendirme tablosu, Azure 'a gidecek şirket içi trafiğin, Azure 'a yönelik olarak tercih edilen yolda ExpressRoute üzerinden olduğunu doğrular (aşağıda gösterilmiştir).

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

Yukarıdaki yol tablosunda, hub ve bağlı bileşen VNet rotaları için--10.17.11.0/25 ve 10.17.11.128/26--VPN bağlantıları üzerinden ExpressRoute bağlantı hattı tercih ederiz. 192.168.11.0 ve 192.168.11.2, CE yönlendiricilerine yönelik güvenlik duvarı arabirimindeki IP 'lardır.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>S2S VPN üzerinden rota değişimi doğrulaması

Bu makalenin önceki kısımlarında, güvenlik duvarlarının şirket içi yol tanıtımını VPN ağ geçidinin birincil ve ikincil BGP eşleri için doğruladık. Ayrıca, VPN ağ geçidinin birincil ve ikincil BGP eşlerinden gelen güvenlik duvarları tarafından alınan Azure yollarını doğrulayalim.

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

Benzer şekilde, Azure VPN ağ geçidi tarafından alınan şirket içi ağ yolu öneklerini doğrulayalım. 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

Yukarıda görüldüğü gibi, VPN ağ geçidinin hem VPN ağ geçidinin birincil ve ikincil BGP eşleri tarafından alınan yolları vardır. Ayrıca birincil ve ikincil ExpressRoute bağlantıları aracılığıyla alınan yolların üzerinde görünebilirliği vardır (AS-Path 'e sahip olanlar 12076 ile sona erer). VPN bağlantıları aracılığıyla alınan yolları onaylamak için, bağlantıların şirket içi BGP eş IP 'sini bilmelidir. Göz önüne alındığında, 192.168.11.88, bundan alınan yolları görmemiz gerekir.

Daha sonra, Azure VPN ağ geçidi tarafından tanıtılan yolları şirket içi güvenlik duvarı BGP eşine (192.168.11.88) doğrulayalim.

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

Yol alışverişlerini görmemesi bağlantı hatasını gösteriyor. Bkz. [sorun giderme: Azure siteden sıteye VPN bağlantısı][VPN Troubleshoot] , VPN bağlantısının sorunlarını gidermeye yönelik yardım için bağlantı ve çalışmayı durduruyor.

## <a name="testing-failover"></a>Yük devretmeyi test etme

Artık VPN bağlantısı (denetim düzlemi) üzerinden başarılı yol alışverişlerini Onayladığımıza göre, ExpressRoute bağlantınızdan VPN bağlantısına trafik (veri düzlemi) geçirmek üzere ayarlandık. 

>[!NOTE] 
>Üretim ortamlarında yük devretme testi, zamanlanmış ağ bakımı çalışma penceresinde, hizmet karışıklığa yol açabileceği için yapılmalıdır.
>

Trafik anahtarını yapmadan önce, ayarımızda bulunan geçerli yolu, şirket içi test sunucusundan bağlı olan VNet 'teki test sanal makinesine yönlendirmenizi sağlar.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

Kurulumumuz birincil ve ikincil ExpressRoute noktadan noktaya bağlantı alt ağları sırasıyla, 192.168.11.16/30 ve 192.168.11.20/30 ' dur. Yukarıdaki izleme rotasında, adım 3 ' te, birincil MSEE 'in Arabirim IP 'si olan 192.168.11.18 'e ulaşdığımızda görüyoruz. MSEE arabirimi varlığı, geçerli yolumuz ExpressRoute 'un üzerinde olduğunu onaylar.

[ExpressRoute][RST]bağlantı hattı eşlerinin sıfırlanması sırasında bildirildiği gibi, ExpressRoute bağlantı hattının birincil ve ikincil eşlemesini devre dışı bırakmak için aşağıdaki PowerShell komutlarını kullanalım.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Yük devretme anahtarı süresi BGP yakınsama zamanına bağlıdır. Kurulumumuzda, yük devretme anahtarı birkaç saniye sürer (10 ' dan az). Anahtardan sonra, izleme yolu ' nu yinelemek aşağıdaki yolu gösterir:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

İzleme işlemi sonucu, S2S VPN aracılığıyla yedekleme bağlantısının etkin olduğunu doğrular ve hem birincil hem de ikincil ExpressRoute bağlantıları başarısız olursa hizmet devamlılığını sağlayabilir. Yük devretme testini gerçekleştirmek için aşağıdaki komut kümesini kullanarak ExpressRoute bağlantılarını geri etkinleştirip trafik akışını normalleştirin.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Trafiğin ExpressRoute 'a geri yüklendiğini doğrulamak için, izleme Oute öğesini tekrarlayın ve ExpressRoute özel eşlemeden devam edin.

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute, Microsoft ağı içinde tek hata noktası olmadan yüksek kullanılabilirlik için tasarlanmıştır. Hala bir ExpressRoute bağlantı hattı, tek bir coğrafi bölgeye ve bir hizmet sağlayıcısına göre sınırlandırlanır. S2S VPN, bir ExpressRoute devresine yönelik iyi bir olağanüstü durum kurtarma pasif yedekleme çözümüdür. Güvenilir bir pasif yedekleme bağlantısı çözümü için, pasif yapılandırmanın düzenli olarak bakımı ve dönemsel doğrulama doğrulaması için bağlantı önemlidir. VPN yapılandırmasının eski hale gelmesi ve düzenli aralıklarla (her üç ayda bir şekilde), bakım penceresi sırasında bu makalede açıklanan doğrulama ve yük devretme testi adımlarını tekrarlamasına yönelik önemli değildir.

VPN ağ geçidi ölçümlerine göre izleme ve uyarıları etkinleştirmek için bkz. [VPN Gateway ölçümleri üzerinde uyarıları ayarlama][VPN-alerts].

ExpressRoute hatasından sonra BGP yakınsama sürecini hızlandırmak için, [BFD 'Yi ExpressRoute üzerinden yapılandırın][BFD].

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "değerlendirme kapsamındaki topoloji"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW yapılandırması"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md
---
title: 'Azure ExpressRoute: Izleme, ölçümler ve uyarılar'
description: Bu sayfa ExpressRoute izleme hakkında bilgi sağlar.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 95083809f8fcfe42ae76faa0af507c7ce4acd709
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083425"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute izleme, ölçümler ve uyarılar

Bu makalede izleme, Ölçümler ve uyarılar Azure İzleyicisi'ni kullanarak ExpressRoute anlamanıza yardımcı olur. Azure İzleyici, uyarıları, tüm Azure tanılama günlükleri sorunlarınız için tüm ölçümleri tek sağlanır.
 
>[!NOTE]
>Kullanarak **Klasik ölçüm** önerilmez.
>

## <a name="expressroute-metrics"></a>ExpressRoute ölçümleri

**Ölçümleri**görüntülemek Için *Azure izleyici* sayfasına gidin ve *ölçümler*' e tıklayın. **ExpressRoute** ölçümlerini, kaynak türü *ExpressRoute devreleri*tarafından doldurulabilir şekilde görüntüleyin. **Global Reach** ölçümlerini görüntülemek Için, *ExpressRoute devreleri* kaynak türüne göre filtreleyin ve Global Reach etkin bir ExpressRoute bağlantı hattı kaynağı seçin. **ExpressRoute doğrudan** ölçümlerini görüntülemek Için kaynak türünü *ExpressRoute bağlantı noktalarıyla*filtreleyin. 

Ölçüm seçildikten sonra, varsayılan toplama uygulanır. İsteğe bağlı olarak, ölçüyü farklı boyutlara sahip olacak şekilde bölerek bölmeyi uygulayabilirsiniz.

### <a name="available-metrics"></a>Kullanılabilir ölçümler
|**Ölçüm**|**Kategori**|**Boyut (ler)**|**Özellik (ler)**|
| --- | --- | --- | --- |
|ARP kullanılabilirliği|Kullanılabilirlik|<ui><li>Eş (birincil/Ikincil ExpressRoute yönlendirici)</ui></li><ui><li> Eşleme türü (özel/genel/Microsoft)</ui></li>|ExpressRoute|
|BGP kullanılabilirliği|Kullanılabilirlik|<ui><li> Eş (birincil/Ikincil ExpressRoute yönlendirici)</ui></li><ui><li> Eşleme türü</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafik|<ui><li> Eşleme türü (ExpressRoute)</ui></li><ui><li>Bağlantı (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trafik| <ui><li>Eşleme türü (ExpressRoute)</ui></li><ui><li> Bağlantı (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Trafik|<ui><li>Eşlenen devre Skey (hizmet anahtarı)</ui></li>|Global Erişim|
|GlobalReachBitsOutPerSecond|Trafik|<ui><li>Eşlenen devre Skey (hizmet anahtarı)</ui></li>|Global Erişim|
|AdminState|Fiziksel bağlantı|Bağlantı|ExpressRoute Direct|
|LineProtocol|Fiziksel bağlantı|Bağlantı|ExpressRoute Direct|
|RxLightLevel|Fiziksel bağlantı|<ui><li>Bağlantı</ui></li><ui><li>Kulvar</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fiziksel bağlantı|<ui><li>Bağlantı</ui></li><ui><li>Kulvar</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* ve *GlobalGlobalReachBitsOutPerSecond* kullanımı yalnızca en az bir Global Reach bağlantısı kurulduysa görünür olur.
>

## <a name="circuits-metrics"></a>Devreler ölçümleri

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Tüm eşlemeler genelinde BITS Içi ve dışı ölçümler

Belirli bir ExpressRoute bağlantı hattındaki tüm eşlemelerde ölçümleri görüntüleyebilirsiniz.

![Bağlantı hattı ölçümleri](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Eşleme başına BITS gelen ve giden ölçümler

Özel, genel ve Microsoft bit/saniye içinde eşleme için ölçümleri görüntüleyebilir.

![Eşleme başına ölçümler](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP kullanılabilirliği-eşe göre bölme  

Eşleme ve eşler arasında BGP 'nin gerçek zamanlı kullanılabilirliğini (birincil ve Ikincil ExpressRoute yönlendiricileri) görüntüleyebilirsiniz. Bu panoda, özel eşleme için birincil BGP oturumu ve özel eşleme için Ikinci BGP oturumu gösterilmektedir. 

![Eş başına BGP kullanılabilirliği](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP kullanılabilirliği-eşlemeye göre bölme  

Eşler arası ve eşlerde [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) 'nin neredeyse gerçek zamanlı kullanılabilirliğini (birincil ve Ikincil ExpressRoute yönlendiricileri) görüntüleyebilirsiniz. Bu Pano, özel eşleme ARP oturumunu her iki eş arasında gösterir, ancak eşlemeler genelinde Microsoft eşlemesi için tamamlanmıştır. Varsayılan toplama (Ortalama) her iki eş üzerinde de kullanıldı.  

![Her eş için ARP kullanılabilirliği](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute doğrudan ölçümleri

### <a name="admin-state---split-by-link"></a>Yönetici durumu-bağlantıya göre Böl
ExpressRoute doğrudan bağlantı noktası çiftinin her bir bağlantısı için yönetici durumunu görüntüleyebilirsiniz.

![er doğrudan yönetici durumu](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Saniyedeki bit/saniye-bağlantıya göre Böl
Her iki ExpressRoute doğrudan bağlantı noktası çiftinin bağlantılarında, saniye başına bit sayısını görüntüleyebilirsiniz. 

![Saniyedeki doğrudan bit/saniye](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Saniye başına BITS-bağlantıya göre bölme
Ayrıca, ExpressRoute doğrudan bağlantı noktası çiftinin her iki bağlantı için de saniyede BITS görüntüleyebilirsiniz. 

![er saniye başına doğrudan BITS](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Satır Protokolü-bağlantıya göre Böl
Her ExpressRoute doğrudan bağlantı noktası çiftinin bağlantı genelinde hat protokolünü görüntüleyebilirsiniz.

![er doğrudan hat Protokolü](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>RX hafif düzeyi-bağlantıya göre bölme
Her bağlantı noktası için RX ışık düzeyini (ExpressRoute doğrudan bağlantı noktasının **aldığı**ışık düzeyi) görüntüleyebilirsiniz. Sağlıklı RX hafif düzeyler genellikle-10 ila 0 dBm aralığında yer almalıdır

![er doğrudan Line RX hafif düzeyi](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>TX hafif düzeyi-bağlantıya göre Böl
Her bağlantı noktası için, TX ışık düzeyini (ExpressRoute doğrudan bağlantı noktasının **Iletmekte**olan ışık düzeyi) görüntüleyebilirsiniz. Sağlıklı TX hafif düzeyleri genellikle-10 ila 0 dBm aralığında yer almalıdır

![er doğrudan Line RX hafif düzeyi](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute ağ geçidi bağlantıları bit/saniye

![Ağ Geçidi bağlantıları](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ağ geçidi bağlantıları için uyarılar

1. Uyarıları yapılandırmak için gidin **Azure İzleyici**, ardından **uyarılar**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Tıklayın **+ seçin hedef** ve ExpressRoute ağ geçidi bağlantı kaynağı seçin.

   ![target]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Uyarı ayrıntılarını tanımlayın.

   ![eylem grubu](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Tanımlamak ve eylem grubunu ekleyin.

   ![eylem grubu Ekle](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Her eşleme bağlı uyarılar

 ![ne](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Bağlantı hatları üzerinde etkinlik günlükleri için uyarıları yapılandırın

İçinde **Uyarı ölçütleri**, seçebileceğiniz **etkinlik günlüğü** sinyal türü ve sinyal seçin.

  ![başka bir](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute bağlantınızı yapılandırın.
  
  * [Bağlantı hattı oluşturma ve değiştirme](expressroute-howto-circuit-arm.md)
  * [Eşleme yapılandırması oluşturma ve değiştirme](expressroute-howto-routing-arm.md)
  * [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-arm.md)

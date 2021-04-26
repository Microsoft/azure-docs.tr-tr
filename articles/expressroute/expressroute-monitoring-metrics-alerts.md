---
title: 'Azure ExpressRoute: Izleme, ölçümler ve uyarılar'
description: Azure Izleyici 'yi kullanarak Azure ExpressRoute izleme, ölçümler ve uyarılar hakkında bilgi edinin. Azure 'da tüm ölçümler, uyarı, tanılama günlükleri için bir durdurma.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725791"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute izleme, ölçümler ve uyarılar

Bu makale, Azure Izleyici kullanarak ExpressRoute izlemeyi, ölçümleri ve uyarıları anlamanıza yardımcı olur. Azure Izleyici, tüm Azure genelinde tüm ölçümler, uyarılar ve tanılama günlükleri için bir dur.
 
>[!NOTE]
>**Klasik ölçümlerin** kullanılması önerilmez.
>

## <a name="expressroute-metrics"></a>ExpressRoute ölçümleri

**Ölçümleri** görüntülemek Için *Azure izleyici* sayfasına gidin ve *ölçümler*' i seçin. **ExpressRoute** ölçümlerini görüntülemek Için, *ExpressRoute devreleri* kaynak türüne göre filtreleyin. **Global Reach** ölçümlerini görüntülemek Için, *ExpressRoute devreleri* kaynak türüne göre filtreleyin ve Global Reach etkin bir ExpressRoute bağlantı hattı kaynağı seçin. **ExpressRoute doğrudan** ölçümlerini görüntülemek Için kaynak türünü *ExpressRoute bağlantı noktalarıyla* filtreleyin. 

Ölçüm seçildikten sonra, varsayılan toplama uygulanır. İsteğe bağlı olarak, ölçüyü farklı boyutlara sahip olacak şekilde bölerek bölmeyi uygulayabilirsiniz.

### <a name="aggregation-types"></a>Toplama türleri:

Ölçüm Gezgini SUM, MAX, MIN, AVG ve COUNT sayısını [toplama türleri](../azure-monitor/essentials/metrics-charts.md#aggregation)olarak destekler. Her bir ExpressRoute ölçümü için öngörüleri gözden geçirirken önerilen toplama türünü kullanmanız gerekir.

* Sum: toplama aralığı boyunca yakalanan tüm değerlerin toplamı. 
* Sayı: toplama aralığı sırasında yakalanan ölçüm sayısı. 
* Average: toplama aralığı sırasında yakalanan ölçüm değerlerinin ortalaması. 
* Min: toplama aralığı sırasında yakalanan en küçük değer. 
* Max: toplama aralığı sırasında yakalanan en büyük değer. 

### <a name="available-metrics"></a>Kullanılabilir ölçümler

|**Ölçüm**|**Kategori**|**Boyut (ler)**|**Özellik (ler)**|
| --- | --- | --- | --- |
|ARP kullanılabilirliği|Kullanılabilirlik|<ui><li>Eş (birincil/Ikincil ExpressRoute yönlendirici)</ui></li><ui><li> Eşleme türü (özel/genel/Microsoft)</ui></li>|ExpressRoute|
|BGP kullanılabilirliği|Kullanılabilirlik|<ui><li> Eş (birincil/Ikincil ExpressRoute yönlendirici)</ui></li><ui><li> Eşleme türü</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafik|<ui><li> Eşleme türü (ExpressRoute)</ui></li><ui><li>Bağlantı (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute ağ geçidi bağlantısı</ui></li>|
|BitsOutPerSecond|Trafik| <ui><li>Eşleme türü (ExpressRoute)</ui></li><ui><li> Bağlantı (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute ağ geçidi bağlantısı</ui></li>|
|CPU Kullanımı|Performans| <ui><li>Örnek</ui></li>|ExpressRoute sanal ağ geçidi|
|Paket/saniye|Performans| <ui><li>Örnek</ui></li>|ExpressRoute sanal ağ geçidi|
|Eşe tanıtılan yolların sayısı |Kullanılabilirlik| <ui><li>Örnek</ui></li>|ExpressRoute sanal ağ geçidi|
|Eşten öğrenilen yolların sayısı |Kullanılabilirlik| <ui><li>Örnek</ui></li>|ExpressRoute sanal ağ geçidi|
|Yolların sıklığı değişikliği |Kullanılabilirlik| <ui><li>Örnek</ui></li>|ExpressRoute sanal ağ geçidi|
|Sanal ağdaki sanal makinelerin sayısı |Kullanılabilirlik| Yok |ExpressRoute sanal ağ geçidi|
|GlobalReachBitsInPerSecond|Trafik|<ui><li>Eşlenen devre Skey (hizmet anahtarı)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Trafik|<ui><li>Eşlenen devre Skey (hizmet anahtarı)</ui></li>|Global Reach|
|AdminState|Fiziksel bağlantı|Bağlantı|ExpressRoute Direct|
|LineProtocol|Fiziksel bağlantı|Bağlantı|ExpressRoute Direct|
|RxLightLevel|Fiziksel bağlantı|<ui><li>Bağlantısının</ui></li><ui><li>Ödül</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fiziksel bağlantı|<ui><li>Bağlantısının</ui></li><ui><li>Ödül</ui></li>|ExpressRoute Direct|
>[!NOTE]
>*GlobalGlobalReachBitsInPerSecond* ve *GlobalGlobalReachBitsOutPerSecond* kullanımı yalnızca en az bir Global Reach bağlantısı kurulduysa görünür olur.
>

## <a name="circuits-metrics"></a>Devreler ölçümleri

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Tüm eşlemeler genelinde BITS Içi ve dışı ölçümler

Toplama türü: *Ort*

Belirli bir ExpressRoute bağlantı hattındaki tüm eşlemelerde ölçümleri görüntüleyebilirsiniz.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="devre ölçümleri":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Eşleme başına BITS gelen ve giden ölçümler

Toplama türü: *Ort*

Özel, genel ve Microsoft eşlemesi için ölçümleri bit/saniye cinsinden görüntüleyebilirsiniz.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="eşleme başına ölçümler":::

### <a name="bgp-availability---split-by-peer"></a>BGP kullanılabilirliği-eşe göre bölme  

Toplama türü: *Ort*

Eşleme ve eşler (birincil ve Ikincil ExpressRoute yönlendiricileri) genelinde BGP 'nin (katman 3 bağlantısı) neredeyse gerçek zamanlı kullanılabilirliğini görüntüleyebilirsiniz. Bu Pano, birincil BGP oturum durumunun özel eşleme için olduğunu ve Ikinci BGP oturum durumunun özel eşleme için doğru olduğunu gösterir. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Eş başına BGP kullanılabilirliği":::

### <a name="arp-availability---split-by-peering"></a>ARP kullanılabilirliği-eşlemeye göre bölme  

Toplama türü: *Ort*

Eşlemeler ve eşler (birincil ve Ikincil ExpressRoute yönlendiricileri) genelinde [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (katman 3 bağlantısı) için neredeyse gerçek zamanlı kullanılabilirliği görüntüleyebilirsiniz. Bu Pano, özel eşleme ARP oturum durumunun her iki eş arasında olduğunu gösterir, ancak her iki eş için de Microsoft eşlemesi için aşağı doğru. Varsayılan toplama (Ortalama) her iki eş üzerinde de kullanıldı.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Her eş için ARP kullanılabilirliği":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute doğrudan ölçümleri

### <a name="admin-state---split-by-link"></a>Yönetici durumu-bağlantıya göre Böl

Toplama türü: *Ort*

ExpressRoute doğrudan bağlantı noktası çiftinin her bir bağlantısı için yönetici durumunu görüntüleyebilirsiniz. Yönetici durumu, fiziksel bağlantı noktasının açık veya kapalı olduğunu temsil eder. Bu durum, trafiği ExpressRoute doğrudan bağlantısı üzerinden geçirmek için gereklidir.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER doğrudan yönetici durumu":::

### <a name="bits-in-per-second---split-by-link"></a>Saniyedeki bit/saniye-bağlantıya göre Böl

Toplama türü: *Ort*

Her iki ExpressRoute doğrudan bağlantı noktası çiftinin bağlantılarında, saniye başına bit sayısını görüntüleyebilirsiniz. Her iki bağlantı için de gelen bant genişliğini karşılaştırmak üzere bu panoyu izleyin.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Saniyedeki doğrudan bit/saniye":::

### <a name="bits-out-per-second---split-by-link"></a>Saniye başına BITS-bağlantıya göre bölme

Toplama türü: *Ort*

Ayrıca, ExpressRoute doğrudan bağlantı noktası çiftinin her iki bağlantı için de saniyede BITS görüntüleyebilirsiniz. Her iki bağlantı için giden bant genişliğini karşılaştırmak üzere bu panoyu izleyin.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER saniye başına doğrudan BITS":::

### <a name="line-protocol---split-by-link"></a>Satır Protokolü-bağlantıya göre Böl

Toplama türü: *Ort*

Her ExpressRoute doğrudan bağlantı noktası çiftinin bağlantı genelinde hat protokolünü görüntüleyebilirsiniz. Line Protocol, fiziksel bağlantının ExpressRoute Direct üzerinden çalışır ve çalışır duruma geçtiğini gösterir. Bu panoyu izleyin ve fiziksel bağlantının ne zaman sona yazıldığını bildirmek için uyarıları ayarlayın.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER doğrudan hat Protokolü":::

### <a name="rx-light-level---split-by-link"></a>RX hafif düzeyi-bağlantıya göre bölme

Toplama türü: *Ort*

Her bağlantı noktası için RX ışık düzeyini (ExpressRoute doğrudan bağlantı noktasının **aldığı** ışık düzeyi) görüntüleyebilirsiniz. Sağlıklı RX hafif düzeyleri genellikle-10 dBm aralığında 0 dBm arasında düşmelidir. RX hafif düzeyi sağlıklı aralığın dışında kalırsa uyarıları bildirilecek şekilde ayarlayın.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER doğrudan Line RX hafif düzeyi":::

### <a name="tx-light-level---split-by-link"></a>TX hafif düzeyi-bağlantıya göre Böl

Toplama türü: *Ort*

Her bağlantı noktası için, TX ışık düzeyini (ExpressRoute doğrudan bağlantı noktasının **Iletmekte** olan ışık düzeyi) görüntüleyebilirsiniz. Sağlıklı TX hafif düzeyleri genellikle 10 ila 0 dBm arasında bir Aralık içinde düşmelidir. TX ışık düzeyi sağlıklı aralığın dışında kalırsa uyarıları bildirilecek şekilde ayarlayın.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER doğrudan hat TX hafif düzeyi":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute sanal ağ geçidi ölçümleri

Toplama türü: *Ort*

ExpressRoute ağ geçidini dağıtırken Azure, ağ geçidinizin işlem ve işlevlerini yönetir. Ağ geçidinizin performansını daha iyi anlamak için kullanabileceğiniz altı ağ geçidi ölçümü bulunmaktadır:

* CPU Kullanımı
* Paket/saniye
* Eşlere tanıtılan yolların sayısı
* Eşlerden öğrenilen yolların sayısı
* Yolların sıklığı değişti
* Sanal ağdaki sanal makinelerin sayısı  

Bu ölçümlerin her biri için uyarıları ayarlamanız önemle önerilir, böylece ağ geçidinizin performans sorunlarını görmekte dikkat etmeniz gerekir.

### <a name="cpu-utilization---split-instance"></a>CPU kullanımı-bölünmüş örnek

Toplama türü: *Ort*

Her ağ geçidi örneğinin CPU kullanımını görüntüleyebilirsiniz. CPU kullanımı, rutin ana bilgisayar bakımı sırasında kısa bir süre içinde bulunabilir, ancak uzun yüksek CPU kullanımı, ağ geçidinizin performans sorununa ulaşmakta olduğunu gösterebilir. ExpressRoute ağ geçidinin boyutunu artırmak bu sorunu çözebilir. CPU kullanımının belirli bir eşiği ne sıklıkta aşmadığını bildiren bir uyarı ayarlayın.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU kullanımı-bölünmüş ölçümlerin ekran görüntüsü.":::

### <a name="packets-per-second---split-by-instance"></a>Saniyedeki paket sayısı-örneğe göre Böl

Toplama türü: *Ort*

Bu ölçüm, ExpressRoute ağ geçidine geçiş yapan gelen paketlerin sayısını yakalar. Ağ geçidinizin şirket içi ağınızdan trafik alıyorsa, burada tutarlı bir veri akışı görmeniz beklenir. Saniyedeki paket sayısı, ağ geçidinizin artık trafik almadığını belirten bir eşiğin altına düştüğünde bir uyarı ayarlayın.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Saniye başına düşen ölçüm için paketlerin ekran görüntüsü.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Örneğe göre eş bölünmüş olarak tanıtılan yolların sayısı

Toplama türü: *sayı*

Bu ölçüm, ExpressRoute ağ geçidinin devreye göre duyurdığı yol sayısının sayısıdır. Adres alanları, VNet eşlemesi kullanılarak bağlanan ve uzak ExpressRoute ağ geçidini kullanan sanal ağlar içerebilir. Sanal ağ adres alanlarında sık yapılan değişiklikler olmadıkça, yolların sayısını tutarlı kalacak şekilde beklemelisiniz. Tanımış olduğunuz sanal ağ adresi alanlarının sayısı için eşiğin altına açılan tanıtılan yolların sayısı için bir uyarı ayarlayın.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Eşe tanıtılan yolların sayısının ekran görüntüsü.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Örneğe göre eş bölünmüş şekilde öğrenilen yolların sayısı

Toplama türü: *en fazla*

Bu ölçüm ExpressRoute bağlantı hattının ExpressRoute devresine bağlanan eşlerden öğrenmekte olduğu yolların sayısını gösterir. Bu yollar, aynı bağlantı hattına bağlı olan veya şirket içinde öğrenilen başka bir sanal ağdan olabilir. Öğrenilmiş yolların sayısının belirli bir eşiğin altına düşürülme için bir uyarı ayarlayın. Bu, ağ geçidinin bir performans sorununu gördüğünü veya uzak eşlerin artık ExpressRoute devresine yönelik yolların duyurulmayacağı anlamına gelebilir. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Eşten öğrenilen yolların sayısının ekran görüntüsü.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Yolların sıklığı değişikliği-örneğe göre bölme

Toplama türü: *Toplam*

Bu ölçüm, uzak eşlerden öğrenilen veya tanıtılan yolların sıklığını gösterir. Ağın neden sık değiştiğini anlamak için öncelikle şirket içi cihazlarınızı araştırmanız gerekir. Rotalar değişikliği için yüksek bir sıklık, ExpressRoute ağ geçidinde, ağ geçidi SKU 'sunun ölçeklendirildiği sorunu çözebileceği bir performans sorunu olduğunu gösterebilir. ExpressRoute ağ geçidinizin olağan dışı yol değişikliklerini gördüğünü fark etmek için bir Sıklık eşiği için uyarı ayarlayın.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Yolların değiştiği sıklık ekranının ekran görüntüsü.":::

### <a name="number-of-vms-in-the-virtual-network"></a>Sanal ağdaki sanal makinelerin sayısı

Toplama türü: *en fazla*

Bu ölçüm, ExpressRoute ağ geçidini kullanan sanal makinelerin sayısını gösterir. Sanal makine sayısı, aynı ExpressRoute ağ geçidini kullanan eşlenmiş sanal ağlardan VM 'Ler içerebilir. VM sayısı, ağ geçidi performansını etkileyebilecek belirli bir eşiğin üzerine gittiğinde bu ölçüm için bir uyarı ayarlayın. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Sanal ağ ölçümünde sanal makine sayısının ekran görüntüsü.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Bit/saniye cinsinden ExpressRoute ağ geçidi bağlantıları

Toplama türü: *Ort*

Bu ölçüm, bir ExpressRoute devresine belirli bir bağlantı için bant genişliği kullanımını gösterir.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Ağ Geçidi bağlantı bant genişliği kullanım ölçümü ekran görüntüsü.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute ağ geçidi bağlantıları için uyarılar

1. Uyarıları yapılandırmak için **Azure İzleyicisi**' ne gidin ve ardından **Uyarılar**' ı seçin.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="uyarılar":::
2. **+ Hedef Seç** ' i seçin ve ExpressRoute ağ geçidi bağlantı kaynağını seçin.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="hedef":::
3. Uyarı ayrıntılarını tanımlayın.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="eylem grubu":::
4. Eylem grubunu tanımlayın ve ekleyin.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="eylem grubu Ekle":::

## <a name="alerts-based-on-each-peering"></a>Her eşlemeye göre uyarılar

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="Her eşleme":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Devrelere ilişkin etkinlik günlüklerine yönelik uyarıları yapılandırın

**Uyarı ölçütlerinde**, sinyal türü Için **etkinlik günlüğü** ' nü seçebilir ve sinyali seçebilirsiniz.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="etkinlik günlükleri":::

## <a name="more-metrics-in-log-analytics"></a>Log Analytics daha fazla ölçüm

Ayrıca, ExpressRoute bağlantı hattı kaynağına giderek ve *Günlükler* sekmesini seçerek ExpressRoute ölçümlerini görüntüleyebilirsiniz. Sorgulama yaptığınız ölçümler için, çıktı aşağıdaki sütunları içerir.

|**Sütun**|**Tür**|**Açıklama**|
| --- | --- | --- |
|ZamanBirimi|string|PT1M (ölçüm değerleri her dakika gönderilir)|
|Count|real|Genellikle 2 ' ye eşit (her MSEE her dakikada tek bir ölçüm değeri gönderir)|
|Minimum|real|İki MSEE tarafından gönderilen iki ölçüm değeri en az|
|Maksimum|real|İki MSEE tarafından gönderilen iki ölçüm değeri en fazla|
|Ortalama|real|Eşittir (en az + en fazla)/2|
|Toplam|real|Hem MSEE hem de her iki ölçüm değerinin toplamı (sorgulanan ölçüm için odaklanmak üzere ana değer)|
  
## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute bağlantınızı yapılandırın.
  
* [Bağlantı hattı oluşturma ve değiştirme](expressroute-howto-circuit-arm.md)
* [Eşleme yapılandırması oluşturma ve değiştirme](expressroute-howto-routing-arm.md)
* [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-arm.md)

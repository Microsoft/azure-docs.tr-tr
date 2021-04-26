---
title: Ağ öngörülerini kullanarak Azure ExpressRoute öngörüleri
description: Ağ öngörülerini kullanarak Azure ExpressRoute Insights hakkında bilgi edinin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: a5e01a4fc84f852b43e5a76114bebe145da10793
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443598"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Ağ öngörülerini kullanarak Azure ExpressRoute öngörüleri

Bu makalede, ağ öngörülerinin tek bir yerde ExpressRoute ölçümlerini ve konfigürasyonlarınızı görüntülemenize nasıl yardımcı olduğu açıklanmaktadır. Ağ öngörüleri aracılığıyla, önemli ExpressRoute bilgilerini içeren topik haritaları ve sistem durumu panolarını, ek bir kurulumu tamamlamaya gerek kalmadan görüntüleyebilirsiniz.

:::image type="content" source="./media/expressroute-network-insights/network-monitor-page.png" alt-text="Ağ İzleyicisi giriş sayfasının ekran görüntüsü." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>İşlevsel bağımlılıkları görselleştirin

1. *Azure izleyici* sayfasına gidin ve ardından *ağlar*' ı seçin.

    :::image type="content" source="./media/expressroute-network-insights/monitor-page.png" alt-text="Izleyici giriş sayfasının ekran görüntüsü.":::

1. *ExpressRoute* bağlantı hattı kartını seçin. 

1. Ardından, görüntülemek istediğiniz devre için topoloji düğmesini seçin.

   :::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="ExpressRoute İzleyicisi giriş sayfasının ekran görüntüsü." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png"::: 

1. İşlevsel bağımlılık görünümü, farklı ExpressRoute bileşenleri (peerler, bağlantılar, ağ geçitleri) arasındaki ilişkiyi göstermek için ExpressRoute kurulumlarınızın net bir resmini sağlar.

    :::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Ağ öngörüleri için topoloji görünümü ekran görüntüsü." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

1. Yapılandırma bilgilerini görüntülemek için topoloji eşlemesindeki herhangi bir bileşenin üzerine gelin. Örneğin, devre bant genişliği ve Global Reach etkinleştirme gibi ayrıntıları görüntülemek için bir ExpressRoute eşleme bileşeninin üzerine gelin.

    :::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Topoloji görünümü kaynakları üzerinde vurgulama ekran görüntüsü." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Ayrıntılı ve önceden yüklenmiş ölçüm panosunu görüntüleme

İşlev bağımlılığı görünümünü kullanarak ExpressRoute kurulumlarınızın topolojisini gözden geçirdikten sonra, devrenizin performansını anlamak için ayrıntılı ölçümler görünümüne gidilmek üzere **ayrıntılı ölçümleri görüntüle** ' yi seçin. Bu görünüm, bağlantılı kaynakların düzenli bir listesini ve önemli ExpressRoute ölçümlerinin zengin bir panosunu sunar.

**Bağlı kaynaklar** bölümünde, bağlı ExpressRoute ağ geçitleri ve yapılandırılmış eşlemeler listelenir. Bu, ilgili kaynak sayfasına gitmek için seçim yapabilirsiniz.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="İzleyici sayfasındaki bağlantılı kaynağın ekran görüntüsü.":::


**ExpressRoute ölçümleri** bölümü, **kullanılabilirlik**, **Aktarım hızı**, **paket bırakmalar** ve **ağ geçidi ölçümlerinin** kategorileri arasında önemli devre ölçümlerinin grafiklerini içerir.

### <a name="availability"></a>Kullanılabilirlik

*Kullanılabilirlik* sekmesi ARP ve BGP kullanılabilirliğini izler, her iki bağlantı için de verileri bir bütün ve tek bağlantı olarak (birincil ve ikincil) olarak çizdirme. 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Kullanılabilirlik ölçüm grafiklerinin ekran görüntüsü." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Aktarım hızı

Benzer şekilde, *üretilen iş* sekmesi bit/saniye cinsinden devre için giriş ve çıkış trafiğinin toplam verimini çizer. Ayrıca, bireysel bağlantılar ve her yapılandırılmış eşleme türü için üretilen iş miktarını görüntüleyebilirsiniz.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Üretilen iş ölçüm grafiklerinin ekran görüntüsü." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Paket Bırakmaları

*Paket bırakılanlar* sekmesi, devre aracılığıyla giriş ve çıkış trafiği için bırakılan bitleri/saniyeyi çizer. Bu sekme, devre bant genişliğinizi düzenli olarak gerektiğinde veya aşarsanız oluşabilecek performans sorunlarını izlemenin kolay bir yolunu sunar.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Bırakılan paket grafiklerinin ekran görüntüsü." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Ağ Geçidi Ölçümleri

Son olarak, ağ geçidi ölçümleri sekmesi seçili bir ExpressRoute Gateway (bağlantılı Kaynaklar bölümünden) için temel ölçüm grafikleriyle doldurulur. Belirli sanal ağlarla olan bağlantınızı izlemeniz gerektiğinde bu sekmeyi kullanın.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Ağ Geçidi verimlilik ve CPU ölçümlerinin ekran görüntüsü." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute bağlantınızı yapılandırın.
  
* [Azure ExpressRoute](expressroute-introduction.md), [ağ öngörüleri](../azure-monitor/insights/network-insights-overview.md)ve [Ağ İzleyicisi](../network-watcher/network-watcher-monitoring-overview.md) hakkında daha fazla bilgi edinin
* [Bağlantı hattı oluşturma ve değiştirme](expressroute-howto-circuit-arm.md)
* [Eşleme yapılandırması oluşturma ve değiştirme](expressroute-howto-routing-arm.md)
* [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-arm.md)
* [Ölçümlerinizi özelleştirin](expressroute-monitoring-metrics-alerts.md) ve bir [Bağlantı İzleyicisi](../network-watcher/connection-monitor-overview.md) oluşturun

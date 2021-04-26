---
title: Günlükleri ve ölçümler
titleSuffix: Azure Virtual WAN
description: Azure sanal WAN günlükleri ve ölçümleri hakkında bilgi edinin
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: a5bbcc4c07826a5093da3f51c709f49ceb231753
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596082"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure sanal WAN günlükleri ve ölçümleri

Azure Izleyici 'yi kullanarak Azure sanal WAN izleyebilirsiniz. Sanal WAN, tek bir işlemsel arabirim sağlamak için birçok ağ, güvenlik ve yönlendirme işlevlerini birlikte getiren bir ağ hizmetidir. Sanal WAN VPN Gateway 'leri, ExpressRoute ağ geçitleri ve Azure Güvenlik Duvarı, Azure Izleyici aracılığıyla günlüğe kaydetme ve ölçümlere sahiptir.

Bu makalede Portal aracılığıyla kullanılabilen ölçümler ve Tanılamalar ele alınmaktadır. Ölçümler hafif ve neredeyse gerçek zamanlı senaryoların yanı sıra uyarı ve hızlı sorun algılama için yararlı hale getirebilirsiniz.

## <a name="monitoring-secured-hub-azure-firewall"></a>Güvenli hub 'ı izleme (Azure Güvenlik Duvarı) 

Azure Güvenlik Duvarı günlüklerini kullanarak güvenli hub 'ı izleyebilirsiniz. Ayrıca etkinlik günlüklerini kullanarak Azure Güvenlik Duvarı kaynaklarıyla ilgili işlemleri denetleyebilirsiniz.

Azure Güvenlik Duvarı 'nı kullanarak sanal hub 'ınızı güvenli hale getirme seçeneğini belirlediyseniz, ilgili günlüklere ve ölçümlere buradan ulaşabilirsiniz: [Azure Güvenlik Duvarı günlükleri ve ölçümleri](../firewall/logs-and-metrics.md).

## <a name="metrics"></a>Ölçümler

Azure Izleyici ölçümleri, belirli bir zamanda sistemin bazı yönlerini tanımlayan sayısal değerlerdir. Ölçümler her dakika toplanır ve sıklıkla örneklenebilir olduğundan uyarma için faydalıdır. Bir uyarı görece basit mantık ile hızlı bir şekilde tetiklenebilir.

### <a name="site-to-site-vpn-gateways"></a>Siteden siteye VPN ağ geçitleri

Azure siteden siteye VPN ağ geçitleri için aşağıdaki ölçümler mevcuttur:

* **Ağ geçidi bant genişliği** – bir ağ geçidinin ortalama siteden siteye toplam bant genişliği (saniye başına bayt).
* **Tünel bant genişliği** – saniye başına bayt cinsinden bir tünelin ortalama bant genişliği.
* **Tünel çıkış baytları** – bir tünelin giden bayt sayısı. 
* **Tünel çıkış paketleri** – bir tünelin giden paket sayısı. 
* **Tünel çıkışları eşleşmeyen paket bırakma** – giden paket bırakma sayısı, bir tünelin trafik seçiciyle uyuşmuyor. 
* **Tünel girişi baytları** – bir tünelin gelen bayt sayısı. 
* **Tünel giriş paketi** – bir tünelin gelen paket sayısı. 
* **Tünel girişi uyumsuz paket bırakma** -gelen paket bırakma sayısı, bir tünelin trafik seçiciyle uyuşmuyor. 

### <a name="point-to-site-vpn-gateways"></a>Noktadan siteye VPN ağ geçitleri

Azure Noktadan siteye VPN ağ geçitleri için aşağıdaki ölçümler mevcuttur:

* **Gateway P2S band genişliği** – bir ağ geçidinin ortalama Noktadan siteye toplam bant genişliği (saniye başına bayt).
* **P2S bağlantı sayısı** – bir ağ geçidinin Noktadan siteye bağlantı sayısı.

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute ağ geçitleri

Azure ExpressRoute ağ geçitleri için aşağıdaki ölçümler mevcuttur:

* **Bitsinpersecond** – BITS giriş/saniye.
* **Bitsoutpersecond** – bit, saniye başına Azure 'a kurtarıldı.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Ağ Geçidi ölçümlerini görüntüle

Aşağıdaki adımlar ölçümleri bulmanıza ve görüntülemenize yardımcı olur:

1. Portalda, ağ geçidine sahip sanal hub 'a gidin.

2. Siteden siteye ağ geçidini bulmak için ExpressRoute, bir ExpressRoute ağ geçidini bulmak için **ExpressRoute** veya bir noktadan siteye ağ geçidini bulmak IÇIN **Kullanıcı VPN (siteye işaret)** **seçeneğini belirleyin.** Sayfasında, ağ geçidi bilgilerini görebilirsiniz. Bu bilgileri kopyalayın. Daha sonra, Azure Izleyici 'yi kullanarak tanılamayı görüntülemek için kullanacaksınız.

3. **Ölçümler**’i seçin.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Ekran görüntüsünde, Azure Izleyici 'de Görünüm ' ün bulunduğu siteden siteye V P N bölmesi görüntülenir.":::

4. **Ölçümler** sayfasında, ilgilendiğiniz ölçümleri görebilirsiniz.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Kategorileri vurgulanmış olan ' ölçümler ' sayfasını gösteren ekran görüntüsü.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Tanılama günlükleri

### <a name="site-to-site-vpn-gateways"></a>Siteden siteye VPN ağ geçitleri

Azure siteden siteye VPN ağ geçitleri için aşağıdaki Tanılamalar mevcuttur:

* **Ağ geçidi tanılama günlükleri** – sistem durumu, yapılandırma, hizmet güncelleştirmeleri ve ek tanılama gibi ağ geçidine özgü Tanılamalar.
* **Tünel tanılama günlükleri** – Bunlar, siteden siteye IPSec tüneli, anlaşmalı SAS, bağlantı kesme nedenleri ve ek Tanılamalar için bağlanma ve bağlantı kesme olayları gibi IPSec tünelinde ilgili günlüklerdir.
* **Tanılama günlüklerini yönlendirme** : Bunlar, statik YOLLARıN, BGP 'nin, yol güncelleştirmelerinin yanı sıra ek Tanılamalar için olaylarla ilgili günlüklerdir.
* **Ike tanılama günlükleri** – IPSec bağlantıları için IKE 'ye özgü Tanılamalar.

### <a name="point-to-site-vpn-gateways"></a>Noktadan siteye VPN ağ geçitleri

Azure Noktadan siteye VPN ağ geçitleri için aşağıdaki Tanılamalar mevcuttur:

* **Ağ geçidi tanılama günlükleri** – sistem durumu, yapılandırma, hizmet güncelleştirmeleri ve diğer Tanılamalar gibi ağ geçidine özgü Tanılamalar.
* **Ike tanılama günlükleri** – IPSec bağlantıları için IKE 'ye özgü Tanılamalar.
* **P2S tanılama günlükleri** – Bunlar, kullanıcı VPN (noktadan sıteye) P2S yapılandırma ve istemci olaylardır. Bunlara istemci bağlantısı/bağlantıyı kes, VPN istemci adresi ayırma ve diğer Tanılamalar de dahildir.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Tanılama günlüklerini görüntüleme

Aşağıdaki adımlar tanılamayı bulmanıza ve görüntülemenize yardımcı olur:

1. Portalda sanal WAN kaynağınız ' ne gidin. Portalda sanal WAN sayfasının **genel bakış** bölümünde, görünüm ve kaynak grubu bilgilerini almak Için **temel parçalar** ' ı seçin. Kaynak grubu bilgilerini kopyalayın.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="' Kopyala ' düğmesine işaret eden bir ok ile ' genel bakış ' bölümünü gösteren ekran görüntüsü.":::

2. Izleme bölümünde kaynak grubuna gidin. **Tanılama ayarları**' nı seçin ve kaynak bilgilerini girin. Bu makalenin önceki bölümlerinde yer alan [ağ geçidi ölçümlerini görüntüle](#metrics-steps) bölümünde 2. adımda kopyaladığınız kaynak bilgileri.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="' Kaynak ' açılan simgesine işaret eden bir ok ile ' Izleme ' bölümünü gösteren ekran görüntüsü.":::

3. Sonuçlar sayfasında **+ Tanılama ayarı Ekle**' yi seçin ve ardından bir seçenek belirleyin. Log Analytics, bir olay hub 'ına ya da yalnızca bir depolama hesabına arşivleme ' ye gönderme seçeneğini belirleyebilirsiniz.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="ölçümler sayfası":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Örnek sorgu Log Analytics

Günlükler **Azure Log Analytics çalışma alanında** bulunur. Log Analytics bir sorgu ayarlayabilirsiniz. Aşağıdaki örnek, siteden siteye yol tanılamayı elde etmek için bir sorgu içerir.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Aşağıdaki değerleri, **= =** öğesinden sonra gerektiği şekilde değiştirin.

* "GatewayDiagnosticLog"
* "Ikediagnosticlog"
* "P2SDiagnosticLog"
* "Tüneldiagnosticlog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Etkinlik günlükleri

**Etkinlik günlüğü** girdileri varsayılan olarak toplanır ve Azure Portal görüntülenebilir. Azure aboneliğinize gönderilen tüm işlemleri görüntülemek için Azure etkinlik günlüklerini (eski adıyla *İşlemsel Günlükler* ve *Denetim günlükleri* olarak bilinir) kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Güvenlik Duvarı günlüklerini ve ölçümlerini izlemeyi öğrenmek için bkz. [öğretici: Azure Güvenlik Duvarı günlüklerini izleme](../firewall/firewall-diagnostics.md).
* Azure Izleyici 'de ölçümler hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de ölçümler](../azure-monitor/essentials/data-platform-metrics.md).

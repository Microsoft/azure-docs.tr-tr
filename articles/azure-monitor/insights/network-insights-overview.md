---
title: Azure Izleyici ağ öngörüleri
description: Azure Izleyici ağ öngörülerine genel bakış. Bu, herhangi bir yapılandırma olmadan dağıtılan tüm ağ kaynakları için sistem durumu ve ölçümlerin kapsamlı bir görünümünü sağlar.
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: f401c143f1f5a9352b56b80f9e473bd7fa819245
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968263"
---
# <a name="azure-monitor-network-insights"></a>Azure Izleyici ağ öngörüleri

Azure Izleyici ağ öngörüleri, tüm dağıtılan ağ kaynakları için herhangi bir yapılandırma gerektirmeden [sistem durumu](../../service-health/resource-health-checks-resource-types.md) ve [ölçümlerinin](../essentials/metrics-supported.md) kapsamlı bir görünümünü sağlar. Ayrıca [bağlantı izleyici](../../network-watcher/connection-monitor-overview.md), [ağ güvenlik grupları (NSG 'ler) için akış günlüğü](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)ve [Trafik Analizi](../../network-watcher/traffic-analytics.md)gibi ağ izleme özelliklerine erişim sağlar. Ayrıca, diğer ağ [Tanılama](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) özellikleri sağlar.

Azure Izleyici ağ öngörüleri, izlemenin bu temel bileşenleri etrafında yapılandırılır:
- [Ağ durumu ve ölçümleri](#networkhealth)
- [Bağlantı](#connectivity)
- [Trafik](#traffic)
- [Tanılama araç seti](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Ağ durumu ve ölçümleri

Azure Izleyici ağ öngörüleri **genel bakış** sayfası, kaynak sistem durumu ve uyarılarla birlikte ağ kaynaklarınızın envanterini görselleştirmenin kolay bir yolunu sunar. Dört önemli işlevsel alana bölünmüştür: arama ve filtreleme, kaynak sistem durumu ve ölçümler, uyarılar ve bağımlılık görünümü.

[![Genel bakış sayfasını gösteren ekran görüntüsü](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Arama ve filtreleme
**Abonelik**, **kaynak grubu** ve **tür** gibi filtreleri kullanarak kaynak sistem durumu ve uyarıları görünümünü özelleştirebilirsiniz.

Arama kutusunu, kaynakları ve bunlarla ilişkili kaynakları aramak için kullanabilirsiniz. Örneğin, genel bir IP bir uygulama ağ geçidi ile ilişkilendirilir. Genel IP DNS adı araması hem genel IP hem de ilişkili uygulama ağ geçidini döndürür:

[![Azure Izleyici ağ öngörüleri arama sonuçlarını gösteren ekran görüntüsü.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Kaynak durumu ve ölçümleri
Aşağıdaki örnekte, her kutucuk bir kaynak türünü temsil eder. Kutucuk, seçilen tüm abonelikler arasında dağıtılan kaynak türünün örnek sayısını görüntüler. Ayrıca kaynağın sistem durumunu görüntüler. Bu örnekte, 105 ER ve VPN bağlantısı dağıtılır. 103 sağlıklı ve 2 kullanılamaz.

![Azure Izleyici ağ öngörülerinde kaynak sistem durumu ve ölçümleri gösteren ekran görüntüsü.](media/network-insights-overview/resource-health.png)

Kullanılamayan ve VPN bağlantılarını seçerseniz, bir ölçüm görünümü görürsünüz: 

![Azure Izleyici ağ öngörülerine yönelik ölçüm görünümünü gösteren ekran görüntüsü.](media/network-insights-overview/metric-view.png)

Kılavuz görünümündeki herhangi bir öğeyi seçebilirsiniz. Bu bağlantının kaynak sistem durumunu almak için **sistem durumu** sütunundaki simgeyi seçin. Bağlantı için uyarılar ve ölçümler sayfasına gitmek üzere **Uyarı** sütunundaki değeri seçin. 

### <a name="alerts"></a>Uyarılar
Sayfanın sağ tarafındaki **Uyarı** kutusu, tüm abonelikler genelinde seçili kaynaklar için oluşturulan tüm uyarıların bir görünümünü sağlar. Ayrıntılı uyarılar sayfasına gitmek için uyarı sayılarını seçin.

### <a name="dependency-view"></a>Bağımlılık görünümü
Bağımlılık görünümü, bir kaynağın nasıl yapılandırıldığını görselleştirmenize yardımcı olur. Bağımlılık görünümü şu anda Azure Application Gateway, Azure sanal WAN ve Azure Load Balancer için kullanılabilir. Örneğin, Application Gateway için, ölçümler kılavuz görünümündeki Application Gateway kaynak adını seçerek bağımlılık görünümüne erişebilirsiniz. Sanal WAN ve Load Balancer aynı şeyi yapabilirsiniz.

![Azure Izleyici ağ öngörülerine Application Gateway görünümünü gösteren sreenshot.](media/network-insights-overview/application-gateway.png)

Application Gateway için bağımlılık görünümü, ön uç IP 'Lerinin dinleyicileri, kuralları ve arka uç havuzunu nasıl bağladığınıza ilişkin basitleştirilmiş bir görünüm sağlar. Bağlantı çizgileri renk kodludur ve arka uç havuzu sistem durumuna göre ek ayrıntılar sağlar. Görünüm aynı zamanda, sanal makine ölçek kümesi ve VM örnekleri gibi ilgili tüm arka uç havuzlarının Application Gateway ölçümlerinin ve ölçümlerinin ayrıntılı bir görünümünü sağlar.

[![Azure Izleyici ağ öngörülerine yönelik bağımlılık görünümünü gösteren ekran görüntüsü.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

Bağımlılık grafiği yapılandırma ayarlarına kolay gezinme sağlar. Diğer bilgilere erişmek için bir arka uç havuzuna sağ tıklayın. Örneğin, arka uç havuzu bir VM ise, bağlantı sorunlarını belirlemek için doğrudan VM öngörülerine ve Azure ağ Izleyicisi bağlantı sorunlarını gidermeye erişebilirsiniz:

![Azure Izleyici ağ öngörüleri ' nde bağımlılık görünümü menüsünü gösteren ekran görüntüsü.](media/network-insights-overview/dependency-view-menu.png)

Bağımlılık görünümündeki arama ve filtre çubuğu, grafikte arama yapmak için kolay bir yol sağlar. Örneğin, önceki örnekte **Appgwtestrule** için arama yaparsanız görünüm, appgwtestrule ile bağlantılı tüm düğümlere ölçeklendirecektir:

![Azure Izleyici ağ öngörülerine bir arama örneği gösteren ekran görüntüsü.](media/network-insights-overview/search-example.png)

Çeşitli filtreler, belirli bir yol ve duruma göre ölçeklendirmenize yardımcı olur. Örneğin, durumun sağlıksız olduğu tüm kenarları göstermek için **sistem** durumu listesinden yalnızca **sağlıksız** ' ı seçin.

Uygulama ağ geçidi, tüm arka uç havuzu kaynakları ve ön uç IP 'Leri için ayrıntılı ölçümler sağlayan önceden yapılandırılmış bir çalışma kitabını açmak için **ayrıntılı ölçümleri görüntüle** ' yi seçin. 

## <a name="connectivity"></a><a name="connectivity"></a>Bilirlik

Bağlantı **sekmesi,** seçili abonelik kümesi Için [bağlantı Izleyicisi](../../network-watcher/connection-monitor-overview.md) ve Bağlantı İzleyicisi (klasik) aracılığıyla yapılandırılan tüm testleri görselleştirmenin kolay bir yolunu sağlar.

![Azure Izleyici ağ öngörülerinin bağlantı sekmesini gösteren ekran görüntüsü.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Testler, **kaynak** ve **hedef** kutucuklarına göre gruplandırılır ve her test için ulaşılabilirlik durumunu görüntüler. Erişilebilir ayarlar, başarısız olan denetim (%) temelinde, erişilebilirlik ölçütlerinizle ilgili yapılandırmalara kolay erişim sağlar. ve RTT (MS). Değerleri ayarladıktan sonra, her test güncelleştirmelerinin durumu seçim ölçütlerine göre yapılır.

[![Azure Izleyici ağ öngörülerine yönelik bağlantı testlerini gösteren ekran görüntüsü.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

Bir ölçüm görünümü açmak için herhangi bir kaynak veya hedef kutucuğu seçebilirsiniz:

[![Azure Izleyici ağ öngörülerinde bağlantı ölçümlerini gösteren ekran görüntüsü.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


Kılavuz görünümündeki herhangi bir öğeyi seçebilirsiniz. Bağlantı Izleyicisi portalı sayfasına gitmek için **ulaşılabilirlik** sütunundaki simgeyi seçin ve tanımlanmış sorunları etkileyen atlama topolojisini ve bağlantısını görüntüleyin. Uyarılara gitmek için **Uyarı** sütunundaki değeri seçin. Seçilen bağlantı izleyicisinin ölçüm sayfasına gitmek için, **başarısız olan yüzde** ve gidiş **-dönüş süresi (MS)** sütunlarındaki grafikleri seçin.

Sayfanın sağ tarafındaki **Uyarı** kutusu, tüm abonelikler genelinde yapılandırılmış bağlantı testleri için oluşturulan tüm uyarıların bir görünümünü sağlar. Ayrıntılı uyarılar sayfasına gitmek için uyarı sayılarını seçin.

## <a name="traffic"></a><a name="traffic"></a>Trafik
**Trafik** sekmesi, [NSG akış günlükleri](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) için yapılandırılmış tüm NSG 'ler ve seçili abonelik kümesi için [Trafik Analizi](../../network-watcher/traffic-analytics.md) konuma göre gruplanmış olarak erişim sağlar. Bu sekmede sunulan arama işlevselliği, Aranan IP adresi için yapılandırılmış NSG 'leri tanımlamanızı sağlar. Ortamınızdaki herhangi bir IP adresini arayabilirsiniz. Döşeli bölgesel görünüm, NSG akış günlükleri ve Trafik Analizi yapılandırma durumuyla birlikte tüm NSG 'leri görüntüler.

[![Azure Izleyici ağ öngörülerinin trafik sekmesini gösteren ekran görüntüsü.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Herhangi bir bölge kutucuğu seçerseniz, bir ızgara görünümü görüntülenir. Kılavuz, NSG akış günlükleri ve Trafik Analizi, okunması ve yapılandırılması kolay bir görünümde sağlar:  

[![Azure Izleyici ağ öngörülerine trafik bölgesi görünümünü gösteren ekran görüntüsü.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

Kılavuz görünümündeki herhangi bir öğeyi seçebilirsiniz. NSG akış günlüğü ve Trafik Analizi yapılandırmasını düzenlemek için **Flowlog yapılandırma durumu** sütunundaki simgeyi seçin. Seçili NSG için yapılandırılmış trafik uyarılarına gitmek için **Uyarı** sütunundaki değeri seçin. Benzer şekilde, **Trafik Analizi çalışma alanını** seçerek trafik analizi görünümüne gidebilirsiniz.  

Sayfanın sağ tarafındaki **Uyarı** kutusu, tüm aboneliklerde tüm trafik analizi çalışma alanı tabanlı uyarıların bir görünümünü sağlar. Ayrıntılı uyarılar sayfasına gitmek için uyarı sayılarını seçin.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Tanılama araç seti
Tanılama araç seti, ağ sorunlarını gidermek için kullanılabilen tüm tanılama özelliklerine erişim sağlar. Bu açılan listeyi [paket yakalama](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN sorunlarını giderme](../../network-watcher/network-watcher-troubleshoot-overview.md), [bağlantı sorunlarını giderme](../../network-watcher/network-watcher-connectivity-overview.md), [sonraki atlama](../../network-watcher/network-watcher-next-hop-overview.md)ve [IP akışı doğrulama](../../network-watcher/network-watcher-ip-flow-verify-overview.md)gibi özelliklere erişmek için kullanabilirsiniz:

![Tanılama araç seti sekmesini gösteren ekran görüntüsü.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="onboarded-resources"></a>Eklendi kaynakları 

Eklendi kaynaklarında yerleşik çalışma kitapları ve bağımlılık görünümleri vardır. Şu anda eklendi kaynakları sanal WAN, Application Gateway, Load Balancer ve ExpressRoute.

## <a name="troubleshooting"></a>Sorun giderme 
Genel sorun giderme kılavuzu için, adanmış çalışma kitabı tabanlı Öngörüler [sorun giderme makalesine](troubleshoot-workbooks.md)bakın.
Bu bölüm, Azure Izleyici ağ öngörülerini kullanırken karşılaşabileceğiniz bazı yaygın sorunları tanılamanıza ve gidermenize yardımcı olur. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Performans sorunlarını veya başarısızlıklarını çözümlemek Nasıl yaparım? mı?

Azure Izleyici ağ öngörüleri ile belirttiğiniz ağla ilgili sorunları giderme hakkında bilgi edinmek için bkz. hatalı kaynak için sorun giderme belgeleri. 

Sık kullanılan hizmetlere ilişkin sorun giderme makalelerine yönelik bazı bağlantılar aşağıda verilmiştir. Bu hizmetlerle ilgili daha fazla sorun giderme hakkında daha fazla bilgi için, hizmetin İçindekiler bölümünün sorun giderme bölümündeki diğer makalelere bakın.
* [Azure Sanal Ağ](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Azure Application Gateway](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Seçdiğim tüm abonelikler için kaynakları neden görmüyorum?

Azure Izleyici ağ öngörüleri tek seferde yalnızca beş aboneliğin kaynaklarını gösterebilir. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-network-insights"></a>Azure Izleyici ağ öngörülerine değişiklik yapmak veya görselleştirme eklemek Nasıl yaparım?.

Değişiklik yapmak için çalışma kitabını değiştirmek üzere **düzenleme modu** ' nu seçin. Daha sonra yaptığınız değişiklikleri, belirlenen aboneliğe ve kaynak grubuna bağlı yeni bir çalışma kitabı olarak kaydedebilirsiniz.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Çalışma kitaplarının herhangi bir bölümünü sabitledikten sonra zaman dilimi nedir?

Azure Izleyici ağ öngörüleri **Otomatik** zaman aralığını kullanır, bu nedenle zaman dilimi seçili zaman aralığına göre belirlenir.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Çalışma kitabının herhangi bir parçası sabitlendiğinde zaman aralığı nedir?

Zaman aralığı Pano ayarlarına bağlıdır.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-network-insights"></a>Diğer verileri görmek veya kendi görselleştirmelerimi yapmak istersem ne yapmalıyım? Azure Izleyici ağ öngörülerini nasıl değiştirebilir?

Herhangi bir kenar panelinde veya ayrıntılı ölçüm görünümünde gördüğünüz çalışma kitabını düzenleme modunu kullanarak düzenleyebilirsiniz. Sonra değişikliklerinizi yeni bir çalışma kitabı olarak kaydedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Ağ izleme hakkında daha fazla bilgi edinin: [Azure ağ Izleyicisi nedir?](../../network-watcher/network-watcher-monitoring-overview.md)
- Çalışma kitaplarının destekleyecek şekilde tasarlandığını, raporların nasıl oluşturulacağını ve mevcut raporların nasıl özelleştirileceğini ve daha fazlasını öğrenin: [Azure izleyici çalışma kitaplarını kullanarak etkileşimli raporlar oluşturma](../visualize/workbooks-overview.md)

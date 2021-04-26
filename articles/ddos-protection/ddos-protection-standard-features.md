---
title: Azure DDoS koruma özellikleri
description: Azure DDoS koruması özelliklerini öğrenin
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 490a65c7d20194956350166d23e2d3450b94b12e
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103105"
---
# <a name="azure-ddos-protection-standard-features"></a>Azure DDoS Koruması Standart'ın özellikleri

Aşağıdaki bölümlerde, Azure DDoS koruması standart hizmetinin temel özellikleri ana hatlarıyla verilmiştir.

## <a name="always-on-traffic-monitoring"></a>Her zaman açık trafik izleme

DDoS koruması standart gerçek trafik kullanımını izler ve DDoS Ilkesinde tanımlanan eşiklere göre sürekli olarak karşılaştırır. Trafik eşiği aşıldığında, DDoS risk azaltma otomatik olarak başlatılır. Trafik eşiklerin altına döndüğünde, risk azaltma durdurulur.

![Azure DDoS koruması standart risk azaltma](./media/ddos-protection-overview/mitigation.png)

Risk azaltma sırasında, korunan kaynağa gönderilen trafik DDoS koruma hizmeti tarafından yeniden yönlendirilir ve şu gibi çeşitli denetimler gerçekleştirilir:

- Paketlerin internet belirtimlerine uyduğundan ve hatalı biçimlendirilmiş olmadığından emin olun.
- Trafiğin potansiyel olarak sahte bir paket olup olmadığını (ör: SYN auth veya SYN tanımlama bilgisi) ve yeniden aktarmak için kaynak için bir paket bırakarak istemciyle etkileşime geçin.
- Başka bir zorlama yöntemi gerçekleştirilmeyen hız limiti paketleri.

DDoS koruması, saldırı trafiğini bırakır ve kalan trafiği amaçlanan hedefine iletir. Saldırının algılanmasından itibaren birkaç dakika içinde Azure İzleyici ölçümleriyle bilgilendirme yapılır. DDoS koruması standart telemetrisinde günlüğe kaydetmeyi yapılandırarak, gelecekteki analizler için günlükleri kullanılabilir seçeneklere yazabilirsiniz. DDoS koruma standardı için Azure Izleyici 'de ölçüm verileri 30 gün boyunca tutulur.

## <a name="adaptive-real-time-tuning"></a>Uyarlamalı gerçek zamanlı ayarlama

Azure DDoS koruması temel hizmeti, müşterileri korumanıza ve diğer müşterilerin etkilerini önlemeye yardımcı olur. Örneğin, altyapı genelindeki DDoS koruma ilkesinin *tetikleme hızından* daha küçük olan bir hizmetin tipik bir birimi için sağlanması halinde, müşterinin kaynaklarına yönelik bir DDoS saldırısı fark etmeyebilir. Daha genel olarak, son saldırıların karmaşıklığı (örneğin, çok vektör DDoS) ve kiracıların uygulamaya özgü davranışları, müşteri başına, özel koruma ilkeleri için çağrı. Hizmet bunu iki öngörü kullanarak gerçekleştirir:

- Katman 3 ve 4 için müşteri başına (genel IP başına) trafik desenlerini otomatik öğrenme.

- Azure 'un ölçeğinin önemli miktarda trafiğe artışlarını devralarak izin verdiğinden emin olmak için hatalı pozitif sonuçları en aza indirir.

![DDoS koruma standardı 'nın "Ilke oluşturma" daire içinde nasıl çalıştığı diyagramı](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS koruması telemetrisi, izleme ve uyarı

DDoS koruma standardı, [Azure izleyici](../azure-monitor/overview.md)aracılığıyla zengin telemetri sunar. DDoS korumasının kullandığı Azure Izleyici ölçümlerinden herhangi biri için uyarıları yapılandırabilirsiniz. Azure Izleyici tanılama arabirimi aracılığıyla, gelişmiş analiz için Azure depolama ile splunk (Azure Event Hubs), Azure Izleyici günlükleri ve Azure Storage ile günlüğe kaydetmeyi tümleştirebilir.

### <a name="ddos-mitigation-policies"></a>DDoS risk azaltma ilkeleri

Azure Portal ölçümleri **İzle**' yi seçin  >  . **Ölçümler** bölmesinde, kaynak grubunu seçin, **ortak IP adresi** kaynak türünü SEÇIN ve Azure genel IP adresinizi seçin. DDoS ölçümleri **kullanılabilir ölçümler** bölmesinde görünür.

DDoS koruması standardı, korumalı kaynağın her genel IP 'si için, DDoS özellikli olan sanal ağdaki üç etkin Azaltma ilkesi (TCP SYN, TCP ve UDP) uygular. **DDoS risk azaltma tetiklenecek ölçüm gelen paketleri** seçerek ilke eşiklerini görüntüleyebilirsiniz.

![Kullanılabilir ölçümler ve ölçümler grafiği](./media/ddos-best-practices/image-7.png)

İlke eşikleri, makine öğrenimi tabanlı ağ trafiği profili oluşturma yoluyla otomatik olarak yapılandırılır. Yalnızca ilke eşiği aşıldığında saldırı altında bir IP adresi için DDoS azaltma durumu oluşur.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>DDoS saldırısı kapsamındaki bir IP adresi ölçümü

Genel IP adresi saldırı altındaysa, DDoS koruması için **DDoS saldırısının altındaki** ölçüm değeri, saldırı trafiği üzerinde hafifletme uygular.

!["DDoS saldırısı altında" ölçüm ve grafik](./media/ddos-best-practices/image-8.png)

Bu ölçüm üzerinde bir uyarı yapılandırmanızı öneririz. Daha sonra, genel IP adresiniz üzerinde gerçekleştirilen etkin bir DDoS risk azaltma olduğunda size bildirim verilecektir.

Daha fazla bilgi için, [Azure Portal kullanarak Azure DDoS koruması standardını yönetme](manage-ddos-protection.md)makalesine bakın.

## <a name="web-application-firewall-for-resource-attacks"></a>Kaynak saldırıları için Web uygulaması güvenlik duvarı

Uygulama katmanında kaynak saldırılarına özgü, Web uygulamalarının güvenliğini sağlamaya yardımcı olmak için bir Web uygulaması güvenlik duvarı (WAF) yapılandırmanız gerekir. Bir WAF, SQL ınjec, siteler arası komut dosyası, DDoS ve diğer katman 7 saldırılarını engellemek için gelen Web trafiğini inceler. Azure, yaygın güvenlik açıklarından ve güvenlik açıklarından Web uygulamalarınızın merkezi olarak korunması için [Application Gateway bir özelliği olarak WAF](../web-application-firewall/ag/ag-overview.md) sağlar. Azure iş ortaklarından [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)aracılığıyla gereksinimlerinize uygun olabilecek başka WAF teklifleri de mevcuttur.

Web uygulaması güvenlik duvarları, Volumetric ve durum tükenmesi saldırılarına açıktır. Volumetric ve protokol saldırılarına karşı korumaya yardımcı olmak için WAF sanal ağı üzerinde DDoS koruma standardının etkinleştirilmesini önemle öneririz. Daha fazla bilgi için [DDoS koruması başvuru mimarileri](ddos-protection-reference-architectures.md) bölümüne bakın.

## <a name="protection-planning"></a>Koruma planlaması

Planlama ve hazırlık, bir sistemin DDoS saldırısı sırasında nasıl gerçekleştirileceğini anlamak için önemlidir. Bir olay yönetimi yanıt planı tasarlamak, bu çabanın bir parçasıdır.

DDoS koruma standardı varsa, internet 'e yönelik uç noktaların sanal ağı üzerinde etkinleştirildiğinden emin olun. DDoS uyarılarını yapılandırmak, altyapınızdaki olası saldırıları sürekli olarak izlemenize yardımcı olur. 

Uygulamalarınızı bağımsız olarak izleyin. Bir uygulamanın normal davranışını anlayın. DDoS saldırısı sırasında uygulama beklendiği gibi davranmıyorsa, uygulamaya hazırlanın. 

Hizmet [benzetimleri aracılığıyla test](test-through-simulations.md)ederek hizmetlerinizin bir saldırıya nasıl yanıt vereceğini öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruma planı oluşturma](manage-ddos-protection.md)hakkında bilgi edinin.

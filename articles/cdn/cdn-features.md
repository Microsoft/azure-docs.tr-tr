---
title: Azure Content Delivery Network (CDN) ürün özelliklerini karşılaştırın
description: Her Azure Content Delivery Network (CDN) ürününün desteklediği özellikler hakkında bilgi edinin.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 9aa394cda245bd3a457a16c19660bfe08553d14d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058893"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Azure CDN ürün özellikleri arasındaki karşılaştırmalar nelerdir?

Azure Content Delivery Network (CDN) dört ürün içerir: 

* **Microsoft 'tan Azure CDN Standart**
* **Akamai 'ten standart Azure CDN**
* **Verizon 'ten standart Azure CDN**
* **Verizon 'Ten Premium Azure CDN**. 

Aşağıdaki tabloda her ürünle birlikte sunulan özellikler karşılaştırılmaktadır.

| **Performans özellikleri ve iyileştirmeler** | **Standart Microsoft** | **Standart Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dinamik site hızlandırma](./cdn-dynamic-site-acceleration.md)  | [Azure ön kapı hizmeti](../frontdoor/front-door-overview.md) ile sunulan | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamik site hızlandırma-uyarlamalı görüntü sıkıştırma](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamik site hızlandırma-nesne önceden getirme](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Genel web teslimi iyileştirmesi](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;**, ortalama dosya BOYUTUNUZ 10 MB 'den küçükse bu iyileştirme türünü seçin  | **&#x2713;** |  **&#x2713;** |
| [Video akışı iyileştirmesi](./cdn-media-streaming-optimization.md)  | Genel Web teslimatı aracılığıyla | **&#x2713;**  | Genel Web teslimatı aracılığıyla |  Genel Web teslimatı aracılığıyla |
| [Büyük dosya iyileştirmesi](./cdn-large-file-optimization.md)  | Genel Web teslimatı aracılığıyla | **&#x2713;**, ortalama dosya BOYUTUNUZ 10 MB 'tan büyükse bu iyileştirme türünü seçin   | Genel Web teslimatı aracılığıyla |  Genel Web teslimatı aracılığıyla |
| İyileştirme türünü değiştir | |**&#x2713;** | | |
| Kaynak bağlantı noktası |Tüm TCP bağlantı noktaları |[İzin verilen kaynak bağlantı noktaları](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Tüm TCP bağlantı noktaları |Tüm TCP bağlantı noktaları |
| [Genel sunucu yük dengelemesi (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Hızlı temizleme](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, tümünü temizle ve joker karakter Temizleme, şu anda Akamai öğesinden Azure CDN tarafından desteklenmiyor |**&#x2713;** |**&#x2713;** |
| [Varlık önceden yükleme](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Önbellek/üst bilgi ayarları ([önbelleğe alma](cdn-caching-rules.md) kullanılarak)  |[Standart kurallar altyapısını](cdn-standard-rules-engine.md) kullanarak **&#x2713;**  |**&#x2713;** |**&#x2713;** | |
| Özelleştirilebilir, kural tabanlı içerik teslim altyapısı |[Standart kurallar altyapısını](cdn-standard-rules-engine.md) kullanarak **&#x2713;**  | | |[Kural altyapısını](./cdn-verizon-premium-rules-engine.md) kullanarak **&#x2713;** |
| Önbellek/üst bilgi ayarları  |[Standart kurallar altyapısını](cdn-standard-rules-engine.md) kullanarak **&#x2713;** | | |[Premium kurallar altyapısını](./cdn-verizon-premium-rules-engine.md) kullanarak **&#x2713;** |
| URL yeniden yönlendirme/yeniden yazma |[Standart kurallar altyapısını](cdn-standard-rules-engine.md) kullanarak **&#x2713;**  | | |[Premium kurallar altyapısını](./cdn-verizon-premium-rules-engine.md) kullanarak **&#x2713;** |
| Mobil cihaz kuralları  |[Standart kurallar altyapısını](cdn-standard-rules-engine.md) kullanarak **&#x2713;** | | |[Premium kurallar altyapısını](./cdn-verizon-premium-rules-engine.md) kullanarak **&#x2713;** |
| [Sorgu dizesi önbelleğe alma](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 ikili yığını | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 desteği](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Güvenlik** | **Standart Microsoft** | **Standart Akamai** | **Standard Verizon** | **Premium Verizon** | 
| CDN uç noktasıyla HTTPS desteği | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Özel etki alanı HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, etkinleştirmek IÇIN doğrudan CNAME gerekir |**&#x2713;** |**&#x2713;** |
| [Özel etki alanı adı desteği](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Coğrafi filtreleme](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Belirteç kimlik doğrulaması](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS koruması](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Kendi sertifikanızı getirme](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Desteklenen TLS sürümleri | TLS 1,2, TLS 1.0/1.1- [yapılandırılabilir](/rest/api/cdn/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analiz ve raporlama** | **Standart Microsoft** | **Standart Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure tanılama günlükleri](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon’dan alınan çekirdek raporlar](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Verizon özel raporları](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Gelişmiş HTTP raporları](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Gerçek zamanlı istatistikler](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Uç düğümü performansı](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Gerçek zamanlı uyarılar](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Kullanım kolaylığı** | **Standart Microsoft** | **Standart Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Depolama](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) ve [Media Services](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) gibi Azure hizmetleriyle kolay tümleştirme  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [REST API](/rest/api/cdn/), [.net](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md)veya [PowerShell](cdn-manage-powershell.md) aracılığıyla yönetim  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Sıkıştırma MIME türleri](./cdn-improve-performance.md)  |Yapılandırılabilir |Yapılandırılabilir |Yapılandırılabilir  |Yapılandırılabilir  |
| Sıkıştırma kodlamaları  |gzip, brotli |gzip |gzip, söndür, bzip2, brotli  |gzip, söndür, bzip2, brotli  |

## <a name="migration"></a>Geçiş

**Verizon'dan Azure CDN Standart** profilinden **Verizon’dan Azure CDN Premium** profiline geçme hakkında bilgi için bkz. [Verizon'dan Azure CDN Standart profilinden Verizon’dan Azure CDN Premium profiline geçiş yapma](cdn-migrate.md). 

> [!NOTE]
> Standart Verizon 'den Premium Verizon 'e yükseltme yolu var, şu anda diğer ürünler arasında dönüştürme mekanizması yoktur.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CDN](cdn-overview.md)hakkında daha fazla bilgi edinin.

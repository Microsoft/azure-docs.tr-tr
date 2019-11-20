---
title: Compare Azure Content Delivery Network (CDN) ürün özelliklerini karşılaştırın | Microsoft Docs
description: Her Azure Content Delivery Network (CDN) ürününün desteklediği özellikler hakkında bilgi edinin.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/28/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 6826c1340de16891933b290eb29bd4fb8d120974
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127913"
---
# <a name="compare-azure-cdn-product-features"></a>Azure CDN ürün özelliklerini karşılaştırın

Azure Content Delivery Network (CDN) dört ürün içerir: Azure CDN **Microsoft 'tan standart Azure CDN**, **Akamai**'ten standart, **Verizon 'den standart Azure CDN**ve **Verizon 'den Premium Azure CDN**. **Verizon'dan Azure CDN Standart** profilinden **Verizon’dan Azure CDN Premium** profiline geçme hakkında bilgi için bkz. [Verizon'dan Azure CDN Standart profilinden Verizon’dan Azure CDN Premium profiline geçiş yapma](cdn-migrate.md). Standart Verizon 'den Premium Verizon 'e yükseltme yolu olduğunu ancak şu anda diğer ürünler arasında dönüştürme mekanizması olmadığını unutmayın.

Aşağıdaki tabloda her ürünle birlikte sunulan özellikler karşılaştırılmaktadır.

| **Performans özellikleri ve iyileştirmeler** | **Standart Microsoft** | **Standart Akamai** | **Standart Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dinamik site hızlandırma](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | [Azure ön kapı hizmeti](https://docs.microsoft.com/azure/frontdoor/front-door-overview) ile sunulan | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamik site hızlandırma - uyarlamalı görüntü sıkıştırma](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamik site hızlandırma - nesneleri önceden getirme](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Genel web teslimi iyileştirmesi](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** , Ortalama dosya boyutunuz 10 MB 'den küçükse bu iyileştirme türünü seçin  | **&#x2713;** |  **&#x2713;** |
| [Video akışı iyileştirmesi](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | Genel Web teslimatı aracılığıyla | **&#x2713;**  | Genel Web teslimatı aracılığıyla |  Genel Web teslimatı aracılığıyla |
| [Büyük dosyaları iyileştirme](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | Genel Web teslimatı aracılığıyla | **&#x2713;** , Ortalama dosya boyutunuz 10 MB 'tan büyükse bu iyileştirme türünü seçin   | Genel Web teslimatı aracılığıyla |  Genel Web teslimatı aracılığıyla |
| İyileştirme türünü değiştir | |**&#x2713;** | | |
| Kaynak bağlantı noktası |Tüm TCP bağlantı noktaları |[İzin verilen kaynak bağlantı noktaları](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Tüm TCP bağlantı noktaları |Tüm TCP bağlantı noktaları |
| [Genel sunucu yük dengelemesi (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Hızlı temizleme](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , Tümünü Temizleme ve joker karakter Temizleme, şu anda Akamai öğesinden Azure CDN desteklenmez |**&#x2713;** |**&#x2713;** |
| [Varlık önceden yükleme](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Önbellek/üst bilgi ayarları ([önbelleğe alma](cdn-caching-rules.md) kullanılarak)  |  |**&#x2713;** |**&#x2713;** | |
| Özelleştirilebilir, kural tabanlı içerik teslim altyapısı ( [kurallar altyapısı](cdn-rules-engine.md)kullanılarak)  |  | | |**&#x2713;** |
| Önbellek/üstbilgi ayarları ([kurallar altyapısı](cdn-rules-engine.md) kullanılarak)  |  | | |**&#x2713;** |
| URL yeniden yönlendirme/yeniden yazma ( [kural altyapısını](cdn-rules-engine.md)kullanma)  |  | | |**&#x2713;** |
| Mobil cihaz kuralları ([kurallar altyapısı](cdn-rules-engine.md) kullanılarak)  |  | | |**&#x2713;** |
| [Sorgu dizesini önbelleğe alma](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 ikili yığını | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 desteği](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Güvenlik** | **Standart Microsoft** | **Standart Akamai** | **Standart Verizon** | **Premium Verizon** | 
| CDN uç noktasıyla HTTPS desteği | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Özel etki alanı HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , Etkinleştirmek için doğrudan CNAME gerekir |**&#x2713;** |**&#x2713;** |
| [Özel etki alanı adı desteği](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Coğrafi filtreleme](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Belirteç kimlik doğrulaması](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS koruması](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Kendi sertifikanızı getirme](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **Analiz ve raporlama** | **Standart Microsoft** | **Standart Akamai** | **Standart Verizon** | **Premium Verizon** | 
| [Azure tanılama günlükleri](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon'dan alınan çekirdek raporlar](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Verizon özel raporları](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Gelişmiş HTTP raporları](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Gerçek zamanlı istatistikler](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Uç düğümü performansı](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Gerçek zamanlı uyarılar](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Kullanım kolaylığı** | **Standart Microsoft** | **Standart Akamai** | **Standart Verizon** | **Premium Verizon** | 
| [Depolama](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) ve [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) gibi Azure hizmetleriyle kolay tümleştirme  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md), veya [PowerShell](cdn-manage-powershell.md) aracılığıyla yönetim  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Sıkıştırma MIME türleri](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Yalnızca varsayılan |Yapılandırılabilir |Yapılandırılabilir  |Yapılandırılabilir  |
| Sıkıştırma kodlamaları  |gzip, brotli |gzip |gzip, söndür, bzip2, brotili  |gzip, söndür, bzip2, brotili  |







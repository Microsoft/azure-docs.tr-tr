---
title: Azure Application Insights Web uygulaması Kullanıcı bekletmesini çözümleme
description: Uygulamanıza kaç Kullanıcı geri dönmeli?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 45f3a7152add8d64d961feb055413760df01eb94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025620"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Application Insights ile Web uygulamaları için Kullanıcı bekletme Analizi

[Azure Application Insights](./app-insights-overview.md) 'de bekletme özelliği, uygulamanıza kaç Kullanıcı dönmeli ve belirli görevleri ne sıklıkta gerçekleştirdikleri ve amaçları elde ettikleri hakkında analiz etmenize yardımcı olur. Örneğin, bir oyun sitesi çalıştırırsanız, kazandıktan sonra geri dönüş sayısı ile bir oyun kaybedildikten sonra siteye geri dönen Kullanıcı numaralarını karşılaştırabilirsiniz. Bu bilgi, hem Kullanıcı deneyiminizi hem de iş stratejinizi iyileştirmenize yardımcı olabilir.

## <a name="get-started"></a>başlarken

Application Insights portalında bekletme aracında verileri henüz görmüyorsanız, [kullanım araçlarını kullanmaya nasıl başlaleyeceğinizi öğrenin](usage-overview.md).

## <a name="the-retention-tool"></a>Bekletme aracı

![Elde tutma aracı](./media/usage-retention/retention.png)

1. Araç çubuğu, kullanıcıların yeni bekletme raporları oluşturmalarına, mevcut bekletme raporlarını açmasına, geçerli bekletme raporunu kaydetmesine veya farklı kaydetmeye, kaydedilen raporlarda yapılan değişiklikleri geri almasına, rapordaki verileri yenilemesine, e-posta veya doğrudan bağlantı aracılığıyla rapor paylaşmasını ve belge sayfasına erişmesini sağlar. 
2. Varsayılan olarak, saklama, daha sonra herhangi bir şeyi yapan tüm kullanıcıları gösterir ve bir süre içinde başka bir şey gerçekleştirdi. Belirli kullanıcı etkinliklerinde odağı daraltmak için farklı olay birleşimleri seçebilirsiniz.
3. Özelliklere bir veya daha fazla filtre ekleyin. Örneğin, belirli bir ülke veya bölgedeki kullanıcılara odaklanırsınız. Filtreleri ayarladıktan sonra **Güncelleştir** ' e tıklayın. 
4. Genel bekletme grafiğinde, seçilen dönemdeki Kullanıcı bekletmesinin bir özeti gösterilir. 
5. Kılavuz, #2 ' deki sorgu oluşturucusuna göre tutulan kullanıcı sayısını gösterir. Her satır, gösterilen süre içinde herhangi bir olayı gerçekleştiren kullanıcıların kohortu temsil eder. Satırdaki her hücrede, bu kohortu sonraki bir dönemde en az bir kez döndürüldüğü gösterilmektedir. Bazı kullanıcılar birden fazla dönemde geri dönebilir. 
6. Öngörüler kartları, kullanıcılara bekletme raporlarını daha iyi anlamak için en fazla beş başlatma olayı ve en fazla beş döndürülen olay gösterir. 

![Bekletme fare üzerine gelme](./media/usage-retention/hover.png)

Kullanıcılar, bir, hücrenin ne anlama geldiğini açıklayan analiz düğmesine ve araç ipuçlarına erişmek için bekletme aracında hücrelerin üzerine gelebilirler. Analiz düğmesi, kullanıcıları hücreden Kullanıcı oluşturmak için önceden doldurulmuş bir sorgu ile analiz aracına götürür. 

## <a name="use-business-events-to-track-retention&quot;></a>Saklama izlemek için iş olaylarını kullanma

En kullanışlı bekletme analizini sağlamak için önemli iş etkinliklerini temsil eden olayları ölçün. 

Örneğin, birçok kullanıcı, görüntülediği oyunu oynamadan uygulamanızdaki bir sayfayı açabilir. Bu nedenle, yalnızca sayfa görünümlerini izlemek, daha önce oynadıktan sonra oyunu çalmak için kaç kişinin geri döndürdüğü kesin bir tahmin sağlar. Oyuncu iadelerinin açık bir görüntüsünü almak için, bir kullanıcı aslında yürütüldüğünde uygulamanız özel bir olay göndermelidir.  

Önemli iş eylemlerini temsil eden özel olayları kodlayarak ve bunları bekletme analizinizi kullanacak şekilde kullanmak iyi bir uygulamadır. Oyunun sonucunu yakalamak için, Application Insights özel bir olay göndermek üzere bir kod satırı yazmanız gerekir. Web sayfası kodunda veya Node.JS yazarsanız, şöyle görünür:

```JavaScript
    appinsights.trackEvent(&quot;won game");
```

ASP.NET sunucu kodu:

```csharp
   telemetry.TrackEvent("won game");
```

[Özel olayları yazma hakkında daha fazla bilgi edinin](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Sonraki adımlar
- Kullanım deneyimlerini etkinleştirmek için [özel olaylar](./api-custom-events-metrics.md#trackevent) veya [sayfa görünümleri](./api-custom-events-metrics.md#page-views)göndermeye başlayın.
- Özel olayları veya sayfa görünümlerini zaten gönderirseniz, kullanıcıların hizmetinizi nasıl kullandığını öğrenmek için kullanım araçları ' nı araştırın.
    - [Kullanıcılar, Oturumlar, Etkinlikler](usage-segmentation.md)
    - [Huniler](usage-funnels.md)
    - [Kullanıcı Akışları](usage-flows.md)
    - [Çalışma Kitapları](../visualize/workbooks-overview.md)
    - [Kullanıcı bağlamı Ekle](./usage-overview.md)
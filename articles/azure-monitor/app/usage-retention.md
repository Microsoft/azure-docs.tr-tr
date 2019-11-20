---
title: Azure Application Insights Web uygulamaları için Kullanıcı bekletme Analizi | Microsoft docs
description: Uygulamanıza kaç Kullanıcı geri dönmeli?
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 5f5f6235354adc565815ac2eab0a1c774267102d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899412"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Application Insights ile Web uygulamaları için Kullanıcı bekletme Analizi

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 'de bekletme özelliği, uygulamanıza kaç Kullanıcı dönmeli ve belirli görevleri ne sıklıkta gerçekleştirdikleri ve amaçları elde ettikleri hakkında analiz etmenize yardımcı olur. Örneğin, bir oyun sitesi çalıştırırsanız, kazandıktan sonra geri dönüş sayısı ile bir oyun kaybedildikten sonra siteye geri dönen Kullanıcı numaralarını karşılaştırabilirsiniz. Bu bilgi, hem Kullanıcı deneyiminizi hem de iş stratejinizi iyileştirmenize yardımcı olabilir.

## <a name="get-started"></a>Kullanmaya Başlayın

Application Insights portalında bekletme aracında verileri henüz görmüyorsanız, [kullanım araçlarını kullanmaya nasıl başlaleyeceğinizi öğrenin](usage-overview.md).

## <a name="the-retention-tool"></a>Bekletme aracı

![Elde tutma aracı](./media/usage-retention/retention.png)

1. Araç çubuğu, kullanıcıların yeni bekletme raporları oluşturmalarına, var olan bekletme raporlarını açmasına, geçerli bekletme raporunu kaydetmesine veya farklı kaydetmeye, kaydedilen raporlarda yapılan değişiklikleri geri almasına, rapordaki verileri yenilemesine, raporu e-posta veya doğrudan bağlantı ile paylaşmaya ve belgelere erişmesine olanak sağlar sayfasında. 
2. Varsayılan olarak, saklama, daha sonra herhangi bir şeyi yapan tüm kullanıcıları gösterir ve bir süre içinde başka bir şey gerçekleştirdi. Belirli kullanıcı etkinliklerinde odağı daraltmak için farklı olay birleşimleri seçebilirsiniz.
3. Özelliklere bir veya daha fazla filtre ekleyin. Örneğin, belirli bir ülke veya bölgedeki kullanıcılara odaklanırsınız. Filtreleri ayarladıktan sonra **Güncelleştir** ' e tıklayın. 
4. Genel bekletme grafiğinde, seçilen dönemdeki Kullanıcı bekletmesinin bir özeti gösterilir. 
5. Kılavuz, #2 ' deki sorgu oluşturucusuna göre tutulan kullanıcı sayısını gösterir. Her satır, gösterilen süre içinde herhangi bir olayı gerçekleştiren kullanıcıların kohortu temsil eder. Satırdaki her hücrede, bu kohortu sonraki bir dönemde en az bir kez döndürüldüğü gösterilmektedir. Bazı kullanıcılar birden fazla dönemde geri dönebilir. 
6. Öngörüler kartları, kullanıcılara bekletme raporlarını daha iyi anlamak için en fazla beş başlatma olayı ve en fazla beş döndürülen olay gösterir. 

![Bekletme fare üzerine gelme](./media/usage-retention/hover.png)

Kullanıcılar, bir, hücrenin ne anlama geldiğini açıklayan analiz düğmesine ve araç ipuçlarına erişmek için bekletme aracında hücrelerin üzerine gelebilirler. Analiz düğmesi, kullanıcıları hücreden Kullanıcı oluşturmak için önceden doldurulmuş bir sorgu ile analiz aracına götürür. 

## <a name="use-business-events-to-track-retention"></a>Saklama izlemek için iş olaylarını kullanma

En kullanışlı bekletme analizini sağlamak için önemli iş etkinliklerini temsil eden olayları ölçün. 

Örneğin, birçok kullanıcı, görüntülediği oyunu oynamadan uygulamanızdaki bir sayfayı açabilir. Bu nedenle, yalnızca sayfa görünümlerini izlemek, daha önce oynadıktan sonra oyunu çalmak için kaç kişinin geri döndürdüğü kesin bir tahmin sağlar. Oyuncu iadelerinin açık bir görüntüsünü almak için, bir kullanıcı aslında yürütüldüğünde uygulamanız özel bir olay göndermelidir.  

Önemli iş eylemlerini temsil eden özel olayları kodlayarak ve bunları bekletme analizinizi kullanacak şekilde kullanmak iyi bir uygulamadır. Oyunun sonucunu yakalamak için, Application Insights özel bir olay göndermek üzere bir kod satırı yazmanız gerekir. Web sayfası koduna veya Node. JS ' ye yazarsanız, şöyle görünür:

```JavaScript
    appinsights.trackEvent("won game");
```

ASP.NET sunucu kodu:

```csharp
   telemetry.TrackEvent("won game");
```

[Özel olayları yazma hakkında daha fazla bilgi edinin](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Sonraki adımlar
- Kullanım deneyimlerini etkinleştirmek için [özel olaylar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) veya [sayfa görünümleri](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)göndermeye başlayın.
- Özel olayları veya sayfa görünümlerini zaten gönderirseniz, kullanıcıların hizmetinizi nasıl kullandığını öğrenmek için kullanım araçları ' nı araştırın.
    - [Kullanıcılar, Oturumlar, Etkinlikler](usage-segmentation.md)
    - [Huniler](usage-funnels.md)
    - [Kullanıcı Akışları](usage-flows.md)
    - [Çalışma kitapları](../../azure-monitor/app/usage-workbooks.md)
    - [Kullanıcı bağlamı Ekle](usage-send-user-context.md)



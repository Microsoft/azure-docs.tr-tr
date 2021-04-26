---
title: Azure Application Insights günlük tabanlı ve önceden toplanmış ölçümler | Microsoft Docs
description: Azure Application Insights günlük tabanlı ve önceden toplanmış ölçümleri kullanma
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: acbe535d740eb527d165be1675f31e759851a987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717834"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Application Insights’daki günlük tabanlı ve önceden toplanmış ölçümler

Bu makalede, günlükleri temel alan "geleneksel" Application Insights ölçümleri ve önceden toplanmış ölçümler arasındaki fark açıklanmaktadır. Her iki ölçüm türü de Application Insights kullanıcıları için kullanılabilir ve her biri, uygulama durumunu, tanılamayı ve analizlerini izlemek için benzersiz bir değer getirir. Uygulamaları seçen geliştiriciler, uygulamanın boyutuna, beklenen telemetri hacmine ve ölçüm duyarlılığı ve uyarı için iş gereksinimlerine bağlı olarak belirli bir senaryoya en uygun ölçüm türü olarak karar verebilir.

## <a name="log-based-metrics"></a>Günlük tabanlı ölçümler

Geçmişte, Application Insights içindeki uygulama izleme telemetri verileri modeli, istekler, özel durumlar, bağımlılık çağrıları, sayfa görünümleri vb. gibi önceden tanımlanmış çok sayıda olay türünü temel alır. Geliştiriciler SDK 'Yı kullanarak bu olayları el ile (SDK 'Yı açıkça çağıran kodu yazarak) veya otomatik izleme 'den otomatik olay koleksiyonuna güvenebilirler. Her iki durumda da Application Insights arka ucu toplanan tüm olayları günlük olarak depolar ve Azure portal Application Insights dikey pencereleri, olay tabanlı verileri günlüklere görselleştirmede analitik ve Tanılama aracı olarak davranır.

Tüm olayların bir kümesini sürdürmek için günlüklerin kullanılması harika analitik ve tanılama değeri getirebilir. Örneğin, bu çağrıları yapan farklı kullanıcı sayısı ile belirli bir URL 'ye yönelik isteklerin tam sayımını yapabilirsiniz. Ya da herhangi bir Kullanıcı oturumu için özel durumlar ve bağımlılık çağrıları da dahil olmak üzere ayrıntılı tanılama izlemeleri edinebilirsiniz. Bu tür bilgilerin olması, uygulama durumunun ve kullanımının görünürlüğünü önemli ölçüde iyileştirebiliyor ve bir uygulamayla ilgili sorunları tanılamak için gereken süreyi kesmeye izin verir.

Aynı zamanda, büyük bir telemetri hacmi üreten uygulamalar için, tüm olayların bir kümesini toplamak pratik (veya hatta imkansız) olabilir. Olay hacmi çok yüksek olduğunda Application Insights, toplanan ve depolanan olayların sayısını azaltan [örnekleme](./sampling.md) ve [filtreleme](./api-filtering-sampling.md) gibi çeşitli telemetri hacmi azaltma tekniklerini uygular. Ne yazık ki, depolanan olayların sayısını azaltmak, arka planda saklanan olayların sorgu süresi toplamaları gerçekleştirmelidir.

> [!NOTE]
> Application Insights, günlüklerde depolanan olayların ve ölçümlerin sorgu süresi toplamasını temel alan ölçümler günlük tabanlı ölçümler olarak adlandırılır. Bu ölçümler genellikle olay özelliklerinden çok sayıda boyuta sahiptir ve bunları analiz için üst düzey yapar, ancak bu ölçümlerin doğruluğu örnekleme ve filtreleme tarafından olumsuz etkilenir.

## <a name="pre-aggregated-metrics"></a>Önceden toplanmış ölçümler

Günlük tabanlı ölçümlere ek olarak, geç 2018 ' de Application Insights ekibi, zaman serisi için iyileştirilmiş özel bir depoda depolanan ölçümlerin genel bir önizlemesini sevk ediyor. Yeni ölçümler artık çok sayıda özelliği olan ayrı olaylar olarak tutulmayacaktır. Bunun yerine, önceden toplanmış zaman serisi olarak ve yalnızca anahtar boyutlarıyla depolanır. Bu, sorgu sırasında yeni ölçümlerin üstün olmasını sağlar: verilerin alınması çok daha hızlı gerçekleşir ve daha az işlem gücü gerektirir. Bu nedenle, ölçüm boyutları, daha fazla yanıt veren [panolar](./overview-dashboard.md)ve daha fazlası [hakkında neredeyse gerçek zamanlı uyarı](../alerts/alerts-metric-near-real-time.md)gibi yeni senaryolar sağlanır.

> [!IMPORTANT]
> Hem günlük tabanlı hem de önceden toplanmış ölçümler Application Insights. İkisini de ayırt etmek için, Application Insights UX ön toplanmış ölçümler artık "standart ölçümler (Önizleme)" olarak adlandırılmaktadır, ancak olaylar için geleneksel ölçümler "günlük tabanlı ölçümler" olarak yeniden adlandırıldı.

Daha yeni SDK 'lar (.NET için[Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK veya üzeri) koleksiyon sırasında ön toplama ölçümleri. Bu,  [Varsayılan olarak gönderilen standart ölçümler](../essentials/metrics-supported.md#microsoftinsightscomponents) için geçerlidir; bu nedenle doğruluk örnekleme veya filtrelemeden etkilenmez. Ayrıca, [GetMetric](./api-custom-events-metrics.md#getmetric) kullanılarak gönderilen özel ölçümler için de geçerlidir ve daha az veri alımı ve daha düşük maliyetle sonuçlanır.

Ön toplamayı uygulamayan SDK 'lar için (yani Application Insights SDK 'ların daha eski sürümleri veya tarayıcı araçları için) Application Insights arka ucu, Application Insights olay toplama uç noktası tarafından alınan olayları toplayarak yeni ölçümleri hala doldurur. Bu, kablo üzerinden iletilen azaltılan veri hacminin avantajlarından faydalanmadığında, önceden toplanmış ölçümleri kullanmaya devam edebilir ve koleksiyon sırasında ölçümleri önceden toplamayan SDK 'lar ile neredeyse gerçek zamanlı boyut uyarısı hakkında daha iyi performans ve destek elde edebilirsiniz.

Koleksiyon uç noktasının, uygulama ile birlikte kullandığınız SDK sürümünden bağımsız olarak önceden toplanmış ölçümlerin doğruluğunu hiçbir şekilde [etkilemeyeceği anlamına](./sampling.md) gelen, giriş bitiş noktası 'nın olayları önceden topladığından emin olmak önemlidir.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>SDK tarafından desteklenen önceden toplanmış ölçümler tablosu

| Geçerli üretim SDK 'Ları | Standart ölçümler (SDK ön toplaması) | Özel ölçümler (SDK ön toplaması olmadan) | Özel ölçümler (SDK ön toplaması ile)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core ve .NET Framework | Desteklenen (V 2.13.1 +)| [Trackmetric](api-custom-events-metrics.md#trackmetric) aracılığıyla desteklenir| [GetMetric](get-metric.md) aracılığıyla desteklenen (v 2.7.2 +) |
| Java                         | Desteklenmiyor       | [Trackmetric](api-custom-events-metrics.md#trackmetric) aracılığıyla desteklenir| Desteklenmiyor                           |
| Node.js                      | Desteklenmiyor       | [Trackmetric](api-custom-events-metrics.md#trackmetric) aracılığıyla desteklenir| Desteklenmiyor                           |
| Python                       | Desteklenmiyor       | Desteklenir                                 | [Opencensus. stats](opencensus-python.md#metrics) aracılığıyla kısmen destekleniyor |  

> [!NOTE]
>  OpenCensus. stats kullanılarak Python için ölçüm uygulama, GetMetric öğesinden farklıdır. Ayrıntılar için bkz. [Ölçümler hakkında Python belgeleri](./opencensus-python.md#metrics).

### <a name="codeless-supported-pre-aggregated-metrics-table"></a>Codeless desteklenen önceden toplanmış ölçümler tablosu

| Geçerli üretim SDK 'Ları | Standart ölçümler (SDK ön toplaması) | Özel ölçümler (SDK ön toplaması olmadan) | Özel ölçümler (SDK ön toplaması ile)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | Desteklenen <sup> 1<sup>    | Desteklenmiyor                             | Desteklenmiyor                           |
| ASP.NET Core            | Desteklenen <sup> 2<sup>    | Desteklenmiyor                             | Desteklenmiyor                           |
| Java                    | Desteklenmiyor            | Desteklenmiyor                             | [Desteklenir](java-in-process-agent.md#metrics) |
| Node.js                 | Desteklenmiyor            | Desteklenmiyor                             | Desteklenmiyor                           |

1. App Service ASP.net kodsuz kullanacaksınız Attach yalnızca "tam" izleme modunda ölçümleri yayar. App Service, VM/VMSS ve şirket içi ASP.net kodsuz kullanacaksınız iliştirme, Boyutlar olmadan standart ölçümleri yayar. Tüm boyutlar için SDK gereklidir.
2. ASP.NET Core kodsuz kullanacaksınız Attach App Service, Boyutlar olmadan standart ölçümleri yayar. Tüm boyutlar için SDK gereklidir.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Application Insights özel ölçümlerle ön toplamayı kullanma

Özel ölçümlerle ön toplamayı kullanabilirsiniz. İki ana avantaj, özel bir ölçümün boyutunu yapılandırma ve uyarma ve SDK 'dan Application Insights koleksiyon uç noktasına gönderilen veri hacmini azaltmaktır.

[APPLICATION INSIGHTS SDK 'dan özel ölçümler göndermenin birkaç yolu](./api-custom-events-metrics.md)vardır. SDK sürümünüz [GetMetric ve TrackValue](./api-custom-events-metrics.md#getmetric) yöntemlerini sunuyorsa, bu durumda yalnızca Azure 'da depolanan veri hacminin azaltılmasından ve ayrıca sdk 'dan Application Insights ' ye aktarılan veri hacminin değil, özel ölçümler göndermek için tercih edilen yöntem budur. Aksi takdirde, veri alımı sırasında ölçüm olaylarını ön toplayan [trackmetric](./api-custom-events-metrics.md#trackmetric)  yöntemini kullanın.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Özel Ölçüm boyutları ve ön toplama

[Trackmetric](./api-custom-events-metrics.md#trackmetric) veya [GetMetric ile trackvalue](./api-custom-events-metrics.md#getmetric) API çağrılarını kullanarak göndereceğiniz tüm ölçümler otomatik olarak hem günlüklerde hem de ölçüm depolarında depolanır. Ancak, özel ölçümünüzün günlük tabanlı sürümü her zaman tüm boyutları koruurken, ölçümün önceden toplanmış sürümü, varsayılan olarak herhangi bir boyut olmadan saklanır. "Özel ölçüm boyutlarında uyarı etkinleştir" seçeneğini işaretleyerek [kullanım ve tahmini maliyet](./pricing.md) sekmesinde özel ölçümlerin boyut toplanmasını etkinleştirebilirsiniz: 

![Kullanım ve tahmini maliyet](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Özel Ölçüm boyutlarının toplanması neden varsayılan olarak kapalı?

Özel Ölçüm boyutlarının toplanması, daha sonra boyutlarla birlikte özel ölçümleri depolamanın Application Insights göre ayrı olarak faturalandırılacağından, boyutlu olmayan özel ölçümlerin depolanması boş kalır (bir kotasına kadar). Resmi [fiyatlandırma sayfamızda](https://azure.microsoft.com/pricing/details/monitor/)yaklaşan fiyatlandırma modeli değişiklikleri hakkında bilgi edinebilirsiniz.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Grafik oluşturma ve günlük tabanlı ve standart önceden toplanmış ölçümleri keşfetme

Önceden toplanmış ve günlük tabanlı ölçülerden grafikler çizmek ve panoları grafiklerle yazmak için [Azure izleyici ölçüm Gezgini](../essentials/metrics-getting-started.md) kullanın. İstenen Application Insights kaynağını seçtikten sonra standart (Önizleme) ve günlük tabanlı ölçümler arasında geçiş yapmak için ad alanı seçicisini kullanın veya özel bir ölçüm ad alanı seçin:

![Ölçüm ad alanı](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Application Insights ölçümleri için fiyatlandırma modelleri

Günlük tabanlı veya ön toplanmış olan Application Insights ölçümleri, [burada](./pricing.md#pricing-model)açıklandığı gibi, alınan verilerin boyutuna göre maliyetler üretir. Tüm boyutları da dahil olmak üzere özel ölçümleriniz her zaman Application Insights log Store 'da depolanır; Ayrıca, özel ölçümlerinizin önceden toplanmış bir sürümü (hiçbir boyut olmadan) varsayılan olarak ölçüm deposuna iletilir.

Önceden toplanmış ölçümlerin tüm boyutlarını ölçüm deposunda depolamak için [özel ölçüm boyutlarında uyarı etkinleştir](#custom-metrics-dimensions-and-pre-aggregation) seçeneğini belirlediğinizde, [özel ölçüm fiyatlandırmasına](https://azure.microsoft.com/pricing/details/monitor/)göre **ek** maliyetler oluşturulabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Neredeyse gerçek zamanlı uyarı](../alerts/alerts-metric-near-real-time.md)
* [GetMetric ve TrackValue](./api-custom-events-metrics.md#getmetric)

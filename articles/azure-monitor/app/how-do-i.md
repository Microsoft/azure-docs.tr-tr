---
title: Nasıl yaparım?... Azure Application Insights | Microsoft Docs
description: Application Insights SSS.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/04/2017
ms.openlocfilehash: 61bd5898c494018a2bacbd894d4dc2aac97f53b4
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928411"
---
# <a name="how-do-i--in-application-insights"></a>Application Insights’ta nasıl ... yapabilirim?
## <a name="get-an-email-when-"></a>Şu durumlarda bir e-posta alın...
### <a name="email-if-my-site-goes-down"></a>Sitemin kapanması durumunda e-posta
Bir [kullanılabilirlik Web testi](../../azure-monitor/app/monitor-web-app-availability.md)ayarlayın.

### <a name="email-if-my-site-is-overloaded"></a>Sitemin aşırı yüklenmiş olması durumunda e-posta
**Sunucu yanıt süresi**üzerinde bir [Uyarı](../../azure-monitor/app/alerts.md) ayarlayın. 1 ile 2 saniye arasında bir eşik çalışmalıdır.

![](./media/how-do-i/030-server.png)

Uygulamanız hata kodları döndürerek zorlamaların işaretlerini de gösterebilir. **Başarısız isteklerde**bir uyarı ayarlayın.

**Sunucu özel durumları**üzerinde bir uyarı ayarlamak isterseniz, verileri görmek için [bazı ek kurulum](../../azure-monitor/app/asp-net-exceptions.md) yapmanız gerekebilir.

### <a name="email-on-exceptions"></a>Özel durumlarla ilgili e-posta
1. [Özel durum izlemeyi ayarlama](../../azure-monitor/app/asp-net-exceptions.md)
2. Özel durum sayısı ölçümünde [bir uyarı ayarlayın](../../azure-monitor/app/alerts.md)

### <a name="email-on-an-event-in-my-app"></a>Uygulamamda bir olay üzerinde e-posta
Belirli bir olay gerçekleştiğinde bir e-posta almak istediğinizi varsayalım. Application Insights bu özelliği doğrudan sağlamaz, ancak bir [ölçüm bir eşiği aştığında uyarı gönderebilir](../../azure-monitor/app/alerts.md).

Özel etkinliklerde değil, [özel ölçümler](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)üzerinde de uyarı ayarlanabilir. Olay gerçekleştiğinde bir ölçümü artırmak için kod yazın:

    telemetry.TrackMetric("Alarm", 10);

Veya

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Uyarıların iki durumu olduğu için, uyarıyı sonlandırdığınızda düşük bir değer göndermeniz gerekir:

    telemetry.TrackMetric("Alarm", 0.5);

Alarmlarınızı görmek için [ölçüm Gezgininde](../../azure-monitor/app/metrics-explorer.md) bir grafik oluşturun:

![](./media/how-do-i/010-alarm.png)

Şimdi, ölçüm kısa bir süre için bir orta değerin üzerine gittiğinde harekete geçmek üzere bir uyarı ayarlayın:

![](./media/how-do-i/020-threshold.png)

Ortalama süreyi minimum olarak ayarlayın.

Hem ölçüm hem de eşiğin altına gittiğinde e-posta alacaksınız.

Dikkat edilmesi gereken bazı noktalar:

* Bir uyarının iki durumu vardır ("uyarı" ve "sağlıklı"). Durum yalnızca bir ölçüm alındığında değerlendirilir.
* Yalnızca durum değiştiğinde bir e-posta gönderilir. Bu nedenle hem yüksek hem de düşük değerli ölçümleri göndermeniz gerekir.
* Uyarıyı değerlendirmek için, önceki dönem içindeki alınan değerlerin ortalaması alınır. Bu, bir ölçüm her alındığında oluşur, böylece e-postalar ayarladığınız dönemden daha sık gönderilebilir.
* E-postalar hem "uyarı" hem de "sağlıklı" olarak gönderildiğinden, tek kararlı olayınızı iki durumlu bir koşul olarak yeniden düşünmek isteyebilirsiniz. Örneğin, bir "iş tamamlandı" olayı yerine bir işin başlangıcında ve sonunda e-posta aldığınız "devam eden iş" koşulu vardır.

### <a name="set-up-alerts-automatically"></a>Uyarıları otomatik olarak ayarlama
[PowerShell kullanarak yeni uyarılar oluşturma](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Application Insights yönetmek için PowerShell 'i kullanma
* [Yeni kaynaklar oluşturma](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Yeni uyarılar oluşturma](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Farklı sürümlerden telemetri ayırın

* Bir uygulamada birden çok rol: tek bir Application Insights kaynağı kullanın ve [cloud_Rolename](../../azure-monitor/app/app-map.md)filtreleyin.
* Geliştirme, test ve yayın sürümlerini ayırma: farklı Application Insights kaynakları kullanın. Web. config dosyasından izleme anahtarlarını seçin. [Daha fazla bilgi](../../azure-monitor/app/separate-resources.md)
* Derleme sürümlerini raporlama: telemetri başlatıcısı kullanarak özellik ekleme. [Daha fazla bilgi](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Arka uç sunucularını ve masaüstü uygulamalarını izleme
[Windows Server SDK modülünü kullanın](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Verileri görselleştirme
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Birden çok uygulama ölçümlerinden oluşan Pano
* [Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md)' nde, grafiğinizi özelleştirin ve sık kullanılan olarak kaydedin. Azure panosuna sabitleyin.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Diğer kaynaklardaki ve Application Insights verileri içeren Pano
* [Telemetriyi Power BI dışa aktarın](../../azure-monitor/app/export-power-bi.md ).

Veya

* SharePoint Web bölümlerinde verileri görüntüleyerek pano olarak SharePoint 'i kullanın. [SQL 'e aktarmak için sürekli dışarı aktarma ve Stream Analytics kullanın](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  PowerView kullanarak veritabanını inceleyin ve PowerView için bir SharePoint Web bölümü oluşturun.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Anonim veya kimliği doğrulanmış kullanıcıları filtreleme
Kullanıcılarınız oturum açtığında, [kimliği doğrulanmış kullanıcı kimliğini](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)ayarlayabilirsiniz. (Otomatik olarak gerçekleşmez.)

Daha sonra şunları yapabilirsiniz:

* Belirli kullanıcı kimliklerinde ara

![](./media/how-do-i/110-search.png)

* Ölçümleri anonim veya kimliği doğrulanmış kullanıcılara göre filtreleyin

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Özellik adlarını veya değerlerini değiştirme
[Filtre](../../azure-monitor/app/api-filtering-sampling.md#filtering)oluşturun. Bu, Application Insights ' ye uygulamadan gönderilmeden önce Telemetriyi değiştirmenize veya filtrelemenize olanak tanır.

## <a name="list-specific-users-and-their-usage"></a>Belirli kullanıcıları ve bunların kullanımını listeleme
Yalnızca [belirli kullanıcıları aramak](#search-specific-users)istiyorsanız [KIMLIĞI doğrulanmış kullanıcı kimliğini](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)ayarlayabilirsiniz.

Hangi sayfaların baktığınıza veya ne sıklıkla oturum açtıklarında, verileri içeren bir kullanıcı listesi istiyorsanız iki seçeneğiniz vardır:

* [Kimliği doğrulanmış kullanıcı kimliğini ayarlayın](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [bir veritabanına aktarın](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) ve kullanıcı verilerinizi burada analiz etmek için uygun araçları kullanın.
* Yalnızca az sayıda kullanıcınız varsa, ölçüm değeri veya olay adı olarak ilgilendiğiniz verileri kullanarak özel olayları ve ölçümleri gönderin, ve kullanıcı KIMLIĞINI bir özellik olarak ayarlama. Sayfa görünümlerini çözümlemek için standart JavaScript trackPageView çağrısını değiştirin. Sunucu tarafı Telemetriyi çözümlemek için, kullanıcı KIMLIĞINI tüm sunucu telemetrisine eklemek üzere bir telemetri başlatıcısı kullanın. Daha sonra Kullanıcı KIMLIĞINDE ölçümleri ve aramaları filtreleyebilir ve segmentleyebilirsiniz.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Uygulamamın trafiğini Application Insights için azaltma
* [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)dosyasında, gerek olmayan tüm modülleri (performans sayacı toplayıcısı gibi) devre dışı bırakın.
* SDK 'da [örnekleme ve filtrelemeyi](../../azure-monitor/app/api-filtering-sampling.md) kullanın.
* Web sayfalarınızda, her sayfa görünümü için raporlanan Ajax çağrılarının sayısını sınırlayın. Kod parçacığında `instrumentationKey:...` sonrasında: `,maxAjaxCallsPerView:3` (veya uygun bir sayı) ekleyin.
* [Trackmetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)kullanıyorsanız, sonucu göndermeden önce ölçüm değerleri toplu iş toplamını hesaplayın. Bunun için sağlayan TrackMetric () aşırı yüklemesi vardır.

[Fiyatlandırma ve Kotalar](../../azure-monitor/app/pricing.md)hakkında daha fazla bilgi edinin.

## <a name="disable-telemetry"></a>Telemetrisi devre dışı bırak
Sunucudan telemetri toplamayı ve iletimini **dinamik olarak durdurmak ve başlatmak** için:

### <a name="aspnet-classic-applications"></a>ASP.NET klasik uygulamalar

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Diğer uygulamalar
Konsol veya ASP.NET Core uygulamalarında `TelemetryConfiguration.Active` Singleton kullanılması önerilmez.
`TelemetryConfiguration` örneği kendiniz oluşturduysanız `DisableTelemetry` `true`olarak ayarlayın.

ASP.NET Core uygulamalar için, [ASP.NET Core bağımlılık ekleme](/aspnet/core/fundamentals/dependency-injection/)kullanarak `TelemetryConfiguration` örneğe erişebilirsiniz. Lütfen [ASP.NET Core uygulamalar Için ApplicationInsights](../../azure-monitor/app/asp-net-core.md) makalesinde daha fazla ayrıntı bulabilirsiniz.

## <a name="disable-selected-standard-collectors"></a>Seçili standart toplayıcıyı devre dışı bırak
Standart toplayıcıları devre dışı bırakabilirsiniz (örneğin, performans sayaçları, HTTP istekleri veya bağımlılıklar)

* **ASP.NET uygulamaları** - [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) dosyasında ilgili satırları silin veya açıklama alın
* **ASP.NET Core uygulamalar** - [ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules) telemetri modülleri yapılandırma seçeneklerini izleyin

## <a name="view-system-performance-counters"></a>Sistem performans sayaçlarını görüntüleme
Ölçüm Gezgini 'nde gösterebilmeniz için ölçümler arasında bir sistem performans sayaçları kümesi vardır. Bunlardan birkaçını görüntüleyen **sunucular** başlıklı önceden tanımlanmış bir dikey pencere vardır.

![Application Insights kaynağınızı açın ve sunucular ' a tıklayın.](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Performans sayacı verisi görmüyorsanız
* Kendi makinenizde veya bir VM 'de **IIS sunucusu** . [Durum İzleyicisi 'ı yükler](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure Web sitesi** -henüz performans sayaçlarını desteklemiyoruz. Azure Web sitesi Denetim Masası 'nın standart bir parçası olarak alabileceğiniz birkaç ölçüm vardır.
* **UNIX Server** - [yüklemesi toplanan](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Daha fazla performans sayacı görüntüleme
* İlk olarak, [Yeni bir grafik ekleyin](../../azure-monitor/app/metrics-explorer.md) ve sayacın sunduğumuz temel bir küme içinde olup olmadığını görün.
* Aksi takdirde, [sayacı performans sayacı modülü tarafından toplanan kümesine ekleyin](../../azure-monitor/app/performance-counters.md).

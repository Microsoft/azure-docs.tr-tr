---
title: Nasıl yaparım?... Azure Application Insights | Microsoft Docs
description: Application Insights SSS.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 74a4d7ee65dccead132cfcebd9bf8c0de9b761a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584165"
---
# <a name="how-do-i--in-application-insights"></a>Application Insights’ta nasıl ... yapabilirim?
## <a name="get-an-email-when-"></a>Şu durumlarda bir e-posta alın...
### <a name="email-if-my-site-goes-down"></a>Sitemin kapanması durumunda e-posta
Bir [kullanılabilirlik Web testi](./monitor-web-app-availability.md)ayarlayın.

### <a name="email-if-my-site-is-overloaded"></a>Sitemin aşırı yüklenmiş olması durumunda e-posta
**Sunucu yanıt süresi** üzerinde bir [Uyarı](../alerts/alerts-log.md) ayarlayın. 1 ile 2 saniye arasında bir eşik çalışmalıdır.

![Sunucu yanıt süresi üzerinde bir uyarının nasıl ayarlanacağını gösteren ekran görüntüsü.](./media/how-do-i/030-server.png)

Uygulamanız hata kodları döndürerek zorlamaların işaretlerini de gösterebilir. **Başarısız isteklerde** bir uyarı ayarlayın.

**Sunucu özel durumları** üzerinde bir uyarı ayarlamak isterseniz, verileri görmek için [bazı ek kurulum](./asp-net-exceptions.md) yapmanız gerekebilir.

### <a name="email-on-exceptions"></a>Özel durumlarla ilgili e-posta
1. [Özel durum izlemeyi ayarlama](./asp-net-exceptions.md)
2. Özel durum sayısı ölçümünde [bir uyarı ayarlayın](../alerts/alerts-log.md)

### <a name="email-on-an-event-in-my-app"></a>Uygulamamda bir olay üzerinde e-posta
Belirli bir olay gerçekleştiğinde bir e-posta almak istediğinizi varsayalım. Application Insights bu özelliği doğrudan sağlamaz, ancak bir [ölçüm bir eşiği aştığında uyarı gönderebilir](../alerts/alerts-log.md).

Özel etkinliklerde değil, [özel ölçümler](./api-custom-events-metrics.md#trackmetric)üzerinde de uyarı ayarlanabilir. Olay gerçekleştiğinde bir ölçümü artırmak için kod yazın:

```csharp
telemetry.TrackMetric("Alarm", 10);
```

veya:

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

Uyarıların iki durumu olduğu için, uyarıyı sonlandırdığınızda düşük bir değer göndermeniz gerekir:

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

Alarmlarınızı görmek için [ölçüm Gezgininde](../essentials/metrics-charts.md) bir grafik oluşturun:

![Alarmlarınızı görmek için ölçüm Gezgininde bir grafik oluşturmayı gösteren ekran görüntüsü.](./media/how-do-i/010-alarm.png)

Şimdi, ölçüm kısa bir süre için bir orta değerin üzerine gittiğinde harekete geçmek üzere bir uyarı ayarlayın:

![Ölçüm, kısa bir dönem için orta değerin üzerine gittiğinde bir uyarının tetiklenmesi için nasıl ayarlanacağını gösteren ekran görüntüsü.](./media/how-do-i/020-threshold.png)

Ortalama süreyi minimum olarak ayarlayın.

Hem ölçüm hem de eşiğin altına gittiğinde e-posta alacaksınız.

Dikkat edilmesi gereken bazı noktalar:

* Bir uyarının iki durumu vardır ("uyarı&quot; ve &quot;sağlıklı"). Durum yalnızca bir ölçüm alındığında değerlendirilir.
* Yalnızca durum değiştiğinde bir e-posta gönderilir. Bu nedenle hem yüksek hem de düşük değerli ölçümleri göndermeniz gerekir.
* Uyarıyı değerlendirmek için, önceki dönem içindeki alınan değerlerin ortalaması alınır. Bu, bir ölçüm her alındığında oluşur, böylece e-postalar ayarladığınız dönemden daha sık gönderilebilir.
* E-postalar hem "uyarı" hem de "sağlıklı" olarak gönderildiğinden, tek kararlı olayınızı iki durumlu bir koşul olarak yeniden düşünmek isteyebilirsiniz. Örneğin, bir "iş tamamlandı" olayı yerine bir işin başlangıcında ve sonunda e-posta aldığınız "devam eden iş" koşulu vardır.

### <a name="set-up-alerts-automatically"></a>Uyarıları otomatik olarak ayarlama
[PowerShell kullanarak yeni uyarılar oluşturma](../alerts/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>Application Insights yönetmek için PowerShell 'i kullanma
* [Yeni kaynaklar oluşturma](./create-new-resource.md#creating-a-resource-automatically)
* [Yeni uyarılar oluşturma](../alerts/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>Farklı sürümlerden telemetri ayırın

* Bir uygulamada birden çok rol: tek bir Application Insights kaynağı kullanın ve [cloud_Rolename](./app-map.md)filtreleyin.
* Geliştirme, test ve yayın sürümlerini ayırma: farklı Application Insights kaynakları kullanın. web.config izleme anahtarlarını seçin. [Daha fazla bilgi](./separate-resources.md)
* Derleme sürümlerini raporlama: telemetri başlatıcısı kullanarak özellik ekleme. [Daha fazla bilgi edinin](./separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Arka uç sunucularını ve masaüstü uygulamalarını izleme
[Windows Server SDK modülünü kullanın](./windows-desktop.md).

## <a name="visualize-data"></a>Verileri görselleştirme
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Birden çok uygulama ölçümlerinden oluşan Pano
* [Ölçüm Gezgini](../essentials/metrics-charts.md)' nde, grafiğinizi özelleştirin ve sık kullanılan olarak kaydedin. Azure panosuna sabitleyin.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Diğer kaynaklardaki ve Application Insights verileri içeren Pano
* [Telemetriyi Power BI dışa aktarın](./export-power-bi.md).

Veya

* SharePoint Web bölümlerinde verileri görüntüleyerek pano olarak SharePoint 'i kullanın. [SQL 'e aktarmak için sürekli dışarı aktarma ve Stream Analytics kullanın](./code-sample-export-sql-stream-analytics.md).  PowerView kullanarak veritabanını inceleyin ve PowerView için bir SharePoint Web bölümü oluşturun.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Anonim veya kimliği doğrulanmış kullanıcıları filtreleme
Kullanıcılarınız oturum açtığında, [kimliği doğrulanmış kullanıcı kimliğini](./api-custom-events-metrics.md#authenticated-users)ayarlayabilirsiniz. (Otomatik olarak gerçekleşmez.)

Ardından şunları yapabilirsiniz:

* Belirli kullanıcı kimliklerinde ara

![Belirli kullanıcı kimliklerinde Searchin seçeneklerini gösteren ekran görüntüsü.](./media/how-do-i/110-search.png)

* Ölçümleri anonim veya kimliği doğrulanmış kullanıcılara göre filtreleyin

![Anonim veya kimliği doğrulanmış kullanıcılar için metrixs filtrelemesinin gösterildiği ekran görüntüsü.](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Özellik adlarını veya değerlerini değiştirme
[Filtre](./api-filtering-sampling.md#filtering)oluşturun. Bu, Application Insights ' ye uygulamadan gönderilmeden önce Telemetriyi değiştirmenize veya filtrelemenize olanak tanır.

## <a name="list-specific-users-and-their-usage"></a>Belirli kullanıcıları ve bunların kullanımını listeleme
Yalnızca [belirli kullanıcıları aramak](#search-specific-users)istiyorsanız [KIMLIĞI doğrulanmış kullanıcı kimliğini](./api-custom-events-metrics.md#authenticated-users)ayarlayabilirsiniz.

Hangi sayfaların baktığınıza veya ne sıklıkla oturum açtıklarında, verileri içeren bir kullanıcı listesi istiyorsanız iki seçeneğiniz vardır:

* [Kimliği doğrulanmış kullanıcı kimliğini ayarlayın](./api-custom-events-metrics.md#authenticated-users), [bir veritabanına aktarın](./code-sample-export-sql-stream-analytics.md) ve kullanıcı verilerinizi burada analiz etmek için uygun araçları kullanın.
* Yalnızca az sayıda kullanıcınız varsa, ölçüm değeri veya olay adı olarak ilgilendiğiniz verileri kullanarak özel olayları ve ölçümleri gönderin, ve kullanıcı KIMLIĞINI bir özellik olarak ayarlama. Sayfa görünümlerini çözümlemek için standart JavaScript trackPageView çağrısını değiştirin. Sunucu tarafı Telemetriyi çözümlemek için, kullanıcı KIMLIĞINI tüm sunucu telemetrisine eklemek üzere bir telemetri başlatıcısı kullanın. Daha sonra Kullanıcı KIMLIĞINDE ölçümleri ve aramaları filtreleyebilir ve segmentleyebilirsiniz.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Uygulamamın trafiğini Application Insights için azaltma
* [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)' de, gerek olmayan tüm modülleri (performans sayacı toplayıcısı gibi) devre dışı bırakın.
* SDK 'da [örnekleme ve filtrelemeyi](./api-filtering-sampling.md) kullanın.
* Web sayfalarınızda, her sayfa görünümü için raporlanan Ajax çağrılarının sayısını sınırlayın. Sonrasında betik kod parçacığında `instrumentationKey:...` , şunu ekleyin: `,maxAjaxCallsPerView:3` (veya uygun bir sayı).
* [Trackmetric](./api-custom-events-metrics.md#trackmetric)kullanıyorsanız, sonucu göndermeden önce ölçüm değerleri toplu iş toplamını hesaplayın. Bunun için sağlayan TrackMetric () aşırı yüklemesi vardır.

[Fiyatlandırma ve Kotalar](./pricing.md)hakkında daha fazla bilgi edinin.

## <a name="disable-telemetry"></a>Telemetrisi devre dışı bırak
Sunucudan telemetri toplamayı ve iletimini **dinamik olarak durdurmak ve başlatmak** için:

### <a name="aspnet-classic-applications"></a>ASP.NET klasik uygulamalar

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Diğer uygulamalar
`TelemetryConfiguration.Active`Tek bir konsolun veya ASP.NET Core uygulamaların kullanılması önerilmez.
`TelemetryConfiguration`örneği kendiniz oluşturduysanız- `DisableTelemetry` olarak ayarlayın `true` .

ASP.NET Core uygulamalar için, `TelemetryConfiguration` [ASP.NET Core bağımlılığı ekleme](/aspnet/core/fundamentals/dependency-injection/)kullanarak örneğe erişebilirsiniz. Lütfen [ASP.NET Core uygulamalar Için ApplicationInsights](./asp-net-core.md) makalesinde daha fazla ayrıntı bulabilirsiniz.

## <a name="disable-selected-standard-collectors"></a>Seçili standart toplayıcıyı devre dışı bırak
Standart toplayıcıları devre dışı bırakabilirsiniz (örneğin, performans sayaçları, HTTP istekleri veya bağımlılıklar)

* **ASP.NET uygulamalar** - [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) ilgili satırları silin veya not edin
* **ASP.NET Core uygulamalar** - [ApplicationInsights ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules) telemetri modülleri yapılandırma seçeneklerini izleyin

## <a name="view-system-performance-counters"></a>Sistem performans sayaçlarını görüntüleme
Ölçüm Gezgini 'nde gösterebilmeniz için ölçümler arasında bir sistem performans sayaçları kümesi vardır. Bunlardan birkaçını görüntüleyen **sunucular** başlıklı önceden tanımlanmış bir dikey pencere vardır.

![Application Insights kaynağınızı açın ve sunucular ' a tıklayın.](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Performans sayacı verisi görmüyorsanız
* Kendi makinenizde veya bir VM 'de **IIS sunucusu** . [Durum İzleyicisi 'ı yükler](./monitor-performance-live-website-now.md).
* **Azure Web sitesi** -henüz performans sayaçlarını desteklemiyoruz. Azure Web sitesi Denetim Masası 'nın standart bir parçası olarak alabileceğiniz birkaç ölçüm vardır.
* **UNIX sunucusu**  -  [Toplanan 'yi yükler](./java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Daha fazla performans sayacı görüntüleme
* İlk olarak, [Yeni bir grafik ekleyin](../essentials/metrics-charts.md) ve sayacın sunduğumuz temel bir küme içinde olup olmadığını görün.
* Aksi takdirde, [sayacı performans sayacı modülü tarafından toplanan kümesine ekleyin](./performance-counters.md).


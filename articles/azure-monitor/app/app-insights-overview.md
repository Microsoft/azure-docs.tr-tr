---
title: Azure Application Insights nedir? | Microsoft Belgeleri
description: Uygulama Performansı Yönetimi ve canlı web uygulamanızın kullanımını izleme.  Sorunları algılayın, önceliklendirin ve tanılayın; kullanıcıların uygulamanızı nasıl kullandığını anlayın.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: f25a53cb227d6894aaabaf82033178f15f61c173
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076216"
---
# <a name="what-is-application-insights"></a>Application Insights nedir?
[Azure izleyici](../overview.md)'nin bir özelliği olan Application Insights, geliştiriciler ve DevOps uzmanları için genişletilebilir bir uygulama performans YÖNETIMI (APM) hizmetidir. Canlı uygulamalarınızı izlemek için bu uygulamayı kullanın. Performans sorunlarını otomatik olarak algılar ve sorunları tanılamanıza ve hangi kullanıcıların uygulamanızla gerçekten ne yaptığını anlamanıza yardımcı olacak güçlü analiz araçları içerir.  Performansı ve kullanılabilirliği sürekli geliştirmenize yardımcı olmak amacıyla tasarlanmıştır. Şirket içi, hibrit veya herhangi bir genel bulut üzerinde barındırılan .NET, Node.js, Java ve Python gibi çok çeşitli platformlarda uygulamalar için geçerlidir. DevOps işleminizle tümleştirilir ve çeşitli geliştirme araçlarıyla bağlantı noktaları vardır. Visual Studio App Center ile tümleştirerek mobil uygulamalardan telemetriyi izleyebilir ve çözümleyebilir.

## <a name="how-does-application-insights-work"></a>Application Insights nasıl çalışır?
Uygulamanızda küçük bir izleme paketi (SDK) yüklersiniz veya [desteklenmiş](./platforms.md)olduğunda Application Insights Aracısı 'nı kullanarak Application Insights etkinleştirebilirsiniz. İzleme, uygulamanızı izler ve bir Izleme anahtarı olarak başvurduğumuz benzersiz bir GUID kullanarak telemetri verilerini bir Azure Application Insights kaynağına yönlendirir.

Yalnızca web hizmeti uygulamasını değil, tüm arka plan bileşenlerini ve web sayfalarının kendisindeki JavaScript’i de izleyebilirsiniz. Uygulama ve bileşenleri, her yerde çalışabilir; Azure 'da barındırılması gerekmez.

![Uygulamanızdaki Application Insights izleme aracı, Application Insights kaynağınıza telemetri gönderir.](./media/app-insights-overview/diagram.png)

Buna ek olarak performans sayaçları, Azure tanılama veya Docker günlükleri gibi konak ortamlarından da telemetri çekebilirsiniz. Web hizmetinize düzenli aralıklarla yapay istekler gönderen web testleri de ayarlayabilirsiniz.

Bu telemetri akışları, Azure İzleyici ile tümleştirilir. Azure portalında ham veriler üzerinde güçlü analiz ve arama araçlarını kullanabilirsiniz.

### <a name="whats-the-overhead"></a>Ne kadar ek yük getirir?
Uygulamanızın performansı üzerindeki etkisi küçüktür. İzleme çağrıları engelleyici değildir ve toplanarak ayrı bir iş parçacığında gönderilir.

## <a name="what-does-application-insights-monitor"></a>Application Insights neleri izler?

Geliştirme takımına yönelik olan Application Insights, uygulamanızın performansını ve nasıl kullanıldığını anlamanıza yardımcı olur. Şunları izler:

* **İstek oranları, yanıt süreleri ve hata oranları**: Hangi sayfaların günün hangi saatlerinde popüler olduğunu ve kullanıcılarınızın konumunu öğrenin. En iyi performansı hangi sayfaların gösterdiğini görün. Daha fazla istek olduğunda yanıt süreleriniz ve hata oranlarınız yükseliyorsa bir kaynak atama sorununuz olabilir. 
* **Bağımlılık oranları, yanıt süreleri ve hata oranları**: Dış hizmetlerin sizi yavaşlatıp yavaşlatmadığını öğrenin.
* **Özel durumlar** -toplu istatistikleri analiz edin veya belirli örnekleri seçip yığın izlemenin ve ilgili isteklerin detayına gidin. Hem sunucu hem de tarayıcı özel durumları raporlanır.
* **Sayfa görüntüleme sayısı ve yükleme performansı**: Kullanıcılarınızın tarayıcıları tarafından gerçekleştirilir.
* Web sayfalarından **AJAX çağrıları**: Oranlar, yanıt süreleri ve hata oranları.
* **Kullanıcı ve oturum sayıları**.
* Windows veya Linux sunucu makinelerinizden CPU, bellek ve ağ kullanımı gibi **performans sayaçları**. 
* Docker veya Azure’dan **konak tanılama**. 
* Uygulamanızdan **tanılama izleme günlükleri**: İzleme olayları ile istekler arasında bağıntı kurmanıza imkan tanır.
* Satılan öğeler ya da kazanılan maçlar gibi iş olaylarını izlemek için istemcide ya da sunucu kodunda kendi yazdığınız **özel olaylar ve ölçümler**.

## <a name="where-do-i-see-my-telemetry"></a>Telemetrimi nerede görebilirim?

Verilerinizi keşfetmenin birçok yolu vardır. Aşağıdaki makaleleri inceleyin:

| Makale açıklaması   | Görüntü |
| --- | --- |
| [**Akıllı algılama ve el ile uyarılar**](./proactive-diagnostics.md)<br/>Uygulamanızın normal telemetri desenlerine uyarlanabilen otomatik uyarıları ayarlayın ve normal desen dışında bir şey olduğunda tetikleyin. Belirli özel veya standart ölçüm düzeylerinde de [uyarılar](../alerts/alerts-log.md) ayarlayabilirsiniz. |![Uyarı örneği](./media/app-insights-overview/alerts-tn.png) |
| [**Uygulama eşlemesi**](./app-map.md)<br/>Ana ölçümler ve uyarılarla uygulamanızın bileşenlerini araştırma. |![Uygulama haritası](./media/app-insights-overview/appmap-tn.png)  |
| [**Profil Oluşturucu**](./profiler.md)<br/>Örnek isteklerinin yürütme profillerini inceleyin. |![Ekran yakalama, Örneklenmiş isteklerin yürütme profillerini gösterir.](./media/app-insights-overview/profiler.png) |
| [**Kullanım Analizi**](./usage-overview.md)<br/>Kullanıcıların segmentlere nasıl ayrıldığını ve nasıl elde tutulduğunu çözümleyin.|![Elde tutma aracı](./media/app-insights-overview/retention.png) |
| [**Örnek verileri için tanılama arama**](./diagnostic-search.md)<br/>İstekler, özel durumlar, bağımlılık çağrıları, günlük izlemeleri ve sayfa görüntülemeleri gibi olaylarda arama yapın ve bunları filtreleyin.  |![Telemetri arama](./media/app-insights-overview/search-tn.png) |
| [**Toplu veriler için Ölçüm Gezgini**](../essentials/metrics-charts.md)<br/>İstek, hata ve özel durum oranları; yanıt süreleri, sayfa yükleme süreleri gibi toplu verileri keşfedin, filtreleyin ve bölümlere ayırın. |![Ölçümler](./media/app-insights-overview/metrics-tn.png) |
| [**Panolar**](./overview-dashboard.md)<br/>Birden çok kaynaktan toplanan verileri birleştirin ve başkalarıyla paylaşın. Çok bileşenli uygulamalar ve takım odasında sürekli görüntüleme için idealdir. |![Pano örneği](./media/app-insights-overview/dashboard-tn.png) |
| [**Canlı Ölçüm Akışı**](./live-stream.md)<br/>Yeni bir derleme dağıttığınızda, her şeyin beklendiği gibi çalıştığından emin olmak için bu neredeyse gerçek zamanlı performans göstergelerini izleyin. |![Canlı ölçüm örneği](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analiz**](../logs/log-query-overview.md)<br/>Bu güçlü sorgulama dilini kullanarak uygulamanızın performansı ve kullanımıyla ilgili zor soruları yanıtlayın. |![Analiz örneği](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Koddaki performans verilerini görün. Yığın izlemelerinden koda gidin.|![Ekran görüntüsü, Visual Studio 'daki özel durum ayrıntılarını ve yığın izlemelerinden koda giden bir örneği gösterir.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Anlık görüntü hata ayıklayıcısı**](./snapshot-debugger.md)<br/>Dinamik işlemlerden örneklenen anlık görüntülerdeki hataları parametre değerleriyle ayıklayın.|![Visual studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Kullanım ölçümlerini diğer iş zekası verileriyle tümleştirin.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Ölçümleriniz ve ham verileriniz üzerinde sorgu çalıştırmak için kod yazın.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Sürekli dışarı aktarma**](./export-telemetry.md)<br/>Ham verilerin ulaşır ulaşmaz toplu olarak depolamaya aktarılması. |![Dışarı Aktarma](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Application Insights’ı nasıl kullanabilirim?

### <a name="monitor"></a>İzleyici
Application Insights’ı uygulamanıza yükleyin, [kullanılabilirlik web testleri](./monitor-web-app-availability.md) ayarlayın ve:

* Yük, yanıt verme ve bağımlılıklarınızın, sayfa yüklerinizin ve AJAX çağrılarının performansını göz önünde tutmak için ekip odasına yönelik varsayılan [uygulama panosuna](./overview-dashboard.md) göz atın.
* En yavaş ve en çok başarısız olan isteklerin hangileri olduğunu keşfedin.
* Yeni bir sürüm dağıttığınızda [Canlı Akış](./live-stream.md)’ı izleyerek herhangi bir performans düşüşünü anında görün.

### <a name="detect-diagnose"></a>Algılama, Tanılama
Bir uyarı aldığınızda veya bir sorun bulduğunuzda:

* Bu durumdan kaç kullanıcının etkilendiğini değerlendirin.
* Özel durumlar, bağımlılık çağrıları ve izlemelerle hatalarla bağıntı kurun.
* Profil oluşturucuyu, anlık görüntüleri, yığın dökümlerini ve izleme günlüklerini inceleyin.

### <a name="build-measure-learn"></a>Oluşturma, Ölçme, Öğrenme
Dağıttığınız her yeni özelliğin [verimliliğini ölçer](./usage-overview.md) .

* Müşterilerin yeni kullanıcı arabirimini veya iş özelliklerini nasıl kullandığını ölçmeyi planlayın.
* Kodunuza özel telemetri yazın.
* Bir sonraki geliştirme döngüsünü telemetrinizden edindiğiniz somut kanıtlara dayandırın.

## <a name="get-started"></a>başlarken
Application Insights, Microsoft Azure’da barındırılan birçok hizmetten biridir ve telemetri verileri analiz edilip sunulmak üzere buraya gönderilir. Bu nedenle, başka bir işlem yapmadan önce bir [Microsoft Azure](https://azure.com) aboneliğinizin olması gerekir. Kaydolmak ücretsizdir ve Application Insights’ın temel [fiyatlandırma planını](https://azure.microsoft.com/pricing/details/application-insights/) seçerseniz, uygulamanız önemli bir kullanım oranına ulaşana kadar ücret ödemezsiniz. Kuruluşunuzun zaten aboneliği varsa, Microsoft hesabınızı bu aboneliğe eklettirebilirsiniz.

Hizmeti kullanmaya başlamanın birkaç yolu vardır. Sizin için en uygun yöntemi kullanarak başlayın. Diğerlerini daha sonra ekleyebilirsiniz.

* **Çalışma zamanında: Sunucuda web uygulamanızı izleyin.** Zaten dağıtılan uygulamalar için idealdir. Kodda herhangi bir güncelleştirme yapmaktan kaçınır.
  * [**Azure Web Apps 'de barındırılan uygulamalar ASP.NET veya ASP.NET Core**](./azure-web-apps.md)
  * [**Azure VM veya Azure sanal makine ölçek kümesi üzerinde IIS 'de barındırılan ASP.NET uygulamaları**](./azure-vm-vmss-apps.md)
  * [**ASP.NET IIS şirket içi VM 'de barındırılan uygulamalar**](./monitor-performance-live-website-now.md)
* **Geliştirme zamanında: Application Insights’ı kodunuza ekleyin.** Telemetri toplamayı özelleştirmenizi ve ek telemetri göndermenizi sağlar.
  * [ASP.NET uygulamaları](./asp-net.md)
  * [ASP.NET Core uygulamalar](./asp-net-core.md)
  * [.NET konsol uygulamaları](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Diğer platformlar](./platforms.md)
* **[Web sayfalarınızı](./javascript.md)** sayfa görünümü, Ajax ve diğer istemci tarafı telemetri için işaretleyin.
* Visual Studio App Center ile tümleştirerek **[mobil uygulama kullanımını çözümleyin](../app/mobile-center-quickstart.md)**.
* **[Kullanılabilirlik testleri](./monitor-web-app-availability.md)**: Sunucularımızdan web sitenize düzenli aralıklarla ping gönderin.

## <a name="next-steps"></a>Sonraki adımlar
Çalışma zamanında şunlarla kullanmaya başlayın:

* [Azure VM ve Azure sanal makine ölçek kümesi IIS tarafından barındırılan uygulamalar](./azure-vm-vmss-apps.md)
* [IIS sunucusu](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

Geliştirme zamanında şunlarla kullanmaya başlayın:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Destek ve geri bildirim
* Sorular ve Sorunlar:
  * [Sorun giderme][qna]
  * [Soru sayfası Microsoft Q&](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Önerileriniz:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Application Insights blogu](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md


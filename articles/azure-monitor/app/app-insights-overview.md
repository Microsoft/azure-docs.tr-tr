---
title: Azure Application Insights nedir? | Microsoft Docs
description: Uygulama Performansı Yönetimi ve canlı web uygulamanızın kullanımını izleme.  Sorunları algılayın, önceliklendirin ve tanılayın; kullanıcıların uygulamanızı nasıl kullandığını anlayın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: overview
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: 089b37761c11c02e34c35eb378c64862bc4fbc98
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064147"
---
# <a name="what-is-application-insights"></a>Application Insights nedir?
[Azure izleyici](../overview.md)'nin bir özelliği olan Application Insights, birden çok platformda Web geliştiricileri için genişletilebilir bir uygulama performans YÖNETIMI (APM) hizmetidir. Canlı web uygulamanızı izlemek için kullanabilirsiniz. Performans anormalliklerini otomatik olarak algılar. Sorunları tanılamanıza ve kullanıcıların uygulamanızla aslında neler yaptığını anlamanıza yardımcı olan güçlü analiz araçları içerir.  Performansı ve kullanılabilirliği sürekli geliştirmenize yardımcı olmak amacıyla tasarlanmıştır. .NET, Node. js ve Java EE dahil olmak üzere şirket içi, karma veya herhangi bir genel bulut gibi çok çeşitli platformlarda uygulamalar için geçerlidir. DevOps işleminizle tümleştirilir ve çeşitli geliştirme araçlarıyla bağlantı noktaları vardır. Visual Studio App Center ile tümleştirerek mobil uygulamalardan telemetriyi izleyebilir ve çözümleyebilir.

## <a name="how-does-application-insights-work"></a>Application Insights nasıl çalışır?
Uygulamanıza küçük bir izleme paketi yüklersiniz ve Microsoft Azure portalında bir Application Insights kaynağı ayarlarsınız. İzleme, uygulamanızı izler ve Azure Izleyici 'ye telemetri verileri gönderir. (Uygulamanın nerede çalıştığı önemli değildir ve Azure’da barındırılması gerekmez.)

Yalnızca web hizmeti uygulamasını değil, tüm arka plan bileşenlerini ve web sayfalarının kendisindeki JavaScript’i de izleyebilirsiniz. 

![Uygulamanızdaki Application Insights izleme aracı, Application Insights kaynağınıza telemetri gönderir.](./media/app-insights-overview/diagram.png)

Buna ek olarak performans sayaçları, Azure tanılama veya Docker günlükleri gibi konak ortamlarından da telemetri çekebilirsiniz. Web hizmetinize düzenli aralıklarla yapay istekler gönderen web testleri de ayarlayabilirsiniz.

Tüm bu telemetri akışları Azure Izleyici ile tümleşiktir. Azure portal, ham verilere güçlü analitik ve arama araçları uygulayabilirsiniz.

### <a name="whats-the-overhead"></a>Ne kadar ek yük getirir?
Uygulamanızın performansı üzerindeki etkisi çok küçüktür. İzleme çağrıları engelleyici değildir ve toplanarak ayrı bir iş parçacığında gönderilir.

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

|  |  |
| --- | --- |
| [**Akıllı algılama ve el ile uyarılar**](../../azure-monitor/app/proactive-diagnostics.md)<br/>Otomatik uyarılar, uygulamanızın normal telemetri desenlerine uyum sağlar ve normal desenin dışında bir durum gelişirse tetiklenir. Belirli özel veya standart ölçüm düzeylerinde de [uyarılar](../../azure-monitor/app/alerts.md) ayarlayabilirsiniz. |![Uyarı örneği](./media/app-insights-overview/alerts-tn.png) |
| [**Uygulama eşlemesi**](../../azure-monitor/app/app-map.md)<br/>Uygulamanızın bileşenlerinin yanı sıra önemli ölçüm ve uyarılar. |![Uygulama eşlemesi](./media/app-insights-overview/appmap-tn.png)  |
| [**Profil Oluşturucu**](../../azure-monitor/app/profiler.md)<br/>Örnek isteklerinin yürütme profillerini inceleyin. |![Profil Oluşturucu](./media/app-insights-overview/profiler.png) |
| [**Kullanım analizi**](../../azure-monitor/app/usage-overview.md)<br/>Kullanıcıların segmentlere nasıl ayrıldığını ve nasıl elde tutulduğunu çözümleyin.|![Elde tutma aracı](./media/app-insights-overview/retention.png) |
| [**Örnek verileri için tanılama arama**](../../azure-monitor/app/diagnostic-search.md)<br/>İstekler, özel durumlar, bağımlılık çağrıları, günlük izlemeleri ve sayfa görüntülemeleri gibi olaylarda arama yapın ve bunları filtreleyin.  |![Telemetri arama](./media/app-insights-overview/search-tn.png) |
| [**Toplu veriler için Ölçüm Gezgini**](../../azure-monitor/app/metrics-explorer.md)<br/>İstek, hata ve özel durum oranları; yanıt süreleri, sayfa yükleme süreleri gibi toplu verileri keşfedin, filtreleyin ve bölümlere ayırın. |![Ölçümler](./media/app-insights-overview/metrics-tn.png) |
| [**Panolar**](../../azure-monitor/app/overview-dashboard.md)<br/>Birden çok kaynaktan toplanan verileri birleştirin ve başkalarıyla paylaşın. Çok bileşenli uygulamalar ve takım odasında sürekli görüntüleme için idealdir. |![Pano örneği](./media/app-insights-overview/dashboard-tn.png) |
| [**Canlı Ölçüm Akışı**](../../azure-monitor/app/live-stream.md)<br/>Yeni bir derleme dağıttığınızda, her şeyin beklendiği gibi çalıştığından emin olmak için bu neredeyse gerçek zamanlı performans göstergelerini izleyin. |![Canlı ölçüm örneği](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analiz**](../../azure-monitor/app/analytics.md)<br/>Bu güçlü sorgulama dilini kullanarak uygulamanızın performansı ve kullanımıyla ilgili zor soruları yanıtlayın. |![Analiz örneği](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>Koddaki performans verilerini görün. Yığın izlemelerinden koda gidin.|![Visual studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Anlık görüntü hata ayıklayıcısı**](../../azure-monitor/app/snapshot-debugger.md)<br/>Dinamik işlemlerden örneklenen anlık görüntülerdeki hataları parametre değerleriyle ayıklayın.|![Visual studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>Kullanım ölçümlerini diğer iş zekası verileriyle tümleştirin.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Ölçümleriniz ve ham verileriniz üzerinde sorgu çalıştırmak için kod yazın.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Sürekli dışarı aktarma**](../../azure-monitor/app/export-telemetry.md)<br/>Ham verilerin ulaşır ulaşmaz toplu olarak depolamaya aktarılması. |![Dışarı Aktar](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Application Insights’ı nasıl kullanabilirim?

### <a name="monitor"></a>İzleyin
Application Insights’ı uygulamanıza yükleyin, [kullanılabilirlik web testleri](../../azure-monitor/app/monitor-web-app-availability.md) ayarlayın ve:

* Yük, yanıt verme ve bağımlılıklarınızın, sayfa yüklerinizin ve AJAX çağrılarının performansını göz önünde tutmak için ekip odasına yönelik varsayılan [uygulama panosunu](../../azure-monitor/app/overview-dashboard.md) kullanıma alma.
* En yavaş ve en çok başarısız olan isteklerin hangileri olduğunu keşfedin.
* Yeni bir sürüm dağıttığınızda [Canlı Akış](../../azure-monitor/app/live-stream.md)’ı izleyerek herhangi bir performans düşüşünü anında görün.

### <a name="detect-diagnose"></a>Algılama, Tanılama
Bir uyarı aldığınızda veya bir sorun bulduğunuzda:

* Bu durumdan kaç kullanıcının etkilendiğini değerlendirin.
* Özel durumlar, bağımlılık çağrıları ve izlemelerle hatalarla bağıntı kurun.
* Profil oluşturucuyu, anlık görüntüleri, yığın dökümlerini ve izleme günlüklerini inceleyin.

### <a name="build-measure-learn"></a>Oluşturma, Ölçme, Öğrenme
Dağıttığınız her yeni özelliğin [ne kadar etkili olduğunu ölçün](../../azure-monitor/app/usage-overview.md).

* Müşterilerin yeni kullanıcı arabirimini veya iş özelliklerini nasıl kullandığını ölçmeyi planlayın.
* Kodunuza özel telemetri yazın.
* Bir sonraki geliştirme döngüsünü telemetrinizden edindiğiniz somut kanıtlara dayandırın.

## <a name="get-started"></a>Kullanmaya Başlayın
Application Insights, Microsoft Azure’da barındırılan birçok hizmetten biridir ve telemetri verileri analiz edilip sunulmak üzere buraya gönderilir. Bu nedenle, başka bir işlem yapmadan önce bir [Microsoft Azure](https://azure.com) aboneliğinizin olması gerekir. Kaydolmak ücretsizdir ve Application Insights’ın temel [fiyatlandırma planını](https://azure.microsoft.com/pricing/details/application-insights/) seçerseniz, uygulamanız önemli bir kullanım oranına ulaşana kadar ücret ödemezsiniz. Kuruluşunuzun zaten aboneliği varsa, Microsoft hesabınızı bu aboneliğe eklettirebilirsiniz.

Hizmeti kullanmaya başlamanın birkaç yolu vardır. Sizin için en uygun yöntemi kullanarak başlayın. Diğerlerini daha sonra ekleyebilirsiniz.

* **Çalışma zamanında: Sunucuda web uygulamanızı izleyin.** Zaten dağıtılan uygulamalar için idealdir. Kodda herhangi bir güncelleştirme yapmaktan kaçınır.
  * [**Azure Web Apps 'de barındırılan uygulamalar ASP.NET veya ASP.NET Core**](../../azure-monitor/app/azure-web-apps.md)
  * [**Azure VM veya Azure sanal makine ölçek kümesi üzerinde IIS 'de barındırılan ASP.NET uygulamaları**](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * [**ASP.NET IIS şirket içi VM 'de barındırılan uygulamalar**](../../azure-monitor/app/monitor-performance-live-website-now.md)
* **Geliştirme zamanında: Application Insights’ı kodunuza ekleyin.** Telemetri toplamayı özelleştirmenizi ve ek telemetri göndermenizi sağlar.
  * [ASP.NET uygulamaları](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core uygulamalar](../../azure-monitor/app/asp-net-core.md)
  * [.NET konsol uygulamaları](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Python (Önizleme)](../../azure-monitor/app/opencensus-python.md)
  * [Diğer platformlar](../../azure-monitor/app/platforms.md)
* **[Web sayfalarınızı](../../azure-monitor/app/javascript.md)** sayfa görünümü, Ajax ve diğer istemci tarafı telemetri için işaretleyin.
* Visual Studio App Center ile tümleştirerek **[mobil uygulama kullanımını çözümleyin](../../azure-monitor/learn/mobile-center-quickstart.md)** .
* **[Kullanılabilirlik testleri](../../azure-monitor/app/monitor-web-app-availability.md)** : Sunucularımızdan web sitenize düzenli aralıklarla ping gönderin.

## <a name="next-steps"></a>Sonraki adımlar
Çalışma zamanında şunlarla kullanmaya başlayın:

* [Azure VM ve Azure sanal makine ölçek kümesi IIS tarafından barındırılan uygulamalar](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS sunucusu](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Geliştirme zamanında şunlarla kullanmaya başlayın:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python (Önizleme)](../../azure-monitor/app/opencensus-python.md)


## <a name="support-and-feedback"></a>Destek ve geri bildirim
* Sorular ve Sorunlar:
  * [Sorun giderme][qna]
  * [MSDN Forumu](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Önerileriniz:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Application Insights blogu](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md

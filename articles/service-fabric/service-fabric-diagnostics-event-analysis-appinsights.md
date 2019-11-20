---
title: Application Insights ile Azure Service Fabric olay Analizi | Microsoft Docs
description: Azure Service Fabric kümelerini izleme ve tanılamaya yönelik Application Insights kullanarak olayları görselleştirme ve çözümleme hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 8e682a5c768ed4b3f35382c87528c1b0d11a3c3d
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019710"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Application Insights ile olay Analizi ve görselleştirme

Azure Izleyici 'nin bir parçası olan Application Insights uygulama izleme ve Tanılama için genişletilebilir bir platformdur. Güçlü bir analiz ve Sorgulama Aracı, özelleştirilebilir Pano ve görselleştirmeler ve otomatik uyarı dahil diğer seçenekleri içerir. Application Insights Service Fabric tümleştirmesi, Visual Studio ve Azure portal için araç deneyimlerini, ayrıca belirli ölçümleri Service Fabric de içerir ve böylece kapsamlı bir kullanıma hazır günlük deneyimi sağlar. Application Insights için çok sayıda günlük otomatik olarak oluşturulup toplanırsa, daha zengin bir tanılama deneyimi oluşturmak için uygulamalarınıza daha fazla özel günlüğe kaydetme eklemenizi öneririz.

Bu makale aşağıdaki yaygın soruların ele sağlanmasına yardımcı olur:

* Uygulama ve hizmetlerim içinde neler olduğunu ve Telemetriyi nasıl toplayabileceğinizi Nasıl yaparım?.
* Uygulamamda sorun giderme, özellikle de hizmetler birbiriyle iletişim kurma Nasıl yaparım??
* Nasıl yaparım? hizmetlerimin nasıl çalıştığı hakkında ölçümler alma, örneğin sayfa yükleme süresi, HTTP istekleri mi?

Bu makalenin amacı, Application Insights içinden öngörülere ve sorun gidermeye nasıl ulaşmak gerektiğini gösterir. Service Fabric Application Insights ayarlamayı ve yapılandırmayı öğrenmek istiyorsanız, bu [öğreticiye](service-fabric-tutorial-monitoring-aspnet.md)göz atın.

## <a name="monitoring-in-application-insights"></a>Application Insights izleme

Application Insights, Service Fabric kullanılırken kullanıma hazır bir deneyime sahiptir. Genel Bakış sayfasında Application Insights, hizmetiniz hakkında yanıt süresi ve işlenen istek sayısı gibi önemli bilgileri sağlar. En üstteki ' ara ' düğmesine tıklayarak uygulamanızdaki son isteklerin listesini görebilirsiniz. Ayrıca, başarısız istekleri burada görebilir ve hataların oluştuğunu tanılayabilirsiniz.

![Application Insights genel bakış](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Yukarıdaki görüntüde sağ panelde, listede iki ana giriş türü vardır: istekler ve olaylar. İstekler bu durumda HTTP istekleri aracılığıyla uygulamanın API 'sine yapılan çağrılardır ve olaylar, kodunuzda herhangi bir yere ekleyebileceğiniz telemetri olarak davranan özel olaylardır. [Özel olaylar ve ölçümler için APPLICATION INSIGHTS API](../azure-monitor/app/api-custom-events-metrics.md)'de uygulamalarınızın gözden geçirebilirliğini inceleyebilirsiniz. Bir isteğe tıkladığınızda, Application Insights Service Fabric NuGet paketinde toplanan Service Fabric özgü veriler de dahil olmak üzere aşağıdaki görüntüde gösterildiği gibi daha fazla ayrıntı görüntülenir. Bu bilgi, sorun gidermede ve uygulamanızın durumunun ne olduğunu bilmekte yararlıdır ve bu bilgilerin tümü Application Insights içinde aranabilir.

![Application Insights Isteği ayrıntıları](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights, içinde gelen tüm verilere yönelik sorgulama için belirlenmiş bir görünüme sahiptir. Application Insights portalına gitmek için genel bakış sayfasının en üstündeki "Ölçüm Gezgini" düğmesine tıklayın. Burada, kusto sorgu dilini kullanarak, istekler, özel durumlar, performans sayaçları ve diğer ölçümler için önce bahsedilen özel olaylara karşı sorgular çalıştırabilirsiniz. Aşağıdaki örnek, son 1 saat içindeki tüm istekleri gösterir.

![Application Insights Isteği ayrıntıları](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Application Insights portalının yeteneklerini daha ayrıntılı incelemek için, [Application Insights Portal belgelerine](../azure-monitor/app/app-insights-dashboards.md)gidin.

### <a name="configuring-application-insights-with-eventflow"></a>EventFlow ile Application Insights yapılandırma

Olayları toplamak için EventFlow kullanıyorsanız, `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`NuGet paketini içeri aktardığınızdan emin olun. *Eventflowconfig. JSON*' ın *çıktılar* bölümünde aşağıdaki kod gereklidir:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Filtrelerinizin gerekli değişikliklerini yaptığınızdan ve diğer tüm girdileri (ilgili NuGet paketleriyle birlikte) eklediğinizden emin olun.

## <a name="application-insights-sdk"></a>Application Insights SDK

Tanılama ve izlemeye yönelik daha modüler bir yaklaşıma izin verdiklerinden, EventFlow ve WAD 'in toplama çözümleri olarak kullanılması önerilir. Örneğin, EventFlow 'tan çıktılarınızı değiştirmek istiyorsanız, gerçek araçlarınız üzerinde hiçbir değişiklik yapılmasını gerektirmez, yalnızca bir yapılandırma dosyanızda basit değişiklik. Ancak, Application Insights kullanarak yatırım yapmanız ve farklı bir platforma değişmemek istiyorsanız, olayları toplamak ve Application Insights göndermek için Application Insights ' yeni SDK 'yi kullanarak bakmanız gerekir. Bu, artık, verilerinizi Application Insights göndermek için EventFlow ' ı yapılandırmanız gerekmediği anlamına gelir, bunun yerine Applicationınsight Service Fabric NuGet paketini yükler. Paket ayrıntılarını [burada](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)bulabilirsiniz.

[Mikro hizmetler ve kapsayıcılar için Application Insights desteği](https://azure.microsoft.com/blog/app-insights-microservices/) , üzerinde çalışılan yeni özelliklerden bazılarını gösterir (Şu anda hala beta sürümünde) ve Application Insights daha zengin kullanıma hazır izleme seçeneklerine sahip olabilirsiniz. Bunlar, bağımlılık izlemeyi (bir kümedeki tüm hizmetlerinizin ve uygulamalarınızın bir AppMap 'i ve aralarındaki iletişimi oluşturma) ve hizmetinizden gelen izlemelerin daha iyi bağıntısını oluşturma (iş akışında bir sorunu daha iyi gösterme konusunda yardımcı olur) bir uygulama veya hizmet).

.NET ' te geliştiriyorsanız ve büyük olasılıkla bazı Service Fabric programlama modellerini kullanıyor ve olay ve günlük verilerini görselleştirmeyi ve analiz etmek için platformunuz olarak Application Insights kullanmak istiyorsanız, Application Insights aracılığıyla gitmeniz önerilir İzleme ve tanılama iş akışınız olarak SDK rotası. Günlüklerinizi toplamak [](../azure-monitor/app/asp-net-trace-logs.md) ve göstermek üzere Application Insights kullanmaya başlamak için bunu okuyun. [](../azure-monitor/app/asp-net-more.md)

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Azure portal Application Insights kaynakta gezinme

Olaylarınız ve günlüklerinizin çıktısı olarak Application Insights yapılandırdıktan sonra, bilgilerin Application Insights kaynağında birkaç dakika içinde gösterilmesi gerekir. Sizi Application Insights kaynak panosuna götürebileceğiniz Application Insights kaynağına gidin. Application Insights görev çubuğunda **Ara** ' ya tıklayarak aldığı en son izlemeleri görüntüleyin ve bunlara filtre uygulayabilir.

*Ölçüm Gezgini* , uygulamalarınızın, hizmetlerinizin ve kümenizin rapor olabileceği ölçümlere dayalı özel panolar oluşturmaya yönelik yararlı bir araçtır. Topladığınız verileri temel alarak kendinize birkaç grafik ayarlamak için [Application Insights ölçümleri keşfetme](../azure-monitor/app/metrics-explorer.md) konusuna bakın.

**Analiz** ' e tıklamak sizi daha fazla kapsam ve seçenek ile olayları ve izlemeleri sorgulayabileceğiniz Application Insights Analytics portalına götürür. [Application Insights 'de](../azure-monitor/app/analytics.md)analizler hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

* Performans veya kullanımlardaki değişiklikler hakkında bildirim almak için [AI 'Deki uyarıları ayarlama](../azure-monitor/app/alerts.md)
* [Application Insights akıllı algılama](../azure-monitor/app/proactive-diagnostics.md) , olası performans sorunları konusunda sizi uyarmak için Application Insights gönderilen telemetrinin proaktif analizini yapar

---
title: Azure Application Insights kullanım deneyimlerini etkinleştirmek için Kullanıcı bağlamı kimlikleri gönderin | Microsoft Docs
description: Kullanıcıların Application Insights ' de benzersiz, kalıcı bir KIMLIK dizesi atayarak hizmetinize nasıl geçiş yapıldığını izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: b105e8ba79e99be02420ca1821f6a080b0862d6f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887405"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Azure Application Insights kullanım deneyimlerini etkinleştirmek için Kullanıcı bağlamı kimlikleri gönderin

## <a name="tracking-users"></a>Kullanıcıları izleme

Application Insights, bir ürün kullanım araçları kümesi aracılığıyla kullanıcılarınızı izlemenizi ve izlemenizi sağlar:

- [Kullanıcılar, Oturumlar, Etkinlikler](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Huniler](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Bekletme](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohortlar
- [Çalışma kitapları](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Kullanıcının zaman içinde ne yaptığını izlemek için, Application Insights her kullanıcı veya oturum için bir KIMLIĞE ihtiyaç duyuyor. Aşağıdaki kimlikleri her özel olay veya sayfa görünümüne ekleyin.

- Kullanıcılar, komik, bekletme ve Cohorts: kullanıcı KIMLIĞINI Içerir.
- Oturumlar: oturum KIMLIĞINI dahil et.

> [!NOTE]
> Bu, Kullanıcı etkinliğini Application Insights ile izlemeye yönelik el ile yapılan adımların ana hattı olan gelişmiş bir makaledir. Birçok **Web uygulaması ile,** [istemci/tarayıcı tarafı JavaScript SDK 'sı](../../azure-monitor/app/website-monitoring.md )ile birlikte varsayılan sunucu tarafı SDK 'ları, Kullanıcı etkinliğini otomatik olarak izlemek için genellikle yeterlidir. Sunucu tarafı SDK 'ya ek olarak [istemci tarafı izlemeyi](../../azure-monitor/app/website-monitoring.md ) yapılandırmadıysanız, önce bunu yapın ve Kullanıcı davranışı analiz araçlarının beklendiği gibi olup olmadığını görmek için test edin.

## <a name="choosing-user-ids"></a>Kullanıcı kimliklerini seçme

Kullanıcı kimlikleri, kullanıcıların zaman içinde nasıl davrandığını izlemek için kullanıcı oturumlarında kalıcı hale gelmelidir. KIMLIĞI kalıcı hale getirme için çeşitli yaklaşımlar vardır.

- Hizmetinize zaten sahip olduğunuz bir kullanıcının tanımı.
- Hizmetin bir tarayıcıya erişimi varsa, tarayıcıya bir kimliğe sahip bir kimlik bilgisi gönderebilir. Tanımlama bilgisi kullanıcının tarayıcısında kaldığı sürece KIMLIĞI devam edecektir.
- Gerekirse, her oturum için yeni bir KIMLIK kullanabilirsiniz, ancak kullanıcıların sonuçları sınırlı olacaktır. Örneğin, bir kullanıcının davranışının zaman içinde nasıl değiştiği hakkında bilgi sahibi olmayacaktır.

KIMLIK, her kullanıcıyı benzersiz bir şekilde tanımlamak için yeterince karmaşık bir GUID veya bir dize olmalıdır. Örneğin, bu uzun bir rastgele sayı olabilir.

KIMLIK, kullanıcı hakkındaki kişisel tanımlama bilgilerini içeriyorsa, Kullanıcı KIMLIĞI olarak Application Insights göndermek için uygun bir değer değildir. Bu tür bir KIMLIĞI kimliği [doğrulanmış bir kullanıcı kimliği](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)olarak gönderebilirsiniz, ancak kullanım senaryoları IÇIN Kullanıcı kimliği gereksinimini karşılamaz.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET Apps: bir ılemetrybaşlatıcısında kullanıcı bağlamını ayarlama

[Burada](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer)ayrıntılı olarak açıklandığı gibi bir telemetri başlatıcısı oluşturun. İstek telemetrisi aracılığıyla oturum KIMLIĞINI geçirin ve Context.User.Id ve Context.Session.Id ayarlayın.

Bu örnek, kullanıcı KIMLIĞINI oturum sonrasında süresi dolan bir tanımlayıcıya ayarlar. Mümkünse, oturumlar arasında devam eden bir kullanıcı KIMLIĞI kullanın.

### <a name="telemetry-initializer"></a>Telemetri başlatıcısı

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- Kullanım deneyimlerini etkinleştirmek için [özel olaylar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) veya [sayfa görünümleri](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)göndermeye başlayın.
- Özel olayları veya sayfa görünümlerini zaten gönderirseniz, kullanıcıların hizmetinizi nasıl kullandığını öğrenmek için kullanım araçları ' nı araştırın.
    - [Kullanıma genel bakış](usage-overview.md)
    - [Kullanıcılar, Oturumlar ve Etkinlikler](usage-segmentation.md)
    - [Huniler](usage-funnels.md)
    - [Bekletme](usage-retention.md)
    - [Çalışma kitapları](../../azure-monitor/app/usage-workbooks.md)

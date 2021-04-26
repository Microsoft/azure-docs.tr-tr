---
title: Azure Service Fabric uygulama düzeyinde Izleme
description: Azure Service Fabric kümelerini izlemek ve tanılamak için kullanılan uygulama ve hizmet düzeyi olayları ve günlükleri hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: a60eef008afae4185acc266c74c4fb0ce694d560
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627498"
---
# <a name="application-logging"></a>Uygulama günlüğüne kaydetme

Kodunuzun seçilmesi yalnızca kullanıcılarınız hakkında öngörüler elde etmenin bir yoludur, ancak uygulamanızda bir şeyin yanlış olup olmadığını ve ne kadar düzeltilmesi gerektiğini bilmenin tek yolu değildir. Teknik olarak bir hata ayıklayıcıyı bir üretim hizmetine bağlamak mümkün olsa da, yaygın bir uygulama değildir. Bu nedenle, ayrıntılı izleme verilerinin olması önemlidir.

Bazı ürünler kodunuzu otomatik olarak işaretleyebilir. Bu çözümler iyi çalışabilse de, el ile izleme neredeyse her zaman iş mantığınızla özgü olmalıdır. Sonunda, uygulamada hata ayıklama yapmak için yeterli bilgiye sahip olmanız gerekir. Service Fabric uygulamalar herhangi bir günlük çerçevesiyle birlikte ayarlanabilir. Bu belgede, kodunuzu denetlemek için birkaç farklı yaklaşım açıklanmakta ve diğeri üzerinde bir yaklaşım seçmektir. 

Bu önerilerin nasıl kullanılacağına ilişkin örnekler için, bkz. [Service Fabric uygulamanıza günlük ekleme](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights, Service Fabric kutudan çıkan zengin bir tümleştirmeye sahiptir. Kullanıcılar, Service Fabric NuGet paketlerini ekleyebilir ve Azure portal, oluşturulup toplanan ve toplanan verileri alabilir. Ayrıca, kullanıcıların uygulamalarını tanılamak ve hatalarını ayıklamak ve uygulamalarının en çok hangi hizmetleri ve bölümlerinin kullanıldığını izlemek için kendi telemetrilerini eklemesi önerilir. SDK 'daki [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) sınıfı, uygulamalarınızda telemetri izlemek için birçok yol sunar. [Bir .NET uygulamasını izlemek ve tanılamak](service-fabric-tutorial-monitoring-aspnet.md) için öğreticimizde Application Insights 'ı nasıl işaretleyip ekleyeceğiniz hakkında bir örneğe göz atın

## <a name="eventsource"></a>EventSource

Visual Studio 'daki bir şablondan bir Service Fabric çözümü oluşturduğunuzda, **EventSource** ile türetilmiş bir sınıf (**Serviceeventsource** veya **ActorEventSource**) oluşturulur. Uygulamanız veya hizmetiniz için olaylar ekleyebileceğiniz bir şablon oluşturulur. **EventSource** adı benzersiz **olmalıdır** ve MyCompany-Solution projesinden varsayılan şablon dizesinden yeniden adlandırılması gerekir &lt; &gt; - &lt; &gt; . Aynı adı kullanan birden çok **EventSource** tanımının olması çalışma zamanında soruna neden olur. Her bir tanımlı olay benzersiz bir tanımlayıcıya sahip olmalıdır. Bir tanımlayıcı benzersiz değilse, bir çalışma zamanı hatası oluşur. Bazı kuruluşlar, ayrı geliştirme takımları arasında çakışmaları önlemek için tanımlayıcıların değer aralıklarını yeniden atayabilir. Daha fazla bilgi için bkz. [Vance 'in blogu](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) veya [MSDN belgeleri](/previous-versions/msp-n-p/dn774985(v=pandp.20)).

## <a name="aspnet-core-logging"></a>Günlüğe kaydetme ASP.NET Core

Kodunuzu nasıl işaretlemelerinizi dikkatle planlamanız önemlidir. Doğru izleme planı, kod tabanınızı kararsız hale getirmenize ve sonra kodu yeniden açmaya gerek kalmadan kaçınmanıza yardımcı olabilir. Riski azaltmak için, Microsoft ASP.NET Core 'un bir parçası olan [Microsoft. Extensions. Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)gibi bir izleme kitaplığı seçebilirsiniz. ASP.NET Core, tercih ettiğiniz sağlayıcıyla kullanabileceğiniz bir [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) arabirimine sahiptir, ancak var olan koddaki etkiyi en aza indirirken. Kodu Windows ve Linux üzerinde ASP.NET Core ve tam .NET Framework ' de kullanarak, izleme kodunuzun standartlaştırılmış olması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalarınızı ve hizmetlerinizi işaretlemek için günlüğe kaydetme sağlayıcınızı seçtikten sonra, günlüklerinizin ve olaylarınızın herhangi bir analiz platformuna gönderilebilmeleri için toplanıp toplanmaları gerekir. Azure Izleyici önerilen seçeneklerinin bazılarını daha iyi anlamak için [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) ve [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) hakkında bilgi edinin.

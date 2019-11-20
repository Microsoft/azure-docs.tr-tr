---
title: Azure Application Insights-Azure Işlevleri desteklenen özellikler | Microsoft Docs
description: Azure Işlevleri için desteklenen özellikler Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 31f962ca96ca5c47d18f9250e567abb8f4024e6f
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677549"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Işlevleri 'nin desteklediği özellikler için Application Insights

Azure Işlevleri, ILogger arabirimi aracılığıyla kullanılabilen Application Insights ile [yerleşik tümleştirme](../../azure-functions/functions-monitoring.md) sunar. Şu anda desteklenen özelliklerin listesi aşağıda verilmiştir. [Kullanmaya başlamak](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)Için Azure işlevleri Kılavuzu ' nu gözden geçirin.

## <a name="supported-features"></a>Desteklenen özellikler

| Azure İşlevleri                       | V1                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Otomatik toplama**        |                 |                   |               
| &bull; Istek                     | Yes             | Yes               | 
| &bull; özel durumları                   | Yes             | Yes               | 
| &bull; performans sayacı         | Yes             | Yes               |
| &bull; bağımlılıkları                   |                   |                   |               
| &nbsp; &nbsp; &nbsp; &mdash; HTTP      |                 | Yes               | 
| &nbsp; &nbsp; &nbsp; &mdash; ServiceBus|                 | Yes               | 
| &nbsp; &nbsp; &nbsp; &mdash; EventHub  |                 | Yes               | 
| &nbsp; &nbsp; &nbsp; &mdash; SQL       |                 | Yes               | 
| | | | 
| **Desteklenen özellikler**                |                   |                   |               
| &bull; QuickPulse/Liveölçümleri       | Yes             | Yes               | 
| &nbsp; &nbsp; &nbsp; &mdash; güvenli denetim kanalı|                 | Yes               | 
| &bull; örnekleme                     | Yes             | Yes               | 
| &bull; sinyal                   |                 | Yes               | 
| | | | 
| **Bağıntılı**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Yes               | 
| &bull; EventHub                       |                   | Yes               | 
| | | | 
| **Yapılandırılabilir**                      |                   |                   |           
| &bull;Tam yapılandırılabilir.<br/>Yönergeler için bkz. [Azure işlevleri](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Tüm seçenekler için bkz. [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) .               |                   | Yes                   | 


## <a name="performance-counters"></a>Performans Sayaçları

Performans sayaçlarının otomatik toplanması yalnızca Windows makinelerini işler.


## <a name="live-metrics--secure-control-channel"></a>Canlı ölçümler & güvenli denetim kanalı

Belirttiğiniz özel filtreler ölçütü, Application Insights SDK 'sindeki canlı ölçümler bileşenine geri gönderilir. Filtreler potansiyel olarak CustomerIDs gibi hassas bilgileri içerebilir. Kanalı gizli bir API anahtarı ile güvenli hale getirebilirsiniz. Yönergeler için bkz. [Denetim kanalını güvenli hale getirme](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) .

## <a name="sampling"></a>Örnekleme

Azure Işlevleri, yapılandırmasında varsayılan olarak örnekleme imkanı sunar. Daha fazla bilgi için bkz. [örneklemesi yapılandırma](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Projeniz, el ile Telemetriyi izlemeyi yapmak üzere Application Insights SDK üzerinde bir bağımlılık alırsa, örnekleme yapılandırmanız Işlevlerin örnekleme yapılandırmasından farklı olduğunda garip davranışlar yaşayabilirsiniz. 

Aynı yapılandırmayı Işlevlerle kullanmanızı öneririz. **İşlev v2**ile, oluşturucuya bağımlılık ekleme kullanarak aynı yapılandırmayı edinebilirsiniz:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```

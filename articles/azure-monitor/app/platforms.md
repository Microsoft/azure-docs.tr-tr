---
title: 'Application Insights: diller, platformlar ve tümleştirmeler | Microsoft Docs'
description: Application Insights için kullanılabilen diller, platformlar ve tümleştirmeler
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 01cebc3a86808d549f1a7dc3adfd2883bc289076
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73061442"
---
# <a name="supported-languages"></a>Desteklenen diller

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python (Önizleme)](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Desteklenen platformlar ve çerçeveler

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Zaten dağıtılan uygulamalar için izleme (codeless, aracı tabanlı)
* [Azure VM ve Azure sanal makine ölçek kümeleri](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - zaten canlı olan uygulamalar için](../../azure-monitor/app/monitor-performance-live-website-now.md)
* Hem Web hem de çalışan rolleri dahil olmak üzere [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Azure İşlevleri](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Kod üzerinden izleme (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python (Önizleme)](../../azure-monitor/app/opencensus-python.md)
* [Evrensel Windows uygulaması](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows masaüstü uygulamaları, hizmetleri ve çalışan rolleri](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Günlük altyapıları
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog veya System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J veya Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash eklentisi](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure İzleyici](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Dışarı aktarma ve veri analizi
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Akış Analizi](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Desteklenmeyen SDK’lar
Diğer topluluk tarafından desteklenen birkaç SDK 'nın mevcut olduğunu biliyoruz. Ancak, Azure Izleyici yalnızca bu sayfada listelenen desteklenen SDK 'Ları kullanırken destek sağlar. Diğer dillere yönelik desteğimizi genişletmeyi sürekli olarak değerlendirdik, bu nedenle en son SDK haberlerini almak için [GitHub duyuruları](https://github.com/microsoft/ApplicationInsights-Announcements/issues) sayfamızı izleyin. 

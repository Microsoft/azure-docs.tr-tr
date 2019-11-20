---
title: Azure Application Insights-bağımlılık otomatik koleksiyonu | Microsoft Docs
description: Application Insights bağımlılıkları otomatik olarak toplayıp görselleştirin
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/16/2018
ms.openlocfilehash: 1d3259c32de7f937d4e5ac8e21c8cab60b9cc635
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819181"
---
# <a name="application-insights-nuget-packages"></a>NuGet paketlerini Application Insights

Application Insights için güncel sürüm NuGet paketlerinin geçerli listesi aşağıda verilmiştir.

## <a name="common-packages-for-aspnet"></a>ASP.NET için ortak paketler

| Paket adı | Kararlı sürüm | Açıklama | İndirin |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights | 2.8.0 | Tüm Application Insights Telemetri türlerinin iletilmesi için çekirdek işlevsellik sağlar ve diğer tüm Application Insights paketleri için bağımlı bir pakettir | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft. ApplicationInsights. Agent. KESMENOKTASI | 2.4.0 | Yöntem çağrılarının ele geçirilmesini sunar | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.8.0 | .NET uygulamaları için bağımlılık toplayıcısı Application Insights. Bu, platforma özgü Application Insights paketlerine yönelik bağımlı bir pakettir ve bağımlılık telemetrinin otomatik olarak toplanmasını sağlar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.8.0 | Application Insights performans sayaçları toplayıcısı, performans sayaçları tarafından toplanan verileri Application Insights göndermenizi sağlar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. Web | 2.8.0 | .NET Web uygulamaları için Application Insights | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.8.0 | Application Insights Windows Server NuGet paketi, .NET uygulamaları için Application Insights telemetrisine yönelik otomatik koleksiyon sağlar. Bu paket, platforma özgü paketlerin Application Insights bağımlı bir paket olarak veya platforma özgü paketlerin (.NET çalışan rolleri gibi) kapsamına girmeyen bağımsız bir paket olarak kullanılabilir. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.8.0 | Windows Server SDK Application Insights, çevrimdışı senaryolarda Telemetriyi koruyacak bir telemetri kanalı sağlar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>ASP.NET Core için ortak paketler

| Paket adı | Kararlı sürüm | Açıklama | İndirin |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AspNetCore | 2.5.0 | ASP.NET Core Web uygulamaları için Application Insights. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft. ApplicationInsights | 2.8.0 | Bu paket, tüm Application Insights Telemetri türlerinin iletilmesi için çekirdek işlevsellik sağlar ve diğer tüm Application Insights paketleri için bağımlı bir pakettir | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.8.0 | .NET uygulamaları için bağımlılık toplayıcısı Application Insights. Bu, platforma özgü Application Insights paketlerine yönelik bağımlı bir pakettir ve bağımlılık telemetrinin otomatik olarak toplanmasını sağlar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.8.0 | Application Insights performans sayaçları toplayıcısı, performans sayaçları tarafından toplanan verileri Application Insights göndermenizi sağlar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.8.0 | Application Insights Windows Server NuGet paketi, .NET uygulamaları için Application Insights telemetrisine yönelik otomatik koleksiyon sağlar. Bu paket, platforma özgü paketlerin Application Insights bağımlı bir paket olarak veya platforma özgü paketlerin (.NET çalışan rolleri gibi) kapsamına girmeyen bağımsız bir paket olarak kullanılabilir. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.8.0 | Windows Server SDK Application Insights, çevrimdışı senaryolarda Telemetriyi koruyacak bir telemetri kanalı sağlar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>OpenCensus kullanarak Python için ortak paketler
| Paket adı | Kararlı sürüm | Açıklama | İndirin |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-Azure | 1.0.0 | OpenCensus aracılığıyla Azure Izleyici kapsamındaki Python uygulamaları için Application Insights. | [Paketi indir](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-docgo | 0.7.2 | Bu paket Python [docgo](https://pypi.org/project/django/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-Flask | 0.7.3 | Bu paket Python [Flask](https://pypi.org/project/flask/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Bu paket, Python3 için Python [http. Client](https://docs.python.org/3/library/http.client.html) kitaplığı ve Python2 için [httplib](https://docs.python.org/2/library/httplib.html) ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-Logging | 0.1.0 | Bu paket, izleme verileriyle günlük kayıtlarını zenginleştirir. | [Paketi indir](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-MySQL | 0.1.2 | Bu paket Python [MySQL-bağlayıcı](https://pypi.org/project/mysql-connector/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-PostgreSQL | 0.1.2 | Bu paket Python [psycopg2](https://pypi.org/project/psycopg2/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Bu paket Python [pymongo](https://pypi.org/project/pymongo/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Bu paket Python [Pymysql](https://pypi.org/project/PyMySQL/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-piramit | 0.7.1 | Bu paket Python [piramit](https://pypi.org/project/pyramid/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-Requests | 0.7.2 | Bu paket Python [istekleri](https://pypi.org/project/requests/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Bu paket Python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) kitaplığı ile tümleştirme sağlar. | [Paketi indir](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Dinleyici/toplayıcı/uygulama

| Paket adı | Kararlı sürüm | Açıklama | İndirin |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. DiagnosticSourceListener | 2.7.2 |  Olayları DiagnosticSource 'tan Application Insights iletmek için izin verir. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft. ApplicationInsights. EventSourceListener | 2.7.2 | Application Insights EventSourceListener, EventSource olaylardaki verilerin Application Insights gönderilmesine izin verir. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft. ApplicationInsights. EtwCollector | 2.7.2 | Application Insights EtwCollector, Windows için olay Izleme (ETW) verilerinin Application Insights gönderilmesine izin verir. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft. ApplicationInsights. TraceListener | 2.7.2 | İzleme günlüğü iletilerini Application Insights göndermenizi sağlayan özel bir TraceListener. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft. ApplicationInsights. Log4NetAppender | 2.7.2 | Özel bir uygulama, Application Insights için Log4Net günlük iletileri göndermenize olanak tanır. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft. ApplicationInsights. NLogTarget | 2.7.2 |  bir özel hedef, NLog günlük iletilerini Application Insights göndermenizi sağlar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft. ApplicationInsights. SnapshotCollector | 1.3.1 | Uygulamanızdaki özel durumları izler ve çevrimdışı analiz için anlık görüntüleri otomatik olarak toplar. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Paket adı | Kararlı sürüm | Açıklama | İndirin |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. ServiceFabric | 2.2.0 | Bu paket, uygulamanın üzerinde çalıştığı Service Fabric bağlamı ile Telemetriyi otomatik olarak dekorasyonu sağlar. Bu NuGet 'i Yerel Service Fabric uygulamalar için kullanmayın. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft. ApplicationInsights. ServiceFabric. Native | 2.2.0 | Service Fabric uygulamaları için Application Insights modülü. Bu NuGet 'i yalnızca yerel Service Fabric uygulamaları için kullanın. Kapsayıcılarda çalışan uygulamalar için Microsoft. ApplicationInsights. ServiceFabric paketini kullanın. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Durum İzleyicisi

| Paket adı | Kararlı sürüm | Açıklama | İndirin |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. Agent_x64 | 2.2.1 |  X64 uygulamaları için çalışma zamanı veri toplamayı etkinleştirilir | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft. ApplicationInsights. Agent_x86 | 2.2.1 |  X86 uygulamaları için çalışma zamanı veri toplamayı etkinleştirilir. | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Bu paketler, [durum İzleyicisi](../../azure-monitor/app/monitor-performance-live-website-now.md)çalışma zamanı izlemenin temel işlevlerinin bir parçasını yapar. Bu paketleri doğrudan indirmeniz gerekmez, Durum İzleyicisi yükleyicisini kullanmanız yeterlidir. Bu paketlerin, geliştiricilerimizden birinden bu [Web günlüğü gönderilerinden](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) nasıl çalıştığı hakkında daha fazla bilgi edinmek istiyorsanız iyi bir başlangıç yapın.

## <a name="additional-packages"></a>Ek paketler

| Paket adı | Kararlı sürüm | Açıklama | İndirin |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AzureWebSites | 2.6.5 | Bu uzantı, bir Azure App Service Application Insights izlemeye izin vermez. SDK sürümü 2.6.1. Yönergeler: ' APPINSIGHTS_INSTRUMENTATIONKEY ' uygulama ayarlarını Ikey ile ekleyin ve WebApp 'yi bir efekt alacak şekilde yeniden başlatın.| [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft. ApplicationInsights. Injector | 2.6.7 | Bu paket, kodsuz kullanacaksınız Application Insights ekleme için gereken dosyaları içerir | [Paketi indir](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Sonraki adımlar

- İzleyici [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- [Azure Linux Web apps](../../azure-monitor/app/profiler-aspnetcore-linux.md)ASP.NET Core profil.
- Hata ayıklama ASP.NET [anlık görüntüleri](../../azure-monitor/app/snapshot-debugger.md).
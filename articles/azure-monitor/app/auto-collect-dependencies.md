---
title: Azure Application Insights-bağımlılık otomatik koleksiyonu | Microsoft Docs
description: Application Insights bağımlılıkları otomatik olarak toplayıp görselleştirin
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6fe43eb5963f2918f41ad15f1904f883b5e9c298
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678295"
---
# <a name="dependency-auto-collection"></a>Bağımlılık otomatik toplama

Aşağıda, uygulamanızın kodunda ek değişiklik yapılmasına gerek kalmadan otomatik olarak otomatik olarak algılanan bağımlılık çağrılarının Şu anda desteklenen listesi aşağıda verilmiştir. Bu bağımlılıklar, Application Insights [uygulama Haritası](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) ve [işlem tanılama](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) görünümlerinde görselleştirilir. Bağımlılarınızın aşağıdaki listede olmaması durumunda bunu bir [izleme bağımlılığı çağrısıyla](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)el ile izleyebilirsiniz.

## <a name="net"></a>.NET

| Uygulama çerçeveleri| Sürümler |
| ------------------------|----------|
| ASP.NET WebForms | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b>İletişim kitaplıkları</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [EventHubs Istemci SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Istemci SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Depolama istemcileri</b>|  |
| ADO.NET | 4.5 + |

## <a name="java"></a>Java
| Uygulama sunucuları | Sürümler |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [Jpatron EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Uygulama çerçeveleri</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup> *</sup> |
| Java Servlet | 3.1 + |
| <b>İletişim kitaplıkları</b> |  |
| [Apache HTTP Istemcisi](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Depolama istemcileri</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (Beta desteği)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Günlük kitaplıkları</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4J](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Ölçüm kitaplıkları</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> \* Reaktif programlama desteği hariç.
> <br>† [JVM aracısının](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)yüklenmesini gerektirir.

## <a name="nodejs"></a>Node.js

| İletişim kitaplıkları | Sürümler |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0.10 + |
| <b>Depolama istemcileri</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDB](https://www.npmjs.com/package/mongodb); [MongoDB çekirdeği](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2.16. x |
| [PostgreSQL](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [PG havuzu](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Günlük kitaplıkları</b> | |
| [konsola](https://nodejs.org/api/console.html) | 0.10 + |
| [Bayan](https://www.npmjs.com/package/bunyan) | 'in |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| İletişim kitaplıkları | Sürümler |
| ------------------------|----------|
| [XMLHttpRequest nesnesinden](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Tümü |

## <a name="next-steps"></a>Sonraki adımlar

- [.Net](../../azure-monitor/app/asp-net-dependencies.md)için özel bağımlılık izlemeyi ayarlayın.
- [Java](../../azure-monitor/app/java-agent.md)için özel bağımlılık izlemeyi ayarlayın.
- [Özel bağımlılık telemetrisi yazma](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Application Insights türleri ve veri modeli için [veri modeli](../../azure-monitor/app/data-model.md) 'ne bakın.
- Application Insights tarafından desteklenen [platformları](../../azure-monitor/app/platforms.md) inceleyin.

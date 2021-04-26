---
title: Azure Application Insights-bağımlılık otomatik koleksiyonu | Microsoft Docs
description: Application Insights bağımlılıkları otomatik olarak toplayıp görselleştirin
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: aa4d39ca8964e95ca787d236223e2b475a9597c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873838"
---
# <a name="dependency-auto-collection"></a>Bağımlılık otomatik toplama

Aşağıda, uygulamanızın kodunda ek değişiklik yapılmasına gerek kalmadan otomatik olarak otomatik olarak algılanan bağımlılık çağrılarının Şu anda desteklenen listesi aşağıda verilmiştir. Bu bağımlılıklar, Application Insights [uygulama Haritası](./app-map.md) ve [işlem tanılama](./transaction-diagnostics.md) görünümlerinde görselleştirilir. Bağımlılarınızın aşağıdaki listede olmaması durumunda bunu bir [izleme bağımlılığı çağrısıyla](./api-custom-events-metrics.md#trackdependency)el ile izleyebilirsiniz.

## <a name="net"></a>.NET

| Uygulama çerçeveleri| Sürümler |
| ------------------------|----------|
| ASP.NET WebForms | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b> İletişim kitaplıkları</b> |
| [HttpClient](https://dotnet.microsoft.com) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0-en son kararlı sürüm. (Aşağıdaki nota bakın.)
| [EventHubs Istemci SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Istemci SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Depolama istemcileri</b>|  |
| ADO.NET | 4.5 + |

> [!NOTE]
> Microsoft. Data. SqlClient 'nin eski sürümlerinde [bilinen bir sorun](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) var. Bu sorunu azaltmak için 1.1.0 veya üstünü kullanmanızı öneririz. Entity Framework Core, bu sorundan kaçınmak üzere en az 1.1.0 olduğunu teyit etmek için Microsoft. Data. SqlClient 'ın en son kararlı sürümüyle birlikte gelmez.   


## <a name="java"></a>Java
| Uygulama sunucuları | Sürümler |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Uygulama çerçeveleri </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
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
> * Reaktif programlama desteği hariç.
> <br>† [JVM aracısının](./java-agent.md#install-the-application-insights-agent-for-java)yüklenmesini gerektirir.

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

- [.Net](./asp-net-dependencies.md)için özel bağımlılık izlemeyi ayarlayın.
- [Java](./java-agent.md)için özel bağımlılık izlemeyi ayarlayın.
- [Opencensus Python](./opencensus-python-dependency.md)için özel bağımlılık izlemeyi ayarlayın.
- [Özel bağımlılık telemetrisi yazma](./api-custom-events-metrics.md#trackdependency)
- Application Insights türleri ve veri modeli için [veri modeli](./data-model.md) 'ne bakın.
- Application Insights tarafından desteklenen [platformları](./platforms.md) inceleyin.


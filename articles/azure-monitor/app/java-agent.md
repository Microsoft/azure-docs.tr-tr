---
title: Java Web Apps performans izleme-Azure Application Insights
description: Application Insights ile Java Web sitenizin genişletilmiş performansı ve kullanımı izleniyor.
ms.topic: conceptual
ms.date: 01/10/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: c753e4e254890f9198da9bc913b29bdaae335b78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100573828"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Java Web uygulamalarında bağımlılıkları izleme, özel durumlar ve Yöntem yürütme süreleri

> [!IMPORTANT]
> Bu belgede açıklanan yaklaşım artık önerilmez.
>
> Java uygulamalarını izlemek için önerilen yaklaşım, kodu değiştirmeden otomatik izleme kullanmaktır. Lütfen [Application Insights Java 3,0 Aracısı](./java-in-process-agent.md)için yönergeleri izleyin.

[Java Web uygulamanızı APPLICATION INSIGHTS SDK ile][java]belirttiyseniz, herhangi bir kod değişikliği yapmadan daha derin Öngörüler almak Için Java aracısını kullanabilirsiniz:

* **Bağımlılıklar:** Uygulamanızın diğer bileşenlere yaptığı çağrılar hakkındaki veriler (şunlar dahil):
  * Apache HttpClient, OkHttp ve ile yapılan **gıden http çağrıları** `java.net.HttpURLConnection` yakalanır.
  * Jedsıs istemcisi aracılığıyla yapılan **redsıs çağrıları** yakalanır.
  * **JDBC sorguları** -MySQL ve PostgreSQL için çağrı 10 saniyeden uzun sürerse, aracı sorgu planını raporlar.

* **Uygulama günlüğü:** HTTP istekleri ve diğer telemetri ile uygulama günlüklerinizi yakalayın ve ilişkilendirin
  * **Log4J 1,2**
  * **Log4j2**
  * **Logback**

* **Daha iyi işlem adlandırması:** (portalda isteklerin toplaması için kullanılır)
  * **Yay** tabanlı `@RequestMapping` .
  * **Jax-RS** tabanlı `@Path` . 

Java aracısını kullanmak için sunucunuza yüklersiniz. Web uygulamalarınızın [Application Insights Java SDK 'sı][java]ile işaretlenmiş olması gerekir. 

## <a name="install-the-application-insights-agent-for-java"></a>Java için Application Insights aracısını yükler
1. Java sunucunuzu çalıştıran makinede [2. x aracısını indirin](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/2.6.2). Lütfen kullandığınız 2. x Java aracısının sürümünün kullandığınız 2. x Application Insights Java SDK sürümü ile eşleştiğinden emin olun.
2. Uygulama sunucusu başlangıç betiğini düzenleyin ve aşağıdaki JVM bağımsız değişkenini ekleyin:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Örneğin, bir Linux makinesindeki Tomcat:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Uygulama sunucunuzu yeniden başlatın.

## <a name="configure-the-agent"></a>Configure the agent
Adlı bir dosya oluşturun `AI-Agent.xml` ve aracı jar dosyasıyla aynı klasöre yerleştirin.

XML dosyasının içeriğini ayarlayın. İstediğiniz özellikleri eklemek veya atlamak için aşağıdaki örneği düzenleyin.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Ek yapılandırma (yay önyüklemesi)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Azure Uygulama Hizmetleri için aşağıdakileri yapın:

* Ayarlar > Uygulama Ayarları'nı seçin.
* Uygulama Ayarları'nın altında yeni bir anahtar değer çifti ekleyin:

Anahtar: `JAVA_OPTS` değer: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.6.2.jar`

Aracı, projenizde D:/Home/site/Wwwroot/dizinde bitecek bir kaynak olarak paketlenmesi gerekir. Aracının doğru App Service dizininde olduğunu, **geliştirme araçları**  >  **Gelişmiş Araçlar**  >  **hata ayıklama konsolu** ' na giderek ve site dizininin içeriğini inceleyerek emin olabilirsiniz.    

* Ayarları Kaydedin ve uygulamanızı Yeniden başlatın. (Bu adımlar yalnızca Windows üzerinde çalışan uygulama hizmetleri için geçerlidir.)

> [!NOTE]
> AI-Agent.xml ve aracı jar dosyası aynı klasörde olmalıdır. Bunlar genellikle `/resources` Proje klasörüne yerleştirilir.  

#### <a name="enable-w3c-distributed-tracing"></a>W3C dağıtılmış izlemeyi etkinleştir

Aşağıdakileri AI-Agent.xml ekleyin:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Geriye dönük uyumluluk modu varsayılan olarak etkindir ve enableW3CBackCompat parametresi isteğe bağlıdır ve yalnızca kapatmak istediğinizde kullanılmalıdır. 

İdeal olarak, tüm hizmetlerinizin W3C protokolünü destekleyen SDK 'ların daha yeni bir sürümüne güncelleştirildiği durum söz konusu olabilir. En kısa sürede W3C desteğiyle SDK 'ların daha yeni bir sürümüne taşınması önemle önerilir.

**Hem [gelen](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) hem de giden (aracı) yapılandırmalarının** tam olarak aynı olduğundan emin olun.

## <a name="view-the-data"></a>Verileri görüntüleme
Application Insights kaynağında, toplanan uzak bağımlılık ve Yöntem yürütme süreleri [performans kutucuğunun altında][metrics]görüntülenir.

Bağımlılık, özel durum ve Yöntem raporlarının tek tek örneklerini aramak için [arama][diagnostic]' yı açın.

[Bağımlılık sorunlarını tanılama-daha fazla bilgi edinin](./asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Sorularınız mı var? Sorunlarınız mı var?
* Veri yok mu? [Güvenlik Duvarı özel durumlarını ayarlama](./ip-addresses.md)
* [Java sorun giderme](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md


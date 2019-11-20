---
title: Azure Application Insights Java izleme günlüklerini keşfet | Microsoft Docs
description: Application Insights 'de Log4J veya Logback izlemeleri ara
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/18/2019
ms.openlocfilehash: 23e3116a0cc3283191d00079e0926dc206e677f0
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819345"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Application Insights Java izleme günlüklerini keşfet
İzleme için Logback veya Log4J (v 1.2 veya v 2.0) kullanıyorsanız, izleme günlüklerinizin otomatik olarak gönderilmesini ve bunları araştırıp araygeçirebileceğiniz Application Insights sağlayabilirsiniz.

> [!TIP]
> Uygulamanız için yalnızca Application Insights araçları anahtarınızı bir kez ayarlamanız gerekir. Java Spring gibi bir çerçeve kullanıyorsanız, anahtarı uygulamanızın yapılandırmasına başka bir yere kaydetmiş olabilirsiniz.

## <a name="using-the-application-insights-java-agent"></a>Application Insights Java Aracısı 'nı kullanma

Application Insights Java aracısını, `AI-Agent.xml` dosyasındaki özelliği etkinleştirerek günlüklerinizi otomatik olarak yakalayacak şekilde yapılandırabilirsiniz:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Alternatif olarak, aşağıdaki yönergeleri izleyebilirsiniz.

## <a name="install-the-java-sdk"></a>Java SDK 'sını yükler

Daha önce yapmadıysanız, [Java için APPLICATION INSIGHTS SDK][java]'yı yüklemek için yönergeleri izleyin.

## <a name="add-logging-libraries-to-your-project"></a>Projenize günlük kitaplıklarını ekleme
*Projeniz için uygun yolu seçin.*

#### <a name="if-youre-using-maven"></a>Maven kullanıyorsanız...
Projeniz zaten derleme için Maven kullanmak üzere ayarlandıysa, aşağıdaki kod parçacıklarıyla POTM. xml dosyanıza birleştirme yapın.

Ardından, indirilen ikilileri almak için Proje bağımlılıklarını yenileyin.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Gradle kullanıyorsanız...
Projeniz zaten derleme için Gradle kullanmak üzere ayarlandıysa, yapı. Gradle dosyanızdaki `dependencies` grubuna aşağıdaki satırlardan birini ekleyin:

Ardından, indirilen ikilileri almak için Proje bağımlılıklarını yenileyin.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Aksi taktirde...
Application Insights Java SDK 'sını el ile yüklemek için yönergeleri izleyin (Maven Merkezi sayfasına ulaşan bağlantı kurulduktan sonra) uygun bir uygulama için karşıdan yükleme bölümündeki ' jar ' bağlantısına tıklayın.

| Medi | İndirin | Kitaplık |
| --- | --- | --- |
| Logback |[Logback uygulama jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |ApplicationInsights-günlüğe kaydetme-logback |
| Log4J v 2.0 |[Log4J v2 Ekleyici jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |ApplicationInsights-günlüğe kaydetme-log4j2 |
| Log4J v 1.2 |[Log4J v 1.2 Ekleyici jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |ApplicationInsights-günlüğe kaydetme-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Günlüğe kaydetme çatısını appfıme ekleyin
İzlemeleri almaya başlamak için, ilgili kod parçacığını Log4J veya Logback yapılandırma dosyasına birleştirin: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights appby, yapılandırılmış bir günlükçü tarafından ve kök günlükçü tarafından gerek olmadan (Yukarıdaki kod örneklerinde gösterildiği gibi) başvuru yapabilir.

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Application Insights portalındaki izlemelerinizi keşfet
Projenizi Application Insights izlemeleri gönderecek şekilde yapılandırdığınıza göre, bu izlemeleri [arama][diagnostic] dikey penceresinde Application Insights portalında görüntüleyebilir ve arayabilirsiniz.

Günlükçüler aracılığıyla gönderilen özel durumlar portalda özel durum telemetrisi olarak görüntülenir.

![Application Insights portalında, ara ' yı açın](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Sonraki adımlar
[Tanılama arama][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md



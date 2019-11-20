---
title: Azure Application Insights Java SDK ile mikro ölçüm kullanma | Microsoft Docs
description: 'Application Insights Spring Boot ve Spring Boot uygulamaları ile mikro ölçer kullanma hakkında adım adım kılavuz. '
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: 267665c97f683740c05ae6602a416225c79aa44c
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819315"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Azure Application Insights Java SDK ile mikro ölçüm kullanma
Mikro ölçüm uygulama izleme, JVM tabanlı uygulama kodu için ölçümleri ölçer ve verileri en sevdiğiniz izleme sistemlerine aktarmanıza olanak tanır. Bu makale, hem Spring Boot hem de Spring Boot uygulamaları için Application Insights ile mikro ölçer 'i nasıl kullanacağınızı öğretir.

## <a name="using-spring-boot-15x"></a>Spring Boot 1.5 x kullanma
Pod. xml veya Build. Gradle dosyanıza aşağıdaki bağımlılıkları ekleyin: 
* [Application Insights Spring-Boot-Starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 veya üzeri
* Mikro ölçüm Azure kayıt defteri 1.1.0 veya üzeri
* [Mikro ölçüm yay eski](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 veya üzeri (Bu Işlem, Spring Framework içindeki otomatik yapılandırma kodunu geri barındırır).
* [ApplicationInsights kaynağı](../../azure-monitor/app/create-new-resource.md )

Adımlar

1. Spring Boot uygulamanızın pom. xml dosyasını güncelleştirin ve içine aşağıdaki bağımlılıkları ekleyin:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Aşağıdaki özelliği kullanarak Application. Properties veya YML dosyasını Application Insights Izleme anahtarıyla güncelleştirin:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Uygulamanızı derleyin ve çalıştırın
2. Yukarıdaki, Azure Izleyici 'ye otomatik olarak toplanan önceden toplanmış ölçümler ile çalışmaya başlamanızı sağlamalıdır. Application Insights Spring Boot Starter 'ın ince ayar hakkında daha fazla bilgi için [GitHub 'daki Benioku](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md)dosyasına bakın.

## <a name="using-spring-2x"></a>Spring 2. x kullanma

Pod. xml veya Build. Gradle dosyanıza aşağıdaki bağımlılıkları ekleyin:

* Application Insights Spring-Boot-Starter 2.1.2 'yi veya üzeri
* Azure-Spring-Boot-ölçümleri-Starters 2.0.7 veya üzeri
* [Application Insights kaynağı](../../azure-monitor/app/create-new-resource.md )

Adımlar:

1. Spring Boot uygulamanızın Pod. xml dosyasını güncelleştirin ve aşağıdaki bağımlılığı ekleyin:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Aşağıdaki özelliği kullanarak Application. Properties veya YML dosyasını Application Insights Izleme anahtarıyla güncelleştirin:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Uygulamanızı derleyin ve çalıştırın
4. Yukarıdaki, Azure Izleyici 'ye otomatik olarak toplanan önceden toplanmış ölçümler ile çalışmaya başlamanızı sağlamalıdır. Application Insights Spring Boot Starter 'ın ince ayar hakkında daha fazla bilgi için [GitHub 'daki Benioku](https://github.com/Microsoft/azure-spring-boot/releases/latest)dosyasına bakın.

Varsayılan ölçümler:

*    Tomcat, JVM, Logback ölçümleri, Log4J ölçümleri, çalışma süresi ölçümleri, Işlemci ölçümleri, Filedescriptormetrikleri için otomatik olarak yapılandırılmış ölçümler.
*    Örneğin, bir sınıf yolunda Netflix Hyçabax varsa, bu ölçümleri de alırız. 
*    Aşağıdaki ölçümler ilgili bede ekleyerek kullanılabilir. 
        - Cacheölçümlerini (CaffeineCache, EhCache2, GuavaCache, Hazonbir önbellek, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - Jettymetrikleri 
        - OkHttp3 ölçümleri 
        - Kafka ölçümleri 

 

Otomatik ölçüm toplamayı devre dışı bırakma: 
 
- JVM ölçümleri: 
    - Management. ölçümler. ciltler. JVM. Enabled = false 
- Günlüğe kaydetme ölçümleri: 
    - Management. ölçümler. ciltler. logback. Enabled = false
- Çalışma süresi ölçümleri: 
    - Management. ölçümler. ciltler. çalışma süresi. Enabled = false 
- İşlemci ölçümleri:
    -  Management. ölçümler. ciltler. Processor. Enabled = false 
- Dosya Descriptormetrikleri:
    - Management. ölçümler. ciltler. Files. Enabled = false 
- Sınıfyolunda kitaplık varsa hyçabax ölçümleri: 
    - Management. ölçümler. ciltler. hyçabax. Enabled = false 
- AspectJ ölçümleri, sınıfyolunda kitaplık: 
    - yay. AOP. Enabled = false 

> [!NOTE]
> Yukarıdaki özellikleri, Spring Boot uygulamanızın Application. Properties veya Application. yıml dosyasında belirtin

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Spring Boot Web uygulamalarıyla mikro ölçüm kullanın

Pod. xml veya Build. Gradle dosyanıza aşağıdaki bağımlılıkları ekleyin:

* Application Insights Web Auto 2.5.0 veya üzeri
* Mikro ölçüm Azure kayıt defteri 1.1.0 veya üzeri
* [Application Insights kaynağı](../../azure-monitor/app/create-new-resource.md )

Adımlar:

1. Pod. xml veya Build. Gradle dosyanıza aşağıdaki bağımlılıkları ekleyin:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. `ApplicationInsights.xml` dosyasını Resources klasörüne Yerleştir:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Örnek servlet sınıfı (bir Zamanlayıcı ölçümü yayar):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Örnek yapılandırma sınıfı:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Ölçümler hakkında daha fazla bilgi edinmek için [mikro ölçüm belgelerine](https://micrometer.io/docs/)bakın.

Farklı ölçüm türlerinin nasıl oluşturulacağı hakkında diğer örnek kod[, resmi mikro ölçüm GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)deposunda bulunabilir.

## <a name="how-to-bind-additional-metrics-collection"></a>Ek ölçüm koleksiyonu bağlama

### <a name="springbootspring"></a>SpringBoot/Spring

İlgili ölçüm kategorisinin bir çekirdeklere oluşturun. Örneğin, guava önbellek ölçümlerinin gerekli olduğunu varsayalım:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Varsayılan olarak etkinleştirilmemiş ancak yukarıdaki biçimde bağlanabilen birkaç ölçüm vardır. Tüm liste için [resmi mikro ölçüm GitHub deposu](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder )' na bakın.

### <a name="non-spring-apps"></a>Spring olmayan uygulamalar
Aşağıdaki bağlama kodunu yapılandırma dosyasına ekleyin:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Sonraki adımlar

* Mikro ölçüm hakkında daha fazla bilgi için bkz. resmi [mikro ölçüm belgeleri](https://micrometer.io/docs).
* Azure 'daki Spring hakkında bilgi edinmek için bkz. [Azure 'da resmi yay belgeleri](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).

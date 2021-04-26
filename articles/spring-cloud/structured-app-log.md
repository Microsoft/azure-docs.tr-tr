---
title: Azure yay bulutu için yapılandırılmış uygulama günlüğü | Microsoft Docs
description: Bu makalede, Azure Spring Cloud 'da yapılandırılmış uygulama günlüğü verilerinin nasıl oluşturulacağı ve toplanacağı açıklanmaktadır.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 844530c6d1650b5fddd27b10c775c4364a3f5147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878757"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Azure yay bulutu için yapılandırılmış uygulama günlüğü

Bu makalede, Azure Spring Cloud 'da yapılandırılmış uygulama günlüğü verilerinin nasıl oluşturulacağı ve toplanacağı açıklanmaktadır. Azure yay bulutu, doğru yapılandırmayla Log Analytics aracılığıyla faydalı uygulama günlüğü sorgusu ve analizi sağlar.

## <a name="log-schema-requirements"></a>Günlük şeması gereksinimleri
Günlük sorgu deneyimini geliştirmek için, bir uygulama günlüğünün JSON biçiminde olması ve bir şemaya uyması gerekir. Azure yay bulutu, uygulamanızı ve akışınızı Log Analytics ayrıştırmak için bu şemayı kullanır. 

**JSON şema gereksinimleri:**

| JSON anahtarı      | JSON değer türü|  Gerekli | Log Analytics sütun| Description |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | string      |     Yes   | AppTimestamp    | UTC biçiminde zaman damgası  |
| Medi        | dize      |     No    | Medi          | Medi                   |
| düzey         | dize      |     No    | CustomLevel     | günlük düzeyi                |
| thread        | dize      |     No    | İş Parçacığı          | thread                   |
| message       | dize      |     No    | İleti         | günlük iletisi              |
| stackTrace    | dize      |     No    | StackTrace      | özel durum yığın izlemesi    |
| exceptionClass| dize      |     No    | ExceptionClass  | özel durum sınıfı adı     |
| MDC           | iç içe JSON |     No    |                 | eşlenen tanılama bağlamı|
| MDC. TraceID   | dize      |     No    | Izleme kimliği         |Dağıtılmış izleme için izleme kimliği|
| MDC. Spankimliği    | dize      |     No    | Spanıd          |Dağıtılmış izleme için yayılma kimliği |
|               |             |           |                 |                          |

* "Timestamp" alanı zorunludur ve UTC biçiminde olmalıdır, diğer tüm alanlar isteğe bağlıdır.
* "MDC" alanındaki "TraceID" ve "Spanıd", izleme amacıyla kullanılır.
* Her JSON kaydını tek bir satırda günlüğe kaydedin. 

**Günlük kaydı örneği** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Şemaya uyumlu JSON günlüğü oluştur  
Spring uygulamaları için, [logback](http://logback.qos.ch/) ve [log4j2](https://logging.apache.org/log4j/2.x/)gibi ortak [günlük çerçeveleri](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)kullanarak beklenen JSON günlük biçimi oluşturabilirsiniz. 

### <a name="log-with-logback"></a>Logback ile günlüğe kaydet 
Spring Boot başlangıçlarını kullanırken, logback varsayılan olarak kullanılır. Logback uygulamaları için, JSON biçimli günlük oluşturmak için [logstash-Encoder](https://github.com/logstash/logstash-logback-encoder) komutunu kullanın. Bu yöntem, Spring Boot sürüm 2.1 + ' de desteklenir. 

Yordam:

1. pom.xml dosyanıza logstash bağımlılığı ekleyin. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. logback.xml config dosyanızı JSON biçimini ayarlamak için güncelleştirin.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Log4j2 ile günlüğe kaydet 

Log4j2 uygulamaları için JSON biçimli günlük oluşturmak için [JSON şablonu düzeni](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) kullanın. Bu yöntem, Spring Boot sürüm 2.1 + ' de desteklenir.

Yordam:

1. pom.xml dosyanızda, ' den hariç tutun `spring-boot-starter-logging` `spring-boot-starter` , bağımlılıklar ekleyin `spring-boot-starter-log4j2` `log4j-layout-template-json` .

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Sınıf yolunuzda bir JSON düzen şablonu dosyası jsonTemplate.jshazırlayın.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. log4j2.xml config dosyanızda bu JSON düzen şablonunu kullanın. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Log Analytics günlükleri çözümleyin

Uygulamanız düzgün kurulduktan sonra, uygulama konsolu günlüğü Log Analytics akışa alınır. Yapı Log Analytics içinde verimli sorgu sağlar.

### <a name="check-log-structure-in-log-analytics"></a>Log Analytics günlük yapısını denetle
Aşağıdaki yordamı kullanın:
1. Hizmet örneğinizin hizmete genel bakış sayfasına gidin.
2. Bölümünde giriş ' e tıklayın `Logs` `Monitoring` .
3. Bu sorguyu çalıştırın.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Uygulama günlükleri aşağıdaki görüntüde gösterildiği gibi döndürülür:

![JSON günlüğü gösterme](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Hata içeren günlük girişlerini göster

Hata içeren günlük girişlerini gözden geçirmek için aşağıdaki sorguyu çalıştırın:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Hataları bulmak için bu sorguyu kullanın veya belirli bir özel durum sınıfını veya hata kodunu bulmak için sorgu terimlerini değiştirin. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Belirli bir TraceID için günlük girdilerini göster
Belirli bir izleme KIMLIĞI "trace_id" için günlük girişlerini gözden geçirmek için aşağıdaki sorguyu çalıştırın:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Sonraki Adımlar
* Günlük sorgusu hakkında daha fazla bilgi edinmek için bkz. [Azure izleyici 'de günlük sorgularına başlama](../azure-monitor/logs/get-started-queries.md)
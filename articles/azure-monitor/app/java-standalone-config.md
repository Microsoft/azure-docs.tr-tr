---
title: Yapılandırma seçenekleri-Java için Azure Izleyici Application Insights
description: Java için Azure Izleyici Application Insights yapılandırma
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b78aaa659598e6eb58841c5cef0c209daaced5e0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811985"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Yapılandırma seçenekleri-Java için Azure Izleyici Application Insights

> [!WARNING]
> **3,0 önizlemeden yükseltiyorsanız**
>
> JSON yapısı tamamen değiştiği için, tüm küçük harfleri içeren dosya adının yanı sıra, lütfen tüm yapılandırma seçeneklerini dikkatle gözden geçirin.

## <a name="connection-string-and-role-name"></a>Bağlantı dizesi ve rol adı

Bağlantı dizesi ve rol adı, başlamak için gereken en yaygın ayarlardır:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Bağlantı dizesi gereklidir ve farklı uygulamalardan aynı Application Insights kaynağına veri gönderişinizde rol adı önemlidir.

Daha fazla ayrıntı ve ek yapılandırma seçeneklerini aşağıda bulabilirsiniz.

## <a name="configuration-file-path"></a>Yapılandırma dosyası yolu

Varsayılan olarak Application Insights Java 3,0, yapılandırma dosyasının adlandırılması `applicationinsights.json` ve ile aynı dizinde bulunması beklenir `applicationinsights-agent-3.0.3.jar` .

Kendi yapılandırma dosya yolunuzu aşağıdakilerden birini kullanarak belirtebilirsiniz

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` ortam değişkeni veya
* `applicationinsights.configuration.file` Java sistem özelliği

Göreli bir yol belirtirseniz, bulunduğu dizine göre çözümlenir `applicationinsights-agent-3.0.3.jar` .

## <a name="connection-string"></a>Bağlantı dizesi

Bağlantı dizesi gerekiyor. Bağlantı dizenizi Application Insights kaynağınız için bulabilirsiniz:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights bağlantı dizesi":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Bağlantı dizesini, ortam değişkenini kullanarak da ayarlayabilirsiniz `APPLICATIONINSIGHTS_CONNECTION_STRING` (daha sonra JSON yapılandırmasında belirtilen bağlantı dizesine göre öncelikli olur).

Bağlantı dizesinin ayarlanması, Java aracısını devre dışı bırakacak.

## <a name="cloud-role-name"></a>Bulut rolü adı

Uygulama eşlemesindeki bileşeni etiketlemek için bulut rolü adı kullanılır.

Bulut rolü adını ayarlamak istiyorsanız:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Bulut rolü adı ayarlanmamışsa, uygulama eşlemesindeki bileşeni etiketlemek için Application Insights kaynağın adı kullanılır.

Ayrıca, ortam değişkenini kullanarak `APPLICATIONINSIGHTS_ROLE_NAME` (JSON yapılandırmasında belirtilen bulut rolü adından öncelikli olacak şekilde) bulut rolü adını da ayarlayabilirsiniz.

## <a name="cloud-role-instance"></a>Bulut rolü örneği

Bulut rolü örneği, varsayılan olarak makine adına sahiptir.

Bulut rolü örneğini makine adı yerine farklı bir şekilde ayarlamak istiyorsanız:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Ayrıca, ortam değişkenini kullanarak `APPLICATIONINSIGHTS_ROLE_INSTANCE` (JSON yapılandırmasında belirtilen bulut rolü örneğinden öncelikli olacak şekilde) bulut rolü örneğini de ayarlayabilirsiniz.

## <a name="sampling"></a>Örnekleme

Maliyeti azaltmanız gerekiyorsa örnekleme yararlı olur.
Örnekleme işlem KIMLIĞI üzerinde (izleme KIMLIĞI olarak da bilinir) bir işlev olarak gerçekleştirilir, böylece aynı işlem KIMLIĞI her zaman aynı örnekleme kararına neden olur. Bu, içindeki dağıtılmış bir işlemin parçalarını, diğer bölümleri örneklenirken almanızı sağlar.

Örneğin, örneklemeyi %10 olarak ayarlarsanız, işlemlerinizin yalnızca %10 ' u görürsünüz, ancak bu %10 ' un her biri uçtan uca işlem ayrıntılarına sahip olur.

Örnekleme, **her türlü işlemin yaklaşık 1/3** ' i yakalamak için nasıl ayarlanacağını gösteren bir örnektir. kullanım örneği için doğru olan örnekleme oranını ayarladığınızdan emin olun:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Ayrıca, `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (JSON yapılandırmasında belirtilen örnekleme yüzdesine göre daha öncelikli olacak şekilde), ortam değişkenini kullanarak örnekleme yüzdesini de ayarlayabilirsiniz.

> [!NOTE]
> Örnekleme yüzdesi için, N 'nin bir tamsayı olduğu, 100/N 'e yakın bir yüzde seçin. Şu anda örnekleme diğer değerleri desteklemiyor.

## <a name="sampling-overrides-preview"></a>Örnekleme geçersiz kılmaları (Önizleme)

Bu özellik 3.0.3 'den başlayarak önizlemededir.

Örnekleme geçersiz kılmaları [Varsayılan örnekleme yüzdesini](#sampling)geçersiz kılmanızı sağlar, örneğin:
* Gürültülü durum denetimleri için örnekleme yüzdesini 0 (veya küçük bir değer) olarak ayarlayın.
* Gürültülü bağımlılık çağrıları için örnekleme yüzdesini 0 (veya küçük bir değer) olarak ayarlayın.
* Örnekleme yüzdesini önemli bir istek türü için 100 olarak ayarlayın (örn. `/login` ), varsayılan örneklemenin daha düşük bir değere yapılandırılmış olmasına rağmen.

Daha fazla bilgi için [örnekleme geçersiz kılma](./java-standalone-sampling-overrides.md) belgelerini inceleyin.

## <a name="jmx-metrics"></a>JMX ölçümleri

Bazı ek JMX ölçümleri toplamak istiyorsanız:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` Bu JMX ölçüsüne atanacak ölçüm adıdır (herhangi bir şey olabilir).

`objectName` , toplamak istediğiniz JMX MBean öğesinin [nesne adıdır](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) .

`attribute` , toplamak istediğiniz JMX MBean içindeki öznitelik adıdır.

Sayısal ve Boole JMX ölçüm değerleri desteklenir. Boolean JMX ölçümleri `0` , false için ve true için ile eşleştirilir `1` .

## <a name="custom-dimensions"></a>Özel boyutlar

Tüm telemetrinize özel boyutlar eklemek istiyorsanız:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` , başlangıçta belirtilen ortam değişkeninden değeri okumak için kullanılabilir.

> [!NOTE]
> Sürüm 3.0.2 'den başlayarak, adlı özel bir boyut eklerseniz `service.version` , bu değer `application_Version` özel bir boyut yerine Application Insights logs tablosundaki sütununda depolanır.

## <a name="telemetry-processors-preview"></a>Telemetri işlemcileri (Önizleme)

Bu özellik önizlemede.

İstek, bağımlılık ve izleme telemetrisine uygulanacak kuralları yapılandırmanıza olanak tanır, örneğin:
 * Hassas verileri maskeleme
 * Koşullu özel boyutlar ekleyin
 * Azure portal benzer Telemetriyi toplamak için kullanılan span adını güncelleştirin.
 * Alım maliyetlerini denetlemek için belirli span özniteliklerini bırakın.

Daha fazla bilgi için [telemetri işlemcisi](./java-standalone-telemetry-processors.md) belgelerini inceleyin.

> [!NOTE]
> Alım maliyetini denetlemek için belirli (tam) yayılmayı arıyorsanız, bkz. [örnekleme geçersiz kılmaları](./java-standalone-sampling-overrides.md).

## <a name="auto-collected-logging"></a>Otomatik toplanan günlüğe kaydetme

Log4J, Logback ve Java. util. Logging otomatik olarak işaretlenir ve bu günlüğe kaydetme çerçeveleri aracılığıyla gerçekleştirilen günlüğe kaydetme otomatik olarak toplanır.

Günlüğe kaydetme yalnızca, günlük çerçevesi için yapılandırılan düzeyi karşılıyorsa yakalanır ve ikincisi de Application Insights için yapılandırılan düzeyi karşılar.

Örneğin, günlük oluşturma çatısı `WARN` paketten (ve üzeri) günlüğe kaydedilecek şekilde yapılandırıldıysa `com.example` ve Application Insights `INFO` , (ve üzerine) yakalanacak şekilde yapılandırıldıysa, Application Insights yalnızca paketten (ve üzeri) yakalama yapılır `WARN` `com.example` .

Application Insights için yapılandırılan varsayılan düzey `INFO` . Bu düzeyi değiştirmek istiyorsanız:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Ayrıca, ortam değişkenini kullanarak düzeyi ayarlayabilirsiniz `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (daha sonra JSON yapılandırmasında belirtilen düzeye göre önceliğe sahip olur).

Bunlar `level` , dosyada belirtebileceğiniz geçerli değerlerdir `applicationinsights.json` ve farklı günlük çerçeveleri içindeki günlük düzeylerine nasıl karşılık gelmektedir:

| düzey             | Log4J  | Logback | TEM     |
|-------------------|--------|---------|---------|
| KAPALI               | KAPALI    | KAPALI     | KAPALI     |
| HATAYA             | HATAYA  | HATA   | OLAN  |
| HATA (veya cıddı) | HATA  | HATA   | OLAN  |
| UYAR (veya uyarı) | UYARıR   | UYARıR    | UYARI |
| BILGISINE              | BILGISINE   | BILGISINE    | BILGISINE    |
| KURULUMUNUN            | HATA AYIKLAMA  | HATA AYIKLAMA   | KURULUMUNUN  |
| Hata ayıklama (veya AYRıNTıLı)   | HATA AYIKLAMA  | HATA AYIKLAMA   | AYRıNTı    |
| ZARIF             | HATA AYIKLAMA  | HATA AYIKLAMA   | ZARIF   |
| Izleme (veya FINEST) | TRACE  | TRACE   | FıNEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Bir özel durum nesnesi günlükçü 'e geçirilirse, günlük iletisi (ve özel durum nesnesi ayrıntıları) tablo yerine tablonun altında Azure portal görüntülenir `exceptions` `traces` .

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Otomatik toplanan mikro ölçüm ölçümleri (Spring Boot çalıştırıcı ölçümleri dahil)

Uygulamanız [mikro ölçüm](https://micrometer.io)kullanıyorsa, mikro ölçüm küresel kayıt defterine gönderilen ölçümler otomatik olarak toplanır.

Ayrıca, uygulamanız [Spring Boot çalıştırıcı](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)kullanıyorsa, Spring Boot çalıştırıcı tarafından yapılandırılan ölçümler de otomatik olarak toplanır.

Mikro ölçüm ölçümlerinin otomatik toplanmasını devre dışı bırakmak için (Spring Boot çalıştırıcı ölçümleri dahil):

> [!NOTE]
> Özel ölçümler ayrı olarak faturalandırılır ve ek maliyetler oluşturabilir. Ayrıntılı [fiyatlandırma bilgilerini](https://azure.microsoft.com/pricing/details/monitor/)kontrol ettiğinizden emin olun. Mikro ölçüm ve yay çalıştırıcı ölçümlerini devre dışı bırakmak için aşağıdaki yapılandırmayı yapılandırma dosyanıza ekleyin.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="auto-collected-azure-sdk-telemetry-preview"></a>Otomatik toplanan Azure SDK telemetrisi (Önizleme)

En son Azure SDK kitaplıklarının birçoğu telemetri yayar ( [tam listeye](./java-in-process-agent.md#azure-sdks-preview)bakın).

Application Insights Java 3.0.3 'den başlayarak bu Telemetriyi yakalamayı etkinleştirebilirsiniz.

Bu özelliği etkinleştirmek istiyorsanız:

```json
{
  "preview": {
    "instrumentation": {
      "azureSdk": {
        "enabled": true
      }
    }
  }
}
```

Bu özelliği, ortam değişkenini kullanarak da etkinleştirebilirsiniz `APPLICATIONINSIGHTS_PREVIEW_INSTRUMENTATION_AZURE_SDK_ENABLED`
(Bu daha sonra JSON yapılandırmasında belirtilen etkin bir önceliğe sahip olacaktır).

## <a name="suppressing-specific-auto-collected-telemetry"></a>Belirli otomatik toplanan Telemetriyi gizleme

3.0.3 sürümünden başlayarak, belirli otomatik toplanan telemetri şu yapılandırma seçenekleri kullanılarak gizlenebilir:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

Ayrıca, bu ortam değişkenlerini kullanarak bu enstrümanları da gizleyebilirsiniz:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(Bu daha sonra JSON yapılandırmasında belirtilen etkin bir önceliğe sahip olacaktır).

> Daha ayrıntılı denetim arıyorsanız, örn. bazı redin çağrılarını bastırmak, ancak tüm redin çağrılarını önlemek için bkz. [örnekleme geçersiz kılmaları](./java-standalone-sampling-overrides.md).

## <a name="heartbeat"></a>Sinyal

Application Insights Java 3,0, her 15 dakikada bir sinyal ölçümü gönderir. Uyarıları tetiklemek için sinyal ölçümünü kullanıyorsanız, bu sinyal sıklığını artırabilirsiniz:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Sinyal verileri Application Insights kullanımını izlemek için kullanıldığından, aralığı 15 dakikadan daha uzun bir süre artıramezsiniz.

## <a name="http-proxy"></a>HTTP proxy 'Si

Uygulamanız bir güvenlik duvarının arkasındaysa ve Application Insights doğrudan bağlanamıyorsa (bkz. [Application Insights tarafından kullanılan IP adresleri](./ip-addresses.md)), Application Insights Java 3,0 ' i bir http proxy 'si kullanacak şekilde yapılandırabilirsiniz:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3,0, ayrıca genel `-Dhttps.proxyHost` ve `-Dhttps.proxyPort` bunlar ayarlananlar için de kullanılır.

## <a name="metric-interval"></a>Ölçüm aralığı

Bu özellik önizlemede.

Varsayılan olarak ölçümler her 60 saniyede yakalanır.

3.0.3 sürümünden başlayarak bu aralığı değiştirebilirsiniz:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

Bu ayar tüm bu ölçümler için geçerlidir:

* Varsayılan performans sayaçları, örn. CPU ve bellek
* Varsayılan özel ölçümler, ör. çöp toplama zamanlaması
* Yapılandırılmış JMX ölçümleri ([yukarıya bakın](#jmx-metrics))
* Mikro ölçüm ölçümleri ([yukarıya bakın](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "Opentelemetri API 'sinin 1,0 'e ulaşması için Opentelemetri desteği özel önizlemede"

[//]: # "# # Opentelemetri API 1,0 öncesi sürümler için destek"

[//]: # "Opentelemetri API 'si henüz kararlı olmadığından, Opentelemetri API 'sinin 1,0 öncesi sürümleri için destek kabul edilir"
[//]: # "Bu nedenle, aracının her sürümü Opentelemetri API 'sinin yalnızca belirli bir 1,0 öncesi sürümlerini destekler"
[//]: # "(Bu sınırlama, Opentelemetri API 1,0 yayımlandıktan sonra uygulanmaz)."

[//]: # "' ' ' JSON"
[//]: # "{"
[//]: # "  \"Önizleme \" : {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Kendi kendine tanılama

"Kendi kendine tanılama" Application Insights Java 3,0 ' dan iç günlüğe kaydetme anlamına gelir.

Bu işlevsellik, Application Insights ile ilgili sorunları saptamak ve tanılamak için yararlı olabilir.

Varsayılan olarak, Java 3,0 günlüklerini `INFO` `applicationinsights.log` Bu yapılandırmaya karşılık gelen hem dosya hem de konsola Application Insights.

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination``file`, veya ' den biri `console` olabilir `file+console` .

`level` ,,,,, veya bunlardan biri olabilir `OFF` `ERROR` `WARN` `INFO` `DEBUG` `TRACE` .

`path` mutlak veya göreli bir yol olabilir. Göreli yollar, bulunduğu dizine göre çözümlenir `applicationinsights-agent-3.0.3.jar` .

`maxSizeMb` , oturum açmadan önce günlük dosyasının en büyük boyutudur.

`maxHistory` , tutulan günlük dosyalarının (geçerli günlük dosyasına ek olarak) alınan sayısıdır.

Sürüm 3.0.2 'den başlayarak, ortam değişkenini kullanarak kendi kendine tanılamayı da ayarlayabilirsiniz `level` `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (daha sonra JSON yapılandırmasında belirtilen kendi kendine tanılama düzeyine göre öncelikli olur).

## <a name="an-example"></a>Örnek

Bu yalnızca bir yapılandırma dosyasının birden çok bileşenle nasıl göründüğünü göstermek için bir örnektir.
Lütfen gereksinimlerinize göre belirli seçenekleri yapılandırın.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

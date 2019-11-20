---
title: Azure Application Insights ile Java web uygulaması analizi | Microsoft Docs
description: 'Application Insights ile Java web uygulamaları için Uygulama Performansı İzleme. '
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 28fbb5fcfba2b346d0519dec79e538b1e513b7dd
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817134"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Bir Java web projesiyle Application Insights’ı kullanmaya başlayın

[Application Insights](https://azure.microsoft.com/services/application-insights/), web geliştiricileri için canlı uygulamanızın performansını ve kullanımını anlamanıza yardımcı olan genişletilebilir bir analiz hizmetidir. [İsteği otomatik olarak işaretlemek, bağımlılıkları izlemek ve performans sayaçlarını toplamak](auto-collect-dependencies.md#java), performans sorunlarını ve özel durumları tanılamak ve uygulamanızdaki kullanıcıları izlemek için [kod yazmak][api] üzere kullanın. 

![Genel Bakış örnek verilerinin ekran görüntüsü](./media/java-get-started/overview-graphs.png)

Application Insights; Linux, Unix veya Windows üzerinde çalışan Java uygulamalarını destekler.

Gerekenler:

* Java 7 veya üzeri
* Bir [Microsoft Azure](https://azure.microsoft.com/) aboneliği.

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights bir izleme anahtarı alın
1. [Microsoft Azure portalında](https://portal.azure.com) oturum açın.
2. Bir Application Insights kaynağı oluşturun. Uygulama türünü Java web uygulaması olarak ayarlayın.

3. Yeni kaynağın izleme anahtarını bulun. Bu anahtarı hemen kod projenize yapıştırmalısınız.

    ![Yeni kaynağa genel bakışta, Özellikler'e tıklayıp izleme anahtarını kopyalama](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Java için Application Insights SDK 'sını projenize ekleyin
*Projeniz için uygun yolu seçin.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Maven kullanıyorsanız... <a name="maven-setup" />
Projenizi derleme için zaten Maven kullanmak üzere ayarlanmışsa aşağıdaki kodu pom.xml dosyanızla birleştirin.

Daha sonra, proje bağımlılıklarını ikili dosyaları indirmek için yenileyin.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Gradle kullanıyorsanız... <a name="gradle-setup" />
Projenizi derleme için zaten Gradle kullanmak üzere ayarlanmışsa aşağıdaki kodu build.gradle dosyanızla birleştirin.

Daha sonra, proje bağımlılıklarını ikili dosyaları indirmek için yenileyin.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Aksi takdirde, bağımlılıkları el ile yönetiyorsanız...
[En son sürümü](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) indirin ve önceki sürümleri değiştirerek gerekli dosyaları projenize kopyalayın.

### <a name="questions"></a>Sorular...
* *`-web-auto`, `-web` ve `-core` bileşenleri arasındaki ilişki nedir?*
  * `applicationinsights-web-auto`, çalışma zamanında Application Insights servlet filtresini otomatik olarak kaydederek HTTP servlet istek sayısını ve yanıt sürelerini izleyen ölçümleri sağlar.
  * `applicationinsights-web` Ayrıca, HTTP servlet istek sayısını ve yanıt sürelerini izleyen ölçümleri de verir, ancak uygulamanızda Application Insights servlet filtresi 'nin el ile kaydedilmesini gerektirir.
  * `applicationinsights-core`, Örneğin uygulamanızın servlet tabanlı olmaması durumunda yalnızca tam API 'yi sağlar.
  
* *SDK’yı en son sürüme nasıl güncelleştirmeliyim?*
  * Gradle veya Maven kullanıyorsanız...
    * En son sürümü belirtmek için derleme dosyanızı güncelleştirin.
  * Bağımlılıkları el ile yönetiyorsanız...
    * En son [Java için Application Insights SDK’si](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)’ni indirin ve eskilerle değiştirin. Değişiklikler [SDK sürüm notlarında](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) açıklanmıştır.

## <a name="3-add-an-applicationinsightsxml-file"></a>3. bir ApplicationInsights. xml dosyası ekleyin
Projenizin kaynaklar klasörüne ApplicationInsights.xml dosyasını ekleyin veya projenizin dağıtım sınıfı yoluna eklendiğinden emin olun. Aşağıdaki XML dosyasını buraya kopyalayın.

Azure portalından aldığınız izleme anahtarını bununla değiştirin.

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

İsteğe bağlı olarak, yapılandırma dosyası, uygulamanızın erişebildiği herhangi bir konumda bulunabilir.  `-Dapplicationinsights.configurationDirectory` sistem özelliği, ApplicationInsights.xml dosyasını içeren dizini belirtir. Örneğin, `E:\myconfigs\appinsights\ApplicationInsights.xml` konumunda bulunan bir yapılandırma dosyası, `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` özelliği ile yapılandırılır.

* İzleme anahtarı telemetrinin her öğesiyle birlikte gönderilir ve Application Insights’ın bunu kaynağınızda görüntülemesini isteyin.
* HTTP isteği bileşeni isteğe bağlıdır. İstek ve yanıt süreleri hakkında telemetriyi otomatik olarak portala gönderir.
* Olay bağıntısı, HTTP isteği bileşenine bir ektir. Sunucu tarafından alınan her istek için bir tanımlayıcı atar ve bu tanımlayıcıyı bir özellik olarak, telemetrinin her öğesine 'Operation.Id' özelliği olarak ekler. [Tanılama aramasında][diagnostic]bir filtre ayarlayarak her istekle ilişkili Telemetriyi ilişkilendirmenize olanak tanır.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>İzleme anahtarını ayarlamak için alternatif yollar
Application Insights SDK’sı anahtarı şu sırayla arar:

1. Sistem özelliği:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Ortam değişkeni: APPINSIGHTS_INSTRUMENTATIONKEY
3. Yapılandırma dosyası: ApplicationInsights.xml

Ayrıca [kod içinde ayarlayabilirsiniz](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. aracı ekle

Giden HTTP çağrılarını, JDBC sorgularını, uygulama günlüğünü ve daha iyi işlem adlandırmayı yakalamak için [Java aracısını yükler](java-agent.md) .

## <a name="5-run-your-application"></a>5. uygulamanızı çalıştırın
Geliştirme makinenizde hata ayıklama modunda çalıştırın ya da sunucunuza yayımlayın.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Application Insights içinde telemetrinizi görüntüleyin
[Microsoft Azure portalında](https://portal.azure.com), Application Insights kaynağınıza dönün.

HTTP isteklerine ilişkin veriler genel bakış dikey penceresinde görüntülenir. (Orada değilse, birkaç saniye bekleyip Yenile’ye tıklayın.)

![Genel Bakış örnek verilerinin ekran görüntüsü](./media/java-get-started/overview-graphs.png)

[Ölçümler hakkında daha fazla bilgi edinin.][metrics]

Daha ayrıntılı derlenmiş ölçümler görmek için herhangi bir grafiğe tıklayın.

![Grafiklerle Application Insights başarısızlık bölmesi](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Örnek veriler
Ayrı ayrı örnekleri görmek için belirli bir istek türüne tıklayın.

![Belirli bir örnek görünüm detayına gitme](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analiz: Güçlü sorgu dili
Daha fazla veri birleştirdiğinizde hem veri toplama, hem de tek tek örneklerini bulmak için sorguları çalıştırabilirsiniz.  [Analiz](../../azure-monitor/app/analytics.md) hem performans, hem de kullanım için olmasının yanı sıra tanılama için de güçlü bir araçtır.

![Analizi örneği](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. uygulamanızı sunucuya yükler
Artık uygulamanızı sunucuya yayımlayın, herkesin kullanmasını sağlayın ve portalda gösterilen telemetriye bakın.

* Güvenlik duvarınızın, uygulamanıza şu bağlantı noktalarına telemetri göndermesine izin verdiğinden emin olun:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Giden trafiğin güvenlik duvarından geçirilmesi gerekiyorsa, `http.proxyHost` ve `http.proxyPort` sistem özelliklerini tanımlayın.

* Windows sunucularda yüklenecekler:

  * [Microsoft Visual C++ Yeniden Dağıtılabilir](https://www.microsoft.com/download/details.aspx?id=40784)

    (Bu bileşen, performans sayaçlarını etkinleştirir.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (Spring Boot)

Windows üzerinde çalışan Spring Boot uygulamaları, Azure Uygulama Hizmetleri 'nde çalışması için ek yapılandırma gerektirir. **Web. config** 'i değiştirin ve aşağıdakileri ekleyin:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Özel durumlar ve istek hataları
İşlenmemiş özel durumlar ve istek arızaları Application Insights Web filtresi tarafından otomatik olarak toplanır.

Diğer özel durumlarla ilgili verileri toplamak için [kodunuzda trackException () çağrıları][apiexceptions]ekleyebilirsiniz.

## <a name="monitor-method-calls-and-external-dependencies"></a>Yöntem çağrılarını ve dış bağımlılıkları izleme
Zamanlama verileriyle JDBC üzerinden yapılan belirli dahili yöntemleri ve çağrıları kaydetmek için [Java Agent yükleme](java-agent.md) işlemini gerçekleştirin.

Otomatik işlem adlandırması için.

## <a name="w3c-distributed-tracing"></a>W3C dağıtılmış izleme

Application Insights Java SDK 'Sı artık [W3C dağıtılmış izlemeyi](https://w3c.github.io/trace-context/)destekliyor.

Gelen SDK yapılandırması daha fazla [bağıntı](correlation.md#telemetry-correlation-in-the-java-sdk)makalesinde açıklanmıştır.

Giden SDK yapılandırması [AI-Agent. xml](java-agent.md) dosyasında tanımlanmıştır.

## <a name="performance-counters"></a>Performans sayaçları
Bir dizi performans sayacını görmek için **Araştır**, **ölçümler**' i açın.

![İşlem özel baytları seçiliyken ölçüm bölmesinin ekran görüntüsü](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Performans sayacı koleksiyonunu özelleştirme
Standart performans sayaçları dizisinin koleksiyonunu devre dışı bırakmak için aşağıdaki kodu ApplicationInsights.xml dosyasının kök düğümü altına ekleyin:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Ek performans sayaçlarını toplama
Toplanacak ek performans sayaçları belirtebilirsiniz.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX sayaçları (Java Sanal Makinesi tarafından gösterilen)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – Application Insights portalında görüntülenen ad.
* `objectName` – JMX nesne adı.
* `attribute` – Getirilecek JMX nesne adının özniteliği
* `type` (isteğe bağlı) - JMX nesnenin öznitelik türü:
  * Varsayılan: int veya long gibi basit bir tür.
  * `composite`: performans sayacı verileri 'Attribute.Data' biçimindedir
  * `tabular`: performans sayacı verileri tablo satırı biçimindedir

#### <a name="windows-performance-counters"></a>Windows performans sayaçları
Her [Windows performans sayacı](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) bir kategorinin üyesidir (alanın bir sınıf üyesi olması gibi). Kategoriler genel olabileceği gibi numaralı veya adlı örneklere de sahip olabilir.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Application Insights portalında görüntülenen ad.
* categoryName – Bu performans sayacıyla ilişkili performans sayacı kategorisi (performans nesnesi).
* counterName – Performans sayacının adı.
* instanceName – Performans sayacı kategorisi örneğinin adı veya kategoride tek örnek varsa boş bir dize (""). categoryName adı Process olursa ve uygulamanızın çalıştığı geçerli JVM işleminden performans sayacını toplamak istiyorsanız `"__SELF__"` öğesini belirtin.

### <a name="unix-performance-counters"></a>Unix Performans sayaçları
* Çok çeşitli sistem ve ağ verisi almak için [Application Insights eklentisiyle collectd yükleyin](java-collectd.md).

## <a name="get-user-and-session-data"></a>Kullanıcı ve oturum verilerini alma
Tamam, web sunucunuzdan telemetri gönderiyorsunuz. Uygulamanızın 360 derecelik tam görünümünü almak için izlemeye katabileceğiniz birkaç şey daha vardır:

* Sayfa görünümlerini ve Kullanıcı ölçümlerini izlemek için [Web sayfalarınıza telemetri ekleyin][usage] .
* Uygulamanızın canlı ve hızlı bir şekilde kaldığından emin olmak için [Web testleri ayarlayın][availability] .

## <a name="send-your-own-telemetry"></a>Kendi telemetrinizi gönderme
Artık SDK'yı da yüklediğinize göre, kendi telemetrinizi göndermek için API'yi kullanabilirsiniz.

* Uygulamanızda kullanıcıların neler yaptığını öğrenmek için [özel olayları ve ölçümleri izleyin][api] .
* Sorunları tanılamanıza yardımcı olması için [olayları ve günlükleri arayın][diagnostic] .

## <a name="availability-web-tests"></a>Kullanılabilirlik web testleri
Kullanıma hazır ve düzgün yanıt verdiğini denetlemek için Application Insights belirli aralıklarla web sitenizi test edebilir.

[Kullanılabilirlik Web testlerini ayarlama hakkında daha fazla bilgi edinin.][availability]

## <a name="questions-problems"></a>Sorularınız mı var? Sorunlar?
[Java Sorun Giderme](java-troubleshoot.md)

## <a name="next-steps"></a>Sonraki adımlar
* [Bağımlılık çağrılarını izleme](java-agent.md)
* [Unix Performans sayaçlarını izleme](java-collectd.md)
* Sayfa yükleme sürelerini, AJAX çağrılarını ve tarayıcı özel durumlarını izlemek için [web sayfalarınıza izleme ekleyin](javascript.md).
* Tarayıcıda veya sunucuda kullanımı izlemek için [özel telemetri](../../azure-monitor/app/api-custom-events-metrics.md) yazın.
* Uygulamanızdan telemetri üzerinde güçlü sorgular yapmak için [Analytics](../../azure-monitor/app/analytics.md)'i kullanın
* Daha fazla bilgi için bkz. [Java geliştiricileri için Azure](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md

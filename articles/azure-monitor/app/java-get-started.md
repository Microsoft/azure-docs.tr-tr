---
title: 'Hızlı başlangıç: Azure Application Insights Java Web uygulaması Analizi'
description: 'Application Insights ile Java web uygulamaları için Uygulama Performansı İzleme. '
ms.topic: conceptual
ms.date: 11/22/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cdad5a0ec158f216a63ae7e1e2abc10d2841b3bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593737"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Hızlı Başlangıç: Java web projesinde Application Insights'ı kullanmaya başlama


> [!CAUTION]
> 2020 Kasım itibariyle, Java uygulamalarını izlemek için Azure Izleyici Application Insights Java 3,0 Aracısı 'nı kullanarak otomatik izleme önerilir. Kullanmaya başlama hakkında daha fazla bilgi için bkz. [Application Insights Java 3,0 Aracısı](./java-in-process-agent.md).

Bu hızlı başlangıçta, isteği işaretlemek, bağımlılıkları izlemek ve performans sayaçlarını toplamak, performans sorunlarını ve özel durumları tanılamak ve uygulamanızdaki kullanıcıların neler yaptığını izlemek için kod yazmak üzere Application Insights SDK 'sını kullanırsınız.

Application Insights, web geliştiricileri için canlı uygulamanızın performansını ve kullanımını anlamanıza yardımcı olan genişletilebilir bir analiz hizmetidir. Application Insights; Linux, Unix veya Windows üzerinde çalışan Java uygulamalarını destekler.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Çalışan bir Java uygulaması.

## <a name="get-an-application-insights-instrumentation-key"></a>Application Insights izleme anahtarı edinme

> [!IMPORTANT]
> Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./sdk-connection-string.md?tabs=java) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.
1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure portalındabir Application Insights kaynağı oluşturun. Uygulama türünü Java web uygulaması olarak ayarlayın.

3. Yeni kaynağın izleme anahtarını bulun. Bu anahtarı hemen kod projenize yapıştırmalısınız.

    ![Yeni kaynağa genel bakışta, Özellikler'e tıklayıp izleme anahtarını kopyalama](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Projenize Java için Application Insights SDK’sı ekleme

*Proje türünü seçin.*

# <a name="maven"></a>[Maven](#tab/maven)

Projeniz zaten derleme için Maven kullanmak üzere ayarlandıysa, *pom.xml* dosyanıza aşağıdaki kodu birleştirin.

Daha sonra, proje bağımlılıklarını ikili dosyaları indirmek için yenileyin.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.6.2</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Projeniz zaten derleme için Gradle kullanmak üzere ayarlandıysa, aşağıdaki kodu *Build. Gradle* dosyanıza birleştirin.

Daha sonra, proje bağımlılıklarını ikili dosyaları indirmek için yenileyin.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.6.2'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

---

### <a name="questions"></a>Sorular
* *Ve bileşenleri arasındaki ilişki nedir `-web-auto` `-web` `-core` ?*
  * `applicationinsights-web-auto` , çalışma zamanında Application Insights servlet filtresini otomatik olarak kaydederek HTTP servlet istek sayısını ve yanıt sürelerini izleyen ölçümleri sağlar.
  * `applicationinsights-web` Ayrıca, HTTP servlet istek sayısını ve yanıt sürelerini izleyen ölçümleri de verir, ancak uygulamanızda Application Insights servlet filtresi 'nin el ile kaydedilmesini gerektirir.
  * `applicationinsights-core` , örneğin, uygulamanız servlet tabanlı değilse yalnızca tam API 'yi sağlar.
  
* *SDK’yı en son sürüme nasıl güncelleştirmeliyim?*
  * 2020 Kasım itibariyle, Java uygulamalarını izlemek için Azure Izleyici Application Insights Java 3,0 Aracısı 'nı kullanarak otomatik izleme önerilir. Kullanmaya başlama hakkında daha fazla bilgi için bkz. [Application Insights Java 3,0 Aracısı](./java-in-process-agent.md).

## <a name="add-an-applicationinsightsxml-file"></a>*ApplicationInsights.xml* dosyası Ekle
Projenizdeki Resources klasörüne *ApplicationInsights.xml* ekleyin veya projenizin dağıtım sınıfı yoluna eklendiğinden emin olun. Aşağıdaki XML dosyasını buraya kopyalayın.

İzleme anahtarını, Azure portal aldığınız bir ile değiştirin.

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

İsteğe bağlı olarak, yapılandırma dosyası uygulamanız tarafından erişilebilen herhangi bir konumda olabilir.  System özelliği `-Dapplicationinsights.configurationDirectory` *ApplicationInsights.xml* içeren dizini belirtir. Örneğin, `E:\myconfigs\appinsights\ApplicationInsights.xml` konumunda bulunan bir yapılandırma dosyası, `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` özelliği ile yapılandırılır.

* İzleme anahtarı telemetrinin her öğesiyle birlikte gönderilir ve Application Insights’ın bunu kaynağınızda görüntülemesini isteyin.
* HTTP isteği bileşeni isteğe bağlıdır. İstek ve yanıt süreleri hakkında telemetriyi otomatik olarak portala gönderir.
* Olay bağıntısı, HTTP isteği bileşenine bir ektir. Sunucu tarafından alınan her istek için bir tanımlayıcı atar. Daha sonra bu tanımlayıcıyı, ' Operation.Id ' özelliği olarak telemetri her öğesine bir özellik olarak ekler. [Tanı aramaya][diagnostic] bir filtre ayarlayarak her istekle ilişkili telemetrinin bağıntısını kurmanızı sağlar.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>İzleme anahtarını ayarlamak için alternatif yollar
Application Insights SDK’sı anahtarı şu sırayla arar:

1. Sistem özelliği:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Ortam değişkeni: APPINSIGHTS_INSTRUMENTATIONKEY
3. Yapılandırma dosyası: *ApplicationInsights.xml*

Ayrıca [kod içinde ayarlayabilirsiniz](./api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Aracı Ekle

Giden HTTP çağrılarını, JDBC sorgularını, uygulama günlüğünü ve daha iyi işlem adlandırmayı yakalamak için [Java aracısını yükler](java-agent.md) .

## <a name="run-your-application"></a>Uygulamanızı çalıştırma
Geliştirme makinenizde hata ayıklama modunda çalıştırın ya da sunucunuza yayımlayın.

## <a name="view-your-telemetry-in-application-insights"></a>Application Insights'da telemetrinizi görüntüleme
[Microsoft Azure portal](https://portal.azure.com)Application Insights kaynağına geri dönün.

HTTP isteklerine ilişkin veriler genel bakış dikey penceresinde görüntülenir. (Orada değilse, birkaç saniye bekleyip Yenile’ye tıklayın.)

![Genel Bakış örnek verilerinin ekran görüntüsü](./media/java-get-started/overview-graphs.png)

[Ölçümler hakkında daha fazla bilgi edinin.][metrics]

Daha ayrıntılı derlenmiş ölçümler görmek için herhangi bir grafiğe tıklayın.

![Grafiklerle Application Insights başarısızlık bölmesi](./media/java-get-started/006-barcharts.png)

### <a name="instance-data"></a>Örnek veriler
Ayrı ayrı örnekleri görmek için belirli bir istek türüne tıklayın.

![Belirli bir örnek görünüm detayına gitme](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analiz: Güçlü sorgu dili
Daha fazla veri birleştirdiğinizde hem veri toplama, hem de tek tek örneklerini bulmak için sorguları çalıştırabilirsiniz.  [Analiz](../logs/log-query-overview.md) hem performans, hem de kullanım için olmasının yanı sıra tanılama için de güçlü bir araçtır.

![Analizi örneği](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Uygulamanızı sunucuya yükleme
Artık uygulamanızı sunucuya yayımlayın, herkesin kullanmasını sağlayın ve portalda gösterilen telemetriye bakın.

* Güvenlik duvarınızın, uygulamanıza şu bağlantı noktalarına telemetri göndermesine izin verdiğinden emin olun:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Giden trafiğin güvenlik duvarından geçirilmesi gerekiyorsa, `http.proxyHost` ve `http.proxyPort` sistem özelliklerini tanımlayın.

* Windows sunucularda yüklenecekler:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Bu bileşen, performans sayaçlarını etkinleştirir.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service, AKS, VM yapılandırması

Azure Kaynak sağlayıcılarının hiçbirinde çalışan uygulamalarınızı izlemek için en iyi ve en kolay yaklaşım, [Java 3,0 Aracısı](./java-in-process-agent.md)aracılığıyla Application Insights otomatik izleme kullanmaktır.


## <a name="exceptions-and-request-failures"></a>Özel durumlar ve istek hataları
İşlenmemiş özel durumlar ve istek arızaları Application Insights Web filtresi tarafından otomatik olarak toplanır.

Diğer özel durumlarla ilgili verileri toplamak için [kodunuzda trackException () çağrıları][apiexceptions]ekleyebilirsiniz.

## <a name="monitor-method-calls-and-external-dependencies"></a>Yöntem çağrılarını ve dış bağımlılıkları izleme
Zamanlama verileriyle JDBC üzerinden yapılan belirli dahili yöntemleri ve çağrıları kaydetmek için [Java Agent yükleme](java-agent.md) işlemini gerçekleştirin.

Otomatik işlem adlandırması için.

## <a name="w3c-distributed-tracing"></a>W3C dağıtılmış izleme

Application Insights Java SDK 'Sı artık [W3C dağıtılmış izlemeyi](https://w3c.github.io/trace-context/)destekliyor.

Gelen SDK yapılandırması daha fazla [bağıntı](correlation.md)makalesinde açıklanmıştır.

Giden SDK yapılandırması [AI-Agent.xml](java-agent.md) dosyasında tanımlanmıştır.

## <a name="performance-counters"></a>Performans sayaçları
Bir dizi performans sayacını görmek için **Araştır**, **ölçümler**' i açın.

![İşlem özel baytları seçiliyken ölçüm bölmesinin ekran görüntüsü](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Performans sayacı koleksiyonunu özelleştirme
Standart performans sayaçları kümesinin toplanmasını devre dışı bırakmak için, *ApplicationInsights.xml* dosyanın kök düğümünün altına aşağıdaki kodu ekleyin:

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
* `type` (isteğe bağlı)-JMX nesnesinin özniteliğinin türü:
  * Varsayılan: int veya long gibi basit bir tür.
  * `composite`: performans sayacı verileri 'Attribute.Data' biçimindedir
  * `tabular`: performans sayacı verileri tablo satırı biçimindedir

#### <a name="windows-performance-counters"></a>Windows performans sayaçları
Her [Windows performans sayacı](/windows/win32/perfctrs/performance-counters-portal) bir kategorinin üyesidir (alanın bir sınıf üyesi olması gibi). Kategoriler genel olabileceği gibi numaralı veya adlı örneklere de sahip olabilir.

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

* Sayfa görünümlerini ve kullanıcı ölçümlerini izlemek için [web sayfalarınıza telemetri ekleyin][usage].
* Uygulamanızın canlı ve duyarlı kaldığından emin olmak için [web testleri oluşturun][availability].

## <a name="send-your-own-telemetry"></a>Kendi telemetrinizi gönderme
Artık SDK'yı da yüklediğinize göre, kendi telemetrinizi göndermek için API'yi kullanabilirsiniz.

* Uygulamanızla kullanıcıların ne yaptıklarını öğrenmek için [Özel olayları ve ölçümleri izleyin][api].
* Sorunların tanımlanması için [Olayları ve günlükleri arayın][diagnostic].

## <a name="availability-web-tests"></a>Kullanılabilirlik web testleri
Kullanıma hazır ve düzgün yanıt verdiğini denetlemek için Application Insights belirli aralıklarla web sitenizi test edebilir.

[Kullanılabilirlik Web testlerini ayarlama hakkında daha fazla bilgi edinin.][availability]

## <a name="questions-problems"></a>Sorularınız mı var? Sorunlarınız mı var?
[Java sorun giderme](java-troubleshoot.md)

## <a name="next-steps"></a>Sonraki adımlar
* [Bağımlılık çağrılarını izleme](java-agent.md)
* [Unix Performans sayaçlarını izleme](java-collectd.md)
* Sayfa yükleme sürelerini, AJAX çağrılarını ve tarayıcı özel durumlarını izlemek için [web sayfalarınıza izleme ekleyin](javascript.md).
* Tarayıcıda veya sunucuda kullanımı izlemek için [özel telemetri](./api-custom-events-metrics.md) yazın.
* Uygulamanızdan telemetri üzerinde güçlü sorgular için  [analiz](../logs/log-query-overview.md) kullanın
* Daha fazla bilgi için bkz. [Java geliştiricileri için Azure](/java/azure).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md
[usage]: javascript.md
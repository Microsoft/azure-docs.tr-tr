---
title: Java Web projesinde Application Insights sorunlarını giderme
description: Sorun giderme kılavuzu-Application Insights ile canlı Java uygulamalarını izleme.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 941dcc268c2af9e011af01d3da224b90e9ee5018
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820809"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Java için Application Insights Sorun Giderme, Soru ve Yanıt
[Java 'Da Azure Application Insights][java]sorular veya sorunlar var mı? İşte bazı ipuçları.

## <a name="build-errors"></a>Derleme hataları
**Çakışan Küreler veya IntelliJ fikri içinde, Maven veya Gradle aracılığıyla Application Insights SDK 'Sı eklerken derleme veya sağlama toplamı doğrulama hataları alıyorum.**

* Dependency `<version>` öğesi joker karakterlerle (örn. (Maven) `<version>[2.0,)</version>` veya (Gradle) `version:'2.0.+'`) bir model kullanıyorsa, `2.0.1`gibi belirli bir sürüm belirtmeyi deneyin. En son sürüm için [sürüm notlarına](https://github.com/Microsoft/ApplicationInsights-Java/releases) bakın.

## <a name="no-data"></a>Veri yok
**Başarıyla Application Insights ekledim ve uygulamamı çalıştırdım, ancak portalda hiç veri gördüm.**

* Bir dakika bekleyip Yenile ' ye tıklayın. Grafikler düzenli aralıklarla yenilenir, ancak el ile de yenileme yapabilirsiniz. Yenileme aralığı, grafiğin zaman aralığına bağlıdır.
* ApplicationInsights. xml dosyasında tanımlanan bir izleme anahtarınız olup olmadığını denetleyin (Projenizdeki kaynaklar klasöründe) veya ortam değişkeni olarak yapılandırılmış.
* XML dosyasında `<DisableTelemetry>true</DisableTelemetry>` düğümü olmadığını doğrulayın.
* Güvenlik duvarınızdaki dc.services.visualstudio.com 'e giden trafik için 80 ve 443 TCP bağlantı noktalarını açmanız gerekebilir. [Güvenlik Duvarı özel durumlarının tam listesini](../../azure-monitor/app/ip-addresses.md) görüntüleyin
* Microsoft Azure başlangıç panosunda, hizmet durumu haritasına bakın. Bazı uyarı göstergeleri varsa, Tamam ' a dönene kadar bekleyin ve ardından Application Insights uygulaması dikey penceresini kapatıp yeniden açın.
* ApplicationInsights. xml dosyasındaki kök düğümün altına bir `<SDKLogger />` öğesi ekleyerek [günlüğü açın](#debug-data-from-the-sdk) (Projenizdeki kaynaklar klasöründe) ve tüm şüpheli Günlükler IÇIN, AI: ıNFO/warn/Error ile kullanıma hazır olan girdileri denetleyin. 
* Doğru ApplicationInsights. xml dosyasının Java SDK 'Sı tarafından başarılı bir şekilde yüklendiğinden emin olun. bunun için konsolun "yapılandırma dosyası başarıyla bulundu" bildiriminin çıkış iletilerine göz atalım.
* Yapılandırma dosyası bulunamazsa, yapılandırma dosyasının nerede arandığını görmek için çıkış iletilerini denetleyin ve ApplicationInsights. xml ' in bu arama konumlarından birinde bulunduğundan emin olun. Thumb kuralı olarak, yapılandırma dosyasını Application Insights SDK JARs yakınına yerleştirebilirsiniz. Örneğin: Tomcat 'te bu, WEB-INF/Classes klasörü anlamına gelir. Geliştirme sırasında, ApplicationInsights. xml dosyasını Web projenizin Resources klasörüne yerleştirebilirsiniz.
* Ayrıca, SDK ile ilgili bilinen sorunlar için [GitHub sorunları sayfasına](https://github.com/Microsoft/ApplicationInsights-Java/issues) bakın.
* Sürüm Çakışma sorunlarından kaçınmak için lütfen Application Insights Core, Web, aracı ve günlüğe kaydetme uygulamalarına ait aynı sürümü kullandığınızdan emin olun.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Verileri görmek için kullandım, ancak durdu
* [Durum blogunu](https://blogs.msdn.com/b/applicationinsights-status/)denetleyin.
* Aylık veri noktaları kotasına ulaştınız misiniz? Bulmak için ayarları/kotayı ve fiyatlandırmayı açın. Bu durumda, planınızı yükseltebilir veya ek kapasite için ödeme yapabilirsiniz. [Fiyatlandırma şemasına](https://azure.microsoft.com/pricing/details/application-insights/)bakın.
* Son zamanlarda SDK 'nizi yükseltmişseniz mi? Lütfen proje dizini içinde yalnızca benzersiz SDK jar dosyaları dışındaki bulunduğundan emin olun. SDK 'nın iki farklı sürümü mevcut olmamalıdır.
* Doğru AI kaynağına mı bakıyorsunuz? Lütfen uygulamanızın Ikey değerini Telemetriyi beklediğiniz kaynakla eşleştirin. Aynı olmaları gerekir.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Beklediğiniz tüm verileri göremiyorum
* Kullanım ve tahmini maliyet sayfasını açın ve [örnekleme](../../azure-monitor/app/sampling.md) işleminin çalışıp çalışmadığını denetleyin. (%100 iletim, örnekleme işleminin işlem içinde olmadığı anlamına gelir.) Application Insights hizmeti, yalnızca uygulamanızdan gelen telemetrinin bir bölümünü kabul edecek şekilde ayarlanabilir. Bu, aylık telemetri kotasında tutmanıza yardımcı olur.
* SDK örneklemesi açık mı? Yanıt Evet ise, veriler tüm uygulanabilir türler için belirtilen hızda örneklenir.
* Java SDK 'sının eski bir sürümünü çalıştırıyor musunuz? Sürüm 2.0.1 ile başlayarak, aralıklı ağ ve arka uç hatalarının yanı sıra yerel sürücülerde veri kalıcılığını işlemek için hata toleransı mekanizması sunuyoruz.
* Aşırı telemetri nedeniyle kısıtlanıyor musunuz? BILGI günlüğünü açarsanız, "uygulama kısıtlandı" bir günlük iletisi görürsünüz. Geçerli sınırımız, 32K telemetri öğe/saniye.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java Aracısı bağımlılık verilerini yakalayamaz
* Java aracısını [Yapılandır](java-agent.md) ' ı izleyerek Java aracısını yapılandırdınız mı?
* Hem Java Agent jar hem de AI-Agent. xml dosyasının aynı klasöre yerleştirildiğinden emin olun.
* Otomatik toplamaya çalıştığınız bağımlılığın otomatik toplama için desteklendiğinden emin olun. Şu anda yalnızca MySQL, MsSQL, Oracle DB ve Redsıs bağımlılık koleksiyonu için Azure önbelleğini destekliyoruz.
* JDK 1,7 veya 1,8 mi kullanıyorsunuz? Şu anda JDK 9 ' da bağımlılık toplamayı desteklemiyoruz.

## <a name="no-usage-data"></a>Kullanım verisi yok
**İstekler ve yanıt süreleri hakkında veri görüyorum, ancak sayfa görüntüleme, tarayıcı veya Kullanıcı verisi yok.**

Uygulamanızı sunucudan telemetri gönderecek şekilde ayarlayın. Şimdi bir sonraki adımınız Web [tarayıcısından telemetri göndermek için Web sayfalarınızı ayarlamanıza][usage]olanak sağlar.

Alternatif olarak, istemciniz [telefonda veya diğer bir cihazdaki][platforms]bir uygulama ise, bu bilgisayardan telemetri gönderebilirsiniz.

Hem istemci hem de sunucu telemetrinizi ayarlamak için aynı izleme anahtarını kullanın. Veriler aynı Application Insights kaynağında görünür ve olayları istemci ve sunucudan ilişkilendirebileceksiniz.


## <a name="disabling-telemetry"></a>Telemetri devre dışı bırakılıyor
**Telemetri toplamayı nasıl devre dışı bırakabilirim?**

Kodda:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Veya**

ApplicationInsights. xml dosyasını güncelleştirin (Projenizdeki kaynaklar klasöründe). Kök düğümü altına aşağıdakileri ekleyin:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

XML yöntemini kullanarak, değeri değiştirirken uygulamayı yeniden başlatmanız gerekir.

## <a name="changing-the-target"></a>Hedefi değiştirme
**Projem hangi Azure kaynağını veri gönderdiğini nasıl değiştirebilirim?**

* [Yeni kaynağın izleme anahtarını alır.][java]
* Azure Toolkit for Eclipse kullanarak projenize Application Insights eklediyseniz, Web projenize sağ tıklayın, **Azure**' ı seçin **Application Insights yapılandırın**ve anahtarı değiştirin.
* Izleme anahtarını ortam değişkeni olarak yapılandırdıysanız lütfen ortam değişkeninin değerini yeni Ikey ile güncelleştirin.
* Aksi takdirde, projenizdeki Resources klasöründeki ApplicationInsights. xml dosyasında anahtarı güncelleştirin.

## <a name="debug-data-from-the-sdk"></a>SDK 'dan veri ayıklama

**SDK 'nın ne yaptığını nasıl öğrenebilirim?**

API 'de neler olduğu hakkında daha fazla bilgi edinmek için, ApplicationInsights. xml yapılandırma dosyasının kök düğümü altına `<SDKLogger/>` ekleyin.

### <a name="applicationinsightsxml"></a>ApplicationInsights. xml

Günlükçüde bir dosyanın çıktısını almak için de talimat verebilirsiniz:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring Boot Starter

Application Insights Spring Boot Starter 'ı kullanarak Spring Boot uygulamalarıyla SDK günlüğünü etkinleştirmek için aşağıdaki `application.properties` dosyasına ekleyin:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

veya standart hataya yazdırmak için:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java Aracısı

JVM aracı günlüğünü etkinleştirmek için [AI-Agent. xml dosyasını](java-agent.md)güncelleştirin:

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Java komut satırı özellikleri
_Sürüm 2.4.0 beri_

Komut satırı seçeneklerini kullanarak günlüğe kaydetmeyi etkinleştirmek için yapılandırma dosyalarını değiştirmeden:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

veya standart hataya yazdırmak için:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Azure başlangıç ekranı
**[Azure Portal](https://portal.azure.com)aradım. Harita Uygulamam hakkında bir şey mı anladı?**

Hayır, dünyanın dört bir yanındaki Azure sunucularının sistem durumunu gösterir.

*Azure başlangıç panosu 'nda (ana ekran), Uygulamam hakkında nasıl veri bulabilirim?*

[Uygulamanızı Application Insights için ayarladığımldığında][java], sırasıyla Araştır ' a tıklayın, Application Insights ' i seçin ve uygulamanız için oluşturduğunuz uygulama kaynağını seçin. Gelecekte daha hızlı bir şekilde yararlanmak için uygulamanızı başlangıç panosuna sabitleyebilirsiniz.

## <a name="intranet-servers"></a>Intranet sunucuları
**İntranetinizdeki bir sunucuyu izleyebilir miyim?**

Evet, sunucunuz ortak internet üzerinden Application Insights portalına telemetri gönderebilmektedir.

Güvenlik duvarınızda, dc.services.visualstudio.com ve f5.services.visualstudio.com ' e giden trafik için 80 ve 443 TCP bağlantı noktalarını açmanız gerekebilir.

## <a name="data-retention"></a>Veri saklama
**Veriler portalda ne kadar süreyle tutulur? Güvenli mi?**

Bkz. [veri saklama ve gizliliği][data].

## <a name="debug-logging"></a>Hata ayıklama günlüğü
Application Insights `org.apache.http`kullanır. Bu, `com.microsoft.applicationinsights.core.dependencies.http`ad alanı altındaki Application Insights çekirdek jar dosyaları dışındaki 'ın yeniden konumlandırıldı. Bu, Application Insights aynı `org.apache.http` farklı sürümlerinin tek bir kod tabanında bulunduğu senaryoları işlemesini sağlar.

>[!NOTE]
>Uygulamadaki tüm ad alanları için hata ayıklama düzeyinde günlüğe yazmayı etkinleştirirseniz, `com.microsoft.applicationinsights.core.dependencies.http`olarak yeniden adlandırılan `org.apache.http` dahil olmak üzere tüm çalışan modüller tarafından kabul edilir. Application Insights, günlük çağrısı Apache kitaplığı tarafından yapıldığından, bu çağrılar için filtreleme uygulayamayacak. Hata ayıklama düzeyinde günlüğe kaydetme, önemli miktarda günlük verisi üretir ve canlı üretim örnekleri için önerilmez.


## <a name="next-steps"></a>Sonraki adımlar
**Java sunucusu uygulamamın Application Insights ayarladım. Başka ne yapabilirim?**

* [Web sayfalarınızın kullanılabilirliğini izleyin][availability]
* [Web sayfası kullanımını izle][usage]
* [Cihaz uygulamalarınızdaki kullanımı izleyin ve sorunları tanılayın][platforms]
* [Uygulamanızın kullanımını izlemek için kod yazın][track]
* [Tanılama günlüklerini yakala][javalogs]

## <a name="get-help"></a>Yardım alın
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [GitHub 'da sorun dosya](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md


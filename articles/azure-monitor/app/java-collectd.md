---
title: Linux 'ta Java Web uygulaması performansını izleme-Azure | Microsoft Docs
description: Application Insights için CollectD eklentisi ile Java Web sitenizin genişletilmiş uygulama performansı izlemesi.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 47e5e9dd81174b79e149ba29eec725c8c17eb1a6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176396"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: Application Insights Linux performans ölçümleri


[Application Insights](../../azure-monitor/app/app-insights-overview.md)'deki Linux sistem performans ölçümlerini araştırmak için, Application Insights eklentisiyle [toplanan](https://collectd.org/)'yi birlikte yüklemelisiniz. Bu açık kaynaklı çözüm çeşitli sistem ve ağ istatistiklerini toplar.

Genellikle, [Java Web hizmetinizi Application Insights][java]zaten belirlediyseniz toplanan kullanırsınız. Uygulamanızın performansını geliştirmenize veya sorunları tanılamanıza yardımcı olacak daha fazla veri sunar. 

## <a name="get-your-instrumentation-key"></a>İzleme anahtarınızı alın
[Microsoft Azure Portal](https://portal.azure.com), verilerin görünmesini istediğiniz [Application Insights](../../azure-monitor/app/app-insights-overview.md) kaynağını açın. (Veya [Yeni bir kaynak oluşturun](../../azure-monitor/app/create-new-resource.md ).)

Kaynak tanımlayan izleme anahtarının bir kopyasını alın.

![Tümüne göz atın, kaynağınızı açın ve temel bileşenler açılan penceresinde, Izleme anahtarını seçin ve kopyalayın](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Toplanan ve eklentiyi yükler
Linux sunucu makinelerinizde:

1. [Toplanan](https://collectd.org/) sürüm 5.4.0 veya üstünü yükler.
2. [Application Insights toplanan yazıcı eklentisini](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal)indirin. Sürüm numarasını aklınızda yapın.
3. Eklenti JAR dosyasını `/usr/share/collectd/java`içine kopyalayın.
4. `/etc/collectd/collectd.conf`Düzenle:
   * [Java eklentisinin](https://collectd.org/wiki/index.php/Plugin:Java) etkinleştirildiğinden emin olun.
   * Aşağıdaki JAR 'yi dahil etmek için Java. Class. Path için JVMArg 'yi güncelleştirin. Sürüm numarasını indirdiğiniz bir sürümle eşleşecek şekilde güncelleştirin:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Bu kod parçacığını, kaynağınızın Izleme anahtarını kullanarak ekleyin:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Örnek yapılandırma dosyasının bir parçası aşağıda verilmiştir:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Farklı kaynaklardan çeşitli verileri toplayabilen diğer [toplanan eklentilerini](https://collectd.org/wiki/index.php/Table_of_Plugins)yapılandırın.

[El ile](https://collectd.org/wiki/index.php/First_steps)için toplanan 'yi yeniden başlatın.

## <a name="view-the-data-in-application-insights"></a>Application Insights verileri görüntüleme
Application Insights kaynağınız içinde, ölçümler ' i açın [ve grafikler ekleyin ve][metrics]özel kategoriden görmek istediğiniz ölçümleri seçin.

Varsayılan olarak ölçümler, ölçümlerin toplandığı tüm ana makineler arasında toplanır. Konak başına ölçümleri görüntülemek için, grafik ayrıntıları dikey penceresinde gruplamayı açın ve ardından CollectD-Host ' a göre gruplandırmayı seçin.

## <a name="to-exclude-upload-of-specific-statistics"></a>Belirli istatistiklerin karşıya yüklenmesini dışlamak için
Varsayılan olarak, Application Insights eklentisi tüm etkin toplanan ' okuma ' eklentileri tarafından toplanan tüm verileri gönderir. 

Belirli eklentilerden veya veri kaynaklarından veri dışlamak için:

* Yapılandırma dosyasını düzenleyin. 
* `<Plugin ApplicationInsightsWriter>`, aşağıdaki gibi yönerge satırları ekleyin:

| Deki | Etki |
| --- | --- |
| `Exclude disk` |`disk` eklentisi tarafından toplanan tüm verileri Dışla |
| `Exclude disk:read,write` |`read` ve `write` adlı kaynakları `disk` eklentisine dışlayın. |

Yönergeleri bir yeni satır ile ayırın.

## <a name="problems"></a>Sorunlar?
*Portalda veri görmüyorum*

* Ham olayların ulaşıp ulaşmadığını görmek için [arama][diagnostic] ' yı açın. Bazen Ölçüm Gezgini 'nde görünmesi daha uzun sürer.
* [Giden veriler için güvenlik duvarı özel durumları ayarlamanız](../../azure-monitor/app/ip-addresses.md) gerekebilir
* Application Insights eklentisinde izlemeyi etkinleştirin. Bu satırı `<Plugin ApplicationInsightsWriter>`içine ekleyin:
  * `SDKLogger true`
* Raporlama yaptığı sorunları görmek için bir Terminal açın ve toplanan 'yi ayrıntılı modda başlatın:
  * `sudo collectd -f`

## <a name="known-issue"></a>Bilinen sorun

Application Insights yazma eklentisi, belirli okuma eklentileri ile uyumsuzdur. Bazı Eklentiler bazen Application Insights eklentisinin kayan noktalı bir sayı beklediği "NaN" değerini gönderir.

Belirti: toplanan günlüğünde "AI:... SyntaxError: beklenmeyen belirteç yok.

Geçici çözüm: sorun yazma eklentileri tarafından toplanan verileri dışlayın. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md



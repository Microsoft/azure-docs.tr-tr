---
title: Linux 'ta Java Web uygulaması performansını izleme-Azure | Microsoft Docs
description: Application Insights için CollectD eklentisi ile Java Web sitenizin genişletilmiş uygulama performansı izlemesi.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 08d8deb4c7769c4f8a202050d7b5515439d691f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100573858"
---
# <a name="collectd-linux-performance-metrics-in-application-insights-deprecated"></a>collectd: Application Insights Linux performans ölçümleri [kullanım dışı]

> [!IMPORTANT]
> Java uygulamalarını izlemek için **Önerilen yaklaşım** , kodu değiştirmeden otomatik izleme kullanmaktır. Lütfen **[Application Insights Java 3,0 Aracısı](./java-in-process-agent.md)** için yönergeleri izleyin.

[Application Insights](./app-insights-overview.md)'deki Linux sistem performans ölçümlerini araştırmak için, Application Insights eklentisiyle [toplanan](https://collectd.org/)'yi birlikte yüklemelisiniz. Bu açık kaynaklı çözüm çeşitli sistem ve ağ istatistiklerini toplar.

Genellikle, [Java Web hizmetinizi Application Insights][java]zaten belirlediyseniz toplanan kullanırsınız. Uygulamanızın performansını geliştirmenize veya sorunları tanılamanıza yardımcı olacak daha fazla veri sunar. 

## <a name="get-your-instrumentation-key"></a>İzleme anahtarınızı alın
[Microsoft Azure Portal](https://portal.azure.com), verilerin görünmesini istediğiniz [Application Insights](./app-insights-overview.md) kaynağını açın. (Veya [Yeni bir kaynak oluşturun](./create-new-resource.md).)

Kaynak tanımlayan izleme anahtarının bir kopyasını alın.

![Tümüne göz atın, kaynağınızı açın ve temel bileşenler açılan penceresinde, Izleme anahtarını seçin ve kopyalayın](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Toplanan ve eklentiyi yükler
Linux sunucu makinelerinizde:

1. [Toplanan](https://collectd.org/) sürüm 5.4.0 veya üstünü yükler.
2. [Application Insights toplanan yazıcı eklentisini](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal)indirin. Sürüm numarasını aklınızda yapın.
3. Eklenti JAR dosyasını içine kopyalayın `/usr/share/collectd/java` .
4. Düzenle `/etc/collectd/collectd.conf` :
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
* İçinde `<Plugin ApplicationInsightsWriter>` , aşağıdaki gibi yönerge satırları ekleyin:

| Deki | Etki |
| --- | --- |
| `Exclude disk` |Eklenti tarafından toplanan tüm verileri Dışla `disk` |
| `Exclude disk:read,write` |Ve adlı kaynakları eklentiden hariç tutun `read` `write` `disk` . |

Yönergeleri bir yeni satır ile ayırın.

## <a name="problems"></a>Sorunlarınız mı var?
*Portalda veri görmüyorum*

* Ham olayların ulaşıp ulaşmadığını görmek için [arama][diagnostic] ' yı açın. Bazen Ölçüm Gezgini 'nde görünmesi daha uzun sürer.
* [Giden veriler için güvenlik duvarı özel durumları ayarlamanız](./ip-addresses.md) gerekebilir
* Application Insights eklentisinde izlemeyi etkinleştirin. Bu satırı içine ekleyin `<Plugin ApplicationInsightsWriter>` :
  * `SDKLogger true`
* Raporlama yaptığı sorunları görmek için bir Terminal açın ve toplanan 'yi ayrıntılı modda başlatın:
  * `sudo collectd -f`

## <a name="known-issue"></a>Bilinen sorun

Application Insights yazma eklentisi, belirli okuma eklentileri ile uyumsuzdur. Bazı Eklentiler bazen Application Insights eklentisinin kayan noktalı bir sayı beklediği "NaN" değerini gönderir.

Belirti: toplanan günlüğünde "AI:... SyntaxError: beklenmeyen belirteç yok.

Geçici çözüm: sorun yazma eklentileri tarafından toplanan verileri dışlayın. 

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md


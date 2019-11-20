---
title: Azure Application Insights Profiler sorunlarını giderme | Microsoft Docs
description: Bu makalede, Application Insights Profiler etkinleştirme veya kullanma konusunda sorun yaşayan geliştiricilere yardımcı olacak sorun giderme adımları ve bilgiler sunulmaktadır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 7430f04846a1e66680f85f939854fd50a5df41e4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899984"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler etkinleştirme veya görüntüleme sorunlarını giderme

## <a id="troubleshooting"></a>Genel sorun giderme

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiller yalnızca, profil oluşturucu çalışırken uygulamanıza yönelik istekler varsa yüklenir

Azure Application Insights Profiler her saatte iki dakikalık profil oluşturma verilerini toplar. Ayrıca, **yapılandır Application Insights Profiler** bölmesinde **Şimdi profil** düğmesini seçtiğinizde verileri de toplar. Ancak profil oluşturma verileri, yalnızca profil oluşturucu çalışırken meydana gelen bir isteğe iliştirilebilecek olduğunda karşıya yüklenir. 

Profiler, izleme iletilerini ve özel olayları Application Insights kaynağına yazar. Profil oluşturucunun nasıl çalıştığını görmek için bu olayları kullanabilirsiniz. Profil oluşturucunun çalışıyor ve izlemeleri yakalama olması gerektiğini düşünüyorsanız, ancak **performans** bölmesinde görüntülenmezler, profil oluşturucunun nasıl çalıştığını görmek için kontrol edebilirsiniz:

1. Application Insights kaynağına profil oluşturucu tarafından gönderilen izleme iletilerini ve özel olayları arayın. İlgili verileri bulmak için bu arama dizesini kullanabilirsiniz:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Aşağıdaki görüntüde iki AI kaynağından gelen aramaların iki örneği görüntülenmektedir: 
    
   * Profil Oluşturucu çalışırken uygulama istek almıyor. İleti, hiçbir etkinlik olmadığı için karşıya yüklemenin iptal edildiğini açıklar. 

   * Sağ tarafta, profil oluşturucu çalışırken meydana gelen istekleri algıladığında profil oluşturucu başlatılır ve özel olaylar gönderilir. ServiceProfilerSample özel olayı görüntüleniyorsa, profil oluşturucunun bir istek için bir izleme iliştirdiğinden ve **Application Insights performans** bölmesinde izlemeyi görüntüleyebiliyorsanız, bu, izlemeyi gösterir.

     Hiçbir telemetri görüntülenmiyorsa, Profil Oluşturucu çalışmıyor demektir. Sorunu gidermek için, bu makalenin ilerleyen kısımlarında belirli uygulama türü için sorun giderme bölümlerine bakın.  

     ![Profil Oluşturucu telemetrisi ara][profiler-search-telemetry]

1. Profil oluşturucunun çalıştırıldığı sırada istekler varsa, isteklerin profil oluşturucu etkin olan uygulamanızın parçası tarafından işlendiğinden emin olun. Uygulamalar bazen birden çok bileşenden oluşsa da, bazı bileşenler için profil oluşturucu etkinleştirilir. **Yapılandırma Application Insights Profiler** bölmesi, karşıya yüklenen izlemeler içeren bileşenleri görüntüler.

### <a name="other-things-to-check"></a>Denetlenecek diğer şeyler
* Uygulamanızın .NET Framework 4,6 ' de çalıştığından emin olun.
* Web uygulamanız ASP.NET Core bir uygulama ise, en az ASP.NET Core 2,0 çalıştırıyor olmalıdır.
* Görüntülemeye çalıştığınız veriler birkaç haftadan daha eskiyse, zaman filtrenizi sınırlamayı deneyin ve yeniden deneyin. İzlemeler yedi gün sonra silinir.
* Proxy 'lerin veya güvenlik duvarının https://gateway.azureserviceprofiler.net erişimi engellemediğinden emin olun.

### <a id="double-counting"></a>Paralel iş parçacıklarında Double sayma

Bazı durumlarda, yığın görüntüleyicisinde toplam zaman ölçümü, istek süresinden daha fazla olur.

İki veya daha fazla iş parçacığı bir istekle ilişkilendirildiğinde ve paralel olarak çalıştıklarında bu durum oluşabilir. Bu durumda, toplam iş parçacığı süresi geçen süreden daha fazla. Bir iş parçacığı diğerinin tamamlanmasını bekliyor olabilir. Görüntüleyici bu durumu algılamaya çalışır ve ilgi çekici olmayan beklemeyi atlar. Bunu yaparken, önemli bilgiler olabileceğini atlamak yerine çok fazla bilgi görüntüleme bölümünde yer alır.

İzleyicizlerde paralel iş parçacıkları gördüğünüzde, isteğin kritik yolunu belirleyebilmeniz için hangi iş parçacıklarının beklediğini belirlemek için bu iş parçacıklarını da beklediğini saptayın. Genellikle, bir bekleme durumuna hızlıca giden iş parçacığı yalnızca diğer iş parçacıkları üzerinde bekliyor. Diğer iş parçacıklarıyla yoğunlaşın ve bekleyen iş parçacıklarında süreyi yoksayın.

### <a name="error-report-in-the-profile-viewer"></a>Profil görüntüleyicisinde hata raporu
Portalda bir destek bileti gönderme. Hata iletisinden bağıntı KIMLIĞINI eklediğinizden emin olun.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Azure App Service profil oluşturucu sorunlarını giderme
Profil oluşturucunun düzgün çalışması için:
* Web App Service planınız temel katman veya daha yüksek olmalıdır.
* Web uygulamanızda Application Insights etkinleştirilmiş olması gerekir.
* Web uygulamanız aşağıdaki uygulama ayarlarına sahip olmalıdır:

    |Uygulama Ayarı    | Değer    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights kaynağınız için Ikey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* **ApplicationInsightsProfiler3** WebJob çalışıyor olmalıdır. WebJob 'u denetlemek için:
   1. [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)'ye gidin.
   1. **Araçlar** menüsünde, **Web İşleri Panosu**' nu seçin.  
      **Web işleri** bölmesi açılır. 
   
      ![Profil Oluşturucu-WebJob]   
   
   1. Web işi 'nin ayrıntılarını görüntülemek için, günlüğü de içeren **ApplicationInsightsProfiler3** bağlantısını seçin.  
     **Sürekli WebJob ayrıntıları** bölmesi açılır.

      ![Profiler-WebJob-log]

Profil oluşturucunun neden sizin için çalışmadığını anlamak için günlüğü indirebilir ve yardım için ekibimize gönderebilirsiniz serviceprofilerhelp@microsoft.com. 
    
### <a name="manual-installation"></a>El ile yükleme

Profil oluşturucuyu yapılandırırken, Web uygulamasının ayarlarına güncelleştirmeler yapılır. Ortamınız gerektiriyorsa, güncelleştirmeleri el ile uygulayabilirsiniz. Örneğin, uygulamanızın PowerApps için bir Web Apps ortamda çalışıyor olması olabilir. Güncelleştirmeleri el ile uygulamak için:

1. **Web uygulaması denetim** bölmesinde **Ayarlar**' ı açın.

1. **.NET Framework sürümünü** **v 4.6**olarak ayarlayın.

1. **Her zaman** **Açık olarak ayarlayın.**
1. Bu uygulama ayarlarını oluşturun:

    |Uygulama Ayarı    | Değer    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights kaynağınız için Ikey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Çok fazla etkin profil oluşturma oturumu

Şu anda, profil oluşturucuyu aynı hizmet planında çalışan en fazla dört Azure Web uygulaması ve dağıtım yuvası üzerinde etkinleştirebilirsiniz. Tek bir App Service planında çalışan dörtten fazla Web uygulamanız varsa, Profiler bir *Microsoft. ServiceProfiler. Exceptions. TooManyETWSessionException*oluşturabilir. Profil Oluşturucu her Web uygulaması için ayrı ayrı çalışır ve her bir uygulama için bir Windows için olay Izleme (ETW) oturumu başlatmaya çalışır. Ancak, sınırlı sayıda ETW oturumu tek seferde etkin olabilir. Profiler WebJob çok sayıda etkin profil oluşturma oturumu bildirirse, bazı Web uygulamalarını farklı bir hizmet planına taşıyın.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Dağıtım hatası: Dizin boş değil:\\giriş\\sitesi\\Wwwroot\\App_Data\\işleri '

Web uygulamanızı Profiler özellikli bir Web Apps kaynağına yeniden dağıtıyorsanız, aşağıdaki iletiyi görebilirsiniz:

*Dizin boş değil:\\giriş\\sitesi\\Wwwroot\\App_Data\\işleri '*

Bu hata, betiklerden veya Azure DevOps dağıtım ardışık düzeninde Web Dağıtımı çalıştırırsanız oluşur. Çözüm, Web Dağıtımı görevine aşağıdaki ek dağıtım parametrelerini eklemektir:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Bu parametreler Application Insights Profiler tarafından kullanılan klasörü siler ve yeniden dağıtma işleminin engelini kaldırın. Halen çalışmakta olan profil Oluşturucu örneğini etkilemez.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler çalışıp çalışmadığını Nasıl yaparım? mi?

Profiler, Web uygulamasında sürekli bir WebJob olarak çalışır. Web uygulaması kaynağını [Azure Portal](https://portal.azure.com)açabilirsiniz. **WebJobs** bölmesinde **Applicationınsightsprofiler**durumunu kontrol edin. Çalışmıyorsa, daha fazla bilgi almak için **günlükleri** açın.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Profil Oluşturucu ve Azure Tanılama sorunlarını giderme

>**Cloud Services için WAD içinde yer alan Profiler 'daki hata düzeltildi.** Cloud Services için en son WAD (1.12.2.0) sürümü, App Insights SDK 'sının tüm son sürümleriyle birlikte çalışmaktadır. Bulut hizmeti ana bilgisayarları WAD 'yi otomatik olarak yükseltir, ancak bu işlem anında değil. Bir yükseltmeyi zorlamak için hizmetinizi yeniden dağıtabilirsiniz veya düğümü yeniden başlatabilirsiniz.

Profiler 'ın Azure Tanılama tarafından doğru yapılandırılıp yapılandırılmadığını görmek için aşağıdaki üç şeyi yapın: 
1. İlk olarak, dağıtılan Azure Tanılama yapılandırmanın içeriklerinin beklediğiniz şeydir olup olmadığını denetleyin. 

1. İkinci olarak, Azure Tanılama profil oluşturucu komut satırında uygun Ikey ' i geçirdiğinizden emin olun. 

1. Üçüncü olarak, profil oluşturucunun bir hata döndürdüğünden çalışıp çalışmadığını görmek için profil oluşturucu günlük dosyasını denetleyin. 

Azure Tanılama yapılandırmak için kullanılan ayarları denetlemek için:

1. Sanal makinede (VM) oturum açın ve ardından günlük dosyasını bu konumda açın. (Sürücü c: veya d: olabilir ve eklenti sürümü farklı olabilir.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    or
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. Dosyasında, Azure Tanılama yapılandırmak için sanal makineye geçirilen ayarları bulmak üzere **Wadcfg** dizesini arama yapabilirsiniz. Profil Oluşturucu havuzu tarafından kullanılan Ikey 'in doğru olup olmadığını kontrol edebilirsiniz.

1. Profil oluşturucuyu başlatmak için kullanılan komut satırını kontrol edin. Profil oluşturucuyu başlatmak için kullanılan bağımsız değişkenler aşağıdaki dosyadır. (Sürücü c: veya d:) olabilir

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Profil oluşturucu komut satırındaki Ikey 'in doğru olduğundan emin olun. 

1. Önceki *config. JSON* dosyasında bulunan yolu kullanarak profil oluşturucu günlük dosyasını kontrol edin. Profil oluşturucunun kullandığı ayarları gösteren hata ayıklama bilgilerini görüntüler. Ayrıca, profil oluşturucunun durum ve hata iletilerini görüntüler.  

    Uygulamanız istek alırken profil oluşturucu çalışıyorsa şu ileti görüntülenir: *Ikey 'Den etkinlik algılandı*. 

    İzleme karşıya yüklenirken şu ileti görüntülenir: *izlemeyi karşıya yüklemeye başlayın*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[Profil Oluşturucu-WebJob]:./media/profiler-troubleshooting/Profiler-webjob.png
[Profiler-WebJob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png









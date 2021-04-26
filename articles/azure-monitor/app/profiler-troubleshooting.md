---
title: Azure Application Insights Profiler sorunlarını giderme
description: Bu makalede, geliştiricilerin Application Insights Profiler etkinleştirmesine ve kullanmasına yardımcı olacak sorun giderme adımları ve bilgileri sunulmaktadır.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2c80362c3407f1404c6657997de89c2741264909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026564"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler etkinleştirme veya görüntüleme sorunlarını giderme

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Genel sorun giderme

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>Uygun profil oluşturucu uç noktasını kullandığınızdan emin olun

Şu anda yalnızca uç nokta değişiklikleri gerektiren bölgeler [Azure Kamu](../../azure-government/compare-azure-government-global-azure.md#application-insights) ve [Azure Çin](/azure/china/resources-developer-guide)' dir.

|Uygulama Ayarı    | ABD kamu bulutu | Çin bulutu |   
|---------------|---------------------|-------------|
|Applicationınsightsprofilerendpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|Applicationınsi, Sendpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiller yalnızca, profil oluşturucu çalışırken uygulamanıza yönelik istekler varsa yüklenir

Azure Application Insights Profiler her saat için verileri iki dakika toplar. Ayrıca, **yapılandır Application Insights Profiler** bölmesinde **Şimdi profil** düğmesini seçtiğinizde verileri de toplayabilir.

> [!NOTE]
> Profil oluşturma verileri, yalnızca profil oluşturucu çalışırken meydana gelen bir isteğe iliştirilebilecek olduğunda karşıya yüklenir. 

Profiler, izleme iletilerini ve özel olayları Application Insights kaynağına yazar. Profil oluşturucunun nasıl çalıştığını görmek için bu olayları kullanabilirsiniz:

1. Application Insights kaynağına profil oluşturucu tarafından gönderilen izleme iletilerini ve özel olayları arayın. İlgili verileri bulmak için bu arama dizesini kullanabilirsiniz:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Aşağıdaki görüntüde iki AI kaynağından gelen aramaların iki örneği görüntülenmektedir: 
    
   * Profil Oluşturucu çalışırken uygulama istek almıyor. İleti, hiçbir etkinlik olmadığı için karşıya yüklemenin iptal edildiğini açıklar. 

   * Sağ tarafta, profil oluşturucu çalışırken meydana gelen istekleri algıladığında profil oluşturucu başlatılır ve özel olaylar gönderilir. `ServiceProfilerSample`Özel olay görüntüleniyorsa, bir profilin yakalandığı ve **Application Insights performans** bölmesinde kullanılabildiği anlamına gelir.

     Hiçbir kayıt görüntülenmiyorsa, Profil Oluşturucu çalışmıyor demektir. Sorunu gidermek için, bu makalenin ilerleyen kısımlarında belirli uygulama türü için sorun giderme bölümlerine bakın.  

     ![Profil Oluşturucu telemetrisi ara][profiler-search-telemetry]

### <a name="other-things-to-check"></a>Denetlenecek diğer şeyler
* Uygulamanızın .NET Framework 4,6 ' de çalıştığından emin olun.
* Web uygulamanız ASP.NET Core bir uygulama ise, en az ASP.NET Core 2,0 çalıştırıyor olmalıdır.
* Görüntülemeye çalıştığınız veriler birkaç haftadan daha eskiyse, zaman filtrenizi sınırlamayı deneyin ve yeniden deneyin. İzlemeler yedi gün sonra silinir.
* Proxy 'lerin veya güvenlik duvarının erişiminin engellenmediğinden emin olun https://gateway.azureserviceprofiler.net .
* Profil Oluşturucu ücretsiz veya paylaşılan App Service planlarında desteklenmez. Bu planlardan birini kullanıyorsanız, temel planların birine kadar ölçeği ölçeklendirmeyi deneyin ve profil oluşturucu çalışmaya başlaması gerekir.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Paralel iş parçacıklarında Double sayma

Bazı durumlarda, yığın görüntüleyicisinde toplam zaman ölçümü, istek süresinden daha fazla olur.

İki veya daha fazla paralel iş parçacığı bir istekle ilişkilendirildiğinde bu durum oluşabilir. Bu durumda, toplam iş parçacığı süresi geçen süreden daha fazla.

Bir iş parçacığı diğerinin tamamlanmasını bekliyor olabilir. Görüntüleyici bu durumu algılamaya çalışır ve ilgi çekici olmayan beklemeyi atlar. Bunu yaparken, önemli bilgiler olabileceğini atlamak yerine çok fazla bilgi görüntüleme bölümünde yer alır.

İzleyicizlerde paralel iş parçacıkları gördüğünüzde, isteğin etkin yolunu tanımlayabilmeniz için hangi iş parçacıklarının beklediğini belirlemek için bu iş parçacıklarını da beklediğini saptayın.

Genellikle, bir bekleme durumuna hızlıca giden iş parçacığı yalnızca diğer iş parçacıkları üzerinde bekliyor. Diğer iş parçacıklarıyla yoğunlaşın ve bekleyen iş parçacıklarında süreyi yoksayın.

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
   1. [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret)'ye gidin.
   1. **Araçlar** menüsünde, **Web İşleri Panosu**' nu seçin.  
      **Web işleri** bölmesi açılır. 
   
      ![Ekran görüntüsü, işlerin adını, durumunu ve son çalışma zamanını görüntüleyen WebJobs bölmesini gösterir.][profiler-webjob]   
   
   1. Web işi 'nin ayrıntılarını görüntülemek için, günlüğü de içeren **ApplicationInsightsProfiler3** bağlantısını seçin.  
     **Sürekli WebJob ayrıntıları** bölmesi açılır.

      ![Ekran görüntüsü sürekli WebJob ayrıntıları bölmesini gösterir.][profiler-webjob-log]

Profil Oluşturucu sizin için çalışmıyorsa, günlüğü indirebilir ve yardım için ekibimize gönderebilirsiniz serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>Tanılama Hizmetleri site uzantısının durum sayfasını denetleyin
Profil Oluşturucu portalda [Application Insights bölmesi](profiler.md) aracılığıyla etkinleştirildiyse, tanılama Hizmetleri site uzantısı tarafından etkinleştirilmiştir.

> [!NOTE]
> Codeless Application Insights Profiler yüklemesi .NET Core destek ilkesini izler.
> Desteklenen çalışma zamanları hakkında daha fazla bilgi için bkz. [.NET Core destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Aşağıdaki URL 'ye giderek bu uzantının durum sayfasını kontrol edebilirsiniz: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Durum sayfası bağlantısının etki alanı, buluta bağlı olarak değişir.
Bu etki alanı App Service kudu yönetim sitesiyle aynı olacaktır.

Bu durum sayfası, profil oluşturucu ve Snapshot Collector aracılarının yükleme durumunu gösterir. Beklenmeyen bir hata oluşursa, görüntülenir ve nasıl düzeltileceğini gösterir.

Bu durum sayfasının temel URL 'sini almak için App Service kudu yönetim sitesini kullanabilirsiniz:
1. App Service uygulamanızı Azure portal açın.
2. **Gelişmiş Araçlar**' ı seçin veya **kudu**'yi arayın.
3. **Git**' i seçin.
4. Kudu yönetim sitesinde olduktan sonra URL 'de **aşağıdakini ekleyin `/DiagnosticServices` ve ENTER tuşuna basın**.
 Şu şekilde sona acaktır: `https://<kudu-url>/DiagnosticServices`

Aşağıda benzer bir durum sayfası görüntülenir: ![ Tanılama Hizmetleri durum sayfası](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>El ile yükleme

Profil oluşturucuyu yapılandırırken, Web uygulamasının ayarlarına güncelleştirmeler yapılır. Ortamınız gerektiriyorsa, güncelleştirmeleri el ile uygulayabilirsiniz. Örneğin, uygulamanızın PowerApps için bir Web Apps ortamda çalışıyor olması olabilir. Güncelleştirmeleri el ile uygulamak için:

1. **Web uygulaması denetim** bölmesinde **Ayarlar**' ı açın.

1. **.NET Framework sürümünü** **v 4.6** olarak ayarlayın.

1. **Her zaman** **Açık olarak ayarlayın.**
1. Bu uygulama ayarlarını oluşturun:

    |Uygulama Ayarı    | Değer    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights kaynağınız için Ikey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Çok fazla etkin profil oluşturma oturumu

Profil oluşturucuyu aynı hizmet planında çalışan en fazla dört Web Apps etkinleştirebilirsiniz. Dörtten fazlasına sahipseniz, profil oluşturucu *Microsoft. ServiceProfiler. Exceptions. TooManyETWSessionException* oluşturabilir. Bu çözümü çözümlemek için bazı Web uygulamalarını farklı bir hizmet planına taşıyın.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Dağıtım hatası: Dizin boş değil: \\ ana \\ site \\ Wwwroot \\ App_Data \\ işleri '

Web uygulamanızı Profiler özellikli bir Web Apps kaynağına yeniden dağıtıyorsanız, aşağıdaki iletiyi görebilirsiniz:

*Dizin boş değil: \\ ana \\ site \\ Wwwroot \\ App_Data \\ işleri '*

Bu hata, komut dosyalarından veya Azure Pipelines Web Dağıtımı çalıştırırsanız oluşur. Çözüm, Web Dağıtımı görevine aşağıdaki dağıtım parametrelerini eklemektir:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Bu parametreler Application Insights Profiler tarafından kullanılan klasörü siler ve yeniden dağıtma işleminin engelini kaldırın. Halen çalışmakta olan profil Oluşturucu örneğini etkilemez.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler çalışıp çalışmadığını Nasıl yaparım? mi?

Profiler, Web uygulamasında sürekli bir WebJob olarak çalışır. Web uygulaması kaynağını [Azure Portal](https://portal.azure.com)açabilirsiniz. **WebJobs** bölmesinde **Applicationınsightsprofiler** durumunu kontrol edin. Çalışmıyorsa, daha fazla bilgi almak için **günlükleri** açın.

## <a name="troubleshoot-vms-and-cloud-services"></a>VM 'Ler ve Cloud Services sorunlarını giderme

>**Cloud Services için WAD içinde yer alan Profiler 'daki hata düzeltildi.** Cloud Services için en son WAD (1.12.2.0) sürümü, App Insights SDK 'sının tüm son sürümleriyle birlikte çalışmaktadır. Bulut hizmeti ana bilgisayarları WAD 'yi otomatik olarak yükseltir, ancak bu işlem anında değil. Bir yükseltmeyi zorlamak için hizmetinizi yeniden dağıtabilirsiniz veya düğümü yeniden başlatabilirsiniz.

Profiler 'ın Azure Tanılama tarafından doğru yapılandırılıp yapılandırılmadığını görmek için aşağıdaki adımları izleyin: 
1. Azure Tanılama yapılandırmasının dağıtıldığı içeriğin beklediğiniz şeydir olduğunu doğrulayın. 

1. İkinci olarak, Azure Tanılama profil oluşturucu komut satırında uygun Ikey ' i geçirdiğinizden emin olun. 

1. Üçüncü olarak, profil oluşturucunun bir hata döndürdüğünden çalışıp çalışmadığını görmek için profil oluşturucu günlük dosyasını denetleyin. 

Azure Tanılama yapılandırmak için kullanılan ayarları denetlemek için:

1. Sanal makinede (VM) oturum açın ve ardından günlük dosyasını bu konumda açın. Eklenti sürümü makinenizde daha yeni olabilir.
    
    VM 'Ler için:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Cloud Services için:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. Dosyasında, Azure Tanılama yapılandırmak için sanal makineye geçirilen ayarları bulmak üzere **Wadcfg** dizesini arama yapabilirsiniz. Profil Oluşturucu havuzu tarafından kullanılan Ikey 'in doğru olup olmadığını kontrol edebilirsiniz.

1. Profil oluşturucuyu başlatmak için kullanılan komut satırını kontrol edin. Profil oluşturucuyu başlatmak için kullanılan bağımsız değişkenler aşağıdaki dosyadır. (Sürücü c: veya d: olabilir ve dizin gizli olabilir.)

    VM 'Ler için:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    Cloud Services için:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Profil oluşturucu komut satırındaki Ikey 'in doğru olduğundan emin olun. 

1. Dosyadaki önceki *config.js* bulunan yolu kullanarak, **bootstrapn. log** adlı Profil Oluşturucu günlük dosyasını kontrol edin. Profil oluşturucunun kullandığı ayarları gösteren hata ayıklama bilgilerini görüntüler. Ayrıca, profil oluşturucunun durum ve hata iletilerini görüntüler.  

    VM 'Ler için, bu dosya şu şekildedir:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Cloud Services için:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Uygulamanız istek alırken profil oluşturucu çalışıyorsa şu ileti görüntülenir: *Ikey 'Den etkinlik algılandı*. 

    İzleme karşıya yüklenirken şu ileti görüntülenir: *izlemeyi karşıya yüklemeye başlayın*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Ağ ara sunucusunu veya güvenlik duvarı kurallarını Düzenle

Uygulamanız bir ara sunucu veya güvenlik duvarı üzerinden Internet 'e bağlanırsa, profil oluşturucu hizmetiyle iletişim kurmak için kuralları güncelleştirmeniz gerekebilir.

Application Insights Profiler tarafından kullanılan IP 'Ler, Azure Izleyici hizmeti etiketine dahildir. Daha fazla bilgi için bkz. [hizmet etiketleri belgeleri](../../virtual-network/service-tags-overview.md).


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png
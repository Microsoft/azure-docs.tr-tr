---
title: Veri bulunmama sorunlarını giderme - .NET için Application Insights
description: Azure Application Insights verileri görmüyor musunuz? Buradan deneyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/23/2018
ms.openlocfilehash: 857188ebb5ddc3c24f6a225819c47fc1643417e6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887526"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Veri bulunmama sorunlarını giderme - .NET için Application Insights
## <a name="some-of-my-telemetry-is-missing"></a>Telemetrimin bazıları eksik
*Application Insights, yalnızca Uygulamam tarafından oluşturulan olayların bir bölümünü görüyorum.*

* Aynı kesri sürekli görüyorsanız, büyük olasılıkla Uyarlamalı [örnekleme](../../azure-monitor/app/sampling.md)nedeniyle olur. Bunu onaylamak için, ara ' yı açın (genel bakış dikey penceresinden) ve bir Isteğin örneğine veya başka bir olaya bakın. Özellikler bölümünün en altında "..." seçeneğine tıklayın. Tam özellik ayrıntılarını almak için. Istek sayısı > 1 ise örnekleme işlemi sürüyor.
* Aksi takdirde, fiyatlandırma planınız için bir [veri hızı sınırına](../../azure-monitor/app/pricing.md#limits-summary) ulaşalım olanağınız vardır. Bu sınırlar dakika başına uygulanır.

*Veri kaybı rastgele yaşıyor.*

* [Telemetri kanalında](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost) veri kaybı yaşamadığınızı denetleyin

* Telemetri kanalı [GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) deposunda bilinen sorunları denetle

*Uygulama durmak üzere olduğunda konsol uygulamasında veya Web uygulamasında veri kaybı yaşıyorum.*

* SDK kanalı, Telemetriyi arabelleğe tutar ve bunları toplu işler halinde gönderir. Uygulama kapatıyorsunuz, açıkça [flush ()](api-custom-events-metrics.md#flushing-data)çağrısı yapmanız gerekebilir. `Flush()` davranışı, kullanılan gerçek [kanala](telemetry-channels.md#built-in-telemetry-channels) bağlıdır.

## <a name="no-data-from-my-server"></a>Sunucuma veri yok
*Uygulamamı Web sunucuma yükledim ve bundan sonra herhangi bir telemetri görmüyorum. Geliştirici makinmda Tamam 'a çalıştı.*

* Büyük olasılıkla bir güvenlik duvarı sorunu. [Application Insights veri göndermek için güvenlik duvarı özel durumlarını ayarlayın](../../azure-monitor/app/ip-addresses.md).
* IIS sunucusunda bazı Önkoşullar eksik olabilir: .NET Genişletilebilirliği 4,5 ve ASP.NET 4,5.

*Mevcut uygulamaları izlemek için Web sunucuma [durum İzleyicisi yükledim](../../azure-monitor/app/monitor-performance-live-website-now.md) . Hiç sonuç görmüyorum.*

* Bkz. [sorun giderme durum İzleyicisi](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Visual Studio 'da ' Add Application Insights ' seçeneği yok
*Çözüm Gezgini var olan bir projeye sağ tıkladığımda, hiçbir Application Insights seçeneği görmüyorum.*

* Tüm .NET projesi türleri araçlar tarafından desteklenmez. Web ve WCF projeleri desteklenir. Masaüstü veya hizmet uygulamaları gibi diğer proje türleri için de [projenize el ile bir APPLICATION INSIGHTS SDK ekleyebilirsiniz](../../azure-monitor/app/windows-desktop.md).
* [Visual Studio 2013 güncelleştirme 3 veya sonraki bir sürüme](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs)sahip olduğunuzdan emin olun. Application Insights SDK 'sını sağlayan geliştirici Analizi araçlarıyla önceden yüklenmiş olarak gelir.
* **Araçlar**, **Uzantılar ve güncelleştirmeler** ' i seçin ve **Developer Analytics Tools** yüklendiğini ve etkinleştirildiğini denetleyin. Bu durumda, kullanılabilir bir güncelleştirme olup olmadığını görmek için **güncelleştirmeler** ' e tıklayın.
* Yeni proje iletişim kutusunu açın ve ASP.NET Web uygulaması ' nı seçin. Burada Application Insights seçeneği görürseniz araçlar yüklenir. Aksi takdirde, Developer Analytics Tools kaldırıp yeniden yüklemeyi deneyin.

## <a name="q02"></a>Application Insights eklenemedi
*Mevcut bir projeye Application Insights eklemeye çalıştığımda bir hata iletisi görüyorum.*

Olası nedenler:

* Application Insights portalı ile iletişim kurulamadı; veya
* Azure hesabınızla ilgili bazı sorunlar var;
* Yalnızca [yeni kaynağı oluşturmaya çalıştığınız abonelik veya gruba okuma erişiminizin](../../azure-monitor/app/resources-roles-access-control.md)olması gerekir.

Onar

* Doğru Azure hesabı için oturum açma kimlik bilgileri sağladıysanız emin olun.
* Tarayıcınızda [Azure Portal](https://portal.azure.com)erişiminiz olup olmadığını kontrol edin. Ayarları açın ve herhangi bir kısıtlama olup olmadığını görün.
* [Mevcut projenize Application Insights ekleyin](../../azure-monitor/app/asp-net.md): Çözüm Gezgini, projenize sağ tıklayıp "Application Insights Ekle" seçeneğini belirleyin.

## <a name="emptykey"></a>"Izleme anahtarı boş olamaz" hatası alıyorum
Application Insights yüklerken bir sorun oluştu veya belki de günlüğe kaydetme bağdaştırıcısı.

Çözüm Gezgini, projenize sağ tıklayın ve **Application Insights yapılandır > Application Insights**' i seçin. Azure 'da oturum açmanız ve bir Application Insights kaynağı oluşturmanız ya da var olan bir kaynağın yeniden kullanılması için sizi davet eden bir iletişim kutusu alacaksınız.

## <a name="NuGetBuild"></a>"Derleme sunucum üzerinde" NuGet paketleri eksik "
*Geliştirme makinmda hata ayıklarken her şey tamam, ancak yapı sunucusunda bir NuGet hatası alıyorum.*

Lütfen [NuGet paket geri yükleme](https://docs.nuget.org/Consume/Package-Restore) ve [otomatik paket geri yükleme](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)bölümüne bakın.

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Visual Studio 'dan Application Insights açmak için menü komutu eksik
*Proje Çözüm Gezgini sağ tıkladığımda, hiçbir Application Insights komutu görmüyorum veya açık bir Application Insights komutu görmüyorum.*

Olası nedenler:

* Application Insights kaynağını el ile oluşturduysanız veya proje Application Insights araçları tarafından desteklenmeyen bir türde ise.
* Geliştirici analizi araçları Visual Studio 'da devre dışı bırakılmıştır.
* Visual Studio, 2013 güncelleştirme 3 ' ten eski.

Onar

* Visual Studio sürümünüzün 2013 güncelleştirme 3 veya sonraki bir sürümü olduğundan emin olun.
* **Araçlar**, **Uzantılar ve güncelleştirmeler** ' i seçin ve **Geliştirici analiz araçları** 'nın yüklü ve etkin olduğunu denetleyin. Bu durumda, kullanılabilir bir güncelleştirme olup olmadığını görmek için **güncelleştirmeler** ' e tıklayın.
* Çözüm Gezgini ' de projenize sağ tıklayın. **> Application Insights yapılandırmak Application Insights**komutu görürseniz, projenizi Application Insights hizmetindeki kaynağa bağlamak için kullanın.

Aksi halde, proje türü geliştirici analizi araçları tarafından doğrudan desteklenmez. Telemetriyi görmek için [Azure Portal](https://portal.azure.com)oturum açın, sol gezinti çubuğunda Application Insights ' i seçin ve uygulamanızı seçin.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>Visual Studio 'dan açma Application Insights ' erişim engellendi '
*' Aç Application Insights ' menü komutu beni Azure portal alıyor, ancak ' erişim engellendi ' hatası alıyorum.*

Varsayılan tarayıcınızda en son kullandığınız Microsoft oturum açma [işlemi, Application Insights bu uygulamaya eklendiğinde oluşturulan kaynağa](../../azure-monitor/app/asp-net.md)erişemez. Olası iki neden vardır:

* Birden fazla Microsoft hesabı (Belki de bir iş ve kişisel Microsoft hesabı var mı? Varsayılan tarayıcınızda en son kullandığınız oturum açma işlemi, [projeye Application Insights ekleme](../../azure-monitor/app/asp-net.md)erişimine sahip olandan farklı bir hesaptır.
  * Çözüm: tarayıcı penceresinin sağ üst köşesindeki adınızı tıklayın ve oturumu kapatın. Sonra erişimi olan hesapla oturum açın. Ardından sol gezinti çubuğunda Application Insights ' ye tıklayın ve uygulamanızı seçin.
* Başka birisi projeye Application Insights ekledi ve size oluşturulduğu [kaynak grubuna erişim](../../azure-monitor/app/resources-roles-access-control.md) izni vermenizi unuttu.
  * Çözüm: bir kurumsal hesap kullandıklarında, takıma ekleyebilirler; ya da kaynak grubuna bireysel erişim izni verebilir.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>Visual Studio 'dan açma Application Insights ' varlık bulunamadı '
*' Aç Application Insights ' menü komutu beni Azure portal götürür, ancak ' varlık bulunamadı ' hatası alıyorum.*

Olası nedenler:

* Uygulamanız için Application Insights kaynağı silindi; veya
* İzleme anahtarı, proje dosyasını güncelleştirmeden doğrudan düzenlenerek ApplicationInsights. config dosyasında ayarlanır veya değiştirilmiştir.

ApplicationInsights. config dosyasındaki izleme anahtarı, telemetrinin gönderildiği yeri denetler. Proje dosyasındaki bir satır, Visual Studio 'da komutunu kullandığınızda hangi kaynağın açılacağını denetler.

Onar

* Çözüm Gezgini, projeye sağ tıklayın ve Application Insights, Application Insights Yapılandır ' ı seçin. İletişim kutusunda, mevcut bir kaynağa telemetri gönderilmesini ya da yeni bir tane oluşturmayı seçebilirsiniz. Veya
* Kaynağı doğrudan açın. [Azure Portal](https://portal.azure.com)oturum açın, sol gezinti çubuğunda Application Insights ' e tıklayın ve ardından uygulamanızı seçin.

## <a name="where-do-i-find-my-telemetry"></a>Telemetriyi nerede bulabilirim?
*[Microsoft Azure Portal](https://portal.azure.com)oturum açdım ve Azure giriş panosuna bakıyordum. Application Insights verilerimi nerede bulabilirim?*

* Sol gezinti çubuğunda Application Insights ' a ve ardından uygulamanızın adı ' na tıklayın. Orada hiç projeniz yoksa, [Web projenize Application Insights eklemeniz veya yapılandırmanız](../../azure-monitor/app/asp-net.md)gerekir.  
  Bazı Özet grafikler görürsünüz. Daha fazla ayrıntı görmek için bunlara tıklayabilirsiniz.
* Visual Studio 'da uygulamanızda hata ayıklaması yaparken Application Insights düğmesine tıklayın.

## <a name="q03"></a>Sunucu verisi yok (veya hiç veri yok)
*Uygulamamı çalıştırdım ve Microsoft Azure Application Insights hizmeti 'ni açtık, ancak tüm grafiklerde ' nasıl toplayacağınızı öğrenin... ' gösterilmektedir veya ' yapılandırılmadı. '* Ya da *yalnızca sayfa görünümü ve Kullanıcı verileri, ancak sunucu verisi yok.*

* Uygulamanızı Visual Studio 'da hata ayıklama modunda çalıştırın (F5). Uygulamayı, bazı telemetri oluşturmak için kullanın. Visual Studio çıktı penceresinde günlüğe kaydedilen olayları görebilmeniz için denetleyin.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Application Insights portalında [Tanılama araması](../../azure-monitor/app/diagnostic-search.md)' nı açın. Veriler genellikle önce burada görünür.
* Yenile düğmesine tıklayın. Dikey pencere kendi kendine düzenli olarak yenilenir, ancak bunu el ile de yapabilirsiniz. Yenileme aralığı daha büyük zaman aralıkları için daha uzun.
* İzleme anahtarlarının eşleştiğinden emin olun. Application Insights portalındaki uygulamanızın ana dikey penceresinde, **Essentials** açılan penceresinde, **izleme anahtarı**' na bakın. Ardından, Visual Studio 'daki projenizde ApplicationInsights. config dosyasını açın ve `<instrumentationkey>`bulun. İki anahtarın eşit olup olmadığını denetleyin. Aksi takdirde:  
  * Portalda Application Insights ' a tıklayın ve doğru anahtarla uygulama kaynağını arayın; veya
  * Visual Studio Çözüm Gezgini, projeye sağ tıklayın ve Application Insights, Yapılandır ' ı seçin. Doğru kaynağa telemetri göndermek için uygulamayı sıfırlayın.
  * Eşleşen anahtarları bulamıyorsanız, portalda ' de olduğu gibi Visual Studio 'da aynı oturum açma kimlik bilgilerini kullanıp kullanınızdan emin olun.
* [Microsoft Azure giriş panosunda](https://portal.azure.com), hizmet durumu haritasına bakın. Bazı uyarı göstergeleri varsa, Tamam ' a dönene kadar bekleyin ve ardından Application Insights uygulaması dikey penceresini kapatıp yeniden açın.
* Ayrıca [durum blogumuzu](https://blogs.msdn.microsoft.com/servicemap-status/)inceleyin.
* [Sunucu tarafı SDK](../../azure-monitor/app/api-custom-events-metrics.md) için `TelemetryClient` örneklerindeki veya `TelemetryContext`izleme anahtarını değiştirebilen herhangi bir kod yazdınız mı? Ya da çok fazla filtreleme olabilecek bir [filtre veya örnekleme yapılandırması](../../azure-monitor/app/api-filtering-sampling.md) mı yazdınız?
* ApplicationInsights. config dosyasını düzenlediyseniz, [TelemetryInitializers ve TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md)yapılandırmasını dikkatle kontrol edin. Yanlış adlı bir tür veya parametre SDK 'nın veri gönderememesine neden olabilir.

## <a name="q04"></a>Sayfa görünümlerinde, tarayıcılarda, kullanımında veri yok
*Sunucu yanıt süresi ve sunucu Isteği grafiklerde veri görüyorum, ancak sayfa görünümü yükleme sırasında veya tarayıcıda veya kullanım dikey penceresinde veri yok.*

Veriler Web sayfalarındaki betiklerden gelir. 

* Mevcut bir web projesine Application Insights eklediyseniz, [betikleri el ile eklemeniz gerekir](../../azure-monitor/app/javascript.md).
* Internet Explorer 'ın sitenizin uyumluluk modunda görüntülenmediğinden emin olun.
* Verilerin `dc.services.visualstudio.com`gönderildiğini doğrulamak için tarayıcının hata ayıklama özelliğini (bazı tarayıcılarda F12 ' i seçin, sonra da ağ ' ı seçin.) kullanın.

## <a name="no-dependency-or-exception-data"></a>Bağımlılık veya özel durum verisi yok
Bkz. [bağımlılık telemetrisi](../../azure-monitor/app/asp-net-dependencies.md) ve [özel durum telemetrisi](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Performans verisi yok
Durum İzleyicisi 'ni ve [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md)'yi yüklüyorsanız, [Java Web Hizmetleri](../../azure-monitor/app/java-collectd.md), [Windows Masaüstü uygulamaları](../../azure-monitor/app/windows-desktop.md), [IIS Web UYGULAMALARı ve HIZMETLERI](../../azure-monitor/app/monitor-performance-live-website-now.md)için performans verileri (CPU, GÇ oranı vb.) kullanılabilir. Bunu ayarlar, sunucular bölümünde bulabilirsiniz.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Uygulamayı sunucuma yayımladığım için hiçbir (sunucu) veri yok
* Tüm Microsoft 'un gerçekten kopyalamadığını denetleyin. Microsoft. Diagnostics. Instrumentation. Extensions. KESMENOKTASI. dll ile birlikte, sunucuda ApplicationInsights dll 'Leri
* Güvenlik duvarınızdaki [bazı TCP bağlantı noktalarını açmanız](../../azure-monitor/app/ip-addresses.md)gerekebilir.
* Şirket ağınızı göndermek için bir proxy kullanmanız gerekiyorsa, Web. config dosyasında [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) 'yi ayarlayın
* Windows Server 2008: şu güncelleştirmeleri yüklediğinizden emin olun: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Verileri görmek için kullandım, ancak durdu
* [Durum blogunu](https://blogs.msdn.com/b/applicationinsights-status/)denetleyin.
* Aylık veri noktaları kotasına ulaştınız misiniz? Bulmak için ayarları/kotayı ve fiyatlandırmayı açın. Bu durumda, planınızı yükseltebilir veya ek kapasite için ödeme yapabilirsiniz. [Fiyatlandırma şemasına](https://azure.microsoft.com/pricing/details/application-insights/)bakın.

## <a name="i-dont-see-all-the-data-im-expecting"></a>Beklediğiniz tüm verileri göremiyorum
Uygulamanız çok miktarda veri gönderirse ve ASP.NET Version 2.0.0-Beta3 veya üzeri için Application Insights SDK kullanıyorsanız, [Uyarlamalı örnekleme](../../azure-monitor/app/sampling.md) özelliği yalnızca telemetrinizin yüzdesini işleyebilir ve gönderebilir.

Devre dışı bırakabilirsiniz, ancak bu önerilmez. Örnekleme, tanılama amacıyla ilgili telemetri doğru şekilde iletilebilecek şekilde tasarlanmıştır.

## <a name="client-ip-address-is-0000"></a>İstemci IP adresi 0.0.0.0

5 2018 Şubat 'de Istemci IP adresinin günlüğe kaydedilmesini kaldırdığımız duyurulmuştur. Bu, coğrafi konumu etkilemez.

> [!NOTE]
> IP adresinin ilk 3 sekizlisinin olması gerekiyorsa özel bir öznitelik eklemek için bir [telemetri başlatıcısı](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) kullanabilirsiniz.
> Bu, 5 Şubat 2018’den önce toplanan verileri etkilemez.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Kullanıcı telemetrisinde yanlış coğrafi veriler
Şehir, bölge ve ülke boyutları IP adreslerinden türetilir ve her zaman doğru değildir. Bu IP adresleri önce konum için işlenir ve ardından depolanacak 0.0.0.0 olarak değiştirilir.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure Cloud Services’da çalıştırma üzerine “yöntem bulunamadı” özel durumu
.NET 4.6 için mi oluşturdunuz? 4.6 sürümü Azure Cloud Services rollerinde otomatik olarak desteklenmez. Uygulamanızı çalıştırmadan önce [her role 4.6 sürümünü yükleyin](../../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="troubleshooting-logs"></a>Sorun giderme günlükleri

Çerçeveiniz için sorun giderme günlüklerini yakalamak için bu yönergeleri izleyin.

### <a name="net-framework"></a>.NET Framework

1. NuGet 'den [Microsoft. Aspnet. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) paketini yükler. Yüklediğiniz sürüm, `Microsoft.ApplicationInsighs` yüklü olan geçerli sürümüyle eşleşmelidir

2. ApplicationInsights. config dosyanızı aşağıdakileri içerecek şekilde değiştirin:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Uygulamanızın yapılandırılan konuma yazma izinleri olmalıdır

3. Bu yeni ayarların SDK tarafından çekilmesi için işlemi yeniden başlatın

4. İşiniz bittiğinde bu değişiklikleri döndürür.

### <a name="net-core"></a>.NET Core

1. NuGet 'den [Microsoft. Aspnet. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) paketini yükler. Yüklediğiniz sürüm, `Microsoft.ApplicationInsights` yüklü olan geçerli sürümüyle eşleşmelidir

Microsoft. ApplicationInsights. AspNetCore 'un en son sürümü 2.7.1 'dir ve Microsoft. ApplicationInsights sürüm 2,10 ' e başvurur. Bu nedenle, yüklenecek Microsoft. AspNet. ApplicationInsights. HostingStartup sürümünün 2.10.0 olması gerekir

2. `Startup.cs` sınıfınıza `ConfigureServices` yöntemini değiştirin.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Uygulamanızın yapılandırılan konuma yazma izinleri olmalıdır

3. Bu yeni ayarların SDK tarafından çekilmesi için işlemi yeniden başlatın

4. İşiniz bittiğinde bu değişiklikleri döndürür.


## <a name="PerfView"></a>PerfView ile günlükleri toplama
[PerfView](https://github.com/Microsoft/perfview) , birçok kaynaktan tanılama bilgilerini toplayıp GÖRSELLEŞTIREREK CPU, bellek ve diğer sorunları yalıtmaya yardımcı olan ücretsiz bir tanılama ve performans analizi aracıdır.

Application Insights SDK günlüğü, PerfView tarafından yakalanabilen EventSource kendi kendine sorun giderme günlüklerini günlüğe kaydeder.

Günlükleri toplamak için PerfView indirin ve şu komutu çalıştırın:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Gerektiğinde bu parametreleri değiştirebilirsiniz:
- **Maxcollectsec**. PerfView 'ın süresiz olarak çalışmasını ve sunucunuzun performansını etkilemesini engellemek için bu parametreyi ayarlayın.
- **Yalnızca sağlayıcılar**. Yalnızca SDK 'dan günlükleri toplamak için bu parametreyi ayarlayın. Bu listeyi, özel araştırmalarınız temelinde özelleştirebilirsiniz. 
- **Noguı**. Bu parametreyi, GUI olmadan günlükleri toplamak için ayarlayın.


Daha fazla bilgi için,
- [PerfView ile performans Izlemeleri kaydediliyor](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Olay kaynaklarını Application Insights](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Hala çalışmıyor...
* [Application Insights Forumu](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

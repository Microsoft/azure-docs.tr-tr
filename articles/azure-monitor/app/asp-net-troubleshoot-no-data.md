---
title: Veri bulunmama sorunlarını giderme - .NET için Application Insights
description: Azure Application Insights verileri görmüyor musunuz? Buradan deneyin.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: 3b550e434db5b616ffedef7ebe9891b36fa431a2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311235"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>.NET/.NET Core için veri Application Insights sorunlarını giderme

## <a name="some-of-my-telemetry-is-missing"></a>Telemetrimin bazıları eksik
*Application Insights, yalnızca Uygulamam tarafından oluşturulan olayların bir bölümünü görüyorum.*

* Aynı kesri sürekli görüyorsanız, büyük olasılıkla Uyarlamalı [örnekleme](./sampling.md)nedeniyle olur. Bunu onaylamak için, ara ' yı açın (genel bakış dikey penceresinden) ve bir Isteğin örneğine veya başka bir olaya bakın. Özellikler bölümünün en altında "..." seçeneğine tıklayın. Tam özellik ayrıntılarını almak için. Istek sayısı > 1 ise örnekleme işlemi sürüyor.
* Aksi takdirde, fiyatlandırma planınız için bir [veri hızı sınırına](./pricing.md#limits-summary) ulaşalım olanağınız vardır. Bu sınırlar dakika başına uygulanır.

*Veri kaybı rastgele yaşıyor.*

* [Telemetri kanalında](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost) veri kaybı yaşamadığınızı denetleyin

* Telemetri kanalı [GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) deposunda bilinen sorunları denetle

*Uygulama durmak üzere olduğunda konsol uygulamasında veya Web uygulamasında veri kaybı yaşıyorum.*

* SDK kanalı, Telemetriyi arabelleğe tutar ve bunları toplu işler halinde gönderir. Uygulama kapatıyorsunuz, açıkça [flush ()](api-custom-events-metrics.md#flushing-data)çağrısı yapmanız gerekebilir. Davranış `Flush()` , kullanılan gerçek [kanala](telemetry-channels.md#built-in-telemetry-channels) bağlıdır.

## <a name="request-count-collected-by-application-insights-sdk-does-not-match-the-iis-log-count-for-my-application"></a>Application Insights SDK 'Sı tarafından toplanan istek sayısı uygulamamın IIS günlük sayısıyla eşleşmiyor

Internet Information Services (IIS), IIS 'ye ulaşan tüm istek sayısını günlüğe kaydeder ve kendiliğinden bir uygulamaya ulaşan toplam istekten farklı olabilir. Bu nedenle, SDK 'lar tarafından toplanan istek sayısı, toplam IIS günlük sayısıyla eşleşmeyecektir. 

## <a name="no-data-from-my-server"></a>Sunucuma veri yok
*Uygulamamı Web sunucuma yükledim ve bundan sonra herhangi bir telemetri görmüyorum. Geliştirici makinmda Tamam 'a çalıştı.*

* Büyük olasılıkla bir güvenlik duvarı sorunu. [Application Insights veri göndermek için güvenlik duvarı özel durumlarını ayarlayın](./ip-addresses.md).
* IIS sunucusunda bazı Önkoşullar eksik olabilir: .NET Genişletilebilirliği 4,5 ve ASP.NET 4,5.

*Mevcut uygulamaları izlemek için Web sunucuma [durum İzleyicisi yükledim](./monitor-performance-live-website-now.md) . Hiç sonuç görmüyorum.*

* Bkz. [sorun giderme durum İzleyicisi](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./sdk-connection-string.md?tabs=net) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: ' Microsoft. AspNet TelemetryCorrelation dosyası veya derlemesi yüklenemedi

Bu hata hakkında daha fazla bilgi için bkz. [GitHub sorunu 1610] ( https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) .

(2,4) sürümünden daha eski SDK 'lardan yükseltirken aşağıdaki değişikliklerin ve ' a uygulandığından emin olmanız gerekir `web.config` `ApplicationInsights.config` :

1. Bir yerine iki http modülü. ' De `web.config` iki http modülünüz olmalıdır. Sıra, bazı senaryolar için önemlidir:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. `ApplicationInsights.config`Ayrıca, ' de `RequestTrackingTelemetryModule` aşağıdaki telemetri modülüne sahip olmanız gerekir:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

***Düzgün bir şekilde yükseltilemeyen için beklenmeyen özel durumlar veya telemetri toplanmıyor olabilir.***


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Visual Studio 'da ' Add Application Insights ' seçeneği yok
*Çözüm Gezgini var olan bir projeye sağ tıkladığımda, hiçbir Application Insights seçeneği görmüyorum.*

* Tüm .NET projesi türleri araçlar tarafından desteklenmez. Web ve WCF projeleri desteklenir. Masaüstü veya hizmet uygulamaları gibi diğer proje türleri için de [projenize el ile bir APPLICATION INSIGHTS SDK ekleyebilirsiniz](./windows-desktop.md).
* [Visual Studio 2013 güncelleştirme 3 veya sonraki bir sürüme](/visualstudio/releasenotes/vs2013-update3-rtm-vs)sahip olduğunuzdan emin olun. Application Insights SDK 'sını sağlayan geliştirici Analizi araçlarıyla önceden yüklenmiş olarak gelir.
* **Araçlar**, **Uzantılar ve güncelleştirmeler** ' i seçin ve **Developer Analytics Tools** yüklendiğini ve etkinleştirildiğini denetleyin. Bu durumda, kullanılabilir bir güncelleştirme olup olmadığını görmek için **güncelleştirmeler** ' e tıklayın.
* Yeni proje iletişim kutusunu açın ve ASP.NET Web uygulaması ' nı seçin. Burada Application Insights seçeneği görürseniz araçlar yüklenir. Aksi takdirde, Developer Analytics Tools kaldırıp yeniden yüklemeyi deneyin.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Application Insights eklenemedi
*Mevcut bir projeye Application Insights eklemeye çalıştığımda bir hata iletisi görüyorum.*

Olası nedenler:

* Application Insights portalı ile iletişim kurulamadı; veya
* Azure hesabınızla ilgili bazı sorunlar var;
* Yalnızca [yeni kaynağı oluşturmaya çalıştığınız abonelik veya gruba okuma erişiminizin](./resources-roles-access-control.md)olması gerekir.

Onar

* Doğru Azure hesabı için oturum açma kimlik bilgileri sağladıysanız emin olun.
* Tarayıcınızda [Azure Portal](https://portal.azure.com)erişiminiz olup olmadığını kontrol edin. Ayarları açın ve herhangi bir kısıtlama olup olmadığını görün.
* [Mevcut projenize Application Insights ekleyin](./asp-net.md): Çözüm Gezgini, projenize sağ tıklayıp "Application Insights Ekle" seçeneğini belirleyin.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>"Izleme anahtarı boş olamaz" hatası alıyorum
Application Insights yüklerken bir sorun oluştu veya belki de günlüğe kaydetme bağdaştırıcısı.

Çözüm Gezgini, projenize sağ tıklayın ve **Application Insights yapılandır > Application Insights**' i seçin. Azure 'da oturum açmanız ve bir Application Insights kaynağı oluşturmanız ya da var olan bir kaynağın yeniden kullanılması için sizi davet eden bir iletişim kutusu alacaksınız.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> "Derleme sunucum üzerinde" NuGet paketleri eksik "
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
* Çözüm Gezgini ' de projenize sağ tıklayın. **> Application Insights yapılandırmak Application Insights** komutu görürseniz, projenizi Application Insights hizmetindeki kaynağa bağlamak için kullanın.

Aksi halde, proje türü geliştirici analizi araçları tarafından doğrudan desteklenmez. Telemetriyi görmek için [Azure Portal](https://portal.azure.com)oturum açın, sol gezinti çubuğunda Application Insights ' i seçin ve uygulamanızı seçin.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>Visual Studio 'dan açma Application Insights ' erişim engellendi '
*' Aç Application Insights ' menü komutu beni Azure portal alıyor, ancak ' erişim engellendi ' hatası alıyorum.*

Varsayılan tarayıcınızda en son kullandığınız Microsoft oturum açma [işlemi, Application Insights bu uygulamaya eklendiğinde oluşturulan kaynağa](./asp-net.md)erişemez. Olası iki neden vardır:

* Birden fazla Microsoft hesabı (Belki de bir iş ve kişisel Microsoft hesabı var mı? Varsayılan tarayıcınızda en son kullandığınız oturum açma işlemi, [projeye Application Insights ekleme](./asp-net.md)erişimine sahip olandan farklı bir hesaptır.
  * Çözüm: tarayıcı penceresinin sağ üst köşesindeki adınızı tıklayın ve oturumu kapatın. Sonra erişimi olan hesapla oturum açın. Ardından sol gezinti çubuğunda Application Insights ' ye tıklayın ve uygulamanızı seçin.
* Başka birisi projeye Application Insights ekledi ve size oluşturulduğu [kaynak grubuna erişim](./resources-roles-access-control.md) izni vermenizi unuttu.
  * Çözüm: bir kurumsal hesap kullandıklarında, takıma ekleyebilirler; ya da kaynak grubuna bireysel erişim izni verebilir.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>Visual Studio 'dan açma Application Insights ' varlık bulunamadı '
*' Aç Application Insights ' menü komutu beni Azure portal götürür, ancak ' varlık bulunamadı ' hatası alıyorum.*

Olası nedenler:

* Uygulamanız için Application Insights kaynağı silindi; veya
* İzleme anahtarı, proje dosyasını güncelleştirmeden doğrudan düzenlenerek ApplicationInsights.config ayarlanır veya değiştirilmiştir.

ApplicationInsights.config 'daki izleme anahtarı, telemetrinin gönderildiği yeri denetler. Proje dosyasındaki bir satır, Visual Studio 'da komutunu kullandığınızda hangi kaynağın açılacağını denetler.

Onar

* Çözüm Gezgini, projeye sağ tıklayın ve Application Insights, Application Insights Yapılandır ' ı seçin. İletişim kutusunda, mevcut bir kaynağa telemetri gönderilmesini ya da yeni bir tane oluşturmayı seçebilirsiniz. Veya
* Kaynağı doğrudan açın. [Azure Portal](https://portal.azure.com)oturum açın, sol gezinti çubuğunda Application Insights ' e tıklayın ve ardından uygulamanızı seçin.

## <a name="where-do-i-find-my-telemetry"></a>Telemetriyi nerede bulabilirim?
*[Microsoft Azure Portal](https://portal.azure.com)oturum açdım ve Azure giriş panosuna bakıyordum. Application Insights verilerimi nerede bulabilirim?*

* Sol gezinti çubuğunda Application Insights ' a ve ardından uygulamanızın adı ' na tıklayın. Orada hiç projeniz yoksa, [Web projenize Application Insights eklemeniz veya yapılandırmanız](./asp-net.md)gerekir.  
  Bazı Özet grafikler görürsünüz. Daha fazla ayrıntı görmek için bunlara tıklayabilirsiniz.
* Visual Studio 'da uygulamanızda hata ayıklaması yaparken Application Insights düğmesine tıklayın.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Sunucu verisi yok (veya hiç veri yok)
*Uygulamamı çalıştırdım ve Microsoft Azure Application Insights hizmeti 'ni açtık, ancak tüm grafiklerde ' nasıl toplayacağınızı öğrenin... ' gösterilmektedir veya ' yapılandırılmadı. '* Ya da *yalnızca sayfa görünümü ve Kullanıcı verileri, ancak sunucu verisi yok.*

* Uygulamanızı Visual Studio 'da hata ayıklama modunda çalıştırın (F5). Uygulamayı, bazı telemetri oluşturmak için kullanın. Visual Studio çıktı penceresinde günlüğe kaydedilen olayları görebilmeniz için denetleyin.  
  ![Visual Studio 'da uygulamanızı hata ayıklama modunda çalıştırmayı gösteren ekran görüntüsü.](./media/asp-net-troubleshoot-no-data/output-window.png)
* Application Insights portalında [Tanılama araması](./diagnostic-search.md)' nı açın. Veriler genellikle önce burada görünür.
* Yenile düğmesine tıklayın. Dikey pencere kendi kendine düzenli olarak yenilenir, ancak bunu el ile de yapabilirsiniz. Yenileme aralığı daha büyük zaman aralıkları için daha uzun.
* İzleme anahtarlarının eşleştiğinden emin olun. Application Insights portalındaki uygulamanızın ana dikey penceresinde, **Essentials** açılan penceresinde, **izleme anahtarı**' na bakın. Ardından, Visual Studio 'daki projenizdeki ApplicationInsights.config açın ve bulun `<instrumentationkey>` . İki anahtarın eşit olup olmadığını denetleyin. Aksi takdirde:  
  * Portalda Application Insights ' a tıklayın ve doğru anahtarla uygulama kaynağını arayın; veya
  * Visual Studio Çözüm Gezgini, projeye sağ tıklayın ve Application Insights, Yapılandır ' ı seçin. Doğru kaynağa telemetri göndermek için uygulamayı sıfırlayın.
  * Eşleşen anahtarları bulamıyorsanız, portalda ' de olduğu gibi Visual Studio 'da aynı oturum açma kimlik bilgilerini kullanıp kullanınızdan emin olun.
* [Microsoft Azure giriş panosunda](https://portal.azure.com), hizmet durumu haritasına bakın. Bazı uyarı göstergeleri varsa, Tamam ' a dönene kadar bekleyin ve ardından Application Insights uygulaması dikey penceresini kapatıp yeniden açın.
* Ayrıca [durum blogumuzu](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog)inceleyin.
* [Sunucu tarafı SDK](./api-custom-events-metrics.md) için, örneklerdeki veya içindeki izleme anahtarını değiştirebilen herhangi bir kod yazdınız `TelemetryClient` `TelemetryContext` mı? Ya da çok fazla filtreleme olabilecek bir [filtre veya örnekleme yapılandırması](./api-filtering-sampling.md) mı yazdınız?
* ApplicationInsights.config düzenlediyseniz, [TelemetryInitializers ve TelemetryProcessors](./api-filtering-sampling.md)yapılandırmasını dikkatle kontrol edin. Yanlış adlı bir tür veya parametre SDK 'nın veri gönderememesine neden olabilir.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Sayfa görünümlerinde, tarayıcılarda, kullanımında veri yok
*Sunucu yanıt süresi ve sunucu Isteği grafiklerde veri görüyorum, ancak sayfa görünümü yükleme sırasında veya tarayıcıda veya kullanım dikey penceresinde veri yok.*

Veriler Web sayfalarındaki betiklerden gelir. 

* Mevcut bir web projesine Application Insights eklediyseniz, [betikleri el ile eklemeniz gerekir](./javascript.md).
* Internet Explorer 'ın sitenizin uyumluluk modunda görüntülenmediğinden emin olun.
* Verilerin gönderildiğini doğrulamak için tarayıcının hata ayıklama özelliğini (bazı tarayıcılarda F12 ' i seçin, sonra da ağ ' ı seçin) kullanın `dc.services.visualstudio.com` .

## <a name="no-dependency-or-exception-data"></a>Bağımlılık veya özel durum verisi yok
Bkz. [bağımlılık telemetrisi](./asp-net-dependencies.md) ve [özel durum telemetrisi](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Performans verisi yok
Durum İzleyicisi 'ni ve [Azure Cloud Services](./app-insights-overview.md)'yi yüklüyorsanız, [Java Web Hizmetleri](./java-collectd.md), [Windows Masaüstü uygulamaları](./windows-desktop.md), [IIS Web UYGULAMALARı ve HIZMETLERI](./monitor-performance-live-website-now.md)için performans verileri (CPU, GÇ oranı vb.) kullanılabilir. Bunu ayarlar, sunucular bölümünde bulabilirsiniz.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Uygulamayı sunucuma yayımladığım için hiçbir (sunucu) veri yok
* Tüm Microsoft 'un gerçekten kopyalamadığını denetleyin. Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll ile birlikte sunucuda ApplicationInsights dll 'Leri
* Güvenlik duvarınızdaki [bazı TCP bağlantı noktalarını açmanız](./ip-addresses.md)gerekebilir.
* Şirket ağınızı göndermek için bir proxy kullanmanız gerekiyorsa, Web.config [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) 'yi ayarlayın
* Windows Server 2008: şu güncelleştirmeleri yüklediğinizden emin olun: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://web.archive.org/web/20150129090641/http://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Verileri görmek için kullandım, ancak durdu
* Aylık veri noktaları kotasına ulaştınız misiniz? Bulmak için ayarları/kotayı ve fiyatlandırmayı açın. Bu durumda, planınızı yükseltebilir veya ek kapasite için ödeme yapabilirsiniz. [Fiyatlandırma şemasına](https://azure.microsoft.com/pricing/details/application-insights/)bakın.

## <a name="i-dont-see-all-the-data-im-expecting"></a>Beklediğiniz tüm verileri göremiyorum
Uygulamanız çok miktarda veri gönderirse ve ASP.NET Version 2.0.0-Beta3 veya üzeri için Application Insights SDK kullanıyorsanız, [Uyarlamalı örnekleme](./sampling.md) özelliği yalnızca telemetrinizin yüzdesini işleyebilir ve gönderebilir.

Devre dışı bırakabilirsiniz, ancak bu önerilmez. Örnekleme, tanılama amacıyla ilgili telemetri doğru şekilde iletilebilecek şekilde tasarlanmıştır.

## <a name="client-ip-address-is-0000"></a>İstemci IP adresi 0.0.0.0

5 2018 Şubat 'de Istemci IP adresinin günlüğe kaydedilmesini kaldırdığımız duyurulmuştur. Bu, coğrafi konumu etkilemez.

> [!NOTE]
> IP adresinin ilk 3 sekizlisinin olması gerekiyorsa özel bir öznitelik eklemek için bir [telemetri başlatıcısı](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) kullanabilirsiniz.
> Bu, 5 Şubat 2018’den önce toplanan verileri etkilemez.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Kullanıcı telemetrisinde yanlış coğrafi veriler
Şehir, bölge ve ülke boyutları IP adreslerinden türetilir ve her zaman doğru değildir. Bu IP adresleri önce konum için işlenir ve ardından depolanacak 0.0.0.0 olarak değiştirilir.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure Cloud Services’da çalıştırma üzerine “yöntem bulunamadı” özel durumu
.NET 4.6 için mi oluşturdunuz? 4.6 sürümü Azure Cloud Services rollerinde otomatik olarak desteklenmez. Uygulamanızı çalıştırmadan önce [her role 4.6 sürümünü yükleyin](../../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="troubleshooting-logs"></a>Sorun giderme günlükleri

Çerçeveiniz için sorun giderme günlüklerini yakalamak için bu yönergeleri izleyin.

### <a name="net-framework"></a>.NET Framework

1. NuGet 'den [Microsoft. Aspnet. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) paketini yükler. Yüklediğiniz sürüm, geçerli yüklü sürümü ile eşleşmelidir `Microsoft.ApplicationInsighs`

2. applicationinsights.config dosyanızı aşağıdakileri içerecek şekilde değiştirin:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.FileDiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
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

1. NuGet 'den [ASP.NET Core paketi için APPLICATION INSIGHTS SDK NuGet paketini](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) yükler. Yüklediğiniz sürüm, yüklü olan geçerli sürümüyle eşleşmelidir `Microsoft.ApplicationInsights` .

   Microsoft. ApplicationInsights. AspNetCore 'un en son sürümü 2.14.0 'dir ve Microsoft. ApplicationInsights sürüm 2.14.0 'e başvurur. Bu nedenle, yüklenecek Microsoft. ApplicationInsights. AspNetCore sürümü 2.14.0 olmalıdır.

2. `ConfigureServices`Sınıfında yöntemini değiştirin `Startup.cs` .:

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


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> PerfView ile günlükleri toplama
[PerfView](https://github.com/Microsoft/perfview) , birçok kaynaktan tanılama bilgilerini toplayıp GÖRSELLEŞTIREREK CPU, bellek ve diğer sorunları yalıtmaya yardımcı olan ücretsiz bir tanılama ve performans analizi aracıdır.

Application Insights SDK günlüğü, PerfView tarafından yakalanabilen EventSource kendi kendine sorun giderme günlüklerini günlüğe kaydeder.

Günlükleri toplamak için PerfView indirin ve şu komutu çalıştırın:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Gerektiğinde bu parametreleri değiştirebilirsiniz:
- **Maxcollectsec**. PerfView 'ın süresiz olarak çalışmasını ve sunucunuzun performansını etkilemesini engellemek için bu parametreyi ayarlayın.
- **Yalnızca sağlayıcılar**. Yalnızca SDK 'dan günlükleri toplamak için bu parametreyi ayarlayın. Bu listeyi, özel araştırmalarınız temelinde özelleştirebilirsiniz. 
- **Noguı**. Bu parametreyi, GUI olmadan günlükleri toplamak için ayarlayın.


Daha fazla bilgi için,
- [PerfView ile performans Izlemeleri kaydediliyor](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Olay kaynaklarını Application Insights](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>DotNet-Trace ile günlükleri toplama

Alternatif olarak, müşteriler, [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) sorun gidermeye daha fazla yardımcı olabilecek Günlükler toplamak için platformlar arası bir .NET Core aracı da kullanabilirler. Bu, özellikle Linux tabanlı ortamlar için yararlı olabilir.

Yüklemesinden sonra [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) , Bash içinde aşağıdaki komutu yürütün.

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Application Insights kaldırma

Kaldırma [makalesinde](./remove-application-insights.md)sağladığı adımları Izleyerek Visual Studio 'da Application Insights kaldırmayı öğrenin.

## <a name="still-not-working"></a>Hala çalışmıyor...
* [Microsoft Q&Application Insights için soru sayfası](/answers/topics/azure-monitor.html)

---
title: Azure Application Insights ile canlı bir ASP.NET web uygulamasını izleme | Microsoft Docs
description: Yeniden dağıtmadan web sitesinin performansını izleme. Şirket içinde veya VM 'lerde barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/26/2019
ms.openlocfilehash: ac238ae5715e09b2e64737801a862d89852ec9d9
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820758"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Application Insights codeless Attach ile çalışma zamanında Web uygulamalarını işaretleme

> [!IMPORTANT]
> Durum İzleyicisi artık kullanım için önerilmez. Azure Izleyici Application Insights aracısıyla değiştirilmiştir (eski adıyla Durum İzleyicisi v2). Şirket [içi sunucu dağıtımları](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) veya [Azure sanal makine ve sanal makine ölçek kümesi dağıtımları](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)için belgelerimizi inceleyin.

Kodunuzu değiştirmeye veya yeniden dağıtmaya gerek olmadan canlı bir web uygulamasını Azure Application Insights ile izleyebilirsiniz. [Microsoft Azure](https://azure.com) aboneliğiniz olması gerekir.

Durum İzleyicisi, IIS 'de barındırılan bir .NET uygulamasını şirket içinde ya da bir VM 'de işaretlemek için kullanılır.

- Uygulamanız Azure VM 'ye veya Azure sanal makine ölçek kümesine dağıtılmışsa, [Bu yönergeleri](azure-vm-vmss-apps.md)izleyin.
- Uygulamanız Azure Uygulama Hizmetleri 'ne dağıtılmışsa, [Bu yönergeleri](azure-web-apps.md)izleyin.
- Uygulamanız bir Azure VM 'de dağıtılmışsa, Azure denetim masasından Application Insights izlemeye geçebilirsiniz.
- ( [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)'yi işaretleme hakkında ayrı makaleler de vardır.)


![Başarısız istekler, sunucu yanıt süresi ve sunucu istekleri hakkında bilgi içeren uygulama öngörülerine genel bakış grafiklerinin ekran görüntüsü](./media/monitor-performance-live-website-now/overview-graphs.png)

.NET Web uygulamalarınıza Application Insights uygulamak için iki yol seçeneği vardır:

* **Derleme zamanı:** [Application Insights SDK 'sını][greenbrown] Web uygulaması kodunuza ekleyin.
* **Çalışma zamanı:** Kodu yeniden derlemeden ve yeniden dağıtmadan web uygulamanızı sunucu üzerinde izleyin.

> [!NOTE]
> Derleme zamanı izleme kullanıyorsanız, çalışma zamanı izleme açık olsa bile çalışmayacaktır.

Burada, her yöntemle kazanacaklarınızın bir özeti verilmiştir:

|  | Derleme zamanı | Çalışma zamanı |
| --- | --- | --- |
| İstekler ve özel durumlar |Yes |Yes |
| [Daha ayrıntılı özel durumlar](../../azure-monitor/app/asp-net-exceptions.md) | |Yes |
| [Bağımlılık tanılaması](../../azure-monitor/app/asp-net-dependencies.md) |.NET 4.6+ üzerinde ancak daha az ayrıntılı |Evet, tam ayrıntılı: sonuç kodları, SQL komut metni, HTTP fiili|
| [Sistem performans sayaçları](../../azure-monitor/app/performance-counters.md) |Yes |Yes |
| [Özel telemetri için API][api] |Yes |Hayır |
| [İzleme günlüğü tümleştirmesi](../../azure-monitor/app/asp-net-trace-logs.md) |Yes |Hayır |
| [Sayfa görünümü ve kullanıcı verileri](../../azure-monitor/app/javascript.md) |Yes |Hayır |
| Kodu yeniden derlemeniz gerekir |Yes | Hayır |



## <a name="monitor-a-live-iis-web-app"></a>Canlı bir IIS web uygulamasını izleme

Uygulamanız bir IIS sunucusunda barındırılıyorsa, Durum İzleyici’yi kullanarak Application Insights’ı etkinleştirin.

1. IIS web sunucunuzda yönetici kimlik bilgileriyle oturum açın.
2. Application Insights Durum İzleyicisi henüz yüklü değilse, [yükleyiciyi indirin ve çalıştırın](#download)
3. Durum İzleyicisi'nde yüklü web uygulamasını veya izlemek istediğiniz web sitesini seçin. Azure kimlik bilgilerinizle oturum açın.

    Sonuçlarını Application Insights portalında görmek istediğiniz kaynağı yapılandırın. (Normalde, yeni bir kaynak oluşturmak en iyi yöntemdir. Bu uygulama için [Web testleriniz][availability] veya [istemci izleme][client] zaten varsa var olan bir kaynağı seçin.) 

    ![Uygulama ve kaynak seçin.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. IIS’yi yeniden başlatın.

    ![iletişim kutusunun üstündeki Yeniden Başlat’ı seçin.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Web hizmetiniz kısa bir süre kesintiye uğrar.

## <a name="customize-monitoring-options"></a>İzlemeyi özelleştirme seçenekleri

Application Insights’ın etkinleştirilmesi, web uygulamanıza DLL ve ApplicationInsights.config dosyasını ekler. Bazı seçenekleri değiştirmek için [.config dosyasını düzenleyebilirsiniz](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Uygulamanızı yeniden yayımladığınızda, Application Insights’ı yeniden etkinleştirin

Uygulamanızı yeniden yayımlamadan önce, [Visual Studio 'da koda Application Insights eklemeyi][greenbrown]göz önünde bulundurun. Bu şekilde daha ayrıntılı telemetri alabilir ve özel telemetri yazabilirsiniz.

Koda Application Insights eklemeden yeniden yayımlamak istiyorsanız, dağıtım işleminin yayımlanmış web sitesinden DLL ve ApplicationInsights.config dosyasını silebileceğini unutmayın. Bu nedenle:

1. ApplicationInsights.config dosyasını düzenlediyseniz, uygulamanızı yeniden yayımlamadan önce bir kopyasını alın.
2. Uygulamanızı yeniden yayımlayın.
3. Application Insights izlemeyi yeniden etkinleştirin. (Uygun yöntemi kullanın: Azure web uygulaması denetim masası veya bir IIS konağındaki Durum İzleyicisi.)
4. .config dosyası üzerinde yaptığınız tüm düzenlemeleri yeniden devreye sokun.


## <a name="troubleshoot"></a>Sorun giderme

### <a name="confirm-a-valid-installation"></a>Geçerli bir yüklemeyi Onayla 

Bunlar, yüklemenizin başarılı olduğunu onaylamak için gerçekleştirebileceğiniz bazı adımlardır.

- ApplicationInsights. config dosyasının hedef uygulama dizininde bulunduğunu ve Ikey 'nizi içerdiğini doğrulayın.

- Verilerin eksik olduğunu kuşkulanıyorsanız, şu anda telemetri gönderen tüm bulut rollerini listelemek için [Analytics](../log-query/get-started-portal.md) 'te basit bir sorgu çalıştırabilirsiniz.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Application Insights başarıyla eklendiğinden emin olmanız gerekiyorsa, ApplicationInsights. dll ' nin IIS tarafından yüklendiğini onaylamak için, bir komut penceresinde [Sysinternals tanıtıcısı](https://docs.microsoft.com/sysinternals/downloads/handle) 'nı çalıştırabilirsiniz.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Bağlanamıyor musunuz? Telemetri yok mu?

* Durum İzleyicisi’nin çalışmasına izin vermek için sunucunuzun güvenlik duvarında [gerekli giden bağlantı noktalarını](../../azure-monitor/app/ip-addresses.md#outgoing-ports) açın.

### <a name="unable-to-login"></a>Oturum açılamıyor

* Durum İzleyicisi oturum açamıyor, bunun yerine bir komut satırı yüklemesi yapın. Durum İzleyicisi, Ikey dosyanızı toplamak için oturum açmaya çalışır, ancak komutunu kullanarak bunu el ile sağlayabilirsiniz:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>' System. Diagnostics. DiagnosticSource ' dosyası veya derlemesi yüklenemedi

Application Insights etkinleştirdikten sonra bu hatayı alabilirsiniz. Bunun nedeni, yükleyicinin bin dizininizde bu DLL 'nin yerini almıştır.
Web. config dosyanızı güncelleştirmeyi onarmak için:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Bu sorunu [burada](https://github.com/Microsoft/ApplicationInsights-Home/issues/301)izliyoruz.


### <a name="application-diagnostic-messages"></a>Uygulama tanılama iletileri

* Durum İzleyicisi’ni açın ve sol bölmede uygulamanızı seçin. "Yapılandırma bildirimleri" bölümünde bu uygulamayla ilgili herhangi bir tanılama iletisi olup olmadığını denetleyin:

  ![İstek, yanıt süresi, bağımlılık ve diğer verileri görmek için Performans dikey penceresini açın](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Ayrıntılı günlükler

* Varsayılan olarak Durum İzleyicisi şu adreste tanılama günlüklerini çıktısını alacak: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Ayrıntılı günlükleri çıkarmak için yapılandırma dosyasını değiştirin: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` ve `appsettings` ' ye `<add key="TraceLevel" value="All" />` ekleyin.
Ardından Durum izleyicisini yeniden başlatın.

* Durum İzleyicisi bir .NET uygulaması olduğu gibi, [yapılandırma dosyasına uygun tanılamayı ekleyerek de .net izlemeyi](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element)etkinleştirebilirsiniz. Örneğin, bazı senaryolarda [ağ izlemeyi yapılandırarak](https://docs.microsoft.com/dotnet/framework/network-programming/how-to-configure-network-tracing) ağ düzeyinde neler olduğunu görmek yararlı olabilir

### <a name="insufficient-permissions"></a>Yetersiz izinler
  
* "Yetersiz izinler" hakkında bir ileti görürseniz, sunucuda aşağıdakileri deneyin:
  * IIS Yöneticisi'nde, uygulama havuzunuzu seçin, **Gelişmiş Ayarlar**’ı açın ve **işlem modeli** altında kimliğini not edin.
  * Bilgisayar yönetimi denetim masasında, bu kimliği Performans İzleyicisi Kullanıcıları grubuna ekleyin.

### <a name="conflict-with-systems-center-operations-manager"></a>Systems Center ile çakışma Operations Manager

* Sunucunuza yüklenmiş MMA/SCOM (System Center Operations Manager) varsa bazı sürümler çakışabilir. Hem SCOM’u, hem de Durum İzleyicisi’ni kaldırın ve en son sürümleri yeniden yükleyin.

### <a name="failed-or-incomplete-installation"></a>Başarısız veya eksik yükleme

Yükleme sırasında Durum İzleyicisi başarısız olursa, Durum İzleyicisi kurtaramadığında, tamamlanmamış bir yükleme ile ayrıldınız. Bu, el ile sıfırlama gerektirir.

Uygulama dizininizde bulunan bu dosyalardan herhangi birini silin:
- "Microsoft.AI" ile başlayan bin dizininizdeki tüm dll 'Ler. ya da "Microsoft. ApplicationInsights.".
- "Microsoft. Web. Infrastructure. dll" bin dizininizde bu DLL
- "System. Diagnostics. DiagnosticSource. dll" bin dizininizde bu DLL
- Uygulama dizininizde "App_Data\packages" öğesini kaldırın
- Uygulama dizininizde "ApplicationInsights. config" öğesini kaldırın


### <a name="additional-troubleshooting"></a>Ek Sorun Giderme Seçenekleri

* Ek [sorun giderme][qna]bölümüne bakın.

## <a name="system-requirements"></a>Sistem Gereksinimleri
Sunucuda Application Insights Durum İzleyicisi için işletim sistemi desteği:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

en son SP ve .NET Framework 4,5 (Durum İzleyicisi Framework 'ün bu sürümünde oluşturulmuştur)

İstemci tarafında Windows 7, 8, 8.1 ve 10, .NET Framework 4.5 ile

IIS desteği: IIS 7, 7.5, 8, 8.5 (IIS gereklidir)

## <a name="automation-with-powershell"></a>PowerShell ile Automation
IIS sunucunuzda PowerShell'i kullanarak izlemeyi başlatabilir ve durdurabilirsiniz.

İlk olarak Application Insights modülünü içeri aktarın:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Hangi uygulamaların izlenmekte olduğunu öğrenin:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (İsteğe bağlı) Bir web uygulaması adı.
* Bu IIS sunucusundaki her web uygulaması için (veya adlandırılmış uygulama) Application Insights izleme durumunu görüntüler.
* Her uygulama için `ApplicationInsightsApplication` döndürür:

  * `SdkState==EnabledAfterDeployment`: Uygulama izleniyor ve çalışma zamanında, Durum İzleyicisi aracıyla ya da `Start-ApplicationInsightsMonitoring` ile izleme eklenmiş.
  * `SdkState==Disabled`: Uygulama, Application Insights için izleme eklenmemiş. Hiçbir zaman izlenmeyecek veya Durum İzleyicisi aracıyla veya `Stop-ApplicationInsightsMonitoring` ile çalışma zamanı izlemesi devre dışı bırakılmayacaktır.
  * `SdkState==EnabledByCodeInstrumentation`: Uygulama, kaynak koduna SDK eklenerek izleme eklenmiş. SDK’si güncelleştirilemez veya durdurulamaz.
  * `SdkVersion`, bu uygulamanın izlenmesi için kullanılan sürümü gösterir.
  * `LatestAvailableSdkVersion`, şu anda NuGet galerisinde mevcut olan sürümü gösterir. Uygulama bu sürüme yükseltmek için `Update-ApplicationInsightsMonitoring` kullanın.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Uygulamanın IIS'deki adı
* `-InstrumentationKey` Sonuçların görüntülenmesini istediğiniz Application Insights kaynağına ait iKey.
* Bu cmdlet yalnızca henüz izlenmemiş uygulamaları etkiler; başka bir deyişle zaten işaretlendiğine değil - diğer bir deyişle, SdkState==NotInstrumented.

    Cmdlet, zaten izleme eklenmiş olan uygulamayı etkilemez. Uygulamanın koda SDK eklenerek derleme zamanında izlenmesi veya cmdlet’in önceki bir kullanımıyla çalışma zamanında izlenmesi farklılık yaratmaz.

    Uygulamayı izlemek için kullanılan SDK sürümü sunucuya en son indirilen sürümdür.

    En son sürümü indirmek için Update-ApplicationInsightsVersion kullanın.
* Başarılı bir şekilde `ApplicationInsightsApplication` döndürür. Başarısız olursa, stderr’e bir izleme kaydeder.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Bir uygulamanın IIS'deki adı
* `-All` Bu IIS sunucusunda, şu koşulu sağlayan tüm uygulamaları izlemeyi durdurur: `SdkState==EnabledAfterDeployment`
* Belirtilen uygulamaları izlemeyi durdurur ve izlemeyi kaldırır. Yalnızca, Durum İzleme aracını veya Start-ApplicationInsightsApplication kullanarak çalışma zamanında izleme eklenmiş uygulamalar için çalışır. (`SdkState==EnabledAfterDeployment`)
* ApplicationInsightsApplication döndürür.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Bir web uygulamasının IIS'deki adı.
* `-InstrumentationKey` (Isteğe bağlı) Uygulama telemetrinin gönderildiği kaynağı değiştirmek için bunu kullanın.
* Bu cmdlet:
  * Adlandırılmış uygulamaları SDK'nin bu makineye en son indirilen sürümüne yükseltir. (Yalnızca `SdkState==EnabledAfterDeployment` ise çalışır)
  * İzleme anahtarını sağlarsanız, adlandırılmış uygulama, telemetriyi bu anahtarla kaynağa göndermek için yeniden yapılandırılır. (`SdkState != Disabled` ise çalışır)

`Update-ApplicationInsightsVersion`

* En son Application Insights SDK’sini sunucuya indirir.

## <a name="questions"></a>Durum İzleyicisi hakkında sorular

### <a name="what-is-status-monitor"></a>Durum İzleyicisi nedir?

IIS web sunucunuza yüklediğiniz bir masaüstü uygulamasıdır. Web uygulamalarını izleyip yapılandırmanıza yardımcı olur. 

### <a name="when-do-i-use-status-monitor"></a>Durum İzleyicisi’ni ne zaman kullanırım?

* Halihazırda çalışıyor olsa bile, IIS sunucunuzda çalışan herhangi bir web uygulamasını izleme.
* Derleme zamanında [Application Insights SDK'sı ile derlenmiş](../../azure-monitor/app/asp-net.md) web uygulamaları için ek telemetriyi etkinleştirme. 

### <a name="can-i-close-it-after-it-runs"></a>Çalıştıktan sonra kapatabilir miyim?

Evet. Seçtiğiniz web sitelerini izledikten sonra kapatabilirsiniz.

Telemetriyi tek başına toplamaz. Yalnızca web uygulamalarını yapılandırır ve bazı izinleri ayarlar.

### <a name="what-does-status-monitor-do"></a>Durum İzleyicisi ne yapar?

Durum İzleyicisi’nin izlemesi için bir web uygulaması seçtiğinizde:

* Application Insights derlemelerini ve ApplicationInsights. config dosyasını, Web uygulamasının ikili dosyaları klasörüne indirir ve koyar.
* Bağımlılık çağrılarını toplamak için CLR profil oluşturma özelliğini etkinleştirir.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Application Insights SDK 'nın hangi sürümü Durum İzleyicisi yüklenir?

Şimdilik Durum İzleyicisi, yalnızca 2,3 veya 2,4 Application Insights SDK sürümlerini yükleyebilir. 

Application Insights SDK sürümü 2,4, [.net 4,0 ' ü desteklemeye yönelik son sürümdür](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) ve bu, [2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Bu nedenle, şimdi Durum İzleyicisi .NET 4,0 uygulamasını işaretlemek için kullanılabilir. 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Uygulamayı her güncelleştirdiğimde Durum İzleyicisi’ni çalıştırmam gerekiyor mu?

Artımlı olarak dağıtmanız durumunda gerekli değildir. 

Yayımlama işleminde 'mevcut dosyaları sil' seçeneğini belirlerseniz, Application Insights’ı yapılandırmak için Durum İzleyicisi’ni yeniden çalıştırmanız gerekir.

### <a name="what-telemetry-is-collected"></a>Hangi telemetri toplanır?

Durum İzleyicisi'ni kullanarak yalnızca çalışma zamanında izlediğiniz uygulamalar için:

* HTTP istekleri
* Bağımlılık çağrıları
* Özel Durumlar
* Performans sayaçları

Derleme zamanında zaten izlenmekte olan uygulamalar için:

 * İşlem sayaçları.
 * Bağımlılık çağrıları (.NET 4.5); bağımlılık çağrılarındaki dönüş değerleri (.NET 4.6).
 * Özel durum yığın izleme değerleri.

[Daha fazla bilgi](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download"></a>Durum İzleyicisi indir

- Yeni [PowerShell modülünü](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) kullanın
- [Durum İzleyicisi yükleyicisini](https://go.microsoft.com/fwlink/?LinkId=506648) indir ve Çalıştır
- Veya [Web Platformu Yükleyicisi](https://www.microsoft.com/web/downloads/platform.aspx) 'ni çalıştırın ve Application Insights durum İzleyicisi için arama yapın.

## <a name="next"></a>Sonraki adımlar

Telemetrinizi görüntüleyin:

* Performans ve kullanımı izlemek için [ölçümleri keşfedin](../../azure-monitor/app/metrics-explorer.md)
* Sorunları tanılamak için [olayları ve günlükleri arayın][diagnostic]
* Daha gelişmiş sorgular için [analiz](../../azure-monitor/app/analytics.md)

Daha fazla telemetri ekleyin:

* Sitenizin canlı kaldığından emin olmak için [Web testleri oluşturun][availability] .
* Web sayfası kodundan özel durumları görmek ve izleme çağrıları eklemenize izin vermek için [Web istemcisi telemetrisini ekleyin][usage] .
* İzleme ve günlük çağrıları ekleyebilmek için [kodunuza APPLICATION INSIGHTS SDK ekleyin][greenbrown]

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md

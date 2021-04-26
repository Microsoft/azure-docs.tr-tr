---
title: Azure Application Insights Snapshot Debugger sorunlarını giderme
description: Bu makalede, geliştiricilerin Application Insights Snapshot Debugger etkinleştirmesine ve kullanmasına yardımcı olacak sorun giderme adımları ve bilgileri sunulmaktadır.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: bd83367ae073e03f03188cdf62cb60faaad7ac97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026462"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Application Insights Snapshot Debugger etkinleştirme veya anlık görüntüleri görüntüleme sorunlarını giderme
Uygulamanız için Application Insights Snapshot Debugger etkinleştirdiyseniz, ancak özel durumlar için anlık görüntüler görmüyorsanız, bu yönergeleri kullanarak sorun giderme yapabilirsiniz.

Anlık görüntülerin üretilmesinin pek çok farklı nedeni olabilir. Olası yaygın nedenlerin bazılarını belirlemek için anlık görüntü sistem durumu denetimini çalıştırarak başlayabilirsiniz.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Uygun Snapshot Debugger uç noktasını kullandığınızdan emin olun

Şu anda yalnızca uç nokta değişiklikleri gerektiren bölgeler [Azure Kamu](../../azure-government/compare-azure-government-global-azure.md#application-insights) ve [Azure Çin](/azure/china/resources-developer-guide)' dir.

Application Insights SDK 'sını kullanan App Service ve uygulamalar için, aşağıda tanımlanan Snapshot Debugger için desteklenen geçersiz kılmaları kullanarak bağlantı dizesini güncelleştirmeniz gerekir:

|Bağlantı dizesi özelliği    | ABD kamu bulutu | Çin bulutu |   
|---------------|---------------------|-------------|
|Anlık görüntü Tendpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Diğer bağlantı geçersiz kılmaları hakkında daha fazla bilgi için bkz. [Application Insights belgeleri](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

İşlev Uygulaması için `host.json` aşağıdaki desteklenen geçersiz kılmaları kullanarak öğesini güncelleştirmeniz gerekir:

|Özellik    | ABD kamu bulutu | Çin bulutu |   
|---------------|---------------------|-------------|
|Tendtendpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Aşağıda `host.json` ABD kamu bulut Aracısı uç noktası ile güncelleştirilmiş bir örneği verilmiştir:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>Anlık görüntü durumu denetimini kullanma
Yaygın olarak karşılaşılan bazı sorunlar açık hata ayıklama anlık görüntüsüne neden görünmüyor. Güncel olmayan Snapshot Collector kullanma, örneğin; günlük karşıya yükleme sınırına ulaşıyor; ya da anlık görüntünün karşıya yüklenmesi uzun zaman almazdır. Sık karşılaşılan sorunları gidermek için anlık görüntü durum denetimini kullanın.

Sizi anlık görüntü sistem durumu denetimine götüren uçtan uca izleme görünümünün özel durum bölmesinde bir bağlantı vardır.

![Anlık görüntü durumu denetimini girin](./media/snapshot-debugger/enter-snapshot-health-check.png)

Etkileşimli, sohbet benzeri arabirim, sık karşılaşılan sorunları ve bunları düzeltmenize yardımcı olan yönergeleri arar.

![Sistem durumu denetimi](./media/snapshot-debugger/healthcheck.png)

Bu sorunu çözmezse, aşağıdaki el ile sorun giderme adımlarına bakın.

## <a name="verify-the-instrumentation-key"></a>İzleme anahtarını doğrulama

Yayımlanmış uygulamanızda doğru izleme anahtarını kullandığınızdan emin olun. Genellikle, izleme anahtarı ApplicationInsights.config dosyasından okunurdur. Değerin portalda gördüğünüz Application Insights kaynağı için izleme anahtarıyla aynı olduğunu doğrulayın.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>TLS/SSL istemci ayarlarını denetle (ASP.NET)

Bir sanal makinede Azure App Service veya IIS 'de barındırılan bir ASP.NET uygulamanız varsa, uygulamanız eksik bir SSL güvenlik protokolü nedeniyle Snapshot Debugger hizmetine bağlanamaz.

[Snapshot Debugger uç noktası TLS sürüm 1,2 gerektirir](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). SSL güvenlik protokolleri kümesi, web.config System. Web bölümündeki httpRuntime targetFramework değeri tarafından etkinleştirilen olağandışı bir bölümdür. HttpRuntime targetFramework 4.5.2 veya düşükse, varsayılan olarak TLS 1,2 dahil değildir.

> [!NOTE]
> HttpRuntime targetFramework değeri, uygulamanızı oluştururken kullanılan hedef çerçeveden bağımsızdır.

Ayarı denetlemek için web.config dosyanızı açın ve System. Web bölümünü bulun. ' `targetFramework` `httpRuntime` In 4,6 veya üzeri olarak ayarlandığından emin olun.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> HttpRuntime targetFramework değerini değiştirmek, uygulamanıza uygulanan çalışma zamanı süslerinizi değiştirir ve diğer, hafif davranış değişikliklerine neden olabilir. Bu değişikliği yaptıktan sonra uygulamanızı iyice test ettiğinizden emin olun. Uyumluluk değişikliklerinin tam listesi için lütfen bkz. https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> TargetFramework 4,7 veya üzeri ise, Windows kullanılabilir protokolleri belirler. Azure App Service, TLS 1,2 kullanılabilir. Ancak, kendi sanal makinenizi kullanıyorsanız, işletim sisteminde TLS 1,2 ' i etkinleştirmeniz gerekebilir.

## <a name="preview-versions-of-net-core"></a>.NET Core 'un önizleme sürümleri
.NET Core 'un önizleme sürümünü kullanıyorsanız veya uygulamanız Application Insights SDK 'ya bir bağımlı derleme aracılığıyla doğrudan veya dolaylı olarak başvuruyorsa, [diğer ortamlar için Snapshot Debugger etkinleştir](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)yönergelerini izleyin.

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>Tanılama Hizmetleri site uzantısının durum sayfasını denetleyin
Snapshot Debugger portalda [Application Insights bölmesi](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) aracılığıyla etkinleştirildiyse, tanılama Hizmetleri site uzantısı tarafından etkinleştirilmiştir.

> [!NOTE]
> Application Insights Snapshot Debugger birlikte yükleme, .NET Core destek ilkesini izler.
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

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>NuGet paketinin en son sürümüne yükseltin
Snapshot Debugger nasıl etkinleştirildiğini temel alarak aşağıdaki seçeneklere bakın:

* Snapshot Debugger [portalda Application Insights bölmesi](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)aracılığıyla etkinleştirildiyse, uygulamanız zaten en son NuGet paketini çalıştırıyor olmalıdır.

* Snapshot Debugger [Microsoft. ApplicationInsights. snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paketini ekleyerek etkinleştirilmişse, Microsoft. ApplicationInsights. snapshotcollector 'ın en son sürümünü kullandığınızdan emin olmak Için Visual Studio 'Nun NuGet paket yöneticisini kullanın.

En son güncelleştirmeler ve hata düzeltmeleri için [sürüm notlarına bakın](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Uploader günlüklerini denetleyin

Bir anlık görüntü oluşturulduktan sonra, diskte bir mini döküm dosyası (. dmp) oluşturulur. Ayrı bir Uploader işlemi, bu mini döküm dosyasını oluşturur ve Snapshot Debugger depolama Application Insights için ilişkili tüm pdb 'leri birlikte karşıya yükler. Mini döküm başarıyla karşıya yüklendikten sonra diskten silinir. Uploader işleminin günlük dosyaları diskte tutulur. App Service ortamında bu günlükleri ' de bulabilirsiniz `D:\Home\LogFiles` . Bu günlük dosyalarını bulmak için App Service kudu yönetim sitesini kullanın.

1. App Service uygulamanızı Azure portal açın.
2. **Gelişmiş Araçlar**' ı seçin veya **kudu**'yi arayın.
3. **Git**' i seçin.
4. **Hata ayıklama konsolu** aşağı açılan liste kutusunda **cmd**' yi seçin.
5. **Günlük dosyaları**' nı seçin.

Veya uzantısıyla başlayan en az bir dosya görmeniz gerekir `Uploader_` `SnapshotUploader_` `.log` . Herhangi bir günlük dosyasını indirmek veya bir tarayıcıda açmak için uygun simgeyi seçin.
Dosya adı, App Service örneğini tanımlayan benzersiz bir sonek içerir. App Service örneğiniz birden fazla makinede barındırılıyorsa, her makine için ayrı günlük dosyaları vardır. Uploader yeni bir mini döküm dosyası algıladığında, günlük dosyasına kaydedilir. İşte başarılı bir anlık görüntü ve karşıya yükleme örneği:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Yukarıdaki örnek, Microsoft. ApplicationInsights. SnapshotCollector NuGet paketinin 1.2.0 sürümüdür. Önceki sürümlerde, Uploader işlemi çağrılır `MinidumpUploader.exe` ve günlük daha az ayrıntılıdır.

Önceki örnekte, izleme anahtarı olur `c12a605e73c44346a984e00000000000` . Bu değer, uygulamanız için izleme anahtarıyla eşleşmelidir.
Mini döküm, KIMLIĞI olan bir anlık görüntü ile ilişkilendirilir `139e411a23934dc0b9ea08a626db16c5` . Application Insights Analytics 'te ilişkili özel durum kaydını bulmak için bu KIMLIĞI daha sonra kullanabilirsiniz.

Yükleyici, 15 dakikada bir daha yeni pdb 'leri tarar. Aşağıda bir örnek verilmiştir:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

App Service _barındırılmayan_ uygulamalar için, Uploader günlükleri mini dökümler ile aynı klasörlerdir: `%TEMP%\Dumps\<ikey>` ( `<ikey>` izleme anahtarınız).

## <a name="troubleshooting-cloud-services"></a>Sorun giderme Cloud Services
Cloud Services, varsayılan geçici klasör, bir mini döküm dosyalarını tutmak için çok küçük olabilir, bu da kayıp anlık görüntülerle önde gelebilir.

Gerekli alan, uygulamanızın toplam çalışma kümesine ve eşzamanlı anlık görüntü sayısına bağlıdır.

32 bitlik bir ASP.NET Web rolü çalışma kümesi, genellikle 200 MB ile 500 MB arasındadır. En az iki eşzamanlı anlık görüntüye izin verin.

Örneğin, uygulamanız 1 GB toplam çalışma kümesi kullanıyorsa, anlık görüntüleri depolamak için en az 2 GB disk alanı olduğundan emin olun.

Bulut hizmeti rolünüzü anlık görüntüler için ayrılmış bir yerel kaynakla yapılandırmak için bu adımları izleyin.

1. Bulut hizmeti tanımı (. csdef) dosyasını düzenleyerek bulut hizmetinize yeni bir yerel kaynak ekleyin. Aşağıdaki örnek, 5 GB boyutunda adlı bir kaynağı tanımlar `SnapshotStore` .
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Yerel kaynağa işaret eden bir ortam değişkeni eklemek için rolünüzün başlangıç kodunu değiştirin `SnapshotStore` . Çalışan rolleri için, kod rolün `OnStart` yöntemine eklenmelidir:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Web rolleri için (ASP.NET), kod Web uygulamanızın `Application_Start` yöntemine eklenmelidir:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Rolün ApplicationInsights.config dosyasını, tarafından kullanılan geçici klasör konumunu geçersiz kılmak üzere güncelleştirin `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Gölge kopya klasörünü geçersiz kılma

Snapshot Collector başladığında, anlık görüntü Uploader işlemini çalıştırmaya uygun diskte bir klasör bulmaya çalışır. Seçilen klasör, gölge kopya klasörü olarak bilinir.

Snapshot Collector, iyi bilinen birkaç Konumu kontrol eder ve anlık görüntü yükleyici ikililerini kopyalama izinlerinin olduğundan emin olur. Aşağıdaki ortam değişkenleri kullanılır:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- KOPYALAR

Uygun bir klasör bulunamazsa, _"uygun gölge kopya klasörü bulunamadı_ " hatasını bildiren bir hata raporlar Snapshot Collector.

Kopyalama başarısız olursa, Snapshot Collector bir hata bildirir `ShadowCopyFailed` .

Uploader başlatılamaz Snapshot Collector bir `UploaderCannotStartFromShadowCopy` hata bildirir. İletinin gövdesi genellikle içerir `System.UnauthorizedAccessException` . Bu hata genellikle uygulama daha düşük izinlere sahip bir hesap altında çalıştığı için oluşur. Hesabın, gölge kopya klasörüne yazma izni var, ancak kodu yürütme izni yok.

Bu hatalar genellikle başlangıç sırasında gerçekleşdiğinden, genellikle `ExceptionDuringConnect` _"Uploader başlatılamadı"_ hatası ortaya çıkar.

Bu hataları geçici olarak çözmek için, yapılandırma seçeneği aracılığıyla gölge kopya klasörünü el ile belirtebilirsiniz `ShadowCopyFolder` . Örneğin, ApplicationInsights.config kullanarak:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ya da bir .NET Core uygulamasıyla üzerinde appsettings.jskullanıyorsanız:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Anlık görüntülerle özel durumları bulmak için Application Insights arama kullanın

Bir anlık görüntü oluşturulduğunda, oluşturan özel durum bir anlık görüntü KIMLIĞIYLE etiketlenir. Özel durum Application Insights olarak bildirildiğinde bu anlık görüntü KIMLIĞI özel bir özellik olarak dahil edilir. Application Insights **arama** kullanarak, tüm kayıtları `ai.snapshot.id` özel özelliğe sahip bulabilirsiniz.

1. Azure portal Application Insights kaynağına gidin.
2. **Ara**' yı seçin.
3. `ai.snapshot.id`Arama metin kutusuna yazın ve ENTER tuşuna basın.

![Portalda anlık görüntü KIMLIĞIYLE telemetri arama](./media/snapshot-debugger/search-snapshot-portal.png)

Bu arama sonuç döndürürse, seçili zaman aralığında Application Insights hiçbir anlık görüntü bildirilmemiştir.

Yükleyici günlüklerinden belirli bir anlık görüntü KIMLIĞINI aramak için, arama kutusuna o KIMLIĞI yazın. Karşıya yüklendiğini bildiğiniz bir anlık görüntü için kayıt bulamazsanız, şu adımları izleyin:

1. İzleme anahtarını doğrulayarak doğru Application Insights kaynağına bakdığınızı bir kez daha kontrol edin.

2. Uploader günlüğünden zaman damgasını kullanarak, aramanın zaman aralığı filtresini bu zaman aralığını kapsayacak şekilde ayarlayın.

Bu anlık görüntü KIMLIĞIYLE ilgili bir özel durum görmüyorsanız, özel durum kaydı Application Insights bildirilmedi. Bu durum, uygulamanız anlık görüntüyü aldıktan sonra, ancak özel durum kaydını rapor ettikten sonra kilitlenirse meydana gelir. Bu durumda, `Diagnose and solve problems` beklenmeyen yeniden başlatmalar veya işlenmemiş özel durumlar olup olmadığını görmek için altındaki App Service günlüklerini denetleyin.

## <a name="edit-network-proxy-or-firewall-rules"></a>Ağ ara sunucusunu veya güvenlik duvarı kurallarını Düzenle

Uygulamanız bir ara sunucu veya güvenlik duvarı üzerinden Internet 'e bağlanırsa, Snapshot Debugger hizmetiyle iletişim kurmak için kuralları güncelleştirmeniz gerekebilir.

Application Insights Snapshot Debugger tarafından kullanılan IP 'Ler Azure Izleyici hizmeti etiketine dahildir. Daha fazla bilgi için bkz. [hizmet etiketleri belgeleri](../../virtual-network/service-tags-overview.md).
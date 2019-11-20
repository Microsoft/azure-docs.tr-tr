---
title: Azure Application Insights Snapshot Debugger sorunlarını giderme | Microsoft Docs
description: Bu makalede, Application Insights Snapshot Debugger etkinleştirme veya kullanmada sorun yaşayan geliştiricilere yardımcı olacak sorun giderme adımları ve bilgiler sunulmaktadır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: mbullwin
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ec70f202a496ec368a483278994c7c5ccb24f40b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899819"
---
# <a id="troubleshooting"></a>Application Insights Snapshot Debugger etkinleştirme veya anlık görüntüleri görüntüleme sorunlarını giderme
Uygulamanız için Application Insights Snapshot Debugger etkinleştirdiyseniz, ancak özel durumlar için anlık görüntüler görmüyorsanız, bu yönergeleri kullanarak sorun giderme yapabilirsiniz. Anlık görüntülerin üretilmesinin pek çok farklı nedeni olabilir. Olası yaygın nedenlerin bazılarını belirlemek için anlık görüntü sistem durumu denetimini çalıştırabilirsiniz.

## <a name="use-the-snapshot-health-check"></a>Anlık görüntü durumu denetimini kullanma
Yaygın olarak karşılaşılan bazı sorunlar açık hata ayıklama anlık görüntüsüne neden görünmüyor. Güncel olmayan Snapshot Collector kullanma, örneğin; günlük karşıya yükleme sınırına ulaşıyor; ya da anlık görüntünün karşıya yüklenmesi uzun zaman almazdır. Sık karşılaşılan sorunları gidermek için anlık görüntü durum denetimini kullanın.

Sizi anlık görüntü sistem durumu denetimine götüren uçtan uca izleme görünümünün özel durum bölmesinde bir bağlantı vardır.

![Anlık görüntü durumu denetimini girin](./media/snapshot-debugger/enter-snapshot-health-check.png)

Etkileşimli, sohbet benzeri arabirim, sık karşılaşılan sorunları ve bunları düzeltmenize yardımcı olan yönergeleri arar.

![Sistem durumu denetimi](./media/snapshot-debugger/healthcheck.png)

Bu sorunu çözmezse, aşağıdaki el ile sorun giderme adımlarına bakın.

## <a name="verify-the-instrumentation-key"></a>İzleme anahtarını doğrulama

Yayımlanmış uygulamanızda doğru izleme anahtarını kullandığınızdan emin olun. Genellikle, izleme anahtarı ApplicationInsights. config dosyasından okunurdur. Değerin portalda gördüğünüz Application Insights kaynağı için izleme anahtarıyla aynı olduğunu doğrulayın.

## <a name="preview-versions-of-net-core"></a>.NET Core 'un önizleme sürümleri
Uygulama .NET Core 'un önizleme sürümünü kullanıyorsa ve Snapshot Debugger portalda [Application Insights bölmesi](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) aracılığıyla etkinleştirildiyse Snapshot Debugger başlatılamayabilir. [Diğer ortamlar Için etkinleştirme Snapshot Debugger](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) konusundaki yönergeleri izleyerek [Microsoft. ApplicationInsights. snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paketini uygulamayla birlikte etkinleştirmeye [ek olarak uygulamayı ekleyin. Application Insights bölmesi](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>NuGet paketinin en son sürümüne yükseltin

Snapshot Debugger [portalda Application Insights bölmesi](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)aracılığıyla etkinleştirildiyse, uygulamanız zaten en son NuGet paketini çalıştırıyor olmalıdır. Snapshot Debugger, [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet paketini ekleyerek etkinleştirilmişse, en son sürümünü kullandığınızdan emin olmak Için Visual Studio 'Nun NuGet paket yöneticisini kullanın. Microsoft. ApplicationInsights. SnapshotCollector. Sürüm notları https://github.com/Microsoft/ApplicationInsights-Home/issues/167 şurada bulunabilir

## <a name="check-the-uploader-logs"></a>Uploader günlüklerini denetleyin

Bir anlık görüntü oluşturulduktan sonra, diskte bir mini döküm dosyası (. dmp) oluşturulur. Ayrı bir Uploader işlemi, bu mini döküm dosyasını oluşturur ve Snapshot Debugger depolama Application Insights için ilişkili tüm pdb 'leri birlikte karşıya yükler. Mini döküm başarıyla karşıya yüklendikten sonra diskten silinir. Uploader işleminin günlük dosyaları diskte tutulur. App Service ortamında bu günlükleri `D:\Home\LogFiles`bulabilirsiniz. Bu günlük dosyalarını bulmak için App Service kudu yönetim sitesini kullanın.

1. App Service uygulamanızı Azure portal açın.
2. **Gelişmiş Araçlar**' a tıklayın veya **kudu**araması yapın.
3. **Git**' e tıklayın.
4. **Hata ayıklama konsolu** aşağı açılan liste kutusunda **cmd**' yi seçin.
5. **Günlük dosyaları**' na tıklayın.

`Uploader_` veya `SnapshotUploader_` ve `.log` uzantılı bir ada sahip en az bir dosya görmeniz gerekir. Herhangi bir günlük dosyasını indirmek veya bir tarayıcıda açmak için uygun simgeye tıklayın.
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
> Yukarıdaki örnek, Microsoft. ApplicationInsights. SnapshotCollector NuGet paketinin 1.2.0 sürümüdür. Önceki sürümlerde, Uploader işlemi `MinidumpUploader.exe` olarak adlandırılır ve günlük daha az ayrıntılıdır.

Önceki örnekte, izleme anahtarı `c12a605e73c44346a984e00000000000`. Bu değer, uygulamanız için izleme anahtarıyla eşleşmelidir.
Mini döküm, KIMLIK `139e411a23934dc0b9ea08a626db16c5`bir anlık görüntü ile ilişkilendirilir. Application Insights Analytics 'te ilişkili özel durum telemetrisini bulmak için bu KIMLIĞI daha sonra kullanabilirsiniz.

Yükleyici, 15 dakikada bir daha yeni pdb 'leri tarar. Bir örneği aşağıda verilmiştir:

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

App Service _barındırılmayan_ uygulamalar için, Uploader günlükleri mini dökümler ile aynı klasörlerdir: `%TEMP%\Dumps\<ikey>` (`<ikey>`, izleme anahtarınız).

## <a name="troubleshooting-cloud-services"></a>Sorun giderme Cloud Services
Cloud Services roller için varsayılan geçici klasör, bir mini döküm dosyalarını tutmak için çok küçük olabilir, bu da kayıp anlık görüntülerle önde gelebilir.
Gerekli alan, uygulamanızın toplam çalışma kümesine ve eşzamanlı anlık görüntü sayısına bağlıdır.
32 bitlik bir ASP.NET Web rolü çalışma kümesi, genellikle 200 MB ile 500 MB arasındadır.
En az iki eşzamanlı anlık görüntüye izin verin.
Örneğin, uygulamanız 1 GB toplam çalışma kümesi kullanıyorsa, anlık görüntüleri depolamak için en az 2 GB disk alanı olduğundan emin olun.
Bulut hizmeti rolünüzü anlık görüntüler için ayrılmış bir yerel kaynakla yapılandırmak için bu adımları izleyin.

1. Bulut hizmeti tanımı (. csdef) dosyasını düzenleyerek bulut hizmetinize yeni bir yerel kaynak ekleyin. Aşağıdaki örnek, 5 GB boyutundaki `SnapshotStore` adlı bir kaynağı tanımlar.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. `SnapshotStore` yerel kaynağına işaret eden bir ortam değişkeni eklemek için rolünüzün başlangıç kodunu değiştirin. Çalışan rolleri için, kod rolün `OnStart` metoduna eklenmelidir:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Web rolleri için (ASP.NET), kod Web uygulamanızın `Application_Start` metoduna eklenmelidir:
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

3. `SnapshotCollector` tarafından kullanılan geçici klasör konumunu geçersiz kılmak için rolünüzün ApplicationInsights. config dosyasını güncelleştirin
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

Uygun bir klasör bulunamazsa, _"uygun bir gölge kopya klasörü bulunamadı_ " hatası bildiren bir hata raporlar Snapshot Collector.

Kopyalama başarısız olursa, `ShadowCopyFailed` bir hata raporlar Snapshot Collector.

Uploader başlatılamaz Snapshot Collector, bir `UploaderCannotStartFromShadowCopy` hatası raporlar. İletinin gövdesi genellikle `System.UnauthorizedAccessException`içerir. Bu hata genellikle uygulama daha düşük izinlere sahip bir hesap altında çalıştığı için oluşur. Hesabın, gölge kopya klasörüne yazma izni var, ancak kodu yürütme izni yok.

Bu hatalar genellikle başlangıç sırasında gerçekleşdiğinden, genellikle _"Uploader başlatılamadı"_ hatası ortaya çıkar `ExceptionDuringConnect` bir hata oluştu.

Bu hataları geçici olarak çözmek için, `ShadowCopyFolder` yapılandırma seçeneği aracılığıyla gölge kopya klasörünü el ile belirtebilirsiniz. Örneğin, ApplicationInsights. config kullanımı:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Veya, .NET Core uygulaması ile appSettings. JSON kullanıyorsanız:

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

Bir anlık görüntü oluşturulduğunda, oluşturan özel durum bir anlık görüntü KIMLIĞIYLE etiketlenir. Özel durum telemetrisi Application Insights olarak bildirildiğinde bu anlık görüntü KIMLIĞI özel bir özellik olarak dahil edilir. Application Insights **arama** kullanarak, tüm telemetrisini `ai.snapshot.id` özel özelliği ile bulabilirsiniz.

1. Azure portal Application Insights kaynağına gidin.
2. **Ara**' ya tıklayın.
3. Arama metin kutusuna `ai.snapshot.id` yazın ve ENTER tuşuna basın.

![Portalda anlık görüntü KIMLIĞIYLE telemetri arama](./media/snapshot-debugger/search-snapshot-portal.png)

Bu arama sonuç döndürürse, seçili zaman aralığında uygulamanız için Application Insights bir anlık görüntü bildirilmemiştir.

Yükleyici günlüklerinden belirli bir anlık görüntü KIMLIĞINI aramak için, arama kutusuna o KIMLIĞI yazın. Karşıya yüklendiğini bildiğiniz bir anlık görüntü için telemetri bulamıyorsanız, şu adımları izleyin:

1. İzleme anahtarını doğrulayarak doğru Application Insights kaynağına bakdığınızı bir kez daha kontrol edin.

2. Uploader günlüğünden zaman damgasını kullanarak, aramanın zaman aralığı filtresini bu zaman aralığını kapsayacak şekilde ayarlayın.

Bu anlık görüntü KIMLIĞIYLE ilgili bir özel durum görmüyorsanız, özel durum telemetrisi Application Insights bildirilmemiştir. Bu durum, uygulamanız anlık görüntüyü aldıktan sonra, ancak özel durum telemetrisini bildirdikten sonra kilitlenirse meydana gelebilir. Bu durumda, beklenmeyen yeniden başlatmalar veya işlenmemiş özel durumlar olup olmadığını görmek için `Diagnose and solve problems` altındaki App Service günlüklerini denetleyin.

## <a name="edit-network-proxy-or-firewall-rules"></a>Ağ ara sunucusunu veya güvenlik duvarı kurallarını Düzenle

Uygulamanız bir ara sunucu veya güvenlik duvarı üzerinden Internet 'e bağlanırsa, uygulamanızın Snapshot Debugger hizmetiyle iletişim kurmasına izin vermek için kuralları düzenlemeniz gerekebilir. [Snapshot Debugger tarafından kullanılan IP adresleri ve bağlantı noktalarının listesi](../../azure-monitor/app/ip-addresses.md#snapshot-debugger)aşağıda verilmiştir.

---
title: Azure Tanılama uzantısı sorunlarını giderme
description: Azure sanal makinelerinde, Service Fabric veya Cloud Services Azure tanılama kullanırken karşılaşılan sorunları giderin.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/08/2019
ms.openlocfilehash: 0a6322edccc2047ffd9d67e4e3ed113e668898da
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834703"
---
# <a name="azure-diagnostics-troubleshooting"></a>Azure Tanılama sorunlarını giderme
Bu makalede Azure Tanılama kullanımı ile ilgili sorun giderme bilgileri açıklanmaktadır. Azure Tanılama hakkında daha fazla bilgi için bkz. [Azure tanılama genel bakış](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Mantıksal bileşenler
**Tanılama eklentisi Başlatıcısı (DiagnosticsPluginLauncher. exe)** : Azure tanılama uzantısını başlatır. Giriş noktası işlemi olarak görev yapar.

**Tanılama eklentisi (DiagnosticsPlugin. exe)** : izleme aracısının ömrünü yapılandırır, başlatır ve yönetir. Bu, başlatıcı tarafından başlatılan ana işlemdir.

**Izleme Aracısı (MonAgent\*. exe işlemleri)** : tanılama verilerini izler, toplar ve aktarır.  

## <a name="logartifact-paths"></a>Günlük/yapıt yolları
Bazı önemli günlüklerin ve yapıtların yolları aşağıda verilmiştir. Belgenin geri kalanı boyunca bu bilgilere değintik.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Yapıt | Yol |
| --- | --- |
| **Azure Tanılama yapılandırma dosyası** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<sürümü > \Config.txt |
| **Günlük dosyaları** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<sürümü > \ |
| **Tanılama verileri için yerel depo** | C:\Resources\Directory\<Cloudservicedeploymentıd >.\<RoleName >. DiagnosticStore\WAD0107\Tables |
| **İzleme Aracısı yapılandırma dosyası** | C:\Resources\Directory\<Cloudservicedeploymentıd >.\<RoleName >. DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Tanılama uzantısı paketi** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<sürümü > |
| **Günlük toplama yardımcı programı yolu** | %SystemDrive%\Packages\GuestAgent\ |
| **Monme Thost günlük dosyası** | C:\Resources\Directory\<Cloudservicedeploymentıd >.\<RoleName >. Diagnosticstore\wad0107\configuration\monbir Thost. < seq_num >. log |

### <a name="virtual-machines"></a>Sanal makineler
| Yapıt | Yol |
| --- | --- |
| **Azure Tanılama yapılandırma dosyası** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<sürümü > \RuntimeSettings |
| **Günlük dosyaları** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \ |
| **Tanılama verileri için yerel depo** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Tables |
| **İzleme Aracısı yapılandırma dosyası** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Configuration\MaConfig.xml |
| **Durum dosyası** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<sürümü > \ durum |
| **Azure Tanılama uzantısı paketi** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion >|
| **Günlük toplama yardımcı programı yolu** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Monme Thost günlük dosyası** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \Wad0107\configuration\monk\thost. < seq_num >. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Ölçüm verileri Azure portal görünmüyor
Azure Tanılama, Azure portal görüntülenebilecek ölçüm verileri sağlar. Portalda verileri görmekte sorun yaşıyorsanız, ilgili ölçüm kayıtlarının orada olup olmadığını görmek için Azure Tanılama depolama hesabındaki Wadölçümlerini\* tablosunu kontrol edin.

Burada, tablonun **Partitionkey** 'ı kaynak kimliği, sanal makine veya sanal makine ölçek kümesidir. **Rowkey** ölçüm adıdır (performans sayacı adı olarak da bilinir).

Kaynak KIMLIĞI yanlışsa, kaynak KIMLIĞININ doğru şekilde **ayarlandığından emin olmak** için bkz **. ResourceID** ** >  > ** **ölçümleri** .

Belirli bir ölçüm için veri yoksa, metriğin (performans sayacı) dahil edilip edilmediğini öğrenmek için **Tanılama yapılandırması** > **PerformanceCounter** bakın. Varsayılan olarak aşağıdaki sayaçları etkinliyoruz:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ ASP.NET uygulamaları (__Toplam__) \ Istek/sn
- \ ASP.NET uygulamaları (__Toplam__)/toplam hata/sn
- \ASP.NET\Requests kuyruğa alındı
- \ASP.NET\Requests reddedildi
- \Processor (W3wp)\% Işlemci zamanı
- \Process (W3wp) \ özel baytlar
- \Process (WaIISHost)\% Işlemci zamanı
- \Process (WaIISHost) \Özel baytlar
- \Process (WaWorkerHost)\% Işlemci zamanı
- \Process (WaWorkerHost) \Özel baytlar
- \Bellek\sayfa hatası/sn
- GC 'de \.NET CLR belleği (_Global_)\% zaman
- \Mantıksaldisk (C:) \Disk yazma bayt/sn
- \Mantıksaldisk (C:) \Disk okuma bayt/sn
- \Mantıksaldisk (D:) \Disk yazma bayt/sn
- \Mantıksaldisk (D:) \Disk okuma bayt/sn

Yapılandırma doğru şekilde ayarlandıysa ancak ölçüm verilerini göremiyorsanız, sorun gidermenize yardımcı olması için aşağıdaki yönergeleri kullanın.


## <a name="azure-diagnostics-is-not-starting"></a>Azure Tanılama başlamıyor
Azure Tanılama başlamamasının nedeni hakkında daha fazla bilgi için daha önce sağlanmış olan günlük dosyaları konumundaki **Diagnosticspluginlauncher. log** ve **Diagnosticsplugin. log** dosyalarına bakın.

Bu Günlükler `Monitoring Agent not reporting success after launch`gösteriyorsa, Monure Thost. exe ' yi başlatırken bir hata olduğu anlamına gelir. Önceki bölümde `MonAgentHost log file` için belirtilen konumdaki günlüklere bakın.

Günlük dosyalarının son satırında çıkış kodu bulunur.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
**Negatif** çıkış kodu bulursanız, [Başvurular bölümünde](#references) [Çıkış kodu tablosuna](#azure-diagnostics-plugin-exit-codes) bakın.

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Tanılama verileri Azure depolama 'ya kayıtlı değil
Verilerin hiçbirinin görünmediğine veya bazı verilerin görünmediğine göre belirlenir.

### <a name="diagnostics-infrastructure-logs"></a>Tanılama altyapı günlükleri
Tanılama, tanılama altyapı günlüklerinde tüm hataları günlüğe kaydeder. [Yapılandırmanızda tanılama altyapı günlükleri yakalama özelliğinin](#how-to-check-diagnostics-extension-configuration)etkinleştirildiğinden emin olun. Daha sonra, yapılandırılmış depolama hesabınızdaki `DiagnosticInfrastructureLogsTable` tablosunda görüntülenen ilgili hatalara hızlı bir şekilde bakabilirsiniz.

### <a name="no-data-is-appearing"></a>Hiçbir veri görünmüyor
Olay verilerinin hiç görünmamasının en yaygın nedeni, depolama hesabı bilgilerinin yanlış tanımlanmadır.

Çözüm: Tanılama yapılandırmanızı düzeltip tanılamayı yeniden yükleyin.

Depolama hesabı doğru şekilde yapılandırıldıysa, makineye uzaktan erişin ve *Diagnosticsplugin. exe* ve *monagentcore. exe* ' nin çalıştığını doğrulayın. Çalışmıyorsa, [Azure tanılama başlatılmayan](#azure-diagnostics-is-not-starting)adımları izleyin.

İşlem çalışıyorsa, [veri yerel olarak yakalanarak](#is-data-getting-captured-locally) ' a gidin ve görüntülenen yönergeleri izleyin.

Bu sorunu çözmezse, şunu deneyin:

1. Aracıyı Kaldırma
2. Dizin C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics kaldır
3. Aracıyı yeniden yükler


### <a name="part-of-the-data-is-missing"></a>Verilerin bir kısmı eksik
Bazı verileri alıyorsanız, ancak veri toplama/aktarım işlem hattının doğru ayarlandığı anlamına gelir. Sorunu daraltmak için buradaki alt bölümleri izleyin.

#### <a name="is-the-collection-configured"></a>Koleksiyon yapılandırılmış mı?
Tanılama yapılandırması, toplanacak belirli bir veri türü için yönergeler içerir. Yalnızca koleksiyon için yapılandırdığınız verileri aradığınızı doğrulamak için [yapılandırmanızı gözden geçirin](#how-to-check-diagnostics-extension-configuration) .

#### <a name="is-the-host-generating-data"></a>Ana bilgisayar veri oluşturuyor mu?
- **Performans sayaçları**: Perfmon ' yı açın ve sayacı denetleyin.

- **İzleme günlükleri**: VM 'ye uzaktan erişim ve uygulamanın yapılandırma dosyasına bir TextWriterTraceListener ekleme.  Metin dinleyicisini ayarlamak için https://msdn.microsoft.com/library/sk36c28t.aspx bakın.  `<trace>` öğesinin `<trace autoflush="true">`sahip olduğundan emin olun.<br />
İzleme günlüklerinin oluşturulmayı görmüyorsanız izleme günlükleri eksik hakkında daha fazla bilgi için bkz.

- **ETW izlemeleri**: VM 'ye uzaktan erişim ve PerfView 'ı yükler.  PerfView ' de, **dosya** > **Kullanıcı komutunu** > **dinleyin etwprovder1** > **etwprovider2**gibi çalıştırın. **Listen** komutu büyük/küçük harfe DUYARLıDıR ve ETW sağlayıcılarının virgülle ayrılmış listesi arasında boşluk olamaz. Komut çalışamazsa, nelerin çalıştırılacağını ve sonucun ne olduğunu görmek için PerfView aracının sağ alt kısmındaki **günlük** düğmesini seçebilirsiniz.  Girişin doğru olduğu varsayıldığında, yeni bir pencere açılır. Birkaç saniye içinde ETW izlemelerini görmeye başlayabilirsiniz.

- **Olay günlükleri**: VM 'ye uzaktan erişim. `Event Viewer`açın ve olayların mevcut olduğundan emin olun.

#### <a name="is-data-getting-captured-locally"></a>Veriler yerel olarak yakalanarak mı?
Sonra, verilerin yerel olarak yakalandığından emin olun.
Veriler, tanılama verileri için yerel depodaki `*.tsf` dosyalarında yerel olarak depolanır. Farklı `.tsf` dosyalarında toplanan farklı türlerde Günlükler alınır. Adlar, Azure Storage 'daki tablo adlarına benzerdir.

Örneğin, `PerformanceCountersTable.tsf``Performance Counters` toplanmaktadır. Olay günlükleri `WindowsEventLogsTable.tsf`' de toplanır. Yerel koleksiyon dosyalarını açmak için [yerel günlük ayıklama](#local-log-extraction) bölümündeki yönergeleri kullanın ve bunları diskte toplandığını doğrulayın.

Yerel olarak toplanan günlükleri görmüyorsanız ve konağın veri oluşturduğunu zaten doğruladıysanız, büyük olasılıkla bir yapılandırma sorununuz olur. Yapılandırmanızı dikkatlice gözden geçirin.

Ayrıca, MonitoringAgent MaConfig. xml için oluşturulan yapılandırmayı gözden geçirin. İlgili günlük kaynağını açıklayan bir bölüm olduğunu doğrulayın. Daha sonra, tanılama yapılandırması ve izleme Aracısı yapılandırması arasındaki çeviride kaybolmamasını doğrulayın.

#### <a name="is-data-getting-transferred"></a>Veriler aktarıldı mı?
Verilerin yerel olarak yakalandığını, ancak depolama hesabınızda görmemenizi doğruladıysanız, aşağıdaki adımları uygulayın:

- Doğru bir depolama hesabı sağladıysanız ve belirtilen depolama hesabı için anahtarları devralmadıysanız emin olun. Azure Cloud Services için bazen kişilerin `useDevelopmentStorage=true`güncelleştirmemenizi görüyoruz.

- Belirtilen depolama hesabının doğru olduğundan emin olun. Bileşenlerin genel depolama uç noktalarına ulaşmasını önleyen ağ kısıtlamalarına sahip olmadığınızdan emin olun. Bunu yapmanın bir yolu, makineye uzaktan erişim sağlar ve ardından aynı depolama hesabına kendiniz yazmaya çalışır.

- Son olarak, izleme Aracısı tarafından hangi hatalara bildirilmekte olduğuna bakabilirsiniz. İzleme Aracısı günlüklerini, tanılama verileri için yerel depoda bulunan `maeventtable.tsf`yazar. Bu dosyayı açmak için [yerel günlük ayıklama](#local-log-extraction) bölümündeki yönergeleri izleyin. Ardından, depolamaya yazma yerel dosyalarına okuma başarısızlıklarını belirten `errors` olup olmadığını belirlemeyi deneyin.

### <a name="capturing-and-archiving-logs"></a>Günlükleri yakalama ve arşivleme
Destek ile iletişim kurmayı düşünüyorsanız, bu durum, makinenizden günlükleri toplamaktır. Kendiniz yaparak zamandan tasarruf edebilirsiniz. Günlük toplama yardımcı programı yolunda `CollectGuestLogs.exe` yardımcı programını çalıştırın. Aynı klasörde tüm ilgili Azure günlükleri ile bir. zip dosyası oluşturur.

## <a name="diagnostics-data-tables-not-found"></a>Tanılama veri tabloları bulunamadı
ETW olaylarını tutan Azure Storage tablolarının tabloları aşağıdaki kod kullanılarak adlandırılır:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Örnek aşağıda verilmiştir:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Bu kod dört tablo üretir:

| Olay | Tablo adı |
| --- | --- |
| Provider = "prov1" &lt;olay kimliği = "1"/&gt; |WADEvent + MD5 ("prov1") + "1" |
| Provider = "prov1" &lt;olay kimliği = "2" eventDestination = "dest1"/&gt; |WADdest1 |
| Provider = "prov1" &lt;DefaultEvents/&gt; |WADDefault + MD5 ("prov1") |
| Provider = "prov2" &lt;DefaultEvents eventDestination = "dest2"/&gt; |WADdest2 |

## <a name="references"></a>Başvurular

### <a name="how-to-check-diagnostics-extension-configuration"></a>Tanılama uzantısı yapılandırmasını denetleme
Uzantı yapılandırmanızı denetlamanın en kolay yolu [Azure Kaynak Gezgini](https://resources.azure.com)' e gitmenin yanı sıra Azure tanılama uzantısının (ıaasdiagnostics/paadiagnostics) olduğu sanal makineye veya bulut hizmetine gitmenize yardımcı olur.

Alternatif olarak, makineye Uzak Masaüstü ve günlük yapıtları yolu bölümünde açıklanan Azure Tanılama yapılandırma dosyasına bakın.

Her iki durumda da, **Microsoft. Azure. Diagnostics**' i ve ardından **Xmlcfg** veya **wadcfg** alanı için arama yapın.

Bir sanal makinede arama yapıyorsanız ve **Wadcfg** alanı mevcutsa, bu, CONFIG 'in JSON biçiminde olduğu anlamına gelir. **Xmlcfg** alanı mevcutsa, yapılandırma XML biçiminde olduğu ve Base64 kodlamalı olduğu anlamına gelir. Tanılama tarafından yüklenen XML 'yi görmek için [Bu dosyanın kodunu çözmelisiniz](https://www.bing.com/search?q=base64+decoder) .

Bulut hizmeti rolü için, yapılandırmayı diskten seçerseniz, veriler Base64 kodlamalı olur, bu nedenle tanılama tarafından yüklenen XML 'yi görmek için [kodu çözmelisiniz](https://www.bing.com/search?q=base64+decoder) .

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Tanılama eklentisi çıkış kodları
Eklenti aşağıdaki çıkış kodlarını döndürür:

| Çıkış kodu | Açıklama |
| --- | --- |
| 0 |Başarılı. |
| -1 |Genel hata. |
| -2 |RCF dosyası yüklenemiyor.<p>Bu iç hata yalnızca, VM 'de Konuk Aracısı eklenti başlatıcısı el ile yanlış çağrıldığında gerçekleşir. |
| -3 |Tanılama yapılandırma dosyası yüklenemiyor.<p><p>Çözüm: bir yapılandırma dosyası, şema doğrulamasını geçirmedi. Çözüm, şemayla uyumlu bir yapılandırma dosyası sağlamaktır. |
| -4 |İzleme Aracısı Tanılamanın başka bir örneği zaten yerel kaynak dizinini kullanıyor.<p><p>Çözüm: **Localresourcedirectory**için farklı bir değer belirtin. |
| -6 |Konuk Aracısı eklenti başlatıcısı, geçersiz bir komut satırı ile tanılamayı başlatmaya çalıştı.<p><p>Bu iç hata yalnızca, VM 'de Konuk Aracısı eklenti başlatıcısı el ile yanlış çağrıldığında gerçekleşir. |
| -10 |Tanılama eklentisi işlenmeyen bir özel durumla çıkış yaptı. |
| -11 |Konuk Aracısı, izleme aracısını başlatma ve izlemeye sorumlu işlemi oluşturamadı.<p><p>Çözüm: yeni süreçler başlatmak için yeterli kullanılabilir sistem kaynakları olduğunu doğrulayın.<p> |
| -101 |Tanılama eklentisi çağrılırken geçersiz bağımsız değişkenler.<p><p>Bu iç hata yalnızca, VM 'de Konuk Aracısı eklenti başlatıcısı el ile yanlış çağrıldığında gerçekleşir. |
| -102 |Eklenti işlemi kendisini başlatamıyor.<p><p>Çözüm: yeni süreçler başlatmak için yeterli kullanılabilir sistem kaynakları olduğunu doğrulayın. |
| -103 |Eklenti işlemi kendisini başlatamıyor. Özel olarak, günlükçü nesnesi oluşturulamıyor.<p><p>Çözüm: yeni süreçler başlatmak için yeterli kullanılabilir sistem kaynakları olduğunu doğrulayın. |
| -104 |Konuk Aracısı tarafından sunulan RCF dosyası yüklenemiyor.<p><p>Bu iç hata yalnızca, VM 'de Konuk Aracısı eklenti başlatıcısı el ile yanlış çağrıldığında gerçekleşir. |
| -105 |Tanılama eklentisi, tanılama yapılandırma dosyasını açamıyor.<p><p>Bu iç hata, yalnızca tanılama eklentisi sanal makinede el ile yanlış çağrılırsa meydana gelir. |
| -106 |Tanılama yapılandırma dosyası okunamıyor.<p><p>Bir yapılandırma dosyası, şema doğrulaması geçirilmediğinde oluşur. <br><br>Çözüm: şemayla uyumlu bir yapılandırma dosyası sağlayın. Daha fazla bilgi için bkz. [Tanılama uzantısı yapılandırmasını denetleme](#how-to-check-diagnostics-extension-configuration). |
| -107 |İzleme aracısına geçiş kaynak dizini geçersiz.<p><p>Bu iç hata yalnızca, sanal makinede izleme Aracısı el ile yanlış çağrıldığında gerçekleşir.</p> |
| -108 |Tanılama yapılandırma dosyası izleme Aracısı yapılandırma dosyasına dönüştürülemiyor.<p><p>Bu iç hata yalnızca tanılama eklentisi geçersiz bir yapılandırma dosyası ile el ile çağrıldığında gerçekleşir. |
| -110 |Genel tanılama yapılandırma hatası.<p><p>Bu iç hata yalnızca tanılama eklentisi geçersiz bir yapılandırma dosyası ile el ile çağrıldığında gerçekleşir. |
| -111 |İzleme Aracısı başlatılamıyor.<p><p>Çözüm: yeterli sistem kaynaklarının kullanılabilir olduğunu doğrulayın. |
| -112 |Genel hata |

### <a name="local-log-extraction"></a>Yerel günlük ayıklama
İzleme Aracısı, günlükleri ve yapıtları `.tsf` dosya olarak toplar. `.tsf` dosyası okunabilir değildir ancak aşağıdaki gibi bir `.csv` dönüştürebilirsiniz:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
`<relevantLogFile>.csv` adlı yeni bir dosya, karşılık gelen `.tsf` dosyası ile aynı yolda oluşturulur.

>[!NOTE]
> Bu yardımcı programı yalnızca Main. TSF dosyasına (örneğin, PerformanceCountersTable. TSF) karşı çalıştırmanız gerekir. Eşlik eden dosyalar (örneğin, PerformanceCountersTables_\*\*001. TSF, PerformanceCountersTables_\*\*002. TSF, vb.) otomatik olarak işlenir.

### <a name="more-about-missing-trace-logs"></a>Eksik izleme günlükleri hakkında daha fazla bilgi

>[!NOTE]
> IaaS VM 'niz üzerinde çalışan bir uygulamada DiagnosticsMonitorTraceListener ' i yapılandırmadığınız sürece aşağıdaki bilgiler çoğunlukla Azure Cloud Services geçerlidir.

- Web. config veya App. config dosyasında **Diagnosticmonitortracelistener** ' ın yapılandırıldığından emin olun.  Bu, bulut hizmeti projelerinde varsayılan olarak yapılandırılır. Bununla birlikte, bazı müşteriler bunu Yorumlar ve izleme deyimlerinin tanılama tarafından toplanmamasını sağlar.

- Günlükler **OnStart** veya **Run** yönteminden yazılmadığından, **diagnosticmonitortracelistener** uygulamasının App. config dosyasında bulunduğundan emin olun.  Varsayılan olarak, Web. config içindedir, ancak yalnızca W3wp. exe içinde çalışan kod için geçerlidir. Bu nedenle, WaIISHost. exe ' de çalışan izlemeleri yakalamak için App. config içinde ihtiyacınız vardır.

- Diagnostics. **Debug. WriteXXX** yerine **Diagnostics. Trace. tracexxx** kullandığınızdan emin olun. Hata ayıklama deyimleri, bir yayın derlemesinden kaldırılır.

- Derlenmiş kodun **Tanılama. Trace satırlarına** sahip olduğundan emin olun (bkz. yansıtıcı, ıldadsm veya ılspy kullanın). TRACE koşullu derleme sembolünü kullanmadığınız müddetçe, **Tanılama. Trace** komutları derlenmiş ikiliden kaldırılır. Bu, bir proje oluşturmak için MSBuild kullandığınızda oluşan yaygın bir sorundur.   

## <a name="known-issues-and-mitigations"></a>Bilinen sorunlar ve azaltmaları
Bilinen hafifletenlerden oluşan bilinen sorunların listesi aşağıda verilmiştir:

**1. .NET 4,5 bağımlılığı**

Windows Azure Tanılama uzantısı, .NET 4,5 Framework veya üzeri sürümlerde bir çalışma zamanı bağımlılığı içerir. Yazma sırasında, Azure Cloud Services için sağlanan tüm makinelerin yanı sıra Azure sanal makinelerini temel alan resmi görüntülerin de .NET 4,5 veya sonraki bir sürümü yüklü.

Windows Azure Tanılama uzantısını .NET 4,5 veya üzeri olmayan bir makinede çalıştırmayı denediğinizde yine de olası bir durumla karşılaşabilirsiniz. Bu durum, makinenizi eski bir görüntüden veya anlık görüntüsünden oluşturduğunuzda ya da kendi özel diskinizi getirdiğinizde oluşur.

Bu genel olarak, **Diagnosticspluginlauncher. exe** çalıştırılırken **255** çıkış kodu olarak bildirim yapılır. Aşağıdaki işlenmeyen özel durum nedeniyle hata oluşur:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Risk azaltma:** Makinenizde .NET 4,5 veya üstünü yükler.

**2. performans sayaçları verileri depolamada kullanılabilir ancak portalda gösterilmiyor**

Sanal makinelerdeki Portal deneyimi, varsayılan olarak belirli performans sayaçlarını gösterir. Performans sayaçlarını görmüyorsanız ve verilerin depolama alanında kullanılabilir olduğu için oluşturulduğunu biliyorsanız, aşağıdakileri denetleyin:

- Depolama alanındaki verilerin Ingilizce 'de sayaç adları olup olmadığı. Sayaç adları Ingilizce değilse, Portal ölçüm grafiği bunu tanıyamaz. **Risk azaltma**: makinenin dilini sistem hesapları için İngilizce olarak değiştirin. Bunu yapmak için, **yönetim** > **kopya ayarları** > **Denetim Masası** > **bölge** ' yi seçin. Ardından, özel dilin sistem hesabına uygulanmaması için **hoş geldiniz ekranının ve sistem hesaplarının** seçimini kaldırın.

- Performans sayacı adlarında joker karakterler (\*) kullanıyorsanız, Portal, performans sayaçları Azure depolama havuzuna gönderildiğinde yapılandırılan ve toplanan sayacı ilişkilendiremez. **Risk azaltma**: joker karakter ve portalın (\*) genişletmesine sahip olduğunuzdan emin olmak için, performans Sayaçlarınızı ["Azure izleyici" havuzuna](diagnostics-extension-schema.md#diagnostics-extension-111)yönlendirin.


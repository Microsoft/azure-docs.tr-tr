---
title: Azure Application Insights .net Aracısı API başvurusu
description: Aracı API başvurusunu Application Insights. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7c5c2f1d055ec6270892873548872b20b17b4158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566906"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Izleyici Application Insights aracı API 'SI başvurusu

Bu makalede, [az. ApplicationMonitor PowerShell modülünün](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)üyesi olan bir cmdlet açıklanmaktadır.

> [!NOTE] 
> - Başlamak için bir izleme anahtarına ihtiyacınız vardır. Daha fazla bilgi için bkz. [kaynak oluşturma](create-new-resource.md#copy-the-instrumentation-key).
> - Bu cmdlet, lisans ve Gizlilik Bildirimimizi incelemenizi ve kabul etmenizi gerektirir.

> [!IMPORTANT] 
> Bu cmdlet, yönetici izinlerine sahip bir PowerShell oturumu ve yükseltilmiş bir yürütme ilkesi gerektirir. Daha fazla bilgi için bkz. [yükseltilmiş yürütme Ilkesiyle PowerShell 'i yönetici olarak çalıştırma](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Bu cmdlet, lisans ve Gizlilik Bildirimimizi incelemenizi ve kabul etmenizi gerektirir.
> - İzleme altyapısı ek yük ekler ve varsayılan olarak kapalıdır.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Bazı kayıt defteri anahtarlarını ayarlayarak izleme altyapısını sunar.
Değişikliklerin etkili olması için IIS 'yi yeniden başlatın.

İzleme altyapısı, .NET SDK 'Ları tarafından toplanan verileri tamamlayabilir.
Yönetilen bir işlemin yürütülmesini tanımlayan olayları ve iletileri toplar. Bu olaylar ve mesajlar bağımlılık sonuç kodlarını, HTTP fiillerini ve [SQL komut metnini](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)içerir.

Şu durumlarda izleme altyapısını etkinleştirin:
- Enable cmdlet 'i ile izlemeyi zaten etkinleştirdiniz, ancak izleme altyapısını etkinleştirmediniz.
- Uygulamanızı .NET SDK 'Ları ile el ile gördünüz ve ek telemetri toplamak istiyorsunuz.

### <a name="examples"></a>Örnekler

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametreler

#### <a name="-acceptlicense"></a>-AcceptLicense
**Seçim.** Gözetimsiz yüklemelerde lisans ve gizlilik bildirimini kabul etmek için bu anahtarı kullanın.

#### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri çıkarmak için bu anahtarı kullanın.

### <a name="output"></a>Çıktı


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>İzleme altyapısını başarıyla etkinleştirmenin örnek çıkışı

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Bir hedef bilgisayardaki IIS uygulamalarının birlikte izlenmesini eklemeyi sunar.

Bu cmdlet, IIS applicationHost.config değiştirecek ve bazı kayıt defteri anahtarlarını ayarlayacaktır.
Ayrıca, her bir uygulama tarafından kullanılan izleme anahtarını tanımlayan bir applicationinsights.ikey.config dosyası oluşturur.
IIS, uygulama başlatılırken Application Insights SDK 'Yı uygulamalara ekleyecektir ve RedfieldModule 'u başlangıçta yükler.
Değişikliklerin etkili olması için IIS 'yi yeniden başlatın.

İzlemeyi etkinleştirdikten sonra, uygulamanızın Telemetriyi hızla gönderdiğini denetlemek için [canlı ölçümleri](live-stream.md) kullanmanızı öneririz.

### <a name="examples"></a>Örnekler

#### <a name="example-with-a-single-instrumentation-key"></a>Tek bir izleme anahtarına sahip örnek
Bu örnekte, geçerli bilgisayardaki tüm uygulamalara tek bir izleme anahtarı atanır.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>İzleme anahtarı eşleme ile örnek
Bu örnekte:
- `MachineFilter` joker karakterini kullanarak geçerli bilgisayarla eşleşir `'.*'` .
- `AppFilter='WebAppExclude'` bir `null` izleme anahtarı sağlar. Belirtilen uygulama gösterilmez.
- `AppFilter='WebAppOne'` belirtilen uygulamaya benzersiz bir izleme anahtarı atar.
- `AppFilter='WebAppTwo'` belirtilen uygulamaya benzersiz bir izleme anahtarı atar.
- Son olarak, `AppFilter` `'.*'` önceki kurallarla eşleşmeyen tüm Web uygulamalarını eşleştirmek ve varsayılan bir izleme anahtarı atamak için joker karakteri de kullanır.
- Okunabilirlik için boşluklar eklenir.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```

> [!NOTE]
> Bu bağlamdaki AppFilter 'ın adlandırması kafa karıştırıcı olabilir, `AppFilter` uygulama adı Regex filtresi (IIS üzerinde .net söz konusu olduğunda HostingEnvironment. SiteName) ayarlar. `VirtualPathFilter` sanal yol Regex filtresini (IIS üzerinde .net söz konusu olduğunda HostingEnvironment. ApplicationVirtualPath) ayarlar. Tek bir uygulamayı işaretlemek için VirtualPathFilter öğesini aşağıdaki gibi kullanırsınız: `Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap @(@{VirtualPathFilter="^/MyAppName$"; InstrumentationSettings=@{InstrumentationKey='<your ikey>'}})`

### <a name="parameters"></a>Parametreler

#### <a name="-instrumentationkey"></a>-Instrumentationkey
**Gerekli.** Hedef bilgisayardaki tüm uygulamalar tarafından kullanılmak üzere tek bir izleme anahtarı sağlamak için bu parametreyi kullanın.

#### <a name="-instrumentationkeymap"></a>-Instrumentationkeymap
**Gerekli.** Birden çok izleme anahtarı ve her bir uygulama tarafından kullanılan izleme anahtarlarının bir eşlemesini sağlamak için bu parametreyi kullanın.
' İ ayarlayarak, birkaç bilgisayar için tek bir yükleme betiği oluşturabilirsiniz `MachineFilter` .

> [!IMPORTANT]
> Uygulamalar kuralların sağlandığı sırada kurallarla eşleştirecektir. Bu nedenle, önce en özel kuralları ve en son genel kuralları belirtmeniz gerekir.

##### <a name="schema"></a>Şema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **Machinefilter** BILGISAYARıN veya VM adının gerekli bir C# Regex.
    - '. * ' tümü eşleşecek
    - ' ComputerName ' yalnızca tam ada sahip bilgisayarlar ile eşleştirecektir.
- **Appfilter** , IIS sitesi adının gerekli bir C# Regex. [Get-iissite](/powershell/module/iisadministration/get-iissite)komutunu çalıştırarak sunucunuzdaki sitelerin bir listesini alabilirsiniz.
    - '. * ' tümü eşleşecek
    - ' SiteName ' yalnızca belirtilen tam ada sahip IIS sitesiyle eşleştirecektir.
- Önceki iki filtrelerle eşleşen uygulamaların izlenmesini etkinleştirmek için **ınstrumentationkey** gereklidir.
    - İzlemeyi hariç tutmak için kurallar tanımlamak istiyorsanız bu değeri boş bırakın.


#### <a name="-enableinstrumentationengine"></a>-Enableınstrumentationengine
**Seçim.** İzleme altyapısının yönetilen bir işlemin yürütülmesi sırasında neler olduğunu hakkında olayları ve iletileri toplamasını sağlamak için bu anahtarı kullanın. Bu olaylar ve mesajlar bağımlılık sonuç kodlarını, HTTP fiillerini ve SQL komut metnini içerir.

İzleme altyapısı ek yük ekler ve varsayılan olarak kapalıdır.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Seçim.** Gözetimsiz yüklemelerde lisans ve gizlilik bildirimini kabul etmek için bu anahtarı kullanın.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Bir Web sunucuları kümeniz olduğunda, [paylaşılan bir yapılandırma](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)kullanıyor olabilirsiniz.
HttpModule bu paylaşılan yapılandırmaya eklenemez.
Bu betik, ek yükleme adımlarının gerekli olduğu iletiyle başarısız olur.
Bu denetimi yoksaymak ve önkoşulları yüklemeye devam etmek için bu anahtarı kullanın. Daha fazla bilgi için bkz. [bilinen çakışma-IIS ile paylaşılan-yapılandırma](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri göstermek için bu anahtarı kullanın.

#### <a name="-whatif"></a>-WhatIf 
**Ortak parametre.** İzlemeyi gerçekten etkinleştirmeden giriş parametrelerinizi test etmek ve doğrulamak için bu anahtarı kullanın.

### <a name="output"></a>Çıktı

#### <a name="example-output-from-a-successful-enablement"></a>Başarılı bir etkinleştirme için örnek çıkış

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Bazı kayıt defteri anahtarlarını kaldırarak izleme altyapısını devre dışı bırakır.
Değişikliklerin etkili olması için IIS 'yi yeniden başlatın.

### <a name="examples"></a>Örnekler

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametreler 

#### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri çıkarmak için bu anahtarı kullanın.

### <a name="output"></a>Çıktı


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>İzleme altyapısını başarıyla devre dışı bırakma örnek çıkışı

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Hedef bilgisayarda izlemeyi devre dışı bırakır.
Bu cmdlet, IIS applicationHost.config düzenlemelerini kaldırır ve kayıt defteri anahtarlarını kaldırır.

### <a name="examples"></a>Örnekler

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametreler 

#### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri göstermek için bu anahtarı kullanın.

### <a name="output"></a>Çıktı


##### <a name="example-output-from-successfully-disabling-monitoring"></a>İzlemeyi başarıyla devre dışı bırakarak oluşan örnek çıkış

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Yapılandırma dosyasını alır ve değerleri konsola yazdırır.

### <a name="examples"></a>Örnekler

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametreler

Parametre gerekli değil.

### <a name="output"></a>Çıktı


##### <a name="example-output-from-reading-the-config-file"></a>Yapılandırma dosyasını okumaktan örnek çıkış

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Bu cmdlet Durum İzleyicisi hakkında sorun giderme bilgileri sağlar.
Bu cmdlet 'i kullanarak izleme durumunu, PowerShell modülünün sürümünü araştırın ve çalışan işlemi inceleyin.
Bu cmdlet, izleme için gerekli olan anahtar dosyaları hakkında sürüm bilgilerini ve bilgileri bildirir.

### <a name="examples"></a>Örnekler

#### <a name="example-application-status"></a>Örnek: uygulama durumu

`Get-ApplicationInsightsMonitoringStatus`Web sitelerinin izleme durumunu göstermek için komutunu çalıştırın.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

Bu örnekte;
- **Makine tanımlayıcısı** , sunucunuzu benzersiz şekilde tanımlamak için kullanılan anonım bir tanıtıcıdır. Bir destek isteği oluşturursanız, sunucunuza yönelik günlükleri bulmak için bu KIMLIĞE ihtiyacımız olacak.
- **Varsayılan Web sItesI** IIS 'de durdurulur
- **DemoWebApp111** , IIS 'de başlatılmış, ancak hiçbir istek almamıştı. Bu rapor, çalışan bir işlem (ProcessId: bulunamadı) olmadığını gösterir.
- **DemoWebApp222** çalışıyor ve Izleniyor (belgelenmiş: true). Kullanıcı yapılandırmasına bağlı olarak, bu site için xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 Izleme anahtarı eşleştirildi.
- **DemoWebApp333** , Application Insights SDK kullanılarak el ile işaretlendi. Durum İzleyicisi SDK algıladı ve bu siteyi izlemez.


#### <a name="example-powershell-module-information"></a>Örnek: PowerShell modülü bilgileri

`Get-ApplicationInsightsMonitoringStatus -PowerShellModule`Geçerli modülle ilgili bilgileri göstermek için komutunu çalıştırın:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Örnek: çalışma zamanı durumu

Tüm dll 'Lerin yüklenip yüklenmediğini görmek için, izlenen bilgisayarda işlemi inceleyebilirsiniz. İzleme çalışıyorsa, en az 12 dll 'nin yüklenmesi gerekir.

`Get-ApplicationInsightsMonitoringStatus -InspectProcess` komutunu çalıştırın:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parametreler

#### <a name="no-parameters"></a>(Parametre yok)

Varsayılan olarak, bu cmdlet Web uygulamalarının izleme durumunu rapor eder.
Uygulamanızın başarıyla işaretlenmiş olup olmadığını gözden geçirmek için bu seçeneği kullanın.
Ayrıca, sitenizdeki hangi Izleme anahtarının eşleştiğini de inceleyebilirsiniz.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Isteğe bağlı**. İzleme için gerekli olan DLL 'lerin sürüm numaralarını ve yollarını raporlamak için bu anahtarı kullanın.
Application Insights SDK dahil olmak üzere herhangi bir DLL sürümünü belirlemeniz gerekiyorsa bu seçeneği kullanın.

#### <a name="-inspectprocess"></a>-Inspectprocess

**Isteğe bağlı**. IIS 'in çalışıp çalışmadığını raporlamak için bu anahtarı kullanın.
Ayrıca, gerekli dll 'Lerin IIS çalışma zamanına yüklenip yüklenmediğini anlamak için dış araçları da indirir.


Bu işlem herhangi bir nedenle başarısız olursa, bu komutları el ile çalıştırabilirsiniz:
- iisreset.exe/Status
- [handle64.exe](/sysinternals/downloads/handle) -p W3wp | Findstr/I "ınstrumentationengine AI. ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) W3wp | Findstr/I "ınstrumentationengine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Isteğe bağlı**. Yalnızca ınspectprocess ile kullanılır. Ek araçlar indirilmeden önce görüntülenen kullanıcı isteğini atlamak için bu anahtarı kullanın.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Tam yeniden yükleme yapmadan yapılandırma dosyasını ayarlar.
Değişikliklerin etkili olması için IIS 'yi yeniden başlatın.

> [!IMPORTANT] 
> Bu cmdlet yönetici izinlerine sahip bir PowerShell oturumu gerektirir.


### <a name="examples"></a>Örnekler

#### <a name="example-with-a-single-instrumentation-key"></a>Tek bir izleme anahtarına sahip örnek
Bu örnekte, geçerli bilgisayardaki tüm uygulamalara tek bir izleme anahtarı atanacaktır.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>İzleme anahtarı eşleme ile örnek
Bu örnekte:
- `MachineFilter` joker karakterini kullanarak geçerli bilgisayarla eşleşir `'.*'` .
- `AppFilter='WebAppExclude'` bir `null` izleme anahtarı sağlar. Belirtilen uygulama gösterilmez.
- `AppFilter='WebAppOne'` belirtilen uygulamaya benzersiz bir izleme anahtarı atar.
- `AppFilter='WebAppTwo'` belirtilen uygulamaya benzersiz bir izleme anahtarı atar.
- Son olarak, `AppFilter` `'.*'` önceki kurallarla eşleşmeyen tüm Web uygulamalarını eşleştirmek ve varsayılan bir izleme anahtarı atamak için joker karakteri de kullanır.
- Okunabilirlik için boşluklar eklenir.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parametreler

#### <a name="-instrumentationkey"></a>-Instrumentationkey
**Gerekli.** Hedef bilgisayardaki tüm uygulamalar tarafından kullanılmak üzere tek bir izleme anahtarı sağlamak için bu parametreyi kullanın.

#### <a name="-instrumentationkeymap"></a>-Instrumentationkeymap
**Gerekli.** Birden çok izleme anahtarı ve her bir uygulama tarafından kullanılan izleme anahtarlarının bir eşlemesini sağlamak için bu parametreyi kullanın.
' İ ayarlayarak, birkaç bilgisayar için tek bir yükleme betiği oluşturabilirsiniz `MachineFilter` .

> [!IMPORTANT]
> Uygulamalar kuralların sağlandığı sırada kurallarla eşleştirecektir. Bu nedenle, önce en özel kuralları ve en son genel kuralları belirtmeniz gerekir.

##### <a name="schema"></a>Şema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **Machinefilter** BILGISAYARıN veya VM adının gerekli bir C# Regex.
    - '. * ' tümü eşleşecek
    - ' ComputerName ' yalnızca belirtilen ada sahip bilgisayarlarla eşleştirecektir.
- **Appfilter** BILGISAYARıN veya VM adının gerekli bir C# Regex.
    - '. * ' tümü eşleşecek
    - ' ApplicationName ' yalnızca belirtilen ada sahip IIS uygulamalarıyla eşleştirecektir.
- Önceki iki filtrelerle eşleşen uygulamaların izlenmesini etkinleştirmek için **ınstrumentationkey** gereklidir.
    - İzlemeyi hariç tutmak için kurallar tanımlamak istiyorsanız bu değeri boş bırakın.


#### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri göstermek için bu anahtarı kullanın.


### <a name="output"></a>Çıktı

Varsayılan olarak, çıkış yok.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-Instrumentationkey aracılığıyla yapılandırma dosyasını ayarlamadan ayrıntılı çıkış örneği

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-Instrumentationkeymap aracılığıyla yapılandırma dosyasını ayarlamadan ayrıntılı çıkış örneği

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Kodsuz kullanacaksınız Attach çalışma zamanından [ETW olaylarını](/windows/desktop/etw/event-tracing-portal) toplar. Bu cmdlet [PerfView](https://github.com/microsoft/perfview)çalıştırmaya alternatiftir.

Toplanan olaylar, gerçek zamanlı olarak konsola yazdırılır ve bir ETL dosyasına kaydedilir. Çıktı ETL dosyası, daha fazla araştırma için [PerfView](https://github.com/microsoft/perfview) tarafından açılabilir.

Bu cmdlet, zaman aşımı süresine (varsayılan 5 dakika) ulaşıncaya kadar veya el ile () durduruluncaya kadar çalışır `Ctrl + C` .

### <a name="examples"></a>Örnekler

#### <a name="how-to-collect-events"></a>Olayları toplama

Normalde, uygulamanızın neden görüntülenmediğini araştırmak için olay toplamanızı isteyeceğiz.

Kodsuz kullanacaksınız Attach çalışma zamanı, IIS başlatıldığında ve uygulamanız başlatıldığında ETW olaylarını yayacaktır.

Bu olayları toplamak için:
1. Yönetici ayrıcalıklarına sahip bir cmd konsolunda, IIS 'yi `iisreset /stop` ve tüm Web uygulamalarını kapatmak için yürütün.
2. Bu cmdlet 'i Yürüt
3. Yönetici ayrıcalıklarına sahip bir cmd konsolunda, `iisreset /start` IIS 'yi başlatmak için yürütün.
4. Uygulamanıza gözatmayı deneyin.
5. Uygulamanızın yüklenmesi tamamlandıktan sonra, el ile durdurabilirsiniz ( `Ctrl + C` ) veya zaman aşımı süresini bekleyebilirsiniz.

#### <a name="what-events-to-collect"></a>Toplanacak olaylar

Olayları toplarken üç seçeneğiniz vardır:
1. `-CollectSdkEvents`APPLICATION INSIGHTS SDK 'dan yayılan olayları toplamak için anahtarını kullanın.
2. `-CollectRedfieldEvents`Durum İzleyicisi ve Redfield çalışma zamanı tarafından yayılan olayları toplamak için anahtarını kullanın. Bu Günlükler IIS ve uygulama başlangıcını tanılarken yararlı olur.
3. Her iki olay türünü de toplamak için her iki anahtarı kullanın.
4. Varsayılan olarak, hiçbir anahtar belirtilmemişse olay türleri toplanacaktır.


### <a name="parameters"></a>Parametreler

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Seçim.** Bu betiğin olay toplama süresini ayarlamak için bu parametreyi kullanın. Varsayılan değer 5 dakikadır.

#### <a name="-logdirectory"></a>-LogDirectory
**Seçim.** ETL dosyasının çıkış dizinini ayarlamak için bu anahtarı kullanın. Varsayılan olarak, bu dosya PowerShell modülleri dizininde oluşturulur. Tam yol, betik yürütme sırasında görüntülenecektir.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Seçim.** Application Insights SDK olaylarını toplamak için bu anahtarı kullanın.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Seçim.** Durum İzleyicisi ve Redfield çalışma zamanının olaylarını toplamak için bu anahtarı kullanın.

#### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri çıkarmak için bu anahtarı kullanın.



### <a name="output"></a>Çıktı


#### <a name="example-of-application-startup-logs"></a>Uygulama başlangıç günlüklerine örnek
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```

## <a name="next-steps"></a>Sonraki adımlar

  Telemetrinizi görüntüleyin:
 - Performansı ve kullanımı izlemek için [ölçümleri](../essentials/metrics-charts.md) bulun.
- Sorunları tanılamak için [olayları ve günlükleri arayın](./diagnostic-search.md) .
- Daha gelişmiş sorgular için [analiz](../logs/log-query-overview.md) kullanın.
- [Panolar oluşturun](./overview-dashboard.md).
 
 Daha fazla telemetri ekleyin:
 - Sitenizin canlı kalması için [web testleri oluşturun](monitor-web-app-availability.md).
- Web sayfası kodundan özel durumları görmek ve izleme çağrılarını etkinleştirmek için [Web istemcisi telemetrisini ekleyin](./javascript.md) .
- İzleme ve günlük çağrısı ekleyebilmeniz [için Application Insights SDK 'sını kodunuza ekleyin](./asp-net.md) .
 
 Application Insights aracısıyla daha fazlasını yapın:
 - Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.


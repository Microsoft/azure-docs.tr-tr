---
title: Azure Application Insights Aracısı API başvurusu
description: Aracı API başvurusunu Application Insights. Get-Applicationınsiısmonitoringstatus. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, sanal makinelerde veya Azure üzerinde ASP.NET web uygulamaları ile çalışır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9b1010404cb876ed818dd54cf527987c6cf0ffe0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899681"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>Application Insights aracı API 'SI: Get-Applicationınsiısmonitoringstatus

Bu makalede, [az. ApplicationMonitor PowerShell modülünün](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)üyesi olan bir cmdlet açıklanmaktadır.

## <a name="description"></a>Açıklama

Bu cmdlet Durum İzleyicisi hakkında sorun giderme bilgileri sağlar.
Bu cmdlet 'i kullanarak izleme durumunu, PowerShell modülünün sürümünü araştırın ve çalışan işlemi inceleyin.
Bu cmdlet, izleme için gerekli olan anahtar dosyaları hakkında sürüm bilgilerini ve bilgileri bildirir.

> [!IMPORTANT] 
> Bu cmdlet yönetici izinlerine sahip bir PowerShell oturumu gerektirir.

## <a name="examples"></a>Örnekler

### <a name="example-application-status"></a>Örnek: uygulama durumu

Web sitelerinin izleme durumunu göstermek için `Get-ApplicationInsightsMonitoringStatus` komutunu çalıştırın.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

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


### <a name="example-powershell-module-information"></a>Örnek: PowerShell modülü bilgileri

Geçerli modülle ilgili bilgileri göstermek için `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` komutunu çalıştırın:

```
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

### <a name="example-runtime-status"></a>Örnek: çalışma zamanı durumu

Tüm dll 'Lerin yüklenip yüklenmediğini görmek için, izlenen bilgisayarda işlemi inceleyebilirsiniz. İzleme çalışıyorsa, en az 12 dll 'nin yüklenmesi gerekir.

Komutu çalıştırın `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


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

## <a name="parameters"></a>Parametreler

### <a name="no-parameters"></a>(Parametre yok)

Varsayılan olarak, bu cmdlet Web uygulamalarının izleme durumunu rapor eder.
Uygulamanızın başarıyla işaretlenmiş olup olmadığını gözden geçirmek için bu seçeneği kullanın.
Ayrıca, sitenizdeki hangi Izleme anahtarının eşleştiğini de inceleyebilirsiniz.


### <a name="-powershellmodule"></a>-PowerShellModule
**İsteğe bağlı**. İzleme için gerekli olan DLL 'lerin sürüm numaralarını ve yollarını raporlamak için bu anahtarı kullanın.
Application Insights SDK dahil olmak üzere herhangi bir DLL sürümünü belirlemeniz gerekiyorsa bu seçeneği kullanın.

### <a name="-inspectprocess"></a>-Inspectprocess

**İsteğe bağlı**. IIS 'in çalışıp çalışmadığını raporlamak için bu anahtarı kullanın.
Ayrıca, gerekli dll 'Lerin IIS çalışma zamanına yüklenip yüklenmediğini anlamak için dış araçları da indirir.


Bu işlem herhangi bir nedenle başarısız olursa, bu komutları el ile çalıştırabilirsiniz:
- ıisreset. exe/status
- [handle64. exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p W3wp | Findstr/I "ınstrumentationengine AI. ApplicationInsights
- [listdlls64. exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) W3wp | Findstr/I "ınstrumentationengine AI ApplicationInsights"


### <a name="-force"></a>-Force

**İsteğe bağlı**. Yalnızca ınspectprocess ile kullanılır. Ek araçlar indirilmeden önce görüntülenen kullanıcı isteğini atlamak için bu anahtarı kullanın.


## <a name="next-steps"></a>Sonraki adımlar

 Application Insights aracısıyla daha fazlasını yapın:
 - Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.

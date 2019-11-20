---
title: Azure Application Insights Aracısı API başvurusu
description: Aracı API başvurusunu Application Insights. Başlat-Izle. Durum İzleyicisi ve Application Insights SDK 'dan ETW günlüklerini toplayın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: c97315b3a215f10e5b8f9533bf09fa5ac30ee16f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899660"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights aracı API 'SI: Start-Applicationınsiısmonitoringtrace

Bu makalede, [az. ApplicationMonitor PowerShell modülünün](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)üyesi olan bir cmdlet açıklanmaktadır.

## <a name="description"></a>Açıklama

Kodsuz kullanacaksınız Attach çalışma zamanından [ETW olaylarını](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) toplar. Bu cmdlet [PerfView](https://github.com/microsoft/perfview)çalıştırmaya alternatiftir.

Toplanan olaylar, gerçek zamanlı olarak konsola yazdırılır ve bir ETL dosyasına kaydedilir. Çıktı ETL dosyası, daha fazla araştırma için [PerfView](https://github.com/microsoft/perfview) tarafından açılabilir.

Bu cmdlet, zaman aşımı süresine (varsayılan 5 dakika) ulaşıncaya kadar veya el ile (`Ctrl + C`) durduruluncaya kadar çalışır.

> [!IMPORTANT] 
> Bu cmdlet yönetici izinlerine sahip bir PowerShell oturumu gerektirir.

## <a name="examples"></a>Örnekler

### <a name="how-to-collect-events"></a>Olayları toplama

Normalde, uygulamanızın neden görüntülenmediğini araştırmak için olay toplamanızı isteyeceğiz.

Kodsuz kullanacaksınız Attach çalışma zamanı, IIS başlatıldığında ve uygulamanız başlatıldığında ETW olaylarını yayacaktır.

Bu olayları toplamak için:
1. Yönetici ayrıcalıklarına sahip bir cmd konsolunda, IIS 'yi ve tüm Web uygulamalarını kapatmak Için `iisreset /stop` yürütün.
2. Bu cmdlet 'i Yürüt
3. Yönetici ayrıcalıklarına sahip bir cmd konsolunda, IIS 'yi başlatmak Için `iisreset /start` yürütün.
4. Uygulamanıza gözatmayı deneyin.
5. Uygulamanızın yüklenmesi tamamlandıktan sonra, el ile durdurabilirsiniz (`Ctrl + C`) veya zaman aşımını bekleyebilirsiniz.

### <a name="what-events-to-collect"></a>Toplanacak olaylar

Olayları toplarken üç seçeneğiniz vardır:
1. Application Insights SDK 'dan yayılan olayları toplamak için `-CollectSdkEvents` anahtarını kullanın.
2. Durum İzleyicisi ve Redfield çalışma zamanı tarafından yayılan olayları toplamak için `-CollectRedfieldEvents` anahtarını kullanın. Bu Günlükler IIS ve uygulama başlangıcını tanılarken yararlı olur.
3. Her iki olay türünü de toplamak için her iki anahtarı kullanın.
4. Varsayılan olarak, hiçbir anahtar belirtilmemişse olay türleri toplanacaktır.


## <a name="parameters"></a>Parametreler

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Seçim.** Bu betiğin olay toplama süresini ayarlamak için bu parametreyi kullanın. Varsayılan değer 5 dakikadır.

### <a name="-logdirectory"></a>-LogDirectory
**Seçim.** ETL dosyasının çıkış dizinini ayarlamak için bu anahtarı kullanın. Varsayılan olarak, bu dosya PowerShell modülleri dizininde oluşturulur. Tam yol, betik yürütme sırasında görüntülenecektir.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Seçim.** Application Insights SDK olaylarını toplamak için bu anahtarı kullanın.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Seçim.** Durum İzleyicisi ve Redfield çalışma zamanının olaylarını toplamak için bu anahtarı kullanın.

### <a name="-verbose"></a>-Ayrıntılı
**Ortak parametre.** Ayrıntılı günlükleri çıkarmak için bu anahtarı kullanın.



## <a name="output"></a>Çıktı


### <a name="example-of-application-startup-logs"></a>Uygulama başlangıç günlüklerine örnek
```
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

Ek sorun giderme:

- Aşağıdaki ek sorun giderme adımlarını gözden geçirin: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Kaçırmış olabileceğiniz parametreler hakkında bilgi edinmek için [API başvurusunu](status-monitor-v2-overview.md#powershell-api-reference) gözden geçirin.
- Ek yardıma ihtiyacınız varsa [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)' da bizimle iletişim kurmanız yeterlidir.



 Application Insights aracısıyla daha fazlasını yapın:
 - Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.
 - Ayarlarınızın doğru kaydedildiğinden emin olmak için [yapılandırmayı alın](status-monitor-v2-api-get-config.md) .
 - İzlemeyi İnceleme [durumunu alın](status-monitor-v2-api-get-status.md) .

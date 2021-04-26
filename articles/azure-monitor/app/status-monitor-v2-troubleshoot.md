---
title: Azure Application Insights Agent sorunlarını giderme ve bilinen sorunlar | Microsoft Docs
description: Application Insights Aracısı ve sorun giderme örnekleri ile ilgili bilinen sorunlar. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: cf3d8fd1566f3d71541aab7648680063e85079bf
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121838"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Application Insights Agent sorunlarını giderme (eski adıyla Durum İzleyicisi v2)

İzlemeyi etkinleştirdiğinizde, veri toplamayı engelleyen sorunlarla karşılaşabilirsiniz.
Bu makalede, bilinen tüm sorunlar listelenmekte ve sorun giderme örnekleri sağlanmaktadır.

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Uygulamanın bin dizininde çakışan dll 'Ler

Bu dll 'Lerden herhangi biri bin dizininde mevcutsa, izleme başarısız olabilir:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Bu dll 'Lerden bazıları, uygulamanız tarafından kullanılmasa bile Visual Studio varsayılan uygulama şablonlarına dahildir.
Sentomatik davranışını görmek için sorun giderme araçları 'nı kullanabilirsiniz:

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset ve uygulama yükü (telemetri olmadan). Sysinternals ile araştır (Handle.exe ve ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>PowerShell sürümleri
Bu ürün, PowerShell v 5.1 kullanılarak yazılmıştır ve test edilmiştir.
Bu modül, 6 veya 7 PowerShell sürümleriyle uyumlu değildir.
PowerShell v 5.1 'yi daha yeni sürümlerle birlikte kullanmanızı öneririz. Daha fazla bilgi için bkz. PowerShell [5,1 Ile PowerShell 7 yan yana kullanımı](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51).

### <a name="conflict-with-iis-shared-configuration"></a>IIS paylaşılan yapılandırmasıyla çakışma

Bir Web sunucuları kümeniz varsa, [paylaşılan bir yapılandırma](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)kullanıyor olabilirsiniz.
HttpModule bu paylaşılan yapılandırmaya eklenemez.
Her bir Web sunucusunda, DLL 'yi her bir sunucunun GAC 'sine yüklemek için Etkinleştir komutunu çalıştırın.

Etkinleştir komutunu çalıştırdıktan sonra şu adımları uygulayın:
1. Paylaşılan yapılandırma dizinine gidin ve applicationHost.config dosyasını bulun.
2. Bu satırı yapılandırmanızın modüller bölümüne ekleyin:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS Iç Içe uygulamalar

IIS 'de iç içe geçmiş uygulamaları, sürüm 1,0 ' de işaretlemeiyoruz.

### <a name="advanced-sdk-configuration-isnt-available"></a>Gelişmiş SDK yapılandırması kullanılamıyor.

SDK yapılandırması, sürüm 1,0 ' deki son kullanıcıya gösterilmez.

    
    
## <a name="troubleshooting"></a>Sorun giderme
    
### <a name="troubleshooting-powershell"></a>PowerShell sorunlarını giderme

#### <a name="determine-which-modules-are-available"></a>Hangi modüllerin kullanılabilir olduğunu belirleme
`Get-Module -ListAvailable`Hangi modüllerin yükleneceğini öğrenmek için komutunu kullanabilirsiniz.

#### <a name="import-a-module-into-the-current-session"></a>Geçerli oturuma bir modül içeri aktar
Bir modül bir PowerShell oturumuna yüklenmediyse, komutunu kullanarak el ile yükleyebilirsiniz `Import-Module <path to psd1>` .


### <a name="troubleshooting-the-application-insights-agent-module"></a>Application Insights Aracısı modülü sorunlarını giderme

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Application Insights Aracısı modülünde kullanılabilen komutları listeleyin
`Get-Command -Module Az.ApplicationMonitor`Kullanılabilir komutları almak için komutunu çalıştırın:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Application Insights Aracısı modülünün geçerli sürümünü belirleme
`Get-ApplicationInsightsMonitoringStatus -PowerShellModule`Modülüyle ilgili aşağıdaki bilgileri göstermek için komutunu çalıştırın:
   - PowerShell modülü sürümü
   - Application Insights SDK sürümü
   - PowerShell modülünün dosya yolları
    
Bu cmdlet 'in nasıl kullanılacağına ilişkin ayrıntılı bir açıklama için [API başvurusunu](status-monitor-v2-api-reference.md) gözden geçirin.


### <a name="troubleshooting-running-processes"></a>Çalışan işlemlerin sorunlarını giderme

Tüm dll 'Lerin yüklenip yüklenmediğini anlamak için, Araçlı bilgisayardaki süreçler inceleyebilirsiniz.
İzleme çalışıyorsa, en az 12 dll 'nin yüklenmesi gerekir.

`Get-ApplicationInsightsMonitoringStatus -InspectProcess`DLL 'leri denetlemek için komutunu kullanın.

Bu cmdlet 'in nasıl kullanılacağına ilişkin ayrıntılı bir açıklama için [API başvurusunu](status-monitor-v2-api-reference.md) gözden geçirin.


### <a name="collect-etw-logs-by-using-perfview"></a>PerfView kullanarak ETW günlüklerini toplama

#### <a name="setup"></a>Kurulum

1. [GitHub](https://github.com/Microsoft/perfview/releases)'dan PerfView.exe ve PerfView64.exe indirin.
2. PerfView64.exe başlatın.
3. **Gelişmiş Seçenekler**' i genişletin.
4. Şu onay kutularını temizleyin:
    - **Zip**
    - **Birleştir**
    - **.NET sembol koleksiyonu**
5. Şu **ek sağlayıcıları** ayarla: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Günlükleri toplama

1. Yönetici ayrıcalıklarına sahip bir komut konsolunda, `iisreset /stop` IIS 'yi ve tüm Web uygulamalarını devre dışı bırakmak için komutunu çalıştırın.
2. PerfView içinde **toplamayı Başlat**' ı seçin.
3. Yönetici ayrıcalıklarına sahip bir komut konsolunda, `iisreset /start` IIS 'yi başlatmak için komutunu çalıştırın.
4. Uygulamanıza gözatmayı deneyin.
5. Uygulamanız yüklendikten sonra PerfView ' a dönüp **toplamayı durdur**' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Kaçırmış olabileceğiniz parametreler hakkında bilgi edinmek için [API başvurusunu](status-monitor-v2-overview.md#powershell-api-reference) gözden geçirin.
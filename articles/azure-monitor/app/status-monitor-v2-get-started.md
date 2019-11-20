---
title: Azure Application Insights Aracısı-Başlarken | Microsoft Docs
description: Application Insights Aracısı için hızlı başlangıç kılavuzu. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1ef354bc61d849a4d536bd26355ef21b0f1c0035
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899627"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Şirket içi sunucular için Azure Izleyici Application Insights Aracısı 'nı kullanmaya başlama

Bu makale, çoğu ortam için çalışması beklenen hızlı başlangıç komutlarını içerir.
Yönergeler, güncelleştirmeleri dağıtmak için PowerShell Galerisi bağımlıdır.
Bu komutlar PowerShell `-Proxy` parametresini destekler.

Bu komutların açıklaması, özelleştirme yönergeleri ve sorun giderme hakkında bilgi için bkz. [ayrıntılı yönergeler](status-monitor-v2-detailed-instructions.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="download-and-install-via-powershell-gallery"></a>PowerShell Galerisi aracılığıyla indirme ve yükleme

### <a name="install-prerequisites"></a>Ön koşulları yükleme
PowerShell 'i yönetici olarak çalıştırın.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
PowerShell 'i kapatın.

### <a name="install-application-insights-agent"></a>Application Insights Aracısı 'nı yükler
PowerShell 'i yönetici olarak çalıştırın.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>İzlemeyi etkinleştirme
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>El ile indir ve yükle (çevrimdışı seçenek)
### <a name="download-the-module"></a>Modülü indir
[PowerShell Galerisi](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)' dan modülün en son sürümünü el ile indirin.

### <a name="unzip-and-install-application-insights-agent"></a>Application Insights aracısını unzip ve yükler
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>İzlemeyi etkinleştirme
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Sonraki adımlar

 Telemetrinizi görüntüleyin:

- Performansı ve kullanımı izlemek için [ölçümleri](../../azure-monitor/app/metrics-explorer.md) bulun.
- Sorunları tanılamak için [olayları ve günlükleri arayın](../../azure-monitor/app/diagnostic-search.md) .
- Daha gelişmiş sorgular için [analiz kullanın](../../azure-monitor/app/analytics.md) .
- [Panolar oluşturun](../../azure-monitor/app/overview-dashboard.md).

 Daha fazla telemetri ekleyin:

- Sitenizin canlı kaldığından emin olmak için [Web testleri oluşturun](monitor-web-app-availability.md) .
- Web sayfası kodundan özel durumları görmek ve izleme çağrılarını etkinleştirmek için [Web istemcisi telemetrisini ekleyin](../../azure-monitor/app/javascript.md) .
- İzleme ve günlük çağrısı ekleyebilmeniz [için Application Insights SDK 'sını kodunuza ekleyin](../../azure-monitor/app/asp-net.md) .

Application Insights aracısıyla daha fazlasını yapın:

- Burada bulunan komutların açıklaması için [ayrıntılı yönergeleri](status-monitor-v2-detailed-instructions.md) gözden geçirin.
- Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.

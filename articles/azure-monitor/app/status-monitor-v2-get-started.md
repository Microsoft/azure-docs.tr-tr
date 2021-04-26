---
title: Azure Application Insights Aracısı-Başlarken | Microsoft Docs
description: Application Insights Aracısı için hızlı başlangıç kılavuzu. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d24e67eac54b3ce4eadfc6a4bde47410d59fae8b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581101"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Şirket içi sunucular için Azure Izleyici Application Insights Aracısı 'nı kullanmaya başlama

Bu makale, çoğu ortam için çalışması beklenen hızlı başlangıç komutlarını içerir.
Yönergeler, güncelleştirmeleri dağıtmak için PowerShell Galerisi bağımlıdır.
Bu komutlar PowerShell parametresini destekler `-Proxy` .

Bu komutların açıklaması, özelleştirme yönergeleri ve sorun giderme hakkında bilgi için bkz. [ayrıntılı yönergeler](status-monitor-v2-detailed-instructions.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="download-and-install-via-powershell-gallery"></a>PowerShell Galerisi aracılığıyla indirme ve yükleme

### <a name="install-prerequisites"></a>Ön koşulları yükleme

- İzlemeyi etkinleştirmek için bir bağlantı dizesi gerekir. Bir bağlantı dizesi, Application Insights kaynağınızın genel bakış dikey penceresinde görüntülenir. Daha fazla bilgi için bkz. sayfa [bağlantı dizeleri](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#finding-my-connection-string).

> [!NOTE]
> 2020 Nisan itibariyle PowerShell Galerisi, TLS 1,1 ve 1,0 kullanım dışıdır.
>
> İhtiyacınız olabilecek ek önkoşullar için, [POWERSHELL GALERISI TLS desteği](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support)' ne bakın.
>

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
Enable-ApplicationInsightsMonitoring -ConnectionString 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
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
Enable-ApplicationInsightsMonitoring -ConnectionString 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```




## <a name="next-steps"></a>Sonraki adımlar

 Telemetrinizi görüntüleyin:

- Performansı ve kullanımı izlemek için [ölçümleri](../essentials/metrics-charts.md) bulun.
- Sorunları tanılamak için [olayları ve günlükleri arayın](./diagnostic-search.md) .
- Daha gelişmiş sorgular için [analiz kullanın](../logs/log-query-overview.md) .
- [Panolar oluşturun](./overview-dashboard.md).

 Daha fazla telemetri ekleyin:

- Sitenizin canlı kalması için [web testleri oluşturun](monitor-web-app-availability.md).
- Web sayfası kodundan özel durumları görmek ve izleme çağrılarını etkinleştirmek için [Web istemcisi telemetrisini ekleyin](./javascript.md) .
- İzleme ve günlük çağrısı ekleyebilmeniz [için Application Insights SDK 'sını kodunuza ekleyin](./asp-net.md) .

Application Insights aracısıyla daha fazlasını yapın:

- Burada bulunan komutların açıklaması için [ayrıntılı yönergeleri](status-monitor-v2-detailed-instructions.md) gözden geçirin.
- Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.


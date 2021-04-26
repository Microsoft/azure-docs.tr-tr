---
title: Azure Application Insights Agent ayrıntılı yönergeleri | Microsoft Docs
description: Application Insights aracısıyla çalışmaya başlama hakkında ayrıntılı yönergeler. Web sitesini yeniden dağıtmaya gerek kalmadan Web sitesi performansını izleyin. Şirket içinde, VM 'lerde veya Azure 'da barındırılan ASP.NET Web Apps ile birlikte kullanılır.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 29922f088a51e4876e5e2ec8fe87c3bbce4482f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521688"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights Aracısı (eski adıyla Durum İzleyicisi v2): ayrıntılı yönergeler

Bu makalede, PowerShell Galerisi nasıl ekleneceği ve ApplicationMonitor modülünün nasıl indirileceği açıklanır.
Kullanmaya başlamak için ihtiyacınız olan en yaygın parametreler dahildir.
Ayrıca, İnternet erişiminizin olmadığı durumlarda el ile karşıdan yükleme yönergeleri de sunuyoruz.

## <a name="get-an-instrumentation-key"></a>İzleme anahtarı al

Başlamak için bir izleme anahtarına ihtiyacınız vardır. Daha fazla bilgi için bkz. [Application Insights kaynağı oluşturma](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>PowerShell 'i yükseltilmiş yürütme ilkesiyle yönetici olarak çalıştırma

### <a name="run-as-admin"></a>Yönetici olarak çalıştır

PowerShell 'in bilgisayarınızda değişiklik yapması için yönetici düzeyinde izinleri olması gerekir.
### <a name="execution-policy"></a>Yürütme ilkesi
- Açıklama: varsayılan olarak, PowerShell betikleri çalıştırıldığında devre dışı bırakılır. RemoteSigned betiklerin yalnızca geçerli kapsam için izin vermesini öneririz.
- Başvuru: [yürütme ilkeleri](/powershell/module/microsoft.powershell.core/about/about_execution_policies) ve [set-executionpolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy)hakkında.
- Komut: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process` .
- İsteğe bağlı parametre:
    - `-Force`. Onay istemi 'ni atlar.

**Örnek hatalar**

```output
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.

Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell önkoşulları

Komutunu çalıştırarak PowerShell örneğinizi denetleyin `$PSVersionTable` .
Bu komut aşağıdaki çıktıyı üretir:

```output
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Bu yönergeler Windows 10 çalıştıran bir bilgisayarda ve yukarıda listelenen sürümlerde yazılmıştır ve test edilmiştir.

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell Galerisi önkoşulları

Bu adımlar, sunucunuzu PowerShell Galerisi 'dan modül indirmek üzere hazırlar.

> [!NOTE] 
> PowerShell Galerisi, Windows 10, Windows Server 2016 ve PowerShell 6 + ' da desteklenir.
> Önceki sürümler hakkında daha fazla bilgi için bkz. [PowerShellGet 'ı yükleme](/powershell/scripting/gallery/installing-psget).


1. PowerShell 'i yükseltilmiş bir yürütme ilkesiyle yönetici olarak çalıştırın.
2. NuGet paket sağlayıcısını yükler.
    - Açıklama: Bu sağlayıcının PowerShell Galerisi gibi NuGet tabanlı depolarla etkileşim kurması gerekir.
    - Başvuru: [Install-PackageProvider](/powershell/module/packagemanagement/install-packageprovider).
    - Komut: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201` .
    - İsteğe bağlı parametreler:
        - `-Proxy`. İstek için bir proxy sunucusu belirtir.
        - `-Force`. Onay istemi 'ni atlar.
    
    NuGet ayarlanmamışsa bu istemi alırsınız:

    ```output
    NuGet provider is required to continue
    PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. 
    The NuGet provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
    'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
    'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
    the NuGet provider now?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```    

3. PowerShell Galerisi güvenilir bir depo olarak yapılandırın.
    - Açıklama: varsayılan olarak, PowerShell Galerisi güvenilmeyen bir depodur.
    - Başvuru: [set-PSRepository](/powershell/module/powershellget/set-psrepository).
    - Komut: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted` .
    - İsteğe bağlı parametre:
        - `-Proxy`. İstek için bir proxy sunucusu belirtir.

    PowerShell Galerisi güvenilmiyorsa bu istemi alırsınız:

    ```output
    Untrusted repository
    You are installing the modules from an untrusted repository. 
    If you trust this repository, change its InstallationPolicy value 
    by running the Set-PSRepository cmdlet. Are you sure you want to 
    install the modules from 'PSGallery'?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
    ```

    Bu değişikliği onaylayıp, komutunu çalıştırarak tüm Psdepolarında denetim yapabilirsiniz `Get-PSRepository` .

4. PowerShellGet 'in en yeni sürümünü yükler.
    - Açıklama: Bu modül, PowerShell Galerisi diğer modülleri almak için kullanılan araçları içerir. Sürüm 1.0.0.1, Windows 10 ve Windows Server ile birlikte gelir. Sürüm 1.6.0 veya üzeri gereklidir. Hangi sürümün yükleneceğini öğrenmek için `Get-Command -Module PowerShellGet` komutunu çalıştırın.
    - Başvuru: [PowerShellGet yükleniyor](/powershell/scripting/gallery/installing-psget).
    - Komut: `Install-Module -Name PowerShellGet` .
    - İsteğe bağlı parametreler:
        - `-Proxy`. İstek için bir proxy sunucusu belirtir.
        - `-Force`. "Önceden yüklenmiş" uyarısını atlar ve en son sürümü yüklüyor.

    PowerShellGet 'in en yeni sürümünü kullanmıyorsanız bu hatayı alırsınız:

    ```output
    Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
    At line:1 char:20
    Install-Module abc -AllowPrerelease
                   ~~~~~~~~~~~~~~~~
    CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
    FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    ```

5. PowerShell 'i yeniden başlatın. Geçerli oturumdaki yeni sürümü yükleyebilirsiniz. Yeni PowerShell oturumları PowerShellGet 'in en son sürümünü yükler.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Modül PowerShell Galerisi aracılığıyla indirin ve yükleyin

Bu adımlar, PowerShell Galerisi az. ApplicationMonitor modülünü indirir.

1. PowerShell Galerisi için tüm önkoşulların karşılandığından emin olun.
2. PowerShell 'i yükseltilmiş bir yürütme ilkesiyle yönetici olarak çalıştırın.
3. Az. ApplicationMonitor modülünü yükler.
    - Başvuru: [Install-Module](/powershell/module/powershellget/install-module).
    - Komut: `Install-Module -Name Az.ApplicationMonitor` .
    - İsteğe bağlı parametreler:
        - `-Proxy`. İstek için bir proxy sunucusu belirtir.
        - `-AllowPrerelease`. Alfa ve beta sürümlerinin yüklenmesine izin verir.
        - `-AcceptLicense`. "Lisansı kabul et" istemi atlanır
        - `-Force`. "Güvenilmeyen depo" uyarısını atlar.

## <a name="download-and-install-the-module-manually-offline-option"></a>Modülü el ile indir ve yükle (çevrimdışı seçenek)

PowerShell modülüne bağlanamazsınız herhangi bir nedenden dolayı, az. ApplicationMonitor modülünü el ile indirip yükleyebilirsiniz.

### <a name="manually-download-the-latest-nupkg-file"></a>En son nupkg dosyasını el ile indirin

1. https://www.powershellgallery.com/packages/Az.ApplicationMonitor öğesine gidin.
2. **Sürüm geçmişi** tablosunda dosyanın en son sürümünü seçin.
3. **Yükleme seçenekleri** altında **el ile indir**' i seçin.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Seçenek 1: PowerShell modülleri dizinine yüklensin
PowerShell oturumları tarafından keşfedilecek şekilde, el ile indirilen PowerShell modülünü bir PowerShell dizinine yükleyebilirsiniz.
Daha fazla bilgi için bkz. [PowerShell modülünü yükleme](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Expand-Archive kullanarak nupkg 'yi ZIP dosyası olarak sıkıştırmayı açın (v 1.0.1.0)

- Açıklama: Microsoft. PowerShell. Archive (v 1.0.1.0) öğesinin temel sürümü nupkg dosyalarını sıkıştırmasını açamıyor. Dosyayı. zip uzantısıyla yeniden adlandırın.
- Başvuru: [Expand-arşiv](/powershell/module/microsoft.powershell.archive/expand-archive).
- Komutundaki

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Expand-Archive kullanarak nupkg sıkıştırmasını açın (v 1.1.0.0)

- Açıklama: uzantıyı değiştirmeden nupkg dosyalarını açmak için Expand-Archive güncel bir sürümünü kullanın.
- Başvuru: [Expand-Archive](/powershell/module/microsoft.powershell.archive/expand-archive) ve [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Komutundaki

    ```console
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>2. seçenek: nupkg 'yı el ile ayıklayın ve içeri aktarın
PowerShell oturumları tarafından keşfedilecek şekilde, el ile indirilen PowerShell modülünü bir PowerShell dizinine yükleyebilirsiniz.
Daha fazla bilgi için bkz. [PowerShell modülünü yükleme](/powershell/scripting/developer/module/installing-a-powershell-module).

Modülü başka bir dizine yüklüyorsanız, [Import-Module](/powershell/module/microsoft.powershell.core/import-module)kullanarak modülü el ile içeri aktarın.

> [!IMPORTANT] 
> Dll 'Ler göreli yollar aracılığıyla yüklenir.
> Paketin içeriğini amaçlanan çalışma zamanı dizininizde depolayın ve erişim izinlerinin okuma izni verme ancak yazma izni olduğunu onaylayın.

1. Uzantıyı ". zip" olarak değiştirin ve paketin içeriğini amaçlanan yükleme dizininize ayıklayın.
2. Az.ApplicationMonitor.psd1 dosyasının yolunu bulun.
3. PowerShell 'i yükseltilmiş bir yürütme ilkesiyle yönetici olarak çalıştırın.
4. Komutunu kullanarak modülü yükleyin `Import-Module Az.ApplicationMonitor.psd1` .
    

## <a name="route-traffic-through-a-proxy"></a>Trafiği bir ara sunucu üzerinden yönlendirme

Özel intranetinizdeki bir bilgisayarı izlerken, HTTP trafiğini bir ara sunucu üzerinden yönlendirmenize gerek duyarsınız.

PowerShell Galerisi az. ApplicationMonitor indirme ve yükleme için PowerShell komutları bir `-Proxy` parametreyi destekler.
Yükleme betiklerinizi yazarken yukarıdaki yönergeleri gözden geçirin.

Application Insights SDK 'sının uygulamanızın telemetrisini Microsoft 'a gönderebilmesi gerekir. web.config dosyanızda uygulamanız için proxy ayarlarını yapılandırmanızı öneririz. Daha fazla bilgi için bkz. [APPLICATION INSIGHTS SSS: proxy geçişi](../faq.md#proxy-passthrough).


## <a name="enable-monitoring"></a>İzlemeyi etkinleştirme

`Enable-ApplicationInsightsMonitoring`İzlemeyi etkinleştirmek için komutunu kullanın.

Bu cmdlet 'in nasıl kullanılacağına ilişkin ayrıntılı bir açıklama için bkz. [API başvurusu](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring) .



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

- Application Insights Aracısı [sorunlarını gidermek](status-monitor-v2-troubleshoot.md) için kılavuzumuzu kullanın.


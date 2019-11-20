---
title: PowerShell kullanarak Azure Cloud Services tanılamayı etkinleştirme | Microsoft Docs
description: PowerShell kullanarak bulut hizmetleri için tanılamayı nasıl etkinleştireceğinizi öğrenin
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: gwallace
ms.openlocfilehash: f2b7e51971cc2e540ee7745b3b44571c58359613
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860221"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>PowerShell kullanarak Azure Cloud Services tanılamayı etkinleştirme
Uygulama günlükleri, performans sayaçları vb. gibi tanılama verilerini bir bulut hizmetinden Azure Tanılama uzantısını kullanarak toplayabilirsiniz. Bu makalede, PowerShell kullanarak bir bulut hizmeti için Azure Tanılama uzantısının nasıl etkinleştirileceği açıklanır.  Bu makale için gereken önkoşullar için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/overview) .

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Bulut Hizmeti dağıtımının bir parçası olarak tanılama uzantısını etkinleştirme
Bu yaklaşım, bulut hizmetini dağıtmanın bir parçası olarak tanılama uzantısının etkinleştirilebileceği, sürekli tümleştirme türü senaryolar için geçerlidir. Yeni bir bulut hizmeti dağıtımı oluştururken, *Extensionconfiguration* parametresini [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) cmdlet 'ine geçirerek tanılama uzantısını etkinleştirebilirsiniz. *Extensionconfiguration* parametresi, [New-Azurezervicediagnokılarsextensionconfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) cmdlet 'i kullanılarak oluşturulabilen bir dizi tanılama yapılandırması alır.

Aşağıdaki örnek, her birinin farklı bir tanılama yapılandırmasına sahip bir WebRole ve WorkerRole ile bulut hizmeti için tanılamayı nasıl etkinleştirekullanabileceğinizi gösterir.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Tanılama yapılandırma dosyası bir depolama hesabı adı `StorageAccount` olan bir öğe belirtiyorsa, `New-AzureServiceDiagnosticsExtensionConfig` cmdlet bu depolama hesabını otomatik olarak kullanacaktır. Bunun çalışması için, depolama hesabının dağıtılmakta olan bulut hizmeti ile aynı abonelikte olması gerekir.

Azure SDK 2,6 ' den itibaren, MSBuild yayımlama hedef çıktısı tarafından oluşturulan uzantı yapılandırma dosyaları, hizmet yapılandırma dosyasında (. cscfg) belirtilen tanılama yapılandırma dizesine göre depolama hesabı adını içerecektir. Aşağıdaki komut dosyası, bulut hizmetini dağıtma sırasında uzantı yapılandırma dosyalarını, hedef çıktısından Yayımla ve her rol için tanılama uzantısını yapılandırma ' yı nasıl ayrıştıracağınızı gösterir.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online, tanılama Uzantısı ile Cloud Services otomatik dağıtımları için benzer bir yaklaşım kullanır. Tüm örnek için bkz. [Publish-AzureCloudDeployment. ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) .

Tanılama yapılandırmasında belirtilmemişse, cmdlet 'e storageAccountName parametresini geçirmeniz gerekir. `StorageAccount` *StorageAccountName* parametresi belirtilmişse, cmdlet, her zaman parametresinde belirtilen depolama hesabını kullanır ve bu, tanılama yapılandırma dosyasında belirtilen bir değer değildir.

Tanılama depolama hesabı, bulut hizmetinden farklı bir abonelikte yer alıyorsa, cmdlet 'e *storageAccountName* ve *storageaccountkey* parametrelerini açıkça geçirmeniz gerekir. Tanılama depolama hesabı aynı abonelikte olduğunda *Storageaccountkey* parametresi gerekli değildir. cmdlet, tanılama uzantısını etkinleştirirken anahtar değerini otomatik olarak sorgulayabilir ve ayarlayabilir. Ancak, tanılama depolama hesabı farklı bir abonelikte ise, cmdlet anahtarı otomatik olarak alamaz ve anahtarı *Storageaccountkey* parametresi aracılığıyla açıkça belirtmeniz gerekir.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Mevcut bir Bulut Hizmetinde tanılama uzantısını etkinleştirme
Zaten çalışmakta olan bir bulut hizmetinde tanılama yapılandırmasını etkinleştirmek veya güncelleştirmek için [set-Azurezervicediagnokıkıma](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet 'ini kullanabilirsiniz.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Güncel tanılama uzantı yapılandırmasını alma
Bir bulut hizmeti için geçerli tanılama yapılandırmasını almak üzere [Get-Azurezervicediagnokıkagama](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet 'ini kullanın.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Tanılama uzantısını kaldırma
Bir bulut hizmetinde tanılamayı devre dışı bırakmak için, [Remove-Azurezervicediagnoyassextenma](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet 'ini kullanabilirsiniz.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Yapılandırma uzantısını, *set-Azurezervicediagnokısexten,* veya *New-Azurezervicediagnoçıkartsextensionconfig* kullanarak *rol* parametresi olmadan etkinleştirdiyseniz, bunu kullanarak *uzantıyı kaldırabilirsiniz. Rol parametresi olmadan-Azurezervicediagnoyapışsextenbir değeri kaldırın* . Uzantı etkinleştirilirken *rol* parametresi kullanılmışsa, uzantı kaldırılırken da kullanılması gerekir.

Tanılama uzantısını her bir rolden kaldırmak için:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Sonraki Adımlar
* Azure tanılama 'yı kullanma hakkında ek yönergeler ve sorunları gidermeye yönelik diğer teknikler için bkz. [azure Cloud Services ve sanal makinelerde tanılamayı etkinleştirme](cloud-services-dotnet-diagnostics.md).
* [Tanılama yapılandırma şeması](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) , tanılama uzantısı için çeşitli XML yapılandırmaları seçeneklerini açıklar.
* Sanal makineler için tanılama uzantısını nasıl etkinleştireceğinizi öğrenmek için bkz. [Azure Resource Manager şablonu kullanarak izleme ve tanılama Ile Windows sanal makinesi oluşturma](../virtual-machines/windows/extensions-diagnostics-template.md)

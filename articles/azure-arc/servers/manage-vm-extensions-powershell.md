---
title: Azure PowerShell kullanarak VM uzantısını etkinleştirme
description: Bu makalede, Azure PowerShell kullanarak karma bulut ortamlarında çalışan Azure Arc etkin sunucularına sanal makine uzantılarının nasıl dağıtılacağı açıklanır.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5723655b61040c7ddf99e5f11488fff379d96a0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832887"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell kullanarak Azure VM uzantılarını etkinleştirme

Bu makalede, Azure Arc etkin sunucuları tarafından desteklenen Azure VM uzantılarının Azure PowerShell kullanarak bir Linux veya Windows hibrit makinesine nasıl dağıtılacağı ve kaldırılacağı gösterilmektedir.

> [!NOTE]
> Azure yay özellikli sunucular, VM uzantılarının Azure sanal makinelerine dağıtılmasını ve yönetilmesini desteklemez. Azure VM 'Leri için aşağıdaki [VM uzantısına genel bakış](../../virtual-machines/extensions/overview.md) makalesine bakın.

## <a name="prerequisites"></a>Önkoşullar

- Azure PowerShell olan bir bilgisayar. Yönergeler için bkz. [Azure PowerShell yükleyip yapılandırma](/powershell/azure/).

Yay etkin sunucularla yönetilen karma sunucunuzda VM uzantılarını yönetmek için Azure PowerShell kullanmadan önce modülünü yüklemeniz gerekir `Az.ConnectedMachine` . Şu komutu yay etkin sunucunuzda çalıştırın:

`Install-Module -Name Az.ConnectedMachine`.

Yükleme tamamlandığında, aşağıdaki ileti döndürülür:

`The installed extension `Az. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Uzantıyı etkinleştir

Yay etkin sunucunuzda bir VM uzantısını etkinleştirmek için,,,, [](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) , `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` ,- `ExtensionType` ve `-Settings` parametreleriyle New-azconnectedmachineextension ' ı kullanın.

Aşağıdaki örnek, bir yay etkin Linux sunucusunda Log Analytics VM uzantısını sunar:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Bir yay etkin Windows Server üzerinde Log Analytics VM uzantısını etkinleştirmek için, `-ExtensionType` önceki örnekteki parametresinin değerini değiştirin `"MicrosoftMonitoringAgent"` .

Aşağıdaki örnek, bir yay etkin sunucusunda özel betik uzantısının etkinleştirilmesini sunar:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault VM Uzantısı (Önizleme)

> [!WARNING]
> PowerShell istemcileri genellikle `\` `"` üzerinde settings.js, akvvm_service hata vererek başarısız olmasına neden olacak. `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Aşağıdaki örnek, bir yay etkin sunucusunda Key Vault VM uzantısını (Önizleme) sunar:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Yüklü liste uzantıları

Yay etkin sunucunuzdaki VM uzantılarının listesini almak için [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) ile `-MachineName` ve `-ResourceGroupName` parametrelerini kullanın.

Örnek:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Yüklü bir uzantıyı kaldırma

Arc etkin sunucunuzdaki yüklü bir VM uzantısını kaldırmak için, ve parametreleriyle [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) kullanın `-Name` `-MachineName` `-ResourceGroupName` .

Örneğin, Linux için Log Analytics VM uzantısını kaldırmak için aşağıdaki komutu çalıştırın:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLI](manage-vm-extensions-cli.md)kullanarak, [Azure Portal](manage-vm-extensions-portal.md)veya [Azure Resource Manager şablonlarından](manage-vm-extensions-template.md)VM uzantılarını dağıtabilir, yönetebilir ve kaldırabilirsiniz.

- Sorun giderme bilgileri, [VM genişletmeleri sorunlarını gider kılavuzunda](troubleshoot-vm-extensions.md)bulunabilir.

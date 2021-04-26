---
title: Azure CLı kullanarak VM uzantısını etkinleştirme
description: Bu makalede, Azure CLı kullanarak hibrit bulut ortamlarında çalışan Azure Arc etkin sunucularına sanal makine uzantılarının nasıl dağıtılacağı açıklanır.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25e75ede30139201789cd86e6ebddda09a664eb4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388746"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure CLı kullanarak Azure VM uzantılarını etkinleştirme

Bu makalede, Azure Arc etkin sunucuları tarafından desteklenen VM uzantılarının Azure CLı kullanarak bir Linux veya Windows hibrit makinesine nasıl dağıtılacağı ve kaldırılacağı gösterilmektedir.

> [!NOTE]
> Azure yay özellikli sunucular, VM uzantılarının Azure sanal makinelerine dağıtılmasını ve yönetilmesini desteklemez. Azure VM 'Leri için aşağıdaki [VM uzantısına genel bakış](../../virtual-machines/extensions/overview.md) makalesine bakın.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Azure CLı uzantısını yükler

ConnectedMachine komutları Azure CLı 'nin bir parçası olarak gönderilmemektedir. Yay etkin sunucularla yönetilen karma sunucunuzda VM uzantılarını yönetmek için Azure CLı 'yı kullanmadan önce, ConnectedMachine uzantısını yüklemeniz gerekir. Almak için aşağıdaki komutu çalıştırın:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Uzantıyı etkinleştir

Yay etkin sunucunuzda bir VM uzantısını etkinleştirmek için [az connectedmachine Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) ,,,, `--machine-name` `--extension-name` `--location` `--type` `settings` ve `--publisher` parametreleriyle birlikte kullanın.

Aşağıdaki örnek, bir yay etkin sunucusunda Log Analytics VM uzantısını sunar:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

Aşağıdaki örnek, bir yay etkin sunucusunda özel betik uzantısının etkinleştirilmesini sunar:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Aşağıdaki örnek, bir yay etkin sunucusunda Key Vault VM uzantısını (Önizleme) sunar:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Yüklü liste uzantıları

Yay etkin sunucunuzdaki VM uzantılarının listesini almak için [az connectedmachine Extension List](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) öğesini `--machine-name` ve `--resource-group` parametrelerini kullanın.

Örnek:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Varsayılan olarak, Azure CLı komutlarının çıkışı JSON (JavaScript Nesne Gösterimi) ' dir. Örneğin, varsayılan çıktıyı bir liste veya tablo olarak değiştirmek için [az configure--output](/cli/azure/reference-index)kullanın. Ayrıca, `--output` Çıkış biçiminde tek bir zaman değişikliği için herhangi bir komuta ekleyebilirsiniz.

Aşağıdaki örnek, komutundan gelen kısmi JSON çıkışını gösterir `az connectedmachine extension -list` :

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Yüklü bir uzantıyı kaldırma

Arc etkin sunucunuzda yüklü bir VM uzantısını kaldırmak için [az connectedmachine Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) ile `--extension-name` `--machine-name` ve `--resource-group` parametrelerini kullanın.

Örneğin, Linux için Log Analytics VM uzantısını kaldırmak için aşağıdaki komutu çalıştırın:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal](manage-vm-extensions-portal.md)veya [Azure Resource Manager ŞABLONLARıNDAN](manage-vm-extensions-template.md) [Azure PowerShell](manage-vm-extensions-powershell.md)kullanarak VM uzantılarını dağıtabilir, yönetebilir ve kaldırabilirsiniz.

- Sorun giderme bilgileri, [VM genişletmeleri sorunlarını gider kılavuzunda](troubleshoot-vm-extensions.md)bulunabilir.

- Komutlar hakkında daha fazla bilgi için Azure CLı VM uzantısına [genel bakış](/cli/azure/ext/connectedmachine/connectedmachine/extension) makalesini gözden geçirin.

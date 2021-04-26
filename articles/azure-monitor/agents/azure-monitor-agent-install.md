---
title: Azure Izleyici aracısını yükler
description: Azure sanal makinelerine ve Azure Arc etkin sunucularına Azure Izleyici aracısını (AMA) yükleme seçenekleri.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: db9ad08f9a939a22e1e0e1cfba0537e6356394ed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731544"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Azure Izleyici aracısını (Önizleme) yükler
Bu makalede, Azure [izleyici aracısını](azure-monitor-agent-overview.md) hem Azure sanal makinelerine hem de Azure Arc özellikli sunuculara yüklemek için şu anda kullanılabilen farklı seçenekler ve ayrıca aracının hangi verileri toplayacağını tanımlayan [veri toplama kurallarıyla ilişkiler](data-collection-rule-azure-monitor-agent.md) oluşturma seçenekleri sunulmaktadır.

## <a name="prerequisites"></a>Önkoşullar
Azure Izleyici aracısını yüklemeden önce aşağıdaki Önkoşullar gereklidir.

- [Yönetilen sistem kimliğinin](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) Azure sanal makinelerinde etkinleştirilmesi gerekir. Bu, Azure Arc etkin sunucuları için gerekli değildir. Aracı, [Azure Portal kullanarak bir veri toplama kuralı oluşturma ve atama sürecinin bir](#install-with-azure-portal)parçası olarak yüklenirse, sistem kimliği otomatik olarak etkinleştirilir.
- [AzureResourceManager hizmet etiketi](../../virtual-network/service-tags-overview.md) , sanal makine için sanal ağda etkinleştirilmelidir.

> [!IMPORTANT]
> Azure Izleyici Aracısı Şu anda ağ proxy 'lerini desteklemiyor.

## <a name="virtual-machine-extension-details"></a>Sanal makine uzantısı ayrıntıları
Azure Izleyici Aracısı, aşağıdaki tablodaki ayrıntılarla bir [Azure VM Uzantısı](../../virtual-machines/extensions/overview.md) olarak uygulanır. Bu makalede açıklananlar dahil sanal makine uzantılarını yükleme yöntemlerinden herhangi biri kullanılarak yüklenebilir.

| Özellik | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. Monitor  | Microsoft. Azure. Monitor |
| Tür      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1,5 |


## <a name="install-with-azure-portal"></a>Azure portal ile yüklensin
Azure Izleyici aracısını Azure portal kullanarak yüklemek için Azure portal [bir veri toplama kuralı oluşturma](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) işlemini izleyin. Bu, veri toplama kuralını bir veya daha fazla Azure sanal makinesi veya Azure Arc etkin sunucularıyla ilişkilendirmenize olanak tanır. Aracı, zaten sahip olmayan bu sanal makinelerin herhangi birine yüklenir.


## <a name="install-with-resource-manager-template"></a>Kaynak Yöneticisi şablonuyla birlikte Install
Azure Izleyici aracısını Azure sanal makinelerine ve Azure Arc etkin sunucularına yüklemek ve veri toplama kurallarıyla bir ilişki oluşturmak için Kaynak Yöneticisi şablonlarını kullanabilirsiniz. İlişkilendirmeyi oluşturmadan önce herhangi bir veri toplama kuralı oluşturmanız gerekir.

Aracıyı yüklemek ve aşağıdaki ilişkiyi oluşturmak için örnek Şablonlar alın: 

- [Azure Izleyici Aracısı 'nı yüklemek için şablon (Azure ve Azure ARC)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Veri toplama kuralıyla ilişki oluşturmak için şablon](./resource-manager-data-collection-rules.md)

Aşağıdaki komutlar gibi [Kaynak Yöneticisi şablonlar için herhangi bir dağıtım yöntemi](../../azure-resource-manager/templates/deploy-powershell.md) kullanarak şablonları yükler.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>PowerShell ile Install
Azure Izleyici aracısını Azure sanal makinelerine ve Azure Arc etkin sunucularına, sanal makine uzantısı eklemek için PowerShell komutunu kullanarak yükleyebilirsiniz. 

### <a name="azure-virtual-machines"></a>Azure sanal makineleri
Azure Izleyici aracısını Azure sanal makinelerine yüklemek için aşağıdaki PowerShell komutlarını kullanın.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
---

### <a name="azure-arc-enabled-servers"></a>Azure Arc özellikli sunucular
Azure Izleyici Aracısı onAzure Arc etkin sunucularını yüklemek için aşağıdaki PowerShell komutlarını kullanın.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI’si
Azure Izleyici aracısını Azure sanal makinelerine ve Azure Arc etkin sunucularına, sanal makine uzantısı eklemek için Azure CLı komutunu kullanarak yükleyebilirsiniz. 

### <a name="azure-virtual-machines"></a>Azure sanal makineleri
Azure Izleyici aracısını Azure sanal makinelerine yüklemek için aşağıdaki CLı komutlarını kullanın.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Azure Arc özellikli sunucular
Azure Izleyici Aracısı onAzure Arc etkin sunucularını yüklemek için aşağıdaki CLı komutlarını kullanın.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Sonraki adımlar

- Aracıdan veri toplamak ve Azure Izleyici 'ye göndermek için [bir veri toplama kuralı oluşturun](data-collection-rule-azure-monitor-agent.md) .

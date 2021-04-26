---
title: Azure Ilkesi Konuk yapılandırma uzantısı
description: Sanal makineler içindeki ayarları denetlemek/yapılandırmak için kullanılan uzantı hakkında bilgi edinin
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368717"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Azure Ilkesi Konuk yapılandırması uzantısına genel bakış

Konuk yapılandırma uzantısı, sanal makineler içinde denetim ve yapılandırma işlemleri gerçekleştiren bir bileşen Azure Ilkesidir.
[Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) ve [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) için güvenlik temeli tanımları gibi ilkeler, uzantı yüklenene kadar makineler içindeki ayarları kontrol edemiyor.

## <a name="prerequisites"></a>Önkoşullar

Makinenin Konuk yapılandırma hizmetinde kimlik doğrulaması yapması için, makinenin [sistem tarafından atanmış bir yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md)olması gerekir.
Aşağıdaki özellik ayarlandıysa, bir sanal makinede kimlik gereksinimi karşılanır.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>İşletim Sistemleri

Konuk yapılandırma uzantısı için destek, [uçtan uca çözüm için belgelenen](../../governance/policy/concepts/guest-configuration.md#supported-client-types)işletim sistemi desteğiyle aynıdır.

### <a name="internet-connectivity"></a>İnternet bağlantısı

Konuk yapılandırma uzantısı tarafından yüklenen Aracı, Konuk yapılandırma atamaları tarafından listelenen içerik paketlerine erişebilmelidir ve durumu Konuk yapılandırma hizmetine rapor edebilir.
Makine, TCP bağlantı noktası 443 üzerinden giden HTTPS kullanarak veya özel ağ aracılığıyla bir bağlantı sağlandıysa bağlanabilir.
Özel ağ hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Konuk yapılandırması, Azure 'da özel bağlantı üzerinden iletişim kurma](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Azure depolama için özel uç noktaları kullanma](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Uzantıyı nasıl yükleyebilirim?

Uzantının en son sürümünü kimlik gereksinimleri dahil olmak üzere ölçekli olarak dağıtmak için, Azure Ilkesini atayın, [sanal makinelerde Konuk yapılandırma ilkelerini etkinleştirmek üzere önkoşulları dağıtın](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json).
Tek makineler için, uzantı Azure CLı, PowerShell, Kaynak Yöneticisi şablonları veya üçüncü taraf araçları kullanılarak dağıtılabilir.

Yukarıda başvurulan ilkeler bu özel dizeleri gerektirdiğinden, uzantının örnek adı "AzurePolicyforWindows" veya "AzurePolicyforLinux" olarak ayarlanmalıdır.

Varsayılan olarak, tüm dağıtımlar en son sürüme günceldir. Aksi belirtilmediği takdirde, _oto Upgrademinorversion_ özelliğinin değeri "true" olarak ayarlanır. Uzantının yeni sürümleri yayınlandığında kodunuzun güncelleştirilmesi konusunda endişelenmeniz gerekmez.

### <a name="azure-cli"></a>Azure CLI

Linux uzantısını dağıtmak için:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Windows uzantısını dağıtmak için:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Linux uzantısını dağıtmak için:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Windows uzantısını dağıtmak için:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager şablonu

Linux uzantısını dağıtmak için:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Windows uzantısını dağıtmak için:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Linux uzantısını dağıtmak için:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

Windows uzantısını dağıtmak için:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Ayarlar

Uzantıya herhangi bir ayar veya korumalı ayar özellikleri eklemeniz gerekmez.
Bu tür bilgiler, [Konuk yapılandırması atama](/rest/api/guestconfiguration/guestconfigurationassignments) kaynaklarından aracı tarafından alınır. Örneğin, [ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [mod](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)ve [configurationsetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) özellikleri, sanal makine uzantısı yerine her yapılandırma başına yönetilmektedir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Ilkesi Konuk yapılandırması hakkında daha fazla bilgi için bkz. [Azure Ilkesinin Konuk yapılandırmasını anlama](../../governance/policy/concepts/guest-configuration.md)
* Linux aracısının ve uzantılarının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Linux Için Azure VM uzantıları ve özellikleri](features-linux.md).
* Windows Konuk aracısının ve uzantılarının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Windows Için Azure VM uzantıları ve özellikleri](features-windows.md).  
* Windows Konuk Aracısı 'nı yüklemek için bkz. [Azure Windows sanal makine aracısına genel bakış](agent-windows.md).  
* Linux Aracısı 'nı yüklemek için bkz. [Azure Linux sanal makine aracısına genel bakış](agent-linux.md).  

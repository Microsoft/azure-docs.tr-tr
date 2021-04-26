---
title: Azure sanal makine ölçek kümeleri ile uygulama durumu uzantısı 'nı kullanma
description: Sanal makine ölçek kümelerine dağıtılan uygulamalarınızın sistem durumunu izlemek için uygulama durumu uzantısının nasıl kullanılacağını öğrenin.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 381573ae40f6c31a1c7dbf18bc60be5944fff39e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762902"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleriyle Uygulama Sistem Durumu uzantısını kullanma
Uygulamanızın sistem durumunu izlemek, dağıtımınızı yönetmek ve yükseltmek için önemli bir sinyaldir. Azure sanal makine ölçek kümeleri, dağıtımınızı yükseltmek için ayrı örneklerin sistem durumu izlemesini kullanan [Otomatik işletim sistemi görüntüsü yükseltmeleri](virtual-machine-scale-sets-automatic-upgrade.md)dahil olmak üzere, çalışan [yükseltmeler](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) için destek sağlar. Ayrıca, ölçek kümesindeki her bir örneğin uygulama durumunu izlemek ve [Otomatik örnek onarımları](virtual-machine-scale-sets-automatic-instance-repairs.md)kullanarak örnek onarımları gerçekleştirmek için sistem durumu uzantısı ' nı da kullanabilirsiniz.

Bu makalede, sanal makine ölçek kümelerine dağıtılan uygulamalarınızın sistem durumunu izlemek için uygulama sistem durumu uzantısını nasıl kullanabileceğiniz açıklanır.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, hakkında bilgi sahibi olduğunuz varsayılmaktadır:
-   Azure sanal makine [uzantıları](../virtual-machines/extensions/overview.md)
-   Sanal makine ölçek kümelerini [değiştirme](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-the-application-health-extension"></a>Uygulama durumu uzantısının ne zaman kullanılacağı
Uygulama sistem durumu uzantısı bir sanal makine ölçek kümesi örneği içine dağıtılır ve VM sistem durumu üzerinde, ölçek kümesi örneği içinden raporlar. Uzantıyı bir uygulama uç noktasında araştırma yapmak ve bu örnekteki uygulamanın durumunu güncelleştirmek için yapılandırabilirsiniz. Bu örnek durumu, bir örneğin yükseltme işlemlerine uygun olup olmadığını öğrenmek için Azure tarafından denetlenir.

Uzantı, bir VM içinden sistem durumunu raporladığında, uzantı, uygulama sistem durumu Araştırmaları (özel Azure Load Balancer [yoklamaları](../load-balancer/load-balancer-custom-probe-overview.md)kullanan) gibi dış araştırmaların kullanılamaz durumda olabilir.

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON, uygulama sistem durumu uzantısının şemasını gösterir. Uzantı, sırasıyla ilişkili bağlantı noktası veya istek yoluyla en az bir "TCP", "http" veya "https" isteği gerektirir.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Özellik değerleri

| Name | Değer/örnek | Veri Türü
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| yayımcı | `Microsoft.ManagedServices` | string |
| tür | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Ayarlar

| Name | Değer/örnek | Veri Türü
| ---- | ---- | ----
| protokol | `http` or `https` veya `tcp` | string |
| port | Protokol veya olduğunda isteğe `http` bağlı `https` , protokol olduğunda zorunludur `tcp` | int |
| Istek yolu | Protokol olduğunda zorunlu `http` `https` , protokol ise izin verilmez `tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Uygulama durumu uzantısını dağıtma
Aşağıdaki örneklerde açıklandığı şekilde, uygulama durumu uzantısını ölçek kümelerinizi dağıtmanın birden çok yolu vardır.

### <a name="rest-api"></a>REST API

Aşağıdaki örnek, uygulama durumu uzantısını (myHealthExtension adlı) Windows tabanlı ölçek kümesinin ölçek kümesi modelinde extensionProfile öğesine ekler.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
`PATCH`Zaten dağıtılmış bir uzantıyı düzenlemek için kullanın.

### <a name="azure-powershell"></a>Azure PowerShell

Uygulama durumu uzantısını ölçek kümesi model tanımına eklemek için [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 'ini kullanın.

Aşağıdaki örnek, uygulama sistem durumu uzantısını `extensionProfile` Windows tabanlı ölçek kümesinin ölçek kümesi modelinde öğesine ekler. Örnek, yeni az PowerShell modülünü kullanır.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Ölçek kümesi model tanımına uygulama durumu uzantısı eklemek için [az VMSS Extension set](/cli/azure/vmss/extension#az_vmss_extension_set) komutunu kullanın.

Aşağıdaki örnek, uygulama durumu uzantısını Linux tabanlı ölçek kümesinin ölçek kümesi modeline ekler.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Dosya içeriğindeki extension.js.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Sorun giderme
Uzantı yürütme çıktısı, aşağıdaki dizinlerde bulunan dosyalara kaydedilir:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

Günlükler Ayrıca uygulamanın sistem durumunu düzenli olarak yakalar.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı sanal makine ölçek kümelerinde [dağıtmayı](virtual-machine-scale-sets-deploy-app.md) öğrenin.

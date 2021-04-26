---
title: Azure sanal makine ölçek kümeleri ile uzantı sıralamasını kullanma
description: Sanal makine ölçek kümelerinde birden çok uzantı dağıtıldığında uzantı sağlamayı nasıl sıraleyeceğinizi öğrenin.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1b5aea1f0f0101231408dc9ad7b57a30f2c86256
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788166"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Sanal makine ölçek kümelerinde dizi uzantısı sağlama
Azure sanal makine uzantıları, dağıtım sonrası yapılandırma ve yönetim, izleme, güvenlik ve daha fazlası gibi yetenekler sağlar. Üretim dağıtımları genellikle, istenen sonuçlara ulaşmak için VM örnekleri için yapılandırılmış birden çok uzantı birleşimini kullanır.

Bir sanal makinede birden çok uzantı kullanırken, aynı işletim sistemi kaynaklarını gerektiren uzantıların aynı anda bu kaynakları almaya çalışmalarından emin olmak önemlidir. Bazı uzantılar, ortam ayarları ve gizli dizileri gibi gerekli konfigürasyonları sağlamak için diğer uzantılara de bağımlıdır. Doğru sıralama ve sıralama olmadan, bağımlı uzantı dağıtımları başarısız olabilir.

Bu makalede, sanal makine ölçek kümelerinde VM örnekleri için yapılandırılacak uzantıları nasıl sıralarınızın ayrıntıları yer alabilir.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, hakkında bilgi sahibi olduğunuz varsayılmaktadır:
-   Azure sanal makine [uzantıları](../virtual-machines/extensions/overview.md)
-   Sanal makine ölçek kümelerini [değiştirme](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-extension-sequencing"></a>Uzantı sıralaması ne zaman kullanılır?
Uzantılar ölçek kümeleri için zorunlu değildir ve belirtilmemişse, Uzantılar bir ölçek kümesi örneği üzerinde herhangi bir sırada sağlanabilir.

Örneğin, ölçek kümesi modelinizde modelde belirtilen iki uzantısı (ExtensionA ve ExtensionB) varsa, aşağıdaki sağlama dizilerinden biri gerçekleşebilir:
-   ExtensionA-> ExtensionB
-   ExtensionB-> ExtensionA

Uygulamanız B uzantısı öncesinde her zaman sağlanacak şekilde bir uzantı gerektiriyorsa, bu makalede açıklandığı gibi uzantı sıralamasını kullanmanız gerekir. Uzantı sıralaması ile yalnızca bir sıra meydana gelir:
-   ExtensionA-> ExtensionB

Tanımlı bir sağlama dizisinde belirtilmemiş uzantılar, tanımlı bir sıra öncesinde, sonrasında veya sırasında dahil olmak üzere herhangi bir zamanda sağlanabilir. Uzantı sıralaması yalnızca belirli bir uzantının, başka bir belirli uzantıdan sonra sağlandığını belirtir. Modelde tanımlanmış başka bir uzantının sağlamasını etkilemez.

Örneğin, ölçek kümesi modelinizde üç uzantı vardır: model A, uzantı B ve uzantı C uzantısı ve C uzantısı, uzantısı bir sonrasında sağlanmak üzere ayarlandıysa, aşağıdaki sağlama dizilerinden biri gerçekleşebilir:
-   ExtensionA-> ExtensionC-> ExtensionB
-   ExtensionB-> ExtensionA-> ExtensionC
-   ExtensionA-> ExtensionB-> ExtensionC

Tanımlı uzantı sırası yürütülürken başka bir uzantının sağlanmadığından emin olmanız gerekiyorsa, ölçek kümesi modelinizdeki tüm uzantıları sıralamayı öneririz. Yukarıdaki örnekte, B uzantısı C uzantısı sonrasında yalnızca bir sıra gerçekleşebileceğini sağlamak üzere ayarlanabilir:
-   ExtensionA-> ExtensionC-> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Uzantı sıralamasını kullanma
Uzantı sağlamayı sıralamak için, ölçek kümesi modelindeki uzantı tanımını, uzantı adları dizisini kabul eden "provisionAfterExtensions" özelliğini içerecek şekilde güncelleştirmeniz gerekir. Özellik dizisi değerinde bahsedilen uzantılar, ölçek kümesi modelinde tam olarak tanımlanmalıdır.

### <a name="template-deployment"></a>Şablon dağıtımı
Aşağıdaki örnek, ölçek kümesinin üç uzantıya (ExtensionA, ExtensionB ve ExtensionC) sahip olduğu bir şablonu tanımlar ve bu tür uzantılar sırayla sağlanabilir:
-   ExtensionA-> ExtensionB-> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

"ProvisionAfterExtensions" özelliği uzantı adları dizisini kabul ettiğinden, yukarıdaki örnek, ExtensionC 'nin ExtensionA ve ExtensionB 'den sonra sağlanması, ancak ExtensionA ve ExtensionB arasında sıralama gerekli değildir. Aşağıdaki şablon bu senaryoya ulaşmak için kullanılabilir:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
Aşağıdaki örnek, bir ölçek kümesi modeline ExtensionC adlı yeni bir uzantı ekler. ExtensionC, zaten ölçek kümesi modelinde tanımlanmış olan ExtensionA ve ExtensionB üzerinde bağımlılıklara sahiptir.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Eğer ExtensionC, ölçek kümesi modelinde daha önce tanımlanmışsa ve bağımlılıklarını eklemek istiyorsanız, `PATCH` zaten dağıtılan uzantının özelliklerini düzenlemek için bir çalıştırabilirsiniz.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Varolan ölçek kümesi örneklerine yapılan değişiklikler bir sonraki [yükseltmeye](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)uygulanır.

### <a name="azure-powershell"></a>Azure PowerShell
Uygulama durumu uzantısını ölçek kümesi model tanımına eklemek için [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet 'ini kullanın. Uzantı sıralaması az PowerShell 1.2.0 veya üzeri kullanılmasını gerektirir.

Aşağıdaki örnek, [uygulama sistem durumu uzantısını](virtual-machine-scale-sets-health-extension.md) `extensionProfile` Windows tabanlı ölçek kümesinin ölçek kümesi modelinde öğesine ekler. Uygulama sistem durumu uzantısı, zaten ölçek kümesinde tanımlanmış olan [Özel Betik uzantısı](../virtual-machines/extensions/custom-script-windows.md)sağlandıktan sonra sağlanır.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Ölçek kümesi model tanımına uygulama durumu uzantısı eklemek için [az VMSS Extension set](/cli/azure/vmss/extension#az_vmss_extension_set) komutunu kullanın. Uzantı sıralaması için Azure CLı 2.0.55 veya üzeri kullanılması gerekir.

Aşağıdaki örnek, [uygulama sistem durumu uzantısını](virtual-machine-scale-sets-health-extension.md) Windows tabanlı ölçek kümesinin ölçek kümesi modeline ekler. Uygulama sistem durumu uzantısı, zaten ölçek kümesinde tanımlanmış olan [Özel Betik uzantısı](../virtual-machines/extensions/custom-script-windows.md)sağlandıktan sonra sağlanır.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Sorun giderme

### <a name="not-able-to-add-extension-with-dependencies"></a>Bağımlılıklar içeren uzantı eklenemiyor mu?
1. ProvisionAfterExtensions içinde belirtilen uzantıların ölçek kümesi modelinde tanımlandığından emin olun.
2. Ortaya çıkan döngüsel bağımlılıkların bulunmadığından emin olun. Örneğin, şu sıraya izin verilmiyor: ExtensionA-> ExtensionB-> ExtensionC-> ExtensionA
3. Bağımlılıkları olan tüm uzantıların "Özellikler" uzantısı altında "Ayarlar" özelliğine sahip olduğundan emin olun. Örneğin, ExtentionB 'nin ExtensionA 'dan sonra sağlanması gerekiyorsa, ExtensionA 'nın ExtensionA "Özellikler" altındaki "Ayarlar" alanına sahip olması gerekir. Uzantı gerekli ayarları zorunlu değilse, boş bir "Ayarlar" özelliği belirtebilirsiniz.

### <a name="not-able-to-remove-extensions"></a>Uzantılar kaldırılamıyor mu?
Kaldırılmakta olan uzantıların diğer uzantılar için provisionAfterExtensions altında listelenmediğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı sanal makine ölçek kümelerinde [dağıtmayı](virtual-machine-scale-sets-deploy-app.md) öğrenin.

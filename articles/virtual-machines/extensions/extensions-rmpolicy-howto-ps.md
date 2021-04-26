---
title: VM Uzantısı yüklemesini (Windows) kısıtlamak için Azure Ilkesini kullanma
description: Uzantı dağıtımlarını kısıtlamak için Azure Ilkesini kullanın.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/23/2018
ms.openlocfilehash: 0587c2af8a90ce362fa6243e9da8a05734f0d8ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559758"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Windows VM 'lerinde uzantıları yüklemeyi kısıtlamak için Azure Ilkesini kullanma

Windows sanal makinelerinizdeki belirli uzantıların kullanımını veya yüklenmesini engellemek isterseniz, bir kaynak grubu içindeki VM 'Ler için uzantıları kısıtlamak üzere PowerShell kullanarak bir Azure Ilke tanımı oluşturabilirsiniz. 

Bu öğretici, en son sürüme sürekli olarak güncellenen Cloud Shell içinde Azure PowerShell kullanır. 

 

## <a name="create-a-rules-file"></a>Bir kural dosyası oluşturma

Hangi uzantıların yüklenebileceğini kısıtlamak için, uzantıyı belirlemek için mantığı sağlamak üzere bir [kuralınız](../../governance/policy/concepts/definition-structure.md#policy-rule) olması gerekir.

Bu örnek, Azure Cloud Shell ' de bir kural dosyası oluşturarak ' Microsoft. COMPUTE ' tarafından yayımlanan uzantıların nasıl reddedileceğini gösterir, ancak PowerShell 'de yerel olarak çalışıyorsanız yerel bir dosya oluşturabilir ve yolu ($home/CloudDrive), makinenizde yerel dosyanın yoluyla değiştirebilirsiniz.

[Cloud Shell](https://shell.azure.com/powershell), şunu yazın:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Aşağıdaki. json dosyasını kopyalayıp dosyaya yapıştırın.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

İşiniz bittiğinde, **CTRL + O** tuşlarına basın ve dosyayı kaydetmek için **girin** . Dosyayı kapatmak ve çıkmak için **CTRL + X** tuşlarına basın.

## <a name="create-a-parameters-file"></a>Parametre dosyası oluşturma

Ayrıca, engellenecek uzantılar listesini geçirmek için kullanabileceğiniz bir yapı oluşturan [Parametreler](../../governance/policy/concepts/definition-structure.md#parameters) dosyasına ihtiyacınız vardır. 

Bu örnek, Cloud Shell VM 'Ler için bir parametre dosyası oluşturmayı gösterir, ancak PowerShell 'de yerel olarak çalışıyorsanız, yerel bir dosya oluşturabilir ve yolu ($home/CloudDrive), makinenizde yerel dosyanın yoluyla değiştirebilirsiniz.

[Cloud Shell](https://shell.azure.com/powershell), şunu yazın:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Aşağıdaki. json dosyasını kopyalayıp dosyaya yapıştırın.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "displayName": "Denied extension"
        }
    }
}
```

İşiniz bittiğinde, **CTRL + O** tuşlarına basın ve dosyayı kaydetmek için **girin** . Dosyayı kapatmak ve çıkmak için **CTRL + X** tuşlarına basın.

## <a name="create-the-policy"></a>İlkeyi oluşturma

İlke tanımı, kullanmak istediğiniz yapılandırmayı depolamak için kullanılan bir nesnedir. İlke tanımı, ilkeyi tanımlamak için kuralları ve parametreler dosyalarını kullanır. [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition) cmdlet 'ini kullanarak bir ilke tanımı oluşturun.

 İlke kuralları ve parametreleri, oluşturduğunuz ve bulut kabuğunuzda. JSON dosyaları olarak depoladığınız dosyalardır.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>İlke atama

Bu örnek, ilkeyi [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment)kullanarak bir kaynak grubuna atar. **Myresourcegroup** kaynak grubunda oluşturulan herhangi bir sanal makıne, VM erişim Aracısı veya özel Betik uzantıları 'nı yükleyemeyecektir. 

[Get-AzSubscription | kullanın ](/powershell/module/az.accounts/get-azsubscription)ABONELIK kimliğinizi örnekteki bir yerde kullanmak üzere almak için biçim tablosu cmdlet 'i.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>İlkeyi test etme

İlkeyi test etmek için VM erişimi uzantısını kullanmayı deneyin. Aşağıdakiler, "set-Azvmaccessextenma: ' myVMAccess ' kaynağının ilke tarafından izin vermedi." iletisini vererek başarısız olması gerekir. "

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Portalda parola değişikliği "ilke ihlali nedeniyle şablon dağıtımı başarısız oldu." hatasıyla başarısız olmalıdır. iletisi döndürmektedir.

## <a name="remove-the-assignment"></a>Atamayı kaldırma

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>İlkeyi kaldır

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [Azure İlkesi](../../governance/policy/overview.md).

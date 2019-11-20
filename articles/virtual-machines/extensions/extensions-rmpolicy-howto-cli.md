---
title: VM Uzantısı yüklemesini kısıtlamak için Azure Ilkesini kullanma
description: VM Uzantısı dağıtımlarını kısıtlamak için Azure Ilkesini kullanın.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 113736198f40510981c80909c862282fa07ac68d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073767"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Linux VM 'lerinde uzantıları yüklemeyi kısıtlamak için Azure Ilkesini kullanma

Linux sanal makinelerinizdeki belirli uzantıların kullanımını veya yüklenmesini engellemek isterseniz, bir kaynak grubu içindeki VM 'Ler için uzantıları kısıtlamak üzere CLı kullanarak bir Azure ilkesi oluşturabilirsiniz. 

Bu öğretici, en son sürüme sürekli olarak güncellenen Azure Cloud Shell içindeki CLı 'yi kullanır. Azure CLı 'yı yerel olarak çalıştırmak istiyorsanız, sürüm 2.0.26 veya üstünü yüklemeniz gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Bir kural dosyası oluşturma

Hangi uzantıların yüklenebileceğini kısıtlamak için, uzantıyı belirlemek için mantığı sağlamak üzere bir [kuralınız](../../governance/policy/concepts/definition-structure.md#policy-rule) olması gerekir.

Bu örnekte, Azure Cloud Shell ' de bir kural dosyası oluşturarak ' Microsoft. OSTCExtensions ' tarafından yayımlanan uzantıların yüklenmesini reddetme gösterilmektedir, ancak CLı 'de yerel olarak çalışıyorsanız, yerel bir dosya oluşturabilir ve yolu (~/CloudDrive), makinenizde yerel dosyanın yoluyla değiştirebilirsiniz.

[Bash Cloud Shell](https://shell.azure.com/bash)şunu yazın:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Aşağıdaki. json dosyasını kopyalayıp dosyaya yapıştırın.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

İşiniz bittiğinde **ESC** tuşuna basın ve ardından dosyayı kaydetmek ve kapatmak için **WQ** yazın.


## <a name="create-a-parameters-file"></a>Parametre dosyası oluşturma

Ayrıca, engellenecek uzantılar listesini geçirmek için kullanabileceğiniz bir yapı oluşturan [Parametreler](../../governance/policy/concepts/definition-structure.md#parameters) dosyasına ihtiyacınız vardır. 

Bu örnek, Cloud Shell Linux VM 'Ler için bir parametre dosyası oluşturmayı gösterir, ancak CLı 'de yerel olarak çalışıyorsanız yerel bir dosya oluşturabilir ve yolu (~/CloudDrive), makinenizde yerel dosyanın yoluyla değiştirebilirsiniz.

[Bash Cloud Shell](https://shell.azure.com/bash)şunu yazın:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Aşağıdaki. json dosyasını kopyalayıp dosyaya yapıştırın.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

İşiniz bittiğinde **ESC** tuşuna basın ve ardından dosyayı kaydetmek ve kapatmak için **WQ** yazın.

## <a name="create-the-policy"></a>İlkeyi oluşturma

İlke tanımı, kullanmak istediğiniz yapılandırmayı depolamak için kullanılan bir nesnedir. İlke tanımı, ilkeyi tanımlamak için kuralları ve parametreler dosyalarını kullanır. [Az Policy Definition Create](/cli/azure/role/assignment?view=azure-cli-latest)kullanarak ilke tanımını oluşturun.

Bu örnekte, kurallar ve parametreler, oluşturduğunuz ve bulut kabuğunuzda. JSON dosyaları olarak depoladığınız dosyalardır.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>İlkeyi ata

Bu örnek, [az Policy atama Create](/cli/azure/policy/assignment)kullanılarak ilkeyi bir kaynak grubuna atar. **Myresourcegroup** kaynak grubunda oluşturulan herhangi bir sanal makıne Linux VM erişimini veya Linux Için özel betik uzantılarını yükleyemeyecektir. İlkeyi atamadan önce kaynak grubunun mevcut olması gerekir.

Abonelik KIMLIĞINIZI örnekteki bir yerde kullanmak üzere almak için [az Account List](/cli/azure/account?view=azure-cli-latest) kullanın.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>İlkeyi test etme

Yeni bir VM oluşturup yeni bir kullanıcı eklemeye çalışırken ilkeyi test edin.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

VM erişimi uzantısını kullanarak **yenkullanıcı** adlı yeni bir kullanıcı oluşturmayı deneyin.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Atamayı kaldırma

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>İlkeyi kaldır

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure İlkesi](../../governance/policy/overview.md).
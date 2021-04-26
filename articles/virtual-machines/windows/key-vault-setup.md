---
title: PowerShell kullanarak Key Vault ayarlama
description: PowerShell kullanarak bir sanal makine ile kullanmak için Key Vault ayarlama.
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 124ab121186ff7c9047515f2ee1ff51cee1114b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557497"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Azure PowerShell kullanarak sanal makineler için Key Vault ayarlama

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager yığınında, gizlilikler/sertifikalar, Key Vault kaynak sağlayıcısı tarafından sağlanmış kaynaklar olarak modellenir. Key Vault hakkında daha fazla bilgi edinmek için bkz. [Azure Key Vault nedir?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Key Vault, Azure Resource Manager sanal makinelerle birlikte kullanılabilmesi için, Key Vault üzerindeki **Enabledfordeployment** özelliğinin true olarak ayarlanması gerekir. Bunu çeşitli istemcilerde yapabilirsiniz.
> 2. Key Vault sanal makineyle aynı abonelikte ve konumda oluşturulması gerekir.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>PowerShell kullanarak Key Vault ayarlama
PowerShell kullanarak bir Anahtar Kasası oluşturmak için bkz. [PowerShell kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](../../key-vault/secrets/quick-create-powershell.md).

Yeni Anahtar kasaları için bu PowerShell cmdlet 'ini kullanabilirsiniz:

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

Mevcut Anahtar kasaları için bu PowerShell cmdlet 'ini kullanabilirsiniz:

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>CLı kullanarak Key Vault ayarlama
Komut satırı arabirimini (CLı) kullanarak bir Anahtar Kasası oluşturmak için bkz. [CLI kullanarak Key Vault yönetme](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

CLı için, dağıtım ilkesini atamadan önce anahtar kasasını oluşturmanız gerekir. Aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

Daha sonra Key Vault şablon dağıtımıyla kullanmak üzere etkinleştirmek için aşağıdaki komutu çalıştırın:

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>Şablonları kullanarak Key Vault ayarlama
Şablon kullanırken, `enabledForDeployment` Key Vault kaynağı için özelliğini olarak ayarlamanız gerekir `true` .

```config
{
  "type": "Microsoft.KeyVault/vaults",
  "name": "ContosoKeyVault",
  "apiVersion": "2015-06-01",
  "location": "<location-of-key-vault>",
  "properties": {
    "enabledForDeployment": "true",
    ....
    ....
  }
}
```

Şablonları kullanarak bir Anahtar Kasası oluştururken yapılandırabileceğiniz diğer seçenekler için bkz. [Anahtar Kasası oluşturma](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

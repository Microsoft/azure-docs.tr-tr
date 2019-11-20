---
title: Azure Resource Manager 'de Windows VM 'Leri için Key Vault ayarlama
description: Azure Resource Manager bir sanal makine ile kullanmak için Key Vault ayarlama.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038202"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Azure Resource Manager sanal makineler için Key Vault ayarlama

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager yığınında, gizlilikler/sertifikalar, Key Vault kaynak sağlayıcısı tarafından sağlanmış kaynaklar olarak modellenir. Key Vault hakkında daha fazla bilgi edinmek için bkz. [Azure Key Vault nedir?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. Key Vault, Azure Resource Manager sanal makinelerle birlikte kullanılabilmesi için, Key Vault üzerindeki **Enabledfordeployment** özelliğinin true olarak ayarlanması gerekir. Bunu çeşitli istemcilerde yapabilirsiniz.
> 2. Key Vault sanal makineyle aynı abonelikte ve konumda oluşturulması gerekir.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>PowerShell kullanarak Key Vault ayarlama
PowerShell kullanarak bir Anahtar Kasası oluşturmak için bkz. [PowerShell kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma](../../key-vault/quick-create-powershell.md).

Yeni Anahtar kasaları için bu PowerShell cmdlet 'ini kullanabilirsiniz:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Mevcut Anahtar kasaları için bu PowerShell cmdlet 'ini kullanabilirsiniz:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>CLı kullanarak Key Vault ayarlama
Komut satırı arabirimini (CLı) kullanarak bir Anahtar Kasası oluşturmak için bkz. [CLI kullanarak Key Vault yönetme](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

CLı için, dağıtım ilkesini atamadan önce anahtar kasasını oluşturmanız gerekir. Aşağıdaki komutu kullanarak bunu yapabilirsiniz:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Daha sonra Key Vault şablon dağıtımıyla kullanmak üzere etkinleştirmek için aşağıdaki komutu çalıştırın:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Şablonları kullanarak Key Vault ayarlama
Şablon kullanırken, Key Vault kaynağı için `enabledForDeployment` özelliğini `true` olarak ayarlamanız gerekir.

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

Şablonları kullanarak bir Anahtar Kasası oluştururken yapılandırabileceğiniz diğer seçenekler için bkz. [Anahtar Kasası oluşturma](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

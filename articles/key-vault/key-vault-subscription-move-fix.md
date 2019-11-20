---
title: Abonelik taşıma-Azure Key Vault sonrasında Anahtar Kasası kiracı KIMLIĞINI değiştirme | Microsoft Docs
description: Abonelik farklı bir kiracıya taşındıktan sonra anahtar kasasına ilişkin kiracı kimliğini nasıl değiştireceğinizi öğrenin
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: bc2e73d037b05c012002d7a07e2a2af2431423fa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428916"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Abonelik taşıma işlemi sonrasında anahtar kasası kiracı kimliğini değiştirme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Abonelikte yeni bir anahtar kasası oluşturduğunuzda, kasa bu abonelik için varsayılan Azure Active Directory kiracı kimliğine otomatik olarak bağlanır. Tüm erişim ilkesi girdileri de bu kiracı kimliğine bağlanır. 

Azure aboneliğinizi A kiracısından B kiracısına taşıdığınızda mevcut anahtar kasalarınız, B kiracısındaki sorumlular (kullanıcılar ve uygulamalar) tarafından erişilemez. Bu sorunu onarmak için şunları yapmanız gerekir:

* Abonelikte var olan tüm anahtar kasalarıyla ilişkili kiracı KIMLIĞINI B kiracısı ile değiştirin.
* Mevcut tüm erişim ilkesi girdilerini kaldırın.
* B kiracısı ile ilişkili yeni erişim ilkesi girdileri ekleme.

Örneğin, A kiracısından B kiracısına taşınan bir abonelikte ' mykasa' Anahtar Kasası varsa, kiracı KIMLIĞINI değiştirmek ve eski erişim ilkelerini kaldırmak için Azure PowerShell kullanabilirsiniz.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Azure CLı 'yi de kullanabilirsiniz.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Artık kasanız doğru kiracı KIMLIĞIYLE ilişkilendirildiğinden ve eski erişim ilkesi girdileri kaldırıldığına göre, Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet 'ı veya Azure CLI [az keykasası Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) komutunu kullanarak yeni erişim ilkesi girdileri ayarlayın.

Azure kaynakları için yönetilen bir kimlik kullanıyorsanız, bunu yeni Azure AD kiracısıyla de güncelleştirmeniz gerekir. Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [yönetilen kimlik ile Key Vault kimlik doğrulaması sağlama](managed-identity.md).


MSI kullanıyorsanız, eski kimlik artık doğru AAD kiracısında yer kalmadığında MSI kimliğini de güncelleştirmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Anahtar Kasası ile ilgili sorularınız varsa bkz. [Azure Anahtar Kasası Forumları](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

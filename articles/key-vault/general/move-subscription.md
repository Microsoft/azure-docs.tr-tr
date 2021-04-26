---
title: Kasayı farklı bir aboneliğe taşımak Azure Key Vault | Microsoft Docs
description: Anahtar kasasını farklı bir aboneliğe taşıma Kılavuzu.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 1a1cd8c051f9e04c09ef2986805873d8e7fea54e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817639"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Azure Key Vault'u başka bir aboneliğe taşıma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış

> [!IMPORTANT]
> **Anahtar kasasının başka bir aboneliğe taşınması ortamınızda bir değişikliğe neden olur.**
> Bu değişikliğin etkisini anladığınızdan emin olun ve anahtar kasasını yeni bir aboneliğe taşımaya karar vermeden önce bu makaledeki yönergeleri dikkatle izleyin.
> Yönetilen hizmet kimlikleri (MSI) kullanıyorsanız lütfen belgenin sonundaki taşıma sonrası yönergeleri okuyun. 

[Azure Key Vault](overview.md) , oluşturulduğu aboneliğin varsayılan [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) kiracı kimliğine otomatik olarak bağlanır. Bu [Kılavuzu](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)izleyerek, aboneliğinizle ILIŞKILI Kiracı kimliğini bulabilirsiniz. Tüm erişim ilkesi girişleri ve rol atamaları Bu kiracı KIMLIĞINE de bağlıdır.  Azure aboneliğinizi A kiracısından B kiracısına taşıdığınızda mevcut anahtar kasalarınız, B kiracısındaki hizmet sorumluları (kullanıcılar ve uygulamalar) tarafından erişilemez olur. Bu sorunu onarmak için şunları yapmanız gerekir:

* Abonelikte var olan tüm anahtar kasalarıyla ilişkili kiracı KIMLIĞINI B kiracısı ile değiştirin.
* Mevcut tüm erişim ilkesi girdilerini kaldırın.
* B kiracısı ile ilişkili yeni erişim ilkesi girdileri ekleme.

Azure Key Vault ve Azure Active Directory hakkında daha fazla bilgi için bkz.
- [Azure Key Vault hakkında](overview.md)
- [Azure Active Directory nedir?](../../active-directory/fundamentals/active-directory-whatis.md)
- [Kiracı kimliğini bulma](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Sınırlamalar

> [!IMPORTANT]
> **Disk şifrelemesi için kullanılan anahtar kasaları taşınamaz** Bir VM için disk şifrelemesi ile Anahtar Kasası kullanıyorsanız, disk şifrelemesi etkinken Anahtar Kasası farklı bir kaynak grubuna veya aboneliğe taşınamaz. Anahtar kasasını yeni bir kaynak grubuna veya aboneliğe taşımadan önce disk şifrelemesini devre dışı bırakmanız gerekir. 

Bazı hizmet sorumluları (kullanıcılar ve uygulamalar) belirli bir kiracıya bağlanır. Anahtar kasanızı başka bir Kiracıdaki bir aboneliğe taşırsanız, belirli bir hizmet sorumlusuna erişimi geri yükleyemeyeceksiniz. Anahtar kasanızı taşıdığınız kiracıda tüm gerekli hizmet sorumlularının mevcut olduğundan emin olun.

## <a name="prerequisites"></a>Önkoşullar

* [Katılımcı](../../role-based-access-control/built-in-roles.md#contributor) düzeyinde, anahtar kasanızın bulunduğu geçerli aboneliğe erişimi veya daha yükseği. [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)veya [PowerShell](../../role-based-access-control/role-assignments-powershell.md)kullanarak rol atayabilirsiniz.
* [Katılımcı](../../role-based-access-control/built-in-roles.md#contributor) düzeyi, anahtar kasanızı taşımak istediğiniz aboneliğe erişim veya daha yüksek. [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)veya [PowerShell](../../role-based-access-control/role-assignments-powershell.md)kullanarak rol atayabilirsiniz.
* Yeni abonelikte bir kaynak grubu. [Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md), [POWERSHELL](../../azure-resource-manager/management/manage-resource-groups-powershell.md)veya [Azure CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md)kullanarak bir tane oluşturabilirsiniz.

[Azure Portal](../../role-based-access-control/role-assignments-list-portal.md), [POWERSHELL](../../role-based-access-control/role-assignments-list-powershell.md), [Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)veya [REST API](../../role-based-access-control/role-assignments-list-rest.md)kullanarak mevcut rolleri kontrol edebilirsiniz.


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Anahtar kasasını yeni bir aboneliğe taşıma

1. https://portal.azure.com adresinden Azure portalında oturum açın.
2. [Anahtar kasanıza](overview.md) gidin
3. "Genel bakış" sekmesine tıklayın
4. "Taşı" düğmesini seçin
5. Açılan menüden "başka aboneliğe geçme" seçeneğini belirleyin
6. Anahtar kasanızı taşımak istediğiniz kaynak grubunu seçin
7. Kaynakları taşıma hakkında uyarı bildirimi
8. "Tamam"ı seçin

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Abonelik yeni bir kiracıda olduğunda ek adımlar

Anahtar kasanızı yeni bir Kiracıdaki bir aboneliğe taşıdıysanız, kiracı KIMLIĞINI el ile güncelleştirmeniz ve eski erişim ilkelerini ve rol atamalarını kaldırmanız gerekir. PowerShell ve Azure CLı 'deki bu adımlarla ilgili öğreticiler aşağıda verilmiştir. PowerShell kullanıyorsanız, geçerli seçili kapsamınız dışında kaynakları görmenizi sağlamak için aşağıda belgelenen Clear-AzContext komutunu çalıştırmanız gerekebilir. 

### <a name="update-tenant-id-in-a-key-vault"></a>Anahtar kasasında kiracı KIMLIĞINI güncelleştirme

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Erişim ilkelerini ve rol atamalarını güncelleştirme

> [!NOTE]
> Key Vault [Azure RBAC](../../role-based-access-control/overview.md) izin modeli kullanıyorsa. Anahtar Kasası rol atamalarını da kaldırmanız gerekir. Rol atamalarını [Azure portalı](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)veya [PowerShell](../../role-based-access-control/role-assignments-powershell.md)kullanarak kaldırabilirsiniz. 

Artık kasanız doğru kiracı KIMLIĞIYLE ilişkilendirildiğinden ve eski erişim ilkesi girdileri ya da rol atamaları kaldırıldıktan sonra, yeni erişim ilkesi girişleri veya rol atamaları ayarlayın.

İlke atamak için bkz.:
- [Portal kullanarak erişim ilkesi atama](assign-access-policy-portal.md)
- [Azure CLı kullanarak bir erişim ilkesi atama](assign-access-policy-cli.md)
- [PowerShell kullanarak bir erişim ilkesi atama](assign-access-policy-powershell.md)

Rol atamaları eklemek için bkz.:
- [Azure portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)
- [Azure CLı kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-cli.md)
- [PowerShell kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Yönetilen kimlikleri Güncelleştir

Tüm aboneliği aktarıyorsanız ve Azure kaynakları için yönetilen bir kimlik kullanıyorsanız, bunu yeni Azure Active Directory kiracısında da güncelleştirmeniz gerekecektir. Yönetilen kimlikler hakkında daha fazla bilgi için, [yönetilen kimliğe genel bakış](../../active-directory/managed-identities-azure-resources/overview.md).

Yönetilen kimlik kullanıyorsanız, eski kimlik artık doğru Azure Active Directory kiracısında yer lamadığından kimliği de güncelleştirmeniz gerekir. Bu sorunu gidermeye yardımcı olması için aşağıdaki belgelere bakın. 

* [MSI güncelleştiriliyor](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Aboneliği yeni dizine aktar](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Anahtarlar, gizli diziler ve sertifikalar](about-keys-secrets-certificates.md) hakkında daha fazla bilgi edinin
- Key Vault günlüklerinin nasıl yorumlanacağı dahil olmak üzere kavramsal bilgiler için bkz. [Key Vault günlüğe kaydetme](logging.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)
- [Azure Key Vault güvenlik özellikleri](security-features.md)
- [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](network-security.md)
---
title: Temsilci erişimini kaldırma
description: Azure açık Thouse için bir hizmet sağlayıcısına temsilci atanmış kaynaklara erişimi kaldırmayı öğrenin.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: c53b678ba6e37ece1bcaf2860abceb9eea980532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555789"
---
# <a name="remove-access-to-a-delegation"></a>Temsilci erişimini kaldırma

Bir müşterinin aboneliği veya kaynak grubu, [Azure açık kullanım](../overview.md)için bir hizmet sağlayıcısına temsilci seçildikten sonra, gerekirse temsilci kaldırılabilir. Bir temsilci kaldırıldıktan sonra, daha önce hizmet sağlayıcı kiracısındaki kullanıcılara verilen [Azure tarafından atanan kaynak yönetimi](../concepts/azure-delegated-resource-management.md) erişimi artık uygulanmaz.

Bir temsilciyi kaldırmak, Kullanıcı uygun izinlere sahip olduğu sürece müşteri kiracısında veya hizmet sağlayıcısı kiracısında bir kullanıcı tarafından yapılabilir.

> [!TIP]
> Bu konudaki hizmet sağlayıcılarına ve müşterilere başvurduğumuz halde, [birden çok kiracıyı yöneten kuruluşlar](../concepts/enterprise.md) aynı işlemlerin aynısını kullanabilir.

## <a name="customers"></a>Müşteriler

Sahibi gibi bir rolü olan müşterinin kiracısındaki kullanıcılar, `Microsoft.Authorization/roleAssignments/write` Bu aboneliğe (veya söz konusu [](../../role-based-access-control/built-in-roles.md#owner)abonelikteki kaynak gruplarına) hizmet sağlayıcı erişimini kaldırabilir. Bunu yapmak için Kullanıcı Azure portal [hizmet sağlayıcılar sayfasına](view-manage-service-providers.md#add-or-remove-service-provider-offers) gidebilir, **servis sağlayıcı teklifleri** ekranında teklifi bulabilir ve bu teklifin satırındaki çöp kutusu simgesini seçebilirsiniz.

Silme işlemini onayladıktan sonra, hizmet sağlayıcısının kiracısındaki hiçbir Kullanıcı daha önce atanmış kaynaklara erişemeyecektir.

## <a name="service-providers"></a>Hizmet sağlayıcıları

Bir yönetim kiracısındaki kullanıcılar, müşterinin kaynakları için [yönetilen hizmetler kayıt atama silme rolü](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) verildiyse, temsilcili kaynaklara erişimi kaldırabilir. Bu rol herhangi bir hizmet sağlayıcı kullanıcısına atanmamışsa, temsili yalnızca müşterinin kiracısındaki bir kullanıcı tarafından kaldırılabilir.

Aşağıdaki örnekte, [ekleme işlemi](onboard-customer.md)sırasında bir parametre dosyasına dahil edilebilir **yönetilen hizmetler kayıt ataması silme rolünü** veren bir atama gösterilmektedir:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Bu rol, Azure Market 'te yayımlanacak [bir yönetilen hizmet teklifi oluştururken](../../marketplace/plan-managed-service-offer.md) de bir **yetkilendirmede** seçilebilir.

Bu izne sahip bir Kullanıcı, aşağıdaki yollarla bir temsilciyi kaldırabilir.

### <a name="azure-portal"></a>Azure portalı

1. [Müşterilerimiz sayfasına](view-manage-customers.md)gidin.
2. **Temsilciler**' ı seçin.
3. Kaldırmak istediğiniz temsilciyi bulun, sonra satırında görüntülenen çöp kutusu simgesini seçin.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure tarafından atanan temsilcinin kaynak yönetimi](../concepts/azure-delegated-resource-management.md) hakkında bilgi edinin.
- Azure portal **müşterilerime** giderek [müşterileri görüntüleyin ve yönetin](view-manage-customers.md) .
- [Önceki bir temsilciyi güncelleştirme](update-delegation.md)hakkında bilgi edinin.

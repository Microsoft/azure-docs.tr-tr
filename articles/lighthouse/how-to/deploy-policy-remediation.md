---
title: Düzeltilebilir bir ilke dağıtma
description: Azure 'un Temsilcili kaynak yönetimine nasıl bir müşteri ekleneceğini ve bunların kendi kiracınız aracılığıyla kaynaklarına erişilmesine ve yönetilmesine izin vermeyi öğrenin.
ms.date: 10/11/2019
ms.topic: overview
ms.openlocfilehash: 662daeb305856fb36bfb84f98e80bedf48b22756
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132479"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Temsilci bir abonelik içinde düzeltilebilir bir ilke dağıtın

[Azure ışıklı kullanım](../overview.md) , hizmet sağlayıcılarının, temsilci bir abonelik içinde ilke tanımları oluşturmalarına ve düzenlemesine izin verir. Ancak, bir [Düzeltme görevi](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) (yani, [Deployifnotexists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) veya [değişiklik](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify) etkisi olan ilkeler) kullanan ilkeleri dağıtmak için, müşteri kiracısında [yönetilen bir kimlik](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) oluşturmanız gerekir. Bu yönetilen kimlik, şablonu ilke içinde dağıtmak için Azure Ilkesi tarafından kullanılabilir. Bu senaryoyu etkinleştirmek için, hem Azure tarafından atanan kaynak yönetimine ilişkin müşteriyi eklediğinizde hem de ilkeyi dağıtırken gereken adımlar vardır.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Müşteri kiracısında yönetilen bir kimliğe roller atayabilecek bir kullanıcı oluşturun

Azure tarafından atanan kaynak yönetimi için bir müşteri eklediğinizde, yönetim kiracınızdaki kullanıcıları, Kullanıcı gruplarını ve hizmet sorumlularını, müşteri kiracısında Temsilcili kaynaklara erişebilecek şekilde tanımlayan bir parametreler dosyası ile birlikte bir [Azure Resource Manager şablonu](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template) kullanırsınız. Parametreleriniz dosyasında, bu kullanıcıların (**PrincipalId**) her birine erişim düzeyini tanımlayan [yerleşik bir rol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**roledefinitionıd**) atanır.

**PrincipalId** 'nin müşteri kiracısında yönetilen bir kimlik oluşturmasına izin vermek Için, **roledefinitionıd** değerini **Kullanıcı erişimi Yöneticisi**olarak ayarlamanız gerekir. Bu rol genellikle desteklenmekle birlikte, bu özel senaryoda kullanılabilir ve bu izne sahip kullanıcıların yönetilen kimliklere bir veya daha fazla yerleşik rol atamasını sağlar. Bu roller, **Delegatedrotadefinitionıds** özelliğinde tanımlanmıştır. Kullanıcı erişimi Yöneticisi veya sahibi dışında, burada herhangi bir yerleşik rolü ekleyebilirsiniz.

Müşteri eklendi olduktan sonra, bu yetkilendirmede oluşturulan **PrincipalId** , bu yerleşik rolleri müşteri kiracısındaki yönetilen kimliklere atayabilecektir. Ancak, normalde Kullanıcı erişimi Yöneticisi rolüyle ilişkili başka hiçbir izinleri olmayacaktır.

Aşağıdaki örnekte, Kullanıcı erişimi yönetici rolüne sahip olacak bir **PrincipalId** gösterilmektedir. Bu Kullanıcı, müşteri kiracısında yönetilen kimliklere iki yerleşik rol atayabilecektir: katkıda bulunan ve Log Analytics katkıda bulunan.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Düzeltilenebilir ilkeleri dağıtma

Yukarıda açıklanan izinlerle Kullanıcı oluşturduktan sonra, bu kullanıcı, müşteri kiracısında düzeltme görevleri kullanan ilkeler dağıtabilir.

Örneğin, bu [örnekte](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring)gösterildiği gibi, müşteri kiracısındaki Azure Key Vault kaynaklarında tanılamayı etkinleştirmek istediğinizi varsayalım. Yönetim kiracısındaki bir kullanıcı uygun izinlerle (yukarıda açıklandığı gibi), bu senaryoyu etkinleştirmek için bir [Azure Resource Manager şablonu](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) dağıtır.

Temsilci olarak kullanılacak ilke atamasının, Azure portal değil, API 'Ler aracılığıyla yapılması gerektiğini unutmayın. Bunu yaparken, **Apiversion** 'ın yeni **Delegatedmanagedıdentityresourceıd** özelliğini içeren **2019-04-01-Preview**olarak ayarlanması gerekir. Bu özellik, müşteri kiracısında bulunan yönetilen bir kimliği (Azure tarafından atanan kaynak yönetimine eklendi bir abonelik veya kaynak grubu) eklemenize olanak tanır.

Aşağıdaki örnek, bir **Delegatedmanagedıdentityresourceıd**ile bir rol atamasını gösterir.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> [Benzer bir örnek](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) , bir etiketi (değiştirme efektini kullanarak) temsilci bir aboneliğe ekleyen veya kaldıran bir ilkenin nasıl dağıtılacağını göstermek için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilkesi](https://docs.microsoft.com/azure/governance/policy/)hakkında bilgi edinin.
- [Azure kaynakları için Yönetilen kimlikler](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)hakkında bilgi edinin.

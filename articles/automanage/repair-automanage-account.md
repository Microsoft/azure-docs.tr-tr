---
title: Bozuk bir Azure oto Yönet hesabını onarma
description: Kısa süre önce yeni bir kiracıya bir oto Yönet hesabı içeren bir abonelik taşıdıysanız, yeniden yapılandırmanız gerekir. Bu makalede, nasıl yapılacağını öğreneceksiniz.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e6bf5404a33e0b4e57c2ff8d82d8791eda3d0f06
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834201"
---
# <a name="repair-an-automanage-account"></a>Bir oto Yönet hesabını onarma
[Azure otomatik Yönet hesabınız](./automanage-virtual-machines.md#automanage-account) , otomatik işlemlerin oluştuğu güvenlik bağlamına veya kimliğe sahiptir. Kısa süre önce yeni bir kiracıya bir oto Yönet hesabı içeren bir abonelik taşıdıysanız, hesabı yeniden yapılandırmanız gerekir. Yeniden yapılandırmak için kimlik türünü sıfırlamanız ve hesap için uygun rolleri atamanız gerekir.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>1. Adım: oto Yönet hesabı kimlik türünü sıfırlayın
Aşağıdaki Azure Resource Manager (ARM) şablonunu kullanarak, hesap kimliği türünü, oto Yönet ' i sıfırlayın. Dosyayı armdeploy.jsolarak yerel olarak veya benzer bir adla kaydedin. ARM şablonunda gerekli parametreler olduklarından, hesap adını ve konumunu, sizin için bir daha göz önüne alın.

1. Aşağıdaki şablonu kullanarak bir Kaynak Yöneticisi dağıtımı oluşturun. `identityType = None` komutunu kullanın.
    * Kullanarak Azure CLı 'de dağıtımı oluşturabilirsiniz `az deployment sub create` . Daha fazla bilgi için bkz. [az Deployment Sub](/cli/azure/deployment/sub).
    * PowerShell 'de, modülünü kullanarak dağıtım oluşturabilirsiniz `New-AzDeployment` . Daha fazla bilgi için bkz. [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Aynı ARM şablonunu ile yeniden çalıştırın `identityType = SystemAssigned` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>2. Adım: oto Yönet hesabı için uygun rolleri atama
Oto Yönet hesabı, bir abonelik üzerinde, oto tarafından yönetilecek VM 'Leri içeren katkıda bulunan ve kaynak Ilkesi katılımcısı rollerinin yapılmasını gerektirir. Azure portal, ARM şablonları veya Azure CLı kullanarak bu rolleri atayabilirsiniz.

ARM şablonu veya Azure CLı kullanıyorsanız, oto Yönet hesabınızın asıl KIMLIĞI (nesne KIMLIĞI olarak da bilinir) gerekir. (Azure portal kullanıyorsanız KIMLIĞE ihtiyacınız yoktur.) Aşağıdaki yöntemleri kullanarak bu KIMLIĞI bulabilirsiniz:

- [Azure CLI](/cli/azure/ad/sp): komutunu kullanın `az ad sp list --display-name <name of your Automanage Account>` .

- Azure portal: **Azure Active Directory** gidin ve hesap adına göre oto Yönet hesabınızı arayın. **Kurumsal uygulamalar**' ın altında, görüntülendiğinde, hesap adını oto Yönet ' i seçin.

### <a name="azure-portal"></a>Azure portalı
1. **Abonelikler**' in altında, oto tarafından yönetilen sanal makinelerinizi içeren aboneliğe gidin.
1. **Access Control (IAM)** sayfasına gidin.
1. **Rol atamaları Ekle**' yi seçin.
1. **Katkıda bulunan** rolünü seçin ve tekrar Yönet hesabınızın adını girin.
1. **Kaydet**’i seçin.
1. Bu kez **kaynak Ilkesi katılımcısı** rolüyle 3 ile 5 arasındaki adımları yineleyin.

### <a name="arm-template"></a>ARM şablonu
Aşağıdaki ARM şablonunu çalıştırın. Oto Yönet hesabınızın asıl KIMLIĞI gerekir. Bu bölümün başlangıcında yer alır. İstendiğinde KIMLIĞI girin.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Şu komutları çalıştırın:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Sonraki adımlar
[Azure oto yönetimi hakkında daha fazla bilgi edinin](./automanage-virtual-machines.md)

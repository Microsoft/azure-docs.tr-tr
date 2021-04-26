---
title: Azure oto Yönet hesabı
description: Bir oto yönetimi hesabının nasıl çalıştığını ve nasıl oluşturulacağını öğrenin.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368743"
---
# <a name="automanage-accounts"></a>Hesapları oto Yönet

Otomatikmanage hesabı otomatik yönetimi hizmeti tarafından otomatikleştirilmiş işlemlerini gerçekleştirmek için kullanılan kimliktir.

Azure portal deneyiminde, sanal makinelerinizdeki oto yönetimini etkinleştirirken, el ile Yönet hesabı atamanıza veya el ile oluşturmanıza imkan tanıyan, **Azure VM en iyi uygulama** dikey penceresinde gelişmiş bir açılan menü bulunur.

Oto Yönet hesabına hem **katkıda bulunan** hem de **kaynak ilkesi katılımcısı** rollerinin, sizin eklediğiniz makineleri içeren abonelikler (ler) e-olarak verilmesi sağlanır. Tüm aboneliklerde hesap **katılımcısı** ve **kaynak ilkesi katkıda** bulunan izinlerini oto yönetmesine izin veren birden çok abonelik genelinde makinelerde aynı oto Yönet hesabını kullanabilirsiniz.

VM 'niz başka bir abonelikteki bir Log Analytics çalışma alanına bağlıysa, oto Yönet hesabına hem **katkıda** bulunan hem de **kaynak ilkesi katılımcısı** da bu diğer abonelikte de verilecektir.

Yeni bir bir oto Yönet hesabıyla bir oto yönetimi etkinleştirirseniz, aboneliğiniz üzerinde aşağıdaki izinlere sahip olmanız gerekir: **sahip** rolü veya **katkıda bulunan** **Kullanıcı erişimi yönetici** rolleriyle birlikte.

Var olan bir oto Yönet hesabıyla bir oto yönetimi etkinleştirirseniz, VM 'lerinizi içeren kaynak grubunda **katkıda** bulunan rolüne sahip olmanız gerekir.

> [!NOTE]
> En Iyi Yönet uygulamalarını devre dışı bıraktığınızda, tüm ilişkili aboneliklerdeki hesap izinlerini oto Yönet olarak kalır. Aboneliğin ıAM sayfasına gidip izinleri el ile kaldırın veya tekrar Yönet hesabını silin. Hala herhangi bir makine yönetiliyorsa, oto Yönet hesabı silinemez.

## <a name="create-an-automanage-account"></a>Bir oto Yönet hesabı oluşturun
Portalı kullanarak veya bir ARM şablonu kullanarak bir oto Yönet hesabı oluşturabilirsiniz.

### <a name="portal"></a>Portal
1. Portalda, **oto Yönet** dikey penceresine gidin
1. **Mevcut makinede etkinleştir** ' e tıklayın
1. **Gelişmiş**' in altında "yeni hesap oluştur" a tıklayın.
1. Gerekli alanları doldurup **Oluştur** ' a tıklayın.

### <a name="arm-template"></a>ARM şablonu
ARM şablonunu kullanarak bir oto Yönet hesabı oluşturmak için 2 adım gerekir:
1. Oto Yönet hesabını oluşturma
1. Hesaba sizin için işlem yapmasına izin vermek üzere yeterli izinleri verin
    1. Bu adım için oluşturduğunuz hesabın nesne KIMLIĞINE ihtiyacınız olacak.
        1. Hesabınızın hizmet sorumlusunun (nesne KIMLIĞI dahil) ayrıntılarını bulma adımları [burada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)bulunabilir.
    1. Hizmet sorumlunuzu bulduktan sonra, **nesne kimliğini** kopyalayın. Aşağıdaki izinleri devretmek için bunu kaydedin.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. bir oto Yönet hesabı oluşturun (bu hesaba izin vermez)
Bir oto Yönet hesabı oluşturmak için aşağıdaki ARM şablonunu olarak kaydedin `azuredeploy.json` ve aşağıdaki Azure CLI komutunu çalıştırın. İşiniz bittiğinde, hesaba yeterli izin vermek için aşağıdaki ikinci şablona geçin.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. oto Yönet hesabına izin verin
Oto Yönet hesabına yeterli izin vermek için aşağıdakileri yapmanız gerekir:
1. Aşağıdaki ARM şablonunu olarak kaydedin `azuredeploy2.json` ve aşağıdaki Azure CLI komutunu çalıştırın.
1. İstendiğinde, oluşturduğunuz ve kaydettiğiniz otomatik Yönet hesabının nesne KIMLIĞINI girin.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar
* [Linux](./automanage-linux.md) ve [Windows](./automanage-windows-server.md) için oto yönetme hizmetleri hakkında bilgi edinin
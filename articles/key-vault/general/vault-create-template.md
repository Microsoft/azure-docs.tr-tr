---
title: ARM şablonunu kullanarak bir Azure Anahtar Kasası ve kasa erişim ilkesi oluşturma
description: Bu makalede, bir Azure Resource Manager şablonu kullanarak Azure Anahtar Kasası ve kasa erişim ilkelerinin nasıl oluşturulacağı gösterilmektedir.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: d157419614ee3a3f89036177e962e5b7fc4466b2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815042"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak Azure Anahtar Kasası ve kasa erişim ilkesi oluşturma

[Azure Key Vault](../general/overview.md) anahtarlar, parolalar ve sertifikalar gibi gizli dizileri için güvenli bir depo sağlayan bir bulut hizmetidir. Bu makalede bir Anahtar Kasası oluşturmak için bir Azure Resource Manager şablonu dağıtma işlemi açıklanmaktadır (ARM şablonu).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları gerçekleştirmek için:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


## <a name="create-a-key-vault-resource-manager-template"></a>Key Vault Kaynak Yöneticisi şablonu oluşturma

Aşağıdaki şablonda, Anahtar Kasası oluşturmanın temel bir yolu gösterilmektedir. Şablonda bazı değerler belirtilmiştir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Key Vault şablon ayarları hakkında daha fazla bilgi için bkz. [Key Vault ARM şablonu başvurusu](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Bir şablon yeniden dağıtılırsa, anahtar kasasındaki mevcut erişim ilkelerinin üzerine yazılacak. `accessPolicies`Anahtar kasasına erişimin kaybedilmesini önlemek için özelliği mevcut erişim ilkeleriyle doldurmanıza önerilir. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Key Vault Kaynak Yöneticisi şablonuna erişim ilkesi ekleme

Tüm Anahtar Kasası şablonunu yeniden dağıtmaya gerek kalmadan, mevcut bir anahtar kasasına erişim ilkeleri dağıtabilirsiniz. Aşağıdaki şablonda erişim ilkeleri oluşturmanın temel bir yolu gösterilmektedir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

Key Vault şablonu ayarları hakkında daha fazla bilgi için bkz. [Key Vault ARM şablonu başvurusu](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Daha fazla Key Vault Kaynak Yöneticisi şablonu

Key Vault nesneleri için kullanılabilen diğer Kaynak Yöneticisi şablonları vardır:

| Gizli Diziler | Anahtarlar | Sertifikalar |
|--|--|--|
|<ul><li>[Hızlı Başlangıç](../secrets/quick-create-template.md)<li>[Başvuru](/azure/templates/microsoft.keyvault/vaults/secrets)|Yok|Yok|

Daha fazla Key Vault şablonu buradan bulabilirsiniz: [Key Vault Kaynak Yöneticisi başvurusu](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Şablonları dağıtma

Yukarıdaki şablonları, burada açıklandığı gibi **Düzenleyici 'de kendi şablonunuzu oluşturma** seçeneğini kullanarak dağıtmak için Azure Portal kullanabilirsiniz: [kaynakları özel bir şablondan dağıtma](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

Ayrıca, önceki şablonları dosyalara kaydedebilir ve şu komutları kullanabilirsiniz:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ve [az Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki hızlı başlangıç ve öğreticilerle devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakabilirsiniz. Kaynaklara daha fazla ihtiyaç duymazsanız, kaynak grubunu silin. Grubu silerseniz, anahtar kasası ve ilgili kaynaklar da silinir. Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silmek için şu adımları uygulayın:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Kaynaklar

- [Azure Key Vault genel bakışını](../general/overview.md)okuyun.
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)hakkında daha fazla bilgi edinin.
- [Azure Key Vault güvenliğine genel bakış](security-features.md) konusunu gözden geçirin

## <a name="next-steps"></a>Sonraki adımlar

- [Anahtar kasasına erişimin güvenliğini sağlama](security-features.md)
- [Anahtar kasasında kimlik doğrulama](authentication.md)
- [Azure Key Vault Geliştirici Kılavuzu](developers-guide.md)

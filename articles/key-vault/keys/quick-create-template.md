---
title: Azure hızlı başlangıç-Azure Resource Manager şablonu kullanarak bir Azure Anahtar Kasası ve anahtar oluşturma | Microsoft Docs
description: Azure Anahtar Kasası oluşturmayı ve Azure Resource Manager şablonu (ARM şablonu) kullanarak kasalarına anahtar eklemeyi gösteren hızlı başlangıç.
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 048482c6b52d3fd9225224bd3b4ff3ee66bf24fd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815369"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak bir Azure Anahtar Kasası ve anahtar oluşturma 

[Azure Key Vault](../general/overview.md) , anahtarlar, parolalar, sertifikalar ve diğer gizli diziler gibi gizli dizileri için güvenli bir depo sağlayan bir bulut hizmetidir. Bu hızlı başlangıç, bir anahtar kasası ve anahtar oluşturmak için bir Azure Resource Manager şablonu dağıtma işlemine odaklanır (ARM şablonu).

> [!NOTE]
> Bu özellik Azure Kamu 'da kullanılamaz.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi gerçekleştirmek için:

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Kullanıcının, örnek olarak atanmış bir Azure yerleşik rolüne sahip olması gerekir. mcý. [Daha fazla bilgi edinin](../../role-based-access-control/role-assignments-portal.md)
- Şablonda izinlerin yapılandırılması için Azure AD kullanıcı nesnesi kimliğiniz gerekir. Aşağıdaki yordam nesne KIMLIĞINI (GUID) alır.

    1. Aşağıdaki Azure PowerShell veya Azure CLı komutunu çalıştırarak **deneyin**' i seçin ve ardından betiği kabuk bölmesine yapıştırın. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    1. Nesne KIMLIĞINI yazın. Bu hızlı başlangıç bölümünün sonraki bölümünde olması gerekir.

## <a name="review-the-template"></a>Şablonu gözden geçirme

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key vault to be created."
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
        "description": "The SKU of the vault to be created."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key to be created."
      }
    },
    "keyType": {
      "type": "string",
      "metadata": {
        "description": "The JsonWebKeyType of the key to be created."
      }
    },
    "keyOps": {
      "type": "array",
      "defaultValue": [],
      "metadata": {
        "description": "The permitted JSON web key operations of the key to be created."
      }
    },
    "keySize": {
      "type": "int",
      "defaultValue": 2048,
      "metadata": {
        "description": "The size in bits of the key to be created."
      }
    },
    "curveName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The JsonWebKeyCurveName of the key to be created."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enableRbacAuthorization": false,
        "enableSoftDelete": false,
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
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
    },
    {
      "type": "Microsoft.KeyVault/vaults/keys",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
        "curveName": "[parameters('curveName')]"
      }
    }
  ],
  "outputs": {
    "proxyKey": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
    }
  }
}
```

Şablonda iki kaynak tanımlanmıştır:

- [Microsoft. Keykasası/kasa](/azure/templates/microsoft.keyvault/vaults)
- Microsoft. Keykasası/kasa/anahtar

Daha fazla Azure Key Vault şablon örneği, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma
[Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal), Azure PowerShell, Azure clı veya REST API kullanabilirsiniz. Dağıtım yöntemleri hakkında bilgi edinmek için bkz. [şablonları dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Anahtar kasasını ve anahtarı denetlemek için Azure portal kullanabilir ya da oluşturulan anahtarı listelemek için aşağıdaki Azure CLı veya Azure PowerShell betiğini kullanabilirsiniz.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Key Vault hızlı başlangıçları ve öğreticileri bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.
Artık gerek kalmadığında kaynak grubunu silin; bunu yaptığınızda Key Vault ve ilgili kaynaklar silinir. Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silmek için:

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

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir ARM şablonu kullanarak bir anahtar kasası ve anahtar oluşturdunuz ve dağıtımı doğruladı. Key Vault ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere devam edin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin

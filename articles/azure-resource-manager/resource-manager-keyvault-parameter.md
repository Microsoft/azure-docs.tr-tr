---
title: Şablonla gizli Key Vault
description: Dağıtım sırasında anahtar kasasından bir parametre olarak bir parolanın nasıl geçirileceğini gösterir.
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 37d21e295eca2b40e91f92d65d6e927ee6857d0e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149479"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma

Güvenli bir değer (parola gibi) doğrudan şablon veya parametre dosyanıza koymak yerine, bir dağıtım sırasında [Azure Key Vault](../key-vault/key-vault-overview.md) değeri alabilirsiniz. Değeri, parametre dosyanızda anahtar kasası ve gizli dizi ile başvurarak alırsınız. Yalnızca kendi Anahtar Kasası KIMLIĞINE başvurduğundan değer hiçbir şekilde gösterilmez. Anahtar Kasası, dağıtmakta olduğunuz kaynak grubundan farklı bir abonelikte bulunabilir.

## <a name="deploy-key-vaults-and-secrets"></a>Anahtar kasaları ve gizli dizileri dağıtma

Şablon dağıtımı sırasında bir anahtar kasasına erişmek için, anahtar kasasındaki `enabledForTemplateDeployment` `true`olarak ayarlayın.

Aşağıdaki Azure CLı ve Azure PowerShell örnekleri, anahtar kasasının nasıl oluşturulacağını ve bir parolanın nasıl ekleneceğini gösterir.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Anahtar kasasının sahibi olarak otomatik olarak gizli dizi oluşturma erişiminiz vardır. Gizli dizi ile çalışan Kullanıcı anahtar kasasının sahibi değilse, şu ile erişim izni verin:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Anahtar kasaları oluşturma ve gizli dizileri ekleme hakkında daha fazla bilgi için bkz.:

- [CLı kullanarak bir gizli dizi ayarlama ve alma](../key-vault/quick-create-cli.md)
- [PowerShell kullanarak bir gizli dizi ayarlama ve alma](../key-vault/quick-create-powershell.md)
- [Portalı kullanarak bir gizli dizi ayarlama ve alma](../key-vault/quick-create-portal.md)
- [.NET kullanarak bir gizli dizi ayarlama ve alma](../key-vault/quick-create-net.md)
- [Node. js kullanarak bir gizli dizi ayarlama ve alma](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Gizli dizileri için erişim izni verme

Şablonu dağıtan Kullanıcı, kaynak grubunun ve anahtar kasasının kapsamı için `Microsoft.KeyVault/vaults/deploy/action` iznine sahip olmalıdır. [Sahip](../role-based-access-control/built-in-roles.md#owner) ve [katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor) rollerinin her ikisi de bu erişimi verir. Anahtar Kasası oluşturduysanız, izninizin olması gerekir.

Aşağıdaki yordamda, en düşük izinle bir rol oluşturma ve kullanıcının nasıl atanacağı gösterilmektedir

1. Özel bir rol tanımı JSON dosyası oluşturun:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    "00000000-0000-0000-0000-000000000000" öğesini abonelik KIMLIĞIYLE değiştirin.

2. JSON dosyasını kullanarak yeni rolü oluşturun:

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    Örnekler, kullanıcıya kaynak grubu düzeyinde özel rol atar.  

[Yönetilen bir uygulama](../managed-applications/overview.md)şablonuyla bir Key Vault kullanırken, **gereç kaynak sağlayıcısı** hizmet sorumlusuna erişim vermeniz gerekir. Daha fazla bilgi için bkz. [Azure yönetilen uygulamalar dağıtıldığında erişim Key Vault gizliliği](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Statik KIMLIK ile başvuru gizli dizileri

Bu yaklaşımda, şablon değil, parametre dosyasındaki anahtar kasasına başvurun. Aşağıdaki görüntüde, parametre dosyasının gizli dizi ile nasıl başvurduğu gösterilmektedir ve bu değer şablona geçirilir.

![Kaynak Yöneticisi Anahtar Kasası tümleştirme statik KIMLIK diyagramı](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Öğretici: Kaynak Yöneticisi Şablon dağıtımı Azure Key Vault tümleştirme](./resource-manager-tutorial-use-key-vault.md) bu yöntemi kullanır.

Aşağıdaki şablon, yönetici parolası içeren bir SQL Server dağıtır. Password parametresi güvenli bir dizeye ayarlanır. Ancak, şablon bu değerin nereden geldiğini belirtmez.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Şimdi, önceki şablon için bir parametre dosyası oluşturun. Parametre dosyasında, şablondaki parametrenin adıyla eşleşen bir parametre belirtin. Parametre değeri için anahtar kasasından gizli dizi başvurusu yapın. Anahtar kasasının kaynak tanımlayıcısını ve gizli anahtar adını geçirerek gizli dizi başvurusunu alırsınız:

Aşağıdaki parametre dosyasında, Anahtar Kasası parolasının zaten mevcut olması ve kaynak KIMLIĞI için statik bir değer sağlamanız gerekir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Parolanın geçerli sürümden farklı bir sürümünü kullanmanız gerekiyorsa `secretVersion` özelliğini kullanın.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Şablonu dağıtın ve parametre dosyasını geçirin:

Azure CLI için şunu kullanın:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

PowerShell için şunu kullanın:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Dinamik KIMLIK ile başvuru gizli dizileri

Önceki bölümde, bir statik kaynak KIMLIĞININ parametresinden Anahtar Kasası gizli dizisi için nasıl geçirilediği gösterildi. Ancak bazı senaryolarda, geçerli dağıtıma göre farklılık gösteren bir Anahtar Kasası gizliliğine başvurmanız gerekir. Ya da parametre değerlerini parametre dosyasında bir başvuru parametresi oluşturmak yerine şablona geçirmek isteyebilirsiniz. Her iki durumda da, bağlantılı bir şablon kullanarak bir Anahtar Kasası parolasının kaynak KIMLIĞINI dinamik olarak oluşturabilirsiniz.

Parametreler dosyasında şablon ifadelerine izin verilmediğinden, Parametreler dosyasında dinamik olarak kaynak KIMLIĞI oluşturamazsınız. 

Ana şablonunuzda, bağlantılı şablonu ekler ve dinamik olarak üretilen kaynak KIMLIĞINI içeren bir parametreyi geçirin. Aşağıdaki görüntüde, bağlantılı şablondaki bir parametrenin gizli dizi nasıl başvurduğu gösterilmektedir.

![Dinamik KIMLIK](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[Aşağıdaki şablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) Anahtar Kasası kimliğini dinamik olarak oluşturur ve bir parametre olarak geçirir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Önceki şablonu dağıtın ve parametreler için değerler sağlayın. GitHub 'dan örnek şablonu kullanabilirsiniz, ancak ortamınız için parametre değerleri sağlamanız gerekir.

Azure CLI için şunu kullanın:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

PowerShell için şunu kullanın:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Sonraki adımlar

- Anahtar kasaları hakkında genel bilgi için bkz. [Azure Key Vault nedir?](../key-vault/key-vault-overview.md).
- Anahtar gizli dizileri ile ilgili tüm örnekler için, [Key Vault örneklere](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)bakın.

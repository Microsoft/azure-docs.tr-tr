---
title: Dağıtım için şablonları bağlama
description: Modüler şablon çözümü oluşturmak için bir Azure Resource Manager şablonunda (ARM şablonu) bağlantılı şablonların nasıl kullanılacağını açıklar. Parametre değerlerinin nasıl geçirileceğini, bir parametre dosyası ve dinamik olarak oluşturulan URL 'Leri gösterir.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 981e9f10e118012911108d634fbb8bdb9524cb88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543984"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Azure kaynaklarını dağıtırken bağlı ve iç içe şablonları kullanma

Karmaşık çözümleri dağıtmak için Azure Resource Manager şablonunuzu (ARM şablonu) birçok ilgili şablona bölebilir ve ardından bunları ana şablon aracılığıyla birlikte dağıtabilirsiniz. İlgili şablonlar, ana şablon içine katıştırılmış ayrı dosyalar veya şablon söz dizimi olabilir. Bu makalede, ana şablondan bir bağlantı aracılığıyla başvurulan ayrı bir şablon dosyasına başvurmak için **bağlantılı şablon** terimi kullanılmaktadır. Ana şablon içindeki katıştırılmış şablon söz dizimini ifade etmek için **iç içe geçmiş şablon** terimini kullanır.

Küçük ve orta ölçekli çözümler için tek bir şablonun anlaşılması ve bakımının yapılması daha kolay olacaktır. Tüm kaynakları ve değerleri tek bir dosyada görebilirsiniz. Gelişmiş senaryolarda bağlantılı şablonlar çözümü hedeflenen bileşenlere ayırmanıza sağlar. Bu şablonları diğer senaryolar için kolayca yeniden kullanabilirsiniz.

Öğretici için bkz. [öğretici: bağlı şablon dağıtma](./deployment-tutorial-linked-template.md).

> [!NOTE]
> Bağlı veya iç içe şablonlar için, Dağıtım modunu yalnızca [artımlı](deployment-modes.md)olarak ayarlayabilirsiniz. Ancak, ana şablon, tamamlanmış modda dağıtılabilir. Ana şablonu tüm modda dağıtırsanız, bağlantılı veya iç içe yerleştirilmiş şablon aynı kaynak grubunu hedefliyorsa, bağlantılı veya iç içe yerleştirilmiş şablonda dağıtılan kaynaklar, tamamlanmış mod dağıtımı değerlendirmesine dahil edilir. Ana şablonda dağıtılan kaynakların birleştirilmiş koleksiyonu ve bağlantılı veya iç içe yerleştirilmiş şablonlar kaynak grubundaki mevcut kaynaklarla karşılaştırılır. Bu Birleşik koleksiyonda bulunmayan kaynaklar silinir.
>
> Bağlantılı veya iç içe yerleştirilmiş şablon farklı bir kaynak grubunu hedefliyorsa, bu dağıtım artımlı mod kullanır.
>

## <a name="nested-template"></a>İç içe yerleştirilmiş şablon

Bir şablonu iç içe aktarmak için ana şablonunuza bir [dağıtımlar kaynağı](/azure/templates/microsoft.resources/deployments) ekleyin. `template`Özelliğinde, Şablon sözdizimini belirtin.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Aşağıdaki örnek, iç içe geçmiş bir şablon aracılığıyla bir depolama hesabı dağıtır.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>İç içe şablonlarda ifade değerlendirme kapsamı

İç içe yerleştirilmiş şablon kullanırken şablon ifadelerinin ana şablon kapsamında mı yoksa iç içe yerleştirilmiş şablon kapsamında mı değerlendirileceğini belirtebilirsiniz. Kapsam, parametrelerin, değişkenlerin ve [resourceGroup](template-functions-resource.md#resourcegroup) ve [abonelik](template-functions-resource.md#subscription) gibi işlevlerin nasıl çözümlendiğini belirler.

Kapsamını özelliği aracılığıyla ayarlarsınız `expressionEvaluationOptions` . Varsayılan olarak, `expressionEvaluationOptions` özelliği olarak ayarlanır, yani `outer` üst şablon kapsamını kullanır. `inner`İfadenin, iç içe geçmiş şablonun kapsamı içinde değerlendirilmesini sağlamak için değerini olarak ayarlayın.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate1",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
  ...
```

> [!NOTE]
>
> Kapsam olarak ayarlandığında `outer` , `reference` iç içe yerleştirilmiş şablonda dağıttığınız bir kaynak için iç içe şablonun çıktılar bölümünde işlevini kullanamazsınız. Dağıtılan bir kaynağın değerlerini iç içe geçmiş bir şablonda döndürmek için `inner` kapsam kullanın veya iç içe geçmiş şablonunuzu bağlı bir şablona dönüştürün.

Aşağıdaki şablon, şablon ifadelerinin kapsama göre nasıl çözümlendiğini gösterir. `exampleVar`Hem üst şablonda hem de iç içe yerleştirilmiş şablonda tanımlı adlı bir değişken içerir. Değişkenin değerini döndürür.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

`exampleVar`İçindeki özelliğinin değerine bağlı olarak değişir `scope` `expressionEvaluationOptions` . Aşağıdaki tabloda her iki kapsamın sonuçları gösterilmektedir.

| Değerlendirme kapsamı | Çıktı |
| ----- | ------ |
| Dahili | iç içe şablondan |
| dış (veya varsayılan) | üst şablondan |

Aşağıdaki örnek, bir SQL Server dağıtır ve parola için kullanılacak bir Anahtar Kasası gizli anahtarı alır. Kapsam, `inner` anahtar KASASı kimliğini dinamik olarak oluşturduğundan ( `adminPassword.reference.keyVault` dış şablonlarda bkz `parameters` .) olarak ayarlanır ve bunu iç içe geçmiş şablona bir parametre olarak geçirir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
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
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

İç içe yerleştirilmiş bir şablonda güvenli parametre değerleri kullanırken dikkatli olun. Kapsamı dıştaki olarak ayarlarsanız, güvenli değerler dağıtım geçmişinde düz metin olarak depolanır. Dağıtım geçmişinde şablonu görüntüleyen bir Kullanıcı güvenli değerleri görebilir. Bunun yerine, iç kapsamı kullanın veya güvenli değerler gerektiren kaynakları üst şablona ekleyin.

Aşağıdaki alıntıda hangi değerlerin güvenli olduğunu ve bunların güvenli olduğunu gösterir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>Bağlantılı şablon

Bir şablonu bağlamak için ana şablonunuza bir [dağıtımlar kaynağı](/azure/templates/microsoft.resources/deployments) ekleyin. `templateLink`Özelliğinde, dahil edilecek ŞABLONUN URI 'sini belirtin. Aşağıdaki örnek, bir depolama hesabındaki bir şablona bağlantı sağlar.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Bağlı bir şablona başvururken, değeri `uri` yerel bir dosya veya yalnızca yerel ağınızda bulunan bir dosya olamaz. Azure Resource Manager şablona erişebilmelidir. HTTP veya HTTPS olarak indirilebilir bir URI değeri sağlayın.

HTTP veya HTTPS içeren parametreleri kullanarak şablonlara başvurabilirsiniz. Örneğin, ortak bir model, `_artifactsLocation` parametresini kullanmaktır. Bağlantılı şablonu şöyle bir ifadeyle ayarlayabilirsiniz:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

GitHub 'daki bir şablona bağlanıyorsanız ham URL 'YI kullanın. Bağlantı şu biçimdedir: `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` . Ham bağlantıyı almak için **RAW**' ı seçin.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Ham URL seçin":::

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

### <a name="parameters-for-linked-template"></a>Bağlantılı şablon parametreleri

Bağlı şablonunuz için parametreleri bir dış dosyada ya da satır içi olarak sağlayabilirsiniz. Dış parametre dosyası sağlarken, `parametersLink` özelliğini kullanın:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Parametre değerlerini satır içi olarak geçirmek için `parameters` özelliğini kullanın.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

Hem satır içi parametreleri hem de bir parametre dosyası bağlantısını kullanamazsınız. Hem hem de belirtildiğinde dağıtım bir hata ile başarısız olur `parametersLink` `parameters` .

### <a name="use-relative-path-for-linked-templates"></a>Bağlantılı şablonlar için göreli yol kullan

`relativePath`Özelliği, `Microsoft.Resources/deployments` bağlantılı şablonlar yazmayı kolaylaştırır. Bu özellik, üst öğeye göre bir konuma uzak bağlantılı şablon dağıtmak için kullanılabilir. Bu özellik tüm şablon dosyalarının GitHub veya Azure depolama hesabı gibi uzak bir URI 'de hazırlanmalıdır ve kullanılabilir olmasını gerektirir. Ana şablon, Azure PowerShell veya Azure CLı 'dan bir URI kullanılarak çağrıldığında, alt dağıtım URI 'SI üst ve relativePath 'in bir birleşimidir.

> [!NOTE]
> TemplateSpec oluştururken, özelliği tarafından başvurulan tüm şablonlar `relativePath` Azure PowerShell veya Azure CLI tarafından templatespec kaynağında paketlenmiştir. Dosyaların hazırlanması gerekmez. Daha fazla bilgi için bkz. [bağlantılı şablonlarla şablon belirtimi oluşturma](./template-specs.md#create-a-template-spec-with-linked-templates).

Şöyle bir klasör yapısı varsayın:

![Resource Manager bağlı şablon göreli yolu](./media/linked-templates/resource-manager-linked-templates-relative-path.png)

Aşağıdaki şablonda *mainTemplate.js* , önceki görüntüde gösterilen *nestedChild.js* nasıl dağıttığı gösterilmektedir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "childLinked",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "children/nestedChild.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

Aşağıdaki dağıtımda, önceki şablondaki bağlantılı şablonun URI 'SI **https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/children/nestedChild.json** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

---

Bağlı şablonları bir Azure depolama hesabında depolanan göreli yol ile dağıtmak için, `QueryString` / `query-string` templateuri parametresiyle kullanılacak SAS belirtecini belirtmek için parametresini kullanın. Bu parametre yalnızca Azure CLı sürüm 2,18 veya üzeri tarafından desteklenir ve sürüm 5,4 veya üzeri Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

---

QueryString içinde önde gelen "?" bulunmadığından emin olun. Dağıtım, dağıtımlar için URI 'yi montaj sırasında bir tane ekler.

## <a name="template-specs"></a>Şablon belirtimleri

Bağlı şablonlarınızı erişilebilir bir uç noktada tutmak yerine, ana şablonu ve onun bağlantılı şablonlarını dağıtabileceğiniz tek bir varlığa paketleyen bir [şablon belirtimi](template-specs.md) oluşturabilirsiniz. Şablon belirtimi, Azure aboneliğinizdeki bir kaynaktır. Şablonu kuruluşunuzdaki kullanıcılarla güvenli bir şekilde paylaşmayı kolaylaştırır. Şablon belirtimine erişim sağlamak için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanırsınız. Bu özellik şu anda önizleme aşamasındadır.

Daha fazla bilgi için bkz.

- [Öğretici: bağlantılı şablonlarla bir şablon belirtimi oluşturun](./template-specs-create-linked.md).
- [Öğretici: bir şablon belirtimini bağlantılı şablon olarak dağıtın](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Bağımlılıklar

Diğer kaynak türlerinde olduğu gibi, bağlantılı şablonlar arasında bağımlılıklar da ayarlayabilirsiniz. Bir bağlantılı şablondaki kaynakların, ikinci bağlantılı şablondaki kaynaklardan önce dağıtılması gerekiyorsa, ikinci şablonu ilk öğesine bağımlı olarak ayarlayın.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

`contentVersion`Veya özelliği için özelliği sağlamanız gerekmez `templateLink` `parametersLink` . Bir sağlamazsanız `contentVersion` , şablonun geçerli sürümü dağıtılır. İçerik sürümü için bir değer sağlarsanız, bu bağlantı bağlantılı şablondaki sürümle eşleşmelidir; Aksi takdirde, dağıtım hata vererek başarısız olur.

## <a name="using-variables-to-link-templates"></a>Şablonları bağlamak için değişkenleri kullanma

Önceki örneklerde, şablon bağlantıları için sabit kodlanmış URL değerleri gösteriliyordu. Bu yaklaşım basit bir şablon için çalışabilir, ancak büyük bir modüler şablonlar kümesi için iyi çalışmaz. Bunun yerine, ana şablon için temel URL 'YI depolayan bir statik değişken oluşturabilir ve ardından bu temel URL 'den bağlantılı şablonlar için dinamik olarak URL 'Ler oluşturabilirsiniz. Bu yaklaşımın avantajı, yalnızca ana şablondaki statik değişkeni değiştirmeniz gerektiği için şablonu kolayca taşıyabilir veya çataldan taşıyabilirsiniz. Ana şablon, ayrıştırılmış şablonun tamamında doğru URI 'Leri geçirir.

Aşağıdaki örnek, bağlantılı şablonlar (ve) için iki URL oluşturmak üzere temel URL 'YI nasıl kullanacağınızı `sharedTemplateUrl` gösterir `vmTemplateUrl` .

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Ayrıca, geçerli şablonun temel URL 'sini almak için [Deployment ()](template-functions-deployment.md#deployment) kullanabilirsiniz ve bunu, aynı konumdaki DIĞER şablonların URL 'sini almak için kullanabilirsiniz. Bu yaklaşım, şablon konumunuz değişirse veya şablon dosyasında sabit kodlama URL 'Lerinin oluşmasını önlemek istediğinizde yararlıdır. `templateLink`Özelliği yalnızca BIR URL ile uzak şablona bağlanılırken döndürülür. Yerel bir şablon kullanıyorsanız, bu özellik kullanılamaz.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Sonuç olarak, değişkenini `uri` bir özelliğin özelliğinde kullanırsınız `templateLink` .

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Kopyayı kullanma

İç içe şablon içeren bir kaynağın birden çok örneğini oluşturmak için, `copy` öğesini kaynak düzeyine ekleyin `Microsoft.Resources/deployments` . Ya da kapsam ise, `inner` kopyayı iç içe yerleştirilmiş şablon içine ekleyebilirsiniz.

Aşağıdaki örnek şablon, `copy` iç içe geçmiş bir şablonla nasıl kullanılacağını göstermektedir.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            // "copy": {
            //   "name": "storagecopy",
            //   "count": 2
            // }
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>Bağlı şablondan değerleri alma

Bağlı bir şablondan çıkış değeri almak için, özellik değerini şu şekilde olan sözdizimiyle alın: `"[reference('deploymentName').outputs.propertyName.value]"` .

Bağlantılı şablondan çıkış özelliği alınırken, özellik adı bir tire içermemelidir.

Aşağıdaki örneklerde, bağlantılı bir şablona nasıl başvurulacağını ve bir çıkış değerinin nasıl alınacağını gösterilmektedir. Bağlantılı şablon basit bir ileti döndürür. İlk olarak, bağlantılı şablon:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

Ana şablon, bağlantılı şablonu dağıtır ve döndürülen değeri alır. Dağıtım kaynağına adına göre başvurduğuna ve bağlı şablon tarafından döndürülen özelliğin adını kullandığını unutmayın.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

Aşağıdaki örnek, genel bir IP adresi dağıtan ve bu genel IP için Azure kaynağının kaynak KIMLIĞINI döndüren bir şablonu gösterir:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Yük dengeleyici dağıtma sırasında önceki şablondaki genel IP adresini kullanmak için, şablona bağlayın ve kaynak üzerinde bir bağımlılık bildirin `Microsoft.Resources/deployments` . Yük dengeleyicideki genel IP adresi, bağlantılı şablondan çıkış değerine ayarlanır.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Dağıtım geçmişi

Kaynak Yöneticisi her bir şablonu dağıtım geçmişinde ayrı bir dağıtım olarak işler. Dağıtım geçmişinde üç bağlantılı veya iç içe şablon içeren bir ana şablon şu şekilde görünür:

![Dağıtım geçmişi](./media/linked-templates/deployment-history.png)

Bu ayrı girdileri, dağıtımdan sonra çıkış değerlerini almak için geçmişte kullanabilirsiniz. Aşağıdaki şablon bir genel IP adresi oluşturur ve IP adresini verir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Aşağıdaki şablon önceki şablona bağlantı sağlar. Üç genel IP adresi oluşturur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Dağıtımdan sonra aşağıdaki PowerShell betiği ile çıkış değerlerini alabilirsiniz:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Veya, bir bash kabuğu 'nda Azure CLı betiği:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Dış şablonun güvenliğini sağlama

Bağlı şablon dışarıdan kullanılabilir olmalıdır, ancak genel kullanıma açık olması gerekmez. Şablonunuzu yalnızca depolama hesabı sahibi tarafından erişilebilen bir özel depolama hesabına ekleyebilirsiniz. Daha sonra, dağıtım sırasında erişimi etkinleştirmek için bir paylaşılan erişim imzası (SAS) belirteci oluşturursunuz. Bu SAS belirtecini bağlantılı şablon için URI 'ye eklersiniz. Belirteç güvenli bir dize olarak geçirilse de, SAS belirteci dahil olmak üzere bağlantılı şablonun URI 'SI dağıtım işlemlerinde günlüğe kaydedilir. Pozlamayı sınırlandırmak için, belirteç için bir süre sonu ayarlayın.

Parametre dosyası bir SAS belirteci aracılığıyla da sınırlı olabilir.

Şu anda [Azure Storage güvenlik duvarının](../../storage/common/storage-network-security.md)arkasındaki bir depolama hesabındaki bir şablona bağlayamazsınız.

> [!IMPORTANT]
> Bağlı şablonunuzu bir SAS belirteci ile güvenli hale getirmek yerine, bir [şablon belirtimi](template-specs.md)oluşturmayı düşünün. Şablon belirtimi, ana şablonu ve bağlı şablonlarını Azure aboneliğinizde bir kaynak olarak güvenli bir şekilde depolar. Şablonu dağıtmaları gereken kullanıcılara erişim sağlamak için Azure RBAC 'yi kullanırsınız.

Aşağıdaki örnek, bir şablona bağlanırken bir SAS belirtecinin nasıl geçirileceğini gösterir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

PowerShell 'de, kapsayıcı için bir belirteç alır ve aşağıdaki komutlarla şablonları dağıtırsınız. `containerSasToken`Parametrenin şablonda tanımlandığından emin olun. Komutta bir parametre değildir `New-AzResourceGroupDeployment` .

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Bash kabuğu 'nda Azure CLı için, kapsayıcı için bir belirteç alır ve aşağıdaki kodla şablonları dağıtabilirsiniz:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Örnek Şablonlar

Aşağıdaki örneklerde, bağlantılı şablonların yaygın kullanımları gösterilmektedir.

|Ana şablon  |Bağlantılı şablon |Description  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[bağlantılı şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Bağlantılı şablondan dize döndürür. |
|[Genel IP adresi ile Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[bağlantılı şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Bağlı şablondan ortak IP adresini döndürür ve yük dengeleyicide bu değeri ayarlar. |
|[Birden çok IP adresi](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [bağlantılı şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Bağlı şablonda birkaç genel IP adresi oluşturur.  |

## <a name="next-steps"></a>Sonraki adımlar

* Öğreticiye gitmek için bkz. [öğretici: bağlı şablon dağıtma](./deployment-tutorial-linked-template.md).
* Kaynaklarınızın dağıtım sırasını tanımlama hakkında bilgi edinmek için bkz. [ARM şablonlarındaki kaynakları dağıtma sırasını tanımlama](define-resource-dependency.md).
* Tek bir kaynağı tanımlama ve birçok örneğini oluşturma hakkında bilgi edinmek için bkz. [ARM şablonlarındaki kaynak yinelemesi](copy-resources.md).
* Bir depolama hesabında bir şablon ayarlama ve SAS belirteci oluşturma adımları için bkz. [ARM şablonlarıyla kaynak dağıtma ve Azure PowerShell](deploy-powershell.md) veya [kaynak ARM ŞABLONLARı ve Azure CLI ile dağıtım](deploy-cli.md).

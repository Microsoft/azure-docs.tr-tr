---
title: Kaynakları dağıtım & çapraz abonelik kaynak grubu
description: Dağıtım sırasında birden fazla Azure aboneliğini ve kaynak grubunu nasıl hedefleyecek olduğunu gösterir.
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: 99c534e1c51dcdf32c2b3a3b779c01d71b8d0c24
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149562"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Azure kaynaklarını birden fazla aboneliğe veya kaynak grubuna dağıtın

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Genellikle, şablonunuzda bulunan tüm kaynakları tek bir [kaynak grubuna](resource-group-overview.md)dağıtırsınız. Ancak, bir kaynak kümesini birlikte dağıtmak ve bunları farklı kaynak gruplarına veya aboneliklerine yerleştirmek istediğiniz senaryolar vardır. Örneğin, Azure Site Recovery için yedekleme sanal makinesini ayrı bir kaynak grubuna ve konuma dağıtmak isteyebilirsiniz. Kaynak Yöneticisi, ana şablon için kullanılan abonelik ve kaynak grubundan farklı abonelikler ve kaynak grupları hedeflemek üzere iç içe geçmiş şablonlar kullanmanıza olanak sağlar.

> [!NOTE]
> Tek bir dağıtımda yalnızca beş kaynak grubuna dağıtım yapabilirsiniz. Genellikle, bu sınırlama, ana şablon için belirtilen bir kaynak grubuna ve iç içe veya bağlı dağıtımlardaki en fazla dört kaynak grubuna dağıtabileceğiniz anlamına gelir. Ancak, ana şablonunuz yalnızca iç içe veya bağlı şablonlar içeriyorsa ve herhangi bir kaynak dağıtmadığından, iç içe veya bağlı dağıtımlara en fazla beş kaynak grubu ekleyebilirsiniz.

## <a name="specify-a-subscription-and-resource-group"></a>Bir abonelik ve kaynak grubu belirtin

Farklı bir kaynağı hedeflemek için, iç içe veya bağlı bir şablon kullanın. `Microsoft.Resources/deployments` kaynak türü `subscriptionId` ve `resourceGroup`için parametreler sağlar. Bu özellikler, iç içe dağıtım için farklı bir abonelik ve kaynak grubu belirtmenizi sağlar. Dağıtımı çalıştırmadan önce tüm kaynak gruplarının mevcut olması gerekir. Abonelik KIMLIĞINI veya kaynak grubunu belirtmezseniz, üst şablondaki abonelik ve kaynak grubu kullanılır.

Şablonu dağıtmak için kullandığınız hesabın, belirtilen abonelik KIMLIĞINE dağıtım izinleri olmalıdır. Belirtilen abonelik farklı bir Azure Active Directory kiracısında varsa, [başka bir dizinden Konuk kullanıcılar eklemeniz](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)gerekir.

Farklı bir kaynak grubu ve abonelik belirtmek için şunu kullanın:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Kaynak gruplarınız aynı abonelikte yer alıyorsa, **SubscriptionID** değerini kaldırabilirsiniz.

Aşağıdaki örnek iki depolama hesabı dağıtır: dağıtım sırasında belirtilen kaynak grubunda bir tane ve bir diğeri de `secondResourceGroup` parametresinde belirtilen bir kaynak grubunda.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Mevcut olmayan bir kaynak grubunun adına `resourceGroup` ayarlarsanız dağıtım başarısız olur.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>ResourceGroup () ve Subscription () işlevlerini kullanın

Çapraz kaynak grubu dağıtımları için [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) ve [Subscription ()](resource-group-template-functions-resource.md#subscription) işlevleri, iç içe geçmiş şablonu nasıl belirtbir şekilde farklı şekilde çözümlenir. 

Bir şablonu başka bir şablon içine eklerseniz, iç içe şablondaki işlevler üst kaynak grubuna ve aboneliğe çözümlenir. Katıştırılmış bir şablon aşağıdaki biçimi kullanır:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Ayrı bir şablona bağlarsanız, bağlantılı şablondaki işlevler, iç içe geçmiş kaynak grubuna ve aboneliğe çözümlenir. Bağlı bir şablon aşağıdaki biçimi kullanır:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Örnek şablonları

Aşağıdaki şablonlar birden çok kaynak grubu dağıtımını göstermektedir. Şablonları dağıtmaya yönelik betikler tablodan sonra gösterilir.

|Şablon  |Açıklama  |
|---------|---------|
|[Çapraz abonelik şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Bir depolama hesabını bir kaynak grubuna ve bir depolama hesabını ikinci bir kaynak grubuna dağıtır. İkinci kaynak grubu farklı bir abonelikte olduğunda, abonelik KIMLIĞI için bir değer ekleyin. |
|[Çapraz kaynak grubu özellikleri şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |`resourceGroup()` işlevinin nasıl çözümlendiğini gösterir. Hiçbir kaynak dağıtmaz. |

### <a name="powershell"></a>PowerShell

PowerShell için, iki depolama hesabını **aynı abonelikte**iki kaynak grubuna dağıtmak için şunu kullanın:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

PowerShell için iki depolama hesabını **iki aboneliğe**dağıtmak üzere şunu kullanın:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

PowerShell için, **kaynak grubu nesnesinin** üst şablon, satır içi şablon ve bağlantılı şablon için nasıl çözümlendiğini sınamak için şunu kullanın:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Yukarıdaki örnekte, **Parentrg** ve **InlineRG** öğeleri **parentgroup**'a çözümlenir. **linkedRG** , **linkedgroup**'a çözümleniyor. Yukarıdaki örnekteki çıktı:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Azure CLI

Azure CLı için, iki depolama hesabını **aynı abonelikte**iki kaynak grubuna dağıtmak için şunu kullanın:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Azure CLı için iki **aboneliğe**iki depolama hesabı dağıtmak için şunu kullanın:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Azure CLı için, **kaynak grubu nesnesinin** üst şablon, satır içi şablon ve bağlantılı şablon için nasıl çözümlendiğini sınamak için şunu kullanın:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Yukarıdaki örnekte, **Parentrg** ve **InlineRG** öğeleri **parentgroup**'a çözümlenir. **linkedRG** , **linkedgroup**'a çözümleniyor. Yukarıdaki örnekteki çıktı:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Sonraki adımlar

* Şablonunuzda parametrelerin nasıl tanımlanacağını anlamak için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).
* Yaygın dağıtım hatalarını çözümlemeye yönelik ipuçları için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarını giderme](resource-manager-common-deployment-errors.md).
* SAS belirteci gerektiren bir şablonu dağıtma hakkında daha fazla bilgi için bkz. [özel şablonu SAS belirteci Ile dağıtma](resource-manager-powershell-sas-token.md).

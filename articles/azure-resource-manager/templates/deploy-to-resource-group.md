---
title: Kaynakları kaynak gruplarına dağıtma
description: Azure Resource Manager şablonunda kaynakların nasıl dağıtılacağını açıklar. Birden fazla kaynak grubunun nasıl hedeflenecek gösterilmektedir.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 1d636be9ffab5a4398e3e12867e601ce6df382bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889799"
---
# <a name="resource-group-deployments-with-arm-templates"></a>ARM şablonlarıyla kaynak grubu dağıtımları

Bu makalede, dağıtımınızın bir kaynak grubuna nasıl kapsam yapılacağı açıklanır. Dağıtım için bir Azure Resource Manager şablonu (ARM şablonu) kullanırsınız. Makalede ayrıca kapsam, dağıtım işlemindeki kaynak grubunun ötesinde nasıl genişlebileceğiniz gösterilmektedir.

## <a name="supported-resources"></a>Desteklenen kaynaklar

Kaynakların çoğu, bir kaynak grubuna dağıtılabilir. Kullanılabilir kaynakların listesi için bkz. [ARM şablon başvurusu](/azure/templates).

## <a name="schema"></a>Şema

Şablonlar için aşağıdaki şemayı kullanın:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Parametre dosyaları için şunu kullanın:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Dağıtım komutları

Bir kaynak grubuna dağıtmak için, kaynak grubu dağıtım komutlarını kullanın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı için [az Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create)kullanın. Aşağıdaki örnek, bir kaynak grubu oluşturmak için bir şablon dağıtır:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell dağıtım komutu için [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)' ı kullanın. Aşağıdaki örnek, bir kaynak grubu oluşturmak için bir şablon dağıtır:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

ARM şablonları dağıtmaya yönelik dağıtım komutları ve seçenekleri hakkında daha ayrıntılı bilgi için bkz.:

* [ARM şablonları ve Azure portal kaynak dağıtma](deploy-portal.md)
* [ARM şablonları ve Azure CLı ile kaynak dağıtma](deploy-cli.md)
* [ARM şablonları ve Azure PowerShell kaynak dağıtma](deploy-powershell.md)
* [ARM şablonlarıyla kaynakları dağıtma ve Azure Resource Manager REST API](deploy-rest.md)
* [GitHub deposundan şablon dağıtmak için bir dağıtım düğmesi kullanın](deploy-to-azure-button.md)
* [ARM şablonlarını Cloud Shell dağıtma](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Dağıtım kapsamları

Bir kaynak grubuna dağıtırken, kaynakların dağıtımını yapabilirsiniz:

* işlemin hedef kaynak grubu
* aynı abonelikte veya diğer aboneliklerdeki diğer kaynak grupları
* Kiracıdaki tüm abonelikler
* kaynak grubu için kiracı

[Uzantı kaynağı](scope-extension-resources.md) , dağıtım hedefinden farklı bir hedef kapsamına eklenebilir.

Şablonu dağıtan kullanıcının belirtilen kapsama erişimi olmalıdır.

Bu bölümde, farklı kapsamların nasıl ayarlanacağı gösterilmektedir. Bu farklı kapsamları tek bir şablonda birleştirebilirsiniz.

### <a name="scope-to-target-resource-group"></a>Hedef kaynak grubu için kapsam

Hedef kaynağa kaynak dağıtmak için bu kaynakları şablonun kaynaklar bölümüne ekleyin.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

Örnek bir şablon için bkz. [hedef kaynak grubuna dağıtma](#deploy-to-target-resource-group).

### <a name="scope-to-resource-group-in-same-subscription"></a>Aynı abonelikte kaynak grubu için kapsam

Aynı abonelikte farklı bir kaynak grubuna kaynak dağıtmak için, iç içe geçmiş bir dağıtım ekleyin ve `resourceGroup` özelliğini ekleyin. Abonelik KIMLIĞINI veya kaynak grubunu belirtmezseniz, üst şablondaki abonelik ve kaynak grubu kullanılır. Dağıtımı çalıştırmadan önce tüm kaynak gruplarının mevcut olması gerekir.

Aşağıdaki örnekte, iç içe dağıtım adlı bir kaynak grubunu hedefler `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

Örnek bir şablon için bkz. [birden çok kaynak grubuna dağıtma](#deploy-to-multiple-resource-groups).

### <a name="scope-to-resource-group-in-different-subscription"></a>Farklı abonelikteki kaynak grubuna kapsam

Kaynakları farklı bir abonelikte kaynak grubuna dağıtmak için, iç içe geçmiş bir dağıtım ekleyin ve `subscriptionId` ve özelliklerini dahil edin `resourceGroup` . Aşağıdaki örnekte, iç içe dağıtım adlı bir kaynak grubunu hedefler `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

Örnek bir şablon için bkz. [birden çok kaynak grubuna dağıtma](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Abonelik kapsamı

Bir aboneliğe kaynak dağıtmak için, iç içe geçmiş bir dağıtım ekleyin ve `subscriptionId` özelliğini ekleyin. Abonelik, hedef kaynak grubu veya Kiracıdaki başka bir abonelik için abonelik olabilir. Ayrıca, `location` iç içe dağıtım için özelliğini ayarlayın.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

Örnek bir şablon için bkz. [kaynak grubu oluşturma](#create-resource-group).

### <a name="scope-to-tenant"></a>Kapsam-kiracı

Kiracıda kaynak oluşturmak için öğesini `scope` olarak ayarlayın `/` . Şablonu dağıtan kullanıcının [kiracıya dağıtmak için gerekli erişimi](deploy-to-tenant.md#required-access)olmalıdır.

İç içe bir dağıtım kullanmak için `scope` ve ayarlayın `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

Ya da kapsamını `/` Yönetim grupları gibi bazı kaynak türleri için olarak ayarlayabilirsiniz.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

Daha fazla bilgi için bkz. [Yönetim grubu](deploy-to-management-group.md#management-group).

## <a name="deploy-to-target-resource-group"></a>Hedef kaynak grubuna dağıt

Hedef kaynak grubunda kaynakları dağıtmak için, bu kaynakları `resources` şablonun bölümünde tanımlayın. Aşağıdaki şablon, dağıtım işleminde belirtilen kaynak grubunda bir depolama hesabı oluşturur.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>Birden çok kaynak grubuna dağıtma

Tek bir ARM şablonunda birden fazla kaynak grubuna dağıtım yapabilirsiniz. Üst şablon için olandan farklı bir kaynak grubunu hedeflemek için, [iç içe veya bağlı bir şablon](linked-templates.md)kullanın. Dağıtım kaynak türü içinde, iç içe şablonun dağıtılmasını istediğiniz abonelik KIMLIĞI ve kaynak grubu için değerleri belirtin. Kaynak grupları farklı aboneliklerde bulunabilir.

> [!NOTE]
> Tek bir dağıtımda **800 kaynak grubuna** dağıtım yapabilirsiniz. Genellikle, bu sınırlama, üst şablon için belirtilen bir kaynak grubuna ve iç içe veya bağlı dağıtımlardaki 799 ' e kadar kaynak grubuna dağıtabileceğiniz anlamına gelir. Ancak, ana şablonunuz yalnızca iç içe veya bağlı şablonlar içeriyorsa ve herhangi bir kaynak dağıtmadığından, iç içe veya bağlı dağıtımlara en fazla 800 kaynak grubu ekleyebilirsiniz.

Aşağıdaki örnek iki depolama hesabı dağıtır. İlk depolama hesabı, dağıtım işleminde belirtilen kaynak grubuna dağıtılır. İkinci depolama hesabı, ve parametrelerinde belirtilen kaynak grubuna dağıtılır `secondResourceGroup` `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

`resourceGroup`Mevcut olmayan bir kaynak grubunun adına ayarlarsanız, dağıtım başarısız olur.

Önceki şablonu test etmek ve sonuçları görmek için PowerShell veya Azure CLı kullanın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

İki depolama hesabını **aynı abonelikte** iki kaynak grubuna dağıtmak için şunu kullanın:

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

İki **aboneliğe** iki depolama hesabı dağıtmak için şunu kullanın:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

İki depolama hesabını **aynı abonelikte** iki kaynak grubuna dağıtmak için şunu kullanın:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

İki **aboneliğe** iki depolama hesabı dağıtmak için şunu kullanın:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

Bir kaynak grubu dağıtımından, bir abonelik düzeyine geçebilir ve bir kaynak grubu oluşturabilirsiniz. Aşağıdaki şablon, hedef kaynak grubuna bir depolama hesabı dağıtır ve belirtilen abonelikte yeni bir kaynak grubu oluşturur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Güvenlik Merkezi için çalışma alanı ayarlarını dağıtmaya ilişkin bir örnek için bkz. [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).

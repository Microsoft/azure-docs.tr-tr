---
title: Mantıksal kuruluş için kaynakları, kaynak grupları ve abonelikleri etiketleme
description: Azure kaynaklarını faturalandırma ve yönetmeye göre düzenlemek için etiketlerin nasıl uygulanacağını gösterir.
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1e755a378fd71ea2763cc3e43477876fa3e8c5d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934198"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Azure kaynaklarınızı ve yönetim hiyerarşisini düzenlemek için etiketleri kullanma

Bunları bir taksonomiye mantıksal olarak düzenlemek için Azure kaynaklarınıza, kaynak gruplarına ve aboneliklerinize Etiketler uygularsınız. Her etiket bir ad ve bir değer çiftinden oluşur. Örneğin, "Ortam" adını ve "Üretim" değerini üretimdeki tüm kaynaklara uygulayabilirsiniz.

Etiketleme stratejisi uygulama hakkında öneriler için bkz. [Kaynak adlandırma ve etiketleme karar Kılavuzu](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> Etiket adları, işlemler için büyük/küçük harfe duyarlıdır. Büyük/küçük harfe bakılmaksızın etiket adı olan bir etiket güncelleştirilir veya alınır. Ancak, kaynak sağlayıcısı, etiket adı için sağladığınız büyük/küçük harfleri tutabilir. Bu büyük küçük harf ' i maliyet raporlarında görürsünüz.
> 
> Etiket değerleri büyük/küçük harfe duyarlıdır.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Gerekli erişim

Kaynakları etiketleyerek gereken erişimi almanın iki yolu vardır.

- **Microsoft. resources/Tags** kaynak türüne yazma erişiminizin olması olabilir. Bu erişim, kaynağın kendisinin erişimi olmasa bile tüm kaynakları etiketlemenizi sağlar. [Etiket katılımcısı](../../role-based-access-control/built-in-roles.md#tag-contributor) rolü bu erişime izin verir. Şu anda, etiket katılımcısı rolü, Portal üzerinden kaynaklara veya kaynak gruplarına Etiketler uygulayamaz. Portal üzerinden aboneliğe Etiketler uygulayabilir. PowerShell ve REST API aracılığıyla tüm etiket işlemlerini destekler.  

- Kaynağın kendisi için yazma erişiminizin olması gerekir. [Katkıda](../../role-based-access-control/built-in-roles.md#contributor) bulunan rolü, herhangi bir varlığa Etiketler uygulamak için gerekli erişimi verir. Etiketleri yalnızca bir kaynak türüne uygulamak için bu kaynak için katkıda bulunan rolünü kullanın. Örneğin, sanal makinelere Etiketler uygulamak için [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)'nı kullanın.

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Etiketleri Uygula

Azure PowerShell- [New-aztag](/powershell/module/az.resources/new-aztag) ve [Update-aztag](/powershell/module/az.resources/update-aztag)etiketlerini uygulamak için iki komut sunar. Az. resources Module 1.12.0 veya sonraki bir sürüme sahip olmanız gerekir. Sürümünüzü ile denetleyebilirsiniz `Get-Module Az.Resources` . Bu modülü yükleyebilir veya Azure PowerShell 3.6.1 veya üstünü [yükleyebilirsiniz](/powershell/azure/install-az-ps) .

**New-AzTag** , kaynaktaki, kaynak grubundaki veya abonelikteki tüm etiketlerin yerini alır. Komutunu çağırırken etiketlemek istediğiniz varlığın kaynak KIMLIĞINI geçirin.

Aşağıdaki örnek, bir depolama hesabına bir etiket kümesi uygular:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Komut tamamlandığında, kaynağın iki etiketi olduğunu fark edersiniz.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Komutu yeniden çalıştırırsanız, ancak bu kez farklı etiketler içeriyorsa, önceki etiketlerin kaldırıldığına dikkat edin.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Zaten etiketlere sahip olan bir kaynağa etiket eklemek için **Update-AzTag**' i kullanın. **-Operation** parametresini **birleştirilecek** şekilde ayarlayın.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

İki yeni etiketin varolan iki etikete eklendiğinden emin olun.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Her etiket adı yalnızca bir değere sahip olabilir. Etiket için yeni bir değer sağlarsanız, birleştirme işlemini kullanıyor olsanız bile eski değer değişir. Aşağıdaki örnek durum etiketini normal iken yeşil olarak değiştirir.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

**Değiştirilecek** **-Operation** parametresini ayarladığınızda, varolan Etiketler Yeni Etiketler kümesiyle değiştirilir.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Kaynak üzerinde yalnızca yeni Etiketler kalır.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Aynı komutlar kaynak gruplarıyla veya aboneliklerle de çalışır. Etiketlemek istediğiniz kaynak grubu veya aboneliğin tanımlayıcısını geçirin.

Bir kaynak grubuna yeni bir etiket kümesi eklemek için şunu kullanın:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Bir kaynak grubunun etiketlerini güncelleştirmek için şunu kullanın:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Bir aboneliğe yeni bir etiket kümesi eklemek için şunu kullanın:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Bir aboneliğin etiketlerini güncelleştirmek için şunu kullanın:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Bir kaynak grubunda aynı ada sahip birden fazla kaynağınız olabilir. Bu durumda, her bir kaynağı aşağıdaki komutlarla ayarlayabilirsiniz:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Etiketleri listeleme

Bir kaynağın, kaynak grubunun veya aboneliğin etiketlerini almak için [Get-AzTag](/powershell/module/az.resources/get-aztag) komutunu kullanın ve varlık IÇIN kaynak kimliğini geçirin.

Bir kaynağın etiketlerini görmek için şunu kullanın:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Bir kaynak grubunun etiketlerini görmek için şunu kullanın:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Bir aboneliğin etiketlerini görmek için şunu kullanın:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Etikete göre Listele

Belirli bir etiket adı ve değeri olan kaynakları almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Herhangi bir etiket değerine sahip belirli bir etiket adına sahip kaynakları almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Belirli bir etiket adı ve değerine sahip kaynak gruplarını almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Etiketleri Kaldır

Belirli etiketleri kaldırmak için **Update-AzTag** ve set **-Operation** komutunu **silmek** üzere kullanın. Silmek istediğiniz etiketleri geçirin.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

Belirtilen Etiketler kaldırılır.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Tüm etiketleri kaldırmak için [Remove-AzTag](/powershell/module/az.resources/remove-aztag) komutunu kullanın.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI’si

### <a name="apply-tags"></a>Etiketleri Uygula

Azure CLı, etiketleri uygulamak için iki komut sunar- [az Tag create](/cli/azure/tag#az_tag_create) ve [az Tag Update](/cli/azure/tag#az_tag_update). Azure CLı 2.10.0 veya sonraki bir sürümü olmalıdır. Sürümünüzü ile denetleyebilirsiniz `az version` . Güncelleştirmek veya yüklemek için bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

**Az Tag create** , kaynak, kaynak grubu veya abonelikteki tüm etiketlerin yerini alır. Komutunu çağırırken etiketlemek istediğiniz varlığın kaynak KIMLIĞINI geçirin.

Aşağıdaki örnek, bir depolama hesabına bir etiket kümesi uygular:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Komut tamamlandığında, kaynağın iki etiketi olduğunu fark edersiniz.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Komutu yeniden çalıştırırsanız, ancak bu kez farklı etiketler içeriyorsa, önceki etiketlerin kaldırıldığına dikkat edin.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Zaten etiketlere sahip olan bir kaynağa etiket eklemek için kullanın `az tag update` . `--operation`Parametresini olarak ayarlayın `Merge` .

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

İki yeni etiketin varolan iki etikete eklendiğinden emin olun.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Her etiket adı yalnızca bir değere sahip olabilir. Etiket için yeni bir değer sağlarsanız, birleştirme işlemini kullanıyor olsanız bile eski değer değişir. Aşağıdaki örnek durum etiketini normal iken yeşil olarak değiştirir.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

`--operation`Parametresini öğesine ayarladığınızda `Replace` , varolan Etiketler Yeni Etiketler kümesiyle değiştirilmiştir.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

Kaynak üzerinde yalnızca yeni Etiketler kalır.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Aynı komutlar kaynak gruplarıyla veya aboneliklerle de çalışır. Etiketlemek istediğiniz kaynak grubu veya aboneliğin tanımlayıcısını geçirin.

Bir kaynak grubuna yeni bir etiket kümesi eklemek için şunu kullanın:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Bir kaynak grubunun etiketlerini güncelleştirmek için şunu kullanın:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Bir aboneliğe yeni bir etiket kümesi eklemek için şunu kullanın:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Bir aboneliğin etiketlerini güncelleştirmek için şunu kullanın:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Etiketleri listeleme

Bir kaynağın, kaynak grubunun veya aboneliğin etiketlerini almak için [az Tag List](/cli/azure/tag#az_tag_list) komutunu kullanın ve varlık IÇIN kaynak kimliğini geçirin.

Bir kaynağın etiketlerini görmek için şunu kullanın:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Bir kaynak grubunun etiketlerini görmek için şunu kullanın:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Bir aboneliğin etiketlerini görmek için şunu kullanın:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Etikete göre Listele

Belirli bir etiket adı ve değeri olan kaynakları almak için şunu kullanın:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Herhangi bir etiket değerine sahip belirli bir etiket adına sahip kaynakları almak için şunu kullanın:

```azurecli-interactive
az resource list --tag Team --query [].name
```

Belirli bir etiket adı ve değerine sahip kaynak gruplarını almak için şunu kullanın:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Etiketleri Kaldır

Belirli etiketleri kaldırmak için öğesini kullanın `az tag update` ve `--operation` olarak ayarlayın `Delete` . Silmek istediğiniz etiketleri geçirin.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

Belirtilen Etiketler kaldırılır.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Tüm etiketleri kaldırmak için [az Tag Delete](/cli/azure/tag#az_tag_delete) komutunu kullanın.

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Boşluk işleme

Etiket adlarınız veya değerleriniz boşluk içeriyorsa, bunları çift tırnak içine alın.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="arm-templates"></a>ARM şablonları

Dağıtım sırasında kaynakları, kaynak grupları ve abonelikleri bir Azure Resource Manager şablonuyla etiketleyebilir (ARM şablonu).

> [!NOTE]
> ARM şablonu aracılığıyla uyguladığınız Etiketler varolan etiketlerin üzerine yazar.

### <a name="apply-values"></a>Değerleri Uygula

Aşağıdaki örnek, üç etiketli bir depolama hesabı dağıtır. Etiketlerin ikisi ( `Dept` ve `Environment` ) değişmez değerler olarak ayarlanır. Bir Tag ( `LastDeployed` ), geçerli tarih için varsayılan değer olan bir parametreye ayarlanır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Nesne uygulama

Birkaç etiketi depolayan bir nesne parametresi tanımlayabilir ve bu nesneyi etiket öğesine uygulayabilirsiniz. Bu yaklaşım, bir önceki örnekte daha fazla esneklik sağlar çünkü nesne farklı özelliklere sahip olabilir. Nesnedeki her özellik, kaynak için ayrı bir etiket haline gelir. Aşağıdaki örnekte etiket parametresine uygulanan `tagValues` adlı bir parametre kullanılmaktadır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>JSON dizesi uygulama

Çok sayıda değeri tek bir etikete depolamak için, değerleri temsil eden bir JSON dizesi uygulayın. JSON dizesinin tamamı 256 karakteri aşmayan bir etiket olarak depolanır. Aşağıdaki örnekte bir JSON dizesindeki çok sayıda değeri içeren `CostCenter` adlı tek bir etiket kullanılmaktadır:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Kaynak grubundan Etiketler uygulama

Kaynak grubundan bir kaynağa etiket uygulamak için [resourceGroup ()](../templates/template-functions-resource.md#resourcegroup) işlevini kullanın. Etiket değerini alırken `tags[tag-name]` sözdizimi yerine sözdizimini kullanın `tags.tag-name` , çünkü bazı karakterler nokta gösteriminde doğru ayrıştırılmaz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Kaynak gruplarına veya aboneliklerine Etiketler uygulama

**Microsoft. resources/Tags** kaynak türünü dağıtarak bir kaynak grubuna veya aboneliğe Etiketler ekleyebilirsiniz. Etiketler, dağıtım için hedef kaynak grubuna veya aboneliğe uygulanır. Şablonu her dağıttığınızda, daha önce uygulanmış olan tüm etiketleri değiştirirsiniz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Etiketleri bir kaynak grubuna uygulamak için PowerShell veya Azure CLı kullanın. Etiketlemek istediğiniz kaynak grubuna dağıtın.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Etiketleri bir aboneliğe uygulamak için PowerShell veya Azure CLı kullanın. Etiketlemek istediğiniz aboneliğe dağıtın.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Abonelik dağıtımları hakkında daha fazla bilgi için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](../templates/deploy-to-subscription.md).

Aşağıdaki şablon bir nesneden bir kaynak grubuna veya aboneliğe Etiketler ekler.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure REST API aracılığıyla etiketlerle çalışmak için şunu kullanın:

* [Etiketler-kapsamda oluştur veya Güncelleştir](/rest/api/resources/resources/tags/createorupdateatscope) (Put işlemi)
* [Etiketler-kapsamda güncelleştirme](/rest/api/resources/resources/tags/updateatscope) (düzeltme eki işlemi)
* [Etiketler-kapsama al](/rest/api/resources/resources/tags/getatscope) (işlemi al)
* [Etiketler-kapsamda Sil](/rest/api/resources/resources/tags/deleteatscope) (silme işlemi)

## <a name="inherit-tags"></a>Etiketleri devralma

Kaynak grubuna veya aboneliğe uygulanan Etiketler kaynaklar tarafından devralınmaz. Bir abonelik veya kaynak grubundan kaynaklara Etiketler uygulamak için bkz. [Azure ilkeleri-Etiketler](tag-policies.md).

## <a name="tags-and-billing"></a>Etiketler ve faturalandırma

Etiketleri kullanarak faturalama verilerinizi gruplandırabilirsiniz. Örneğin, farklı organizasyonlar için birden çok sanal makine çalıştırıyorsanız, maliyet merkezine göre kullanımı gruplandırmak için etiketleri kullanın. Ayrıca etiketleri kullanarak, üretim ortamında çalışan sanal makineler için faturalama kullanımı gibi, maliyetleri çalışma zamanı ortamına göre kategorilere ayırabilirsiniz.

Azure portal kullanılabilir bir virgülle ayrılmış değerler (CSV) dosyası olan kullanım dosyasını indirerek Etiketler hakkında bilgi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Faturalandırma faturanızı ve günlük kullanım verilerinizi indirme veya görüntüleme](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Kullanım dosyasını Azure Hesap Merkezi indirirken **sürüm 2**' yi seçin. İle etiketleri destekleyen hizmetler **için Etiketler, Etiketler sütununda görüntülenir** .

REST API işlemler için bkz. [Azure faturalandırma REST API başvurusu](/rest/api/billing/).

## <a name="limitations"></a>Sınırlamalar

Etiketler için aşağıdaki sınırlamalar geçerlidir:

* Tüm kaynak türleri etiketleri desteklemez. Bir kaynak türüne etiket uygulayıp uygulayacağınızı öğrenmek için bkz. [Azure kaynakları Için etiket desteği](tag-support.md).
* Her kaynak, kaynak grubu ve abonelik en fazla 50 etiket adı/değer çiftine sahip olabilir. İzin verilen maksimum sayıdan daha fazla etiket uygulamanız gerekiyorsa, etiket değeri için bir JSON dizesi kullanın. JSON dizesi, tek etiket adına uygulanan birden fazla değer içerebilir. Bir kaynak grubu veya abonelik, her birinin 50 etiket adı/değer çifti olan çok sayıda kaynak içerebilir.
* Etiket adı 512 karakter ile sınırlıdır ve etiket değeri 256 karakter ile sınırlıdır. Depolama hesapları için etiket adı 128 karakter ile sınırlıdır ve etiket değeri 256 karakter ile sınırlıdır.
* Etiketler, Cloud Services gibi klasik kaynaklara uygulanamaz.
* Etiket adları şu karakterleri içeremez: `<` , `>` ,, `%` `&` , `\` , `?` , `/`

   > [!NOTE]
   > Şu anda Azure DNS bölgeleri ve Traffic Manager hizmetleri etiketinde boşluk kullanılmasına da izin vermez.
   >
   > Azure ön kapısının etiket adında kullanılması desteklenmez `#` .
   >
   > Azure Otomasyonu ve Azure CDN yalnızca kaynaklardaki 15 etiketi destekler.

## <a name="next-steps"></a>Sonraki adımlar

* Tüm kaynak türleri etiketleri desteklemez. Bir kaynak türüne etiket uygulayıp uygulayacağınızı öğrenmek için bkz. [Azure kaynakları Için etiket desteği](tag-support.md).
* Etiketleme stratejisi uygulama hakkında öneriler için bkz. [Kaynak adlandırma ve etiketleme karar Kılavuzu](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

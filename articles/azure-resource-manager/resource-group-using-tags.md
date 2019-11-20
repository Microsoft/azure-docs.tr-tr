---
title: Mantıksal kuruluş için kaynakları etiketleme
description: Azure kaynaklarını faturalandırma ve yönetmeye göre düzenlemek için etiketlerin nasıl uygulanacağını gösterir.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b332ae86e714d4b642f921d217d80e802fa60572
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149597"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Azure kaynaklarınızı düzenlemek için etiketleri kullanma

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Etiketleri kaynaklara uygulamak için, kullanıcının bu kaynak türüne yazma erişimi olmalıdır. Etiketleri tüm kaynak türlerine uygulamak için [katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor) rolünü kullanın. Etiketleri yalnızca bir kaynak türüne uygulamak için bu kaynak için katkıda bulunan rolünü kullanın. Örneğin, sanal makinelere Etiketler uygulamak için [sanal makine katılımcısı](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)'nı kullanın.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>İlkeler

Etiketleme kuralları ve kurallarını zorlamak için [Azure ilkesi](../governance/policy/overview.md) ' ni kullanabilirsiniz. Bir ilke oluşturarak, kuruluşunuz için beklenen etiketlerle uyumlu olmayan, aboneliğinize dağıtılan kaynakların senaryosundan kaçının. Etiketlerin el ile uygulanması veya uyumlu olmayan kaynakları aramak yerine, dağıtım sırasında gerekli etiketleri otomatik olarak uygulayan bir ilke oluşturabilirsiniz. Etiketler artık yeni [değişiklik](../governance/policy/concepts/effects.md#modify) efekti ve bir [Düzeltme göreviyle](../governance/policy/how-to/remediate-resources.md)mevcut kaynaklara da uygulanabilir. Aşağıdaki bölümde, Etiketler için örnek ilkeler gösterilmektedir.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bir *kaynak grubunun* mevcut etiketlerini görmek şunu kullanın:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Bu betik aşağıdaki biçimde veri döndürür:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*Kaynak kimliği belirtilmiş bir kaynağın* mevcut etiketlerini görmek için şunu kullanın:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

*Kaynak kimliği belirtilmiş bir kaynağın* mevcut etiketlerini görmek için şunu da kullanabilirsiniz:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*Belirli bir etikete sahip kaynak gruplarını* almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

*Belirli bir etikete sahip kaynakları* almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

*Belirli bir etiket adına sahip kaynakları*almak için şunu kullanın:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Bir kaynağa veya kaynak grubuna etiket uyguladığınız her durumda ilgili kaynağın veya kaynak grubunun üzerinde mevcut olan etiketlerin üzerine yazarsınız. Bu nedenle, kaynakta veya kaynak grubunda etiketlerin mevcut olup olmamasına bağlı olarak farklı bir yaklaşım kullanmanız gerekir.

*Mevcut etiketi olmayan bir kaynak grubuna* etiket eklemek için şunu kullanın:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

*Mevcut etiketleri olan bir kaynak grubuna* etiket eklemek için, mevcut etiketleri alın, yeni etiketi ekleyin ve etiketleri yeniden uygulayın:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

*Mevcut etiketi olmayan bir kaynağa* etiket eklemek için şunu kullanın:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

*Mevcut etiketi olan bir kaynağa* etiket eklemek için şunu kullanın:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Bir kaynak grubundaki tüm etiketleri kaynaklarına uygulamak ve *mevcut etiketleri kaynaklarda tutmak*için aşağıdaki betiği kullanın:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Bir kaynak grubundaki tüm etiketleri kaynaklarına uygulamak ve *mevcut etiketleri yinelenen olmayan kaynaklarda tutmak*için aşağıdaki betiği kullanın:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Tüm etiketleri kaldırmak için bir boş karma tablosu geçirin:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

Bir *kaynak grubunun* mevcut etiketlerini görmek şunu kullanın:

```azurecli
az group show -n examplegroup --query tags
```

Bu betik aşağıdaki biçimde veri döndürür:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Ya da, *belirtilen bir ada, türe ve kaynak grubuna sahip bir kaynağın*mevcut etiketlerini görmek için şunu kullanın:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Bir kaynak koleksiyonu aracılığıyla döngü oluştururken kaynağı kaynak KIMLIĞIYLE göstermek isteyebilirsiniz. Bu makalenin ilerleyen kısımlarında bir örnek gösterilmektedir. *Kaynak kimliği belirtilmiş bir kaynağın* mevcut etiketlerini görmek için şunu kullanın:

```azurecli
az resource show --id <resource-id> --query tags
```

Belirli bir etikete sahip kaynak gruplarını almak için `az group list`kullanın:

```azurecli
az group list --tag Dept=IT
```

Belirli bir etiketi ve değeri olan tüm kaynakları almak için `az resource list`kullanın:

```azurecli
az resource list --tag Dept=Finance
```

Bir kaynağa veya kaynak grubuna etiket uyguladığınız her durumda ilgili kaynağın veya kaynak grubunun üzerinde mevcut olan etiketlerin üzerine yazarsınız. Bu nedenle, kaynakta veya kaynak grubunda etiketlerin mevcut olup olmamasına bağlı olarak farklı bir yaklaşım kullanmanız gerekir.

*Mevcut etiketi olmayan bir kaynak grubuna* etiket eklemek için şunu kullanın:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

*Mevcut etiketi olmayan bir kaynağa* etiket eklemek için şunu kullanın:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Zaten etiketlere sahip olan bir kaynağa etiket eklemek için, varolan etiketleri alın, bu değeri yeniden biçimlendirin ve var olan ve yeni etiketleri yeniden uygulayın:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Bir kaynak grubundaki tüm etiketleri kaynaklarına uygulamak ve *mevcut etiketleri kaynaklarda tutmak*için aşağıdaki betiği kullanın:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Bir kaynak grubundaki tüm etiketleri kaynaklarına uygulamak ve *mevcut etiketleri kaynaklar üzerinde tutmak*için aşağıdaki betiği kullanın:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Şablonlar

Dağıtım sırasında bir kaynağı etiketlemek için, dağıtmakta olduğunuz kaynağa `tags` öğesini ekleyin. Etiket adını ve değerini belirtin.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Etiket adına değişmez değer uygulama

Aşağıdaki örnekte değişmez değerlere ayarlanmış iki etiketi (`Dept` ve `Environment`) olan bir depolama hesabı gösterilmektedir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "Dept": "Finance",
                "Environment": "Production"
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

Bir tarih saat değerine bir etiket ayarlamak için [UtcNow işlevini](resource-group-template-functions-string.md#utcnow)kullanın.

### <a name="apply-an-object-to-the-tag-element"></a>Nesne etiketine öğe uygulama

Birkaç etiketi depolayan bir nesne parametresi tanımlayabilir ve bu nesneyi etiket öğesine uygulayabilirsiniz. Nesnedeki her özellik, kaynak için ayrı bir etiket haline gelir. Aşağıdaki örnekte etiket parametresine uygulanan `tagValues` adlı bir parametre kullanılmaktadır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="apply-a-json-string-to-the-tag-name"></a>Etiket adına JSON dizesi uygulama

Çok sayıda değeri tek bir etikete depolamak için, değerleri temsil eden bir JSON dizesi uygulayın. JSON dizesinin tamamı 256 karakteri aşmayan bir etiket olarak depolanır. Aşağıdaki örnekte bir JSON dizesindeki çok sayıda değeri içeren `CostCenter` adlı tek bir etiket kullanılmaktadır:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Kaynak grubundan bir kaynağa etiket uygulamak için [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) işlevini kullanın. Etiket değerini alırken, `tags.tag-name` sözdizimi yerine `tags.[tag-name]` sözdizimini kullanın, çünkü bazı karakterler nokta gösteriminde doğru ayrıştırılmaz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure portal ve PowerShell, arka planda [Kaynak Yöneticisi REST API](https://docs.microsoft.com/rest/api/resources/) kullanır. Etiketlemeyi başka bir ortamla tümleştirmeniz gerekiyorsa, kaynak KIMLIĞI üzerinde **Al** ' ı kullanarak Etiketler alabilir ve bir **yama** çağrısı kullanarak etiket kümesini güncelleştirebilirsiniz.

## <a name="tags-and-billing"></a>Etiketler ve faturalandırma

Fatura verilerinizi gruplandırmak için Etiketler kullanabilirsiniz. Örneğin, farklı kuruluşlar için birden çok VM çalıştırıyorsanız, kullanımı maliyet merkezine göre gruplamak için etiketleri kullanın. Ayrıca, üretim ortamında çalışan VM 'Ler için faturalandırma kullanımı gibi çalışma zamanı ortamına göre maliyetleri sınıflandırmak için Etiketler de kullanabilirsiniz.

[Azure Kaynak kullanımı ve RateCard API 'leri](../billing/billing-usage-rate-card-overview.md) veya kullanım virgülle ayrılmış değerler (CSV) dosyası aracılığıyla Etiketler hakkında bilgi alabilirsiniz. Kullanım dosyasını [Azure Hesap Merkezi](https://account.azure.com/Subscriptions) veya Azure Portal indirin. Daha fazla bilgi için bkz. [Azure Faturalandırma faturanızı ve günlük kullanım verilerinizi indirme veya görüntüleme](../billing/billing-download-azure-invoice-daily-usage-date.md). Kullanım dosyasını Azure Hesap Merkezi indirirken **sürüm 2**' yi seçin. İle etiketleri destekleyen hizmetler **için Etiketler, Etiketler sütununda görüntülenir** .

REST API işlemler için bkz. [Azure faturalandırma REST API başvurusu](/rest/api/billing/).

## <a name="next-steps"></a>Sonraki adımlar

* Tüm kaynak türleri etiketleri desteklemez. Bir kaynak türüne etiket uygulayıp uygulayacağınızı öğrenmek için bkz. [Azure kaynakları Için etiket desteği](tag-support.md).
* Portalı kullanmaya giriş için bkz. [Azure kaynaklarınızı yönetmek için Azure Portal kullanma](manage-resource-groups-portal.md).  

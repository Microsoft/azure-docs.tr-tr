---
title: Şablon belirtimlerini oluşturma ve dağıtma
description: Şablon özelliklerinin nasıl oluşturulduğunu ve kuruluşunuzdaki diğer kullanıcılarla nasıl paylaşılacağını açıklar.
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 2f4aec6d9fa07edf36dea68a23ba12eb5f72d308
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626093"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager şablonu özellikleri (Önizleme)

Şablon belirtimi, daha sonra dağıtım için Azure 'da bir Azure Resource Manager şablonu (ARM şablonu) depolamaya yönelik bir kaynak türüdür. Bu kaynak türü, ARM şablonlarını kuruluşunuzdaki diğer kullanıcılarla paylaşmanıza olanak sağlar. Tıpkı diğer Azure kaynakları gibi, şablon belirtimini paylaşmak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz.

**Microsoft. resources/Templatespec** , şablon özelliklerinin kaynak türüdür. Ana şablondan ve herhangi bir sayıda bağlantılı şablondan oluşur. Azure, şablon özelliklerini kaynak gruplarında güvenli bir şekilde depolar. Şablon özellikleri [sürümü oluşturmayı](#versioning)destekler.

Şablon belirtimini dağıtmak için PowerShell, Azure CLı, Azure portal, REST ve diğer desteklenen SDK 'lar ve istemciler gibi standart Azure araçlarını kullanırsınız. Şablonla aynı komutları kullanın.

> [!NOTE]
> Şablon özellikleri şu anda önizleme aşamasındadır. Azure PowerShell ile kullanmak için, [5.0.0 veya üzeri sürümünü](/powershell/azure/install-az-ps)yüklemelisiniz. Azure CLı ile kullanmak için, [Version 2.14.2 veya üstünü](/cli/azure/install-azure-cli)kullanın.

## <a name="why-use-template-specs"></a>Şablon özellikleri neden kullanılmalıdır?

Şablon özellikleri aşağıdaki avantajları sağlar:

* Şablon spec için standart ARM şablonları kullanırsınız.
* Erişimi, SAS belirteçleri yerine Azure RBAC üzerinden yönetirsiniz.
* Kullanıcılar şablon belirtimini, şablona yazma erişimi olmadan dağıtabilir.
* Şablon belirtimini, PowerShell betiği veya DevOps işlem hattı gibi mevcut bir dağıtım işlemiyle tümleştirebilirsiniz.

Şablon Özellikleri, kurallı şablonlar oluşturmanızı ve bunları kuruluşunuzdaki takımlarla paylaşmanızı sağlar. Şablon özellikleri güvenli hale getiriyoruz çünkü dağıtım için Azure Resource Manager kullanılabilir, ancak doğru izin olmadan kullanıcılar için erişilebilir durumda değil. Kullanıcıların şablonunu dağıtmak için yalnızca şablon belirtimine okuma erişimi olması gerekir, böylece şablonu başkalarının değiştirmesine izin vermeden paylaşabilirsiniz.

Şu anda bir GitHub deposu veya depolama hesabında şablonunuz varsa, şablonları paylaşmaya ve kullanmaya çalışırken çeşitli güçlükler çalıştırın. Şablonu dağıtmak için, şablonu genel olarak erişilebilir yapmanız veya SAS belirteçleriyle erişimi yönetmeniz gerekir. Bu kısıtlamayı aşmak için, kullanıcılar, son olarak orijinal şablonınızdan ayrılan yerel kopyalar oluşturabilir. Şablon Özellikleri, şablonları paylaşmayı basitleştirir.

Bir Template spec 'e dahil ettiğiniz şablonlar, kuruluşunuzun gereksinimlerini ve kılavuzunu izlemek için kuruluşunuzdaki yöneticiler tarafından doğrulanmalıdır.

## <a name="create-template-spec"></a>Şablon belirtimi oluşturma

Aşağıdaki örnekte, Azure 'da bir depolama hesabı oluşturmak için basit bir şablon gösterilmektedir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

Şablon belirtimini oluşturduğunuzda, PowerShell veya CLı komutları ana şablon dosyasına geçirilir. Ana şablon bağlantılı şablonlara başvuruyorsa, komutlar şablon belirtimini oluşturacak şekilde bulup paketleyebilir. Daha fazla bilgi için bkz. [bağlantılı şablonlarla şablon belirtimi oluşturma](#create-a-template-spec-with-linked-templates).

Kullanarak bir şablon belirtimi oluşturun:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

Aboneliğinizdeki tüm şablon özelliklerini kullanarak şu şekilde görüntüleyebilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

İle sürümleri de dahil olmak üzere bir şablon belirtiminin ayrıntılarını görüntüleyebilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>Şablon belirtimini dağıt

Şablon belirtimini oluşturduktan sonra, şablon belirtimine **okuma** erişimi olan kullanıcılar onu dağıtabilir. Erişim verme hakkında bilgi için bkz. [öğretici: Azure PowerShell kullanarak Azure kaynaklarına grup erişimi verme](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Şablon Özellikleri Portal, PowerShell, Azure CLı veya daha büyük bir şablon dağıtımında bağlantılı bir şablon aracılığıyla dağıtılabilir. Bir kuruluştaki kullanıcılar, bir şablon belirtimini Azure 'daki herhangi bir kapsama (kaynak grubu, abonelik, yönetim grubu veya kiracı) dağıtabilir.

Bir şablon için bir yol veya URI geçirmek yerine, kaynak KIMLIĞINI sağlayarak bir şablon belirtimi dağıtırsınız. Kaynak KIMLIĞI aşağıdaki biçimdedir:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Kaynak KIMLIĞININ, şablon belirtimi için bir sürüm adı içerdiğine dikkat edin.

Örneğin, bir şablon belirtimini aşağıdaki komutla dağıtırsınız.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

Pratikte, genellikle `Get-AzTemplateSpec` `az ts show` dağıtmak istediğiniz şablon belirtiminin kimliğini almak için veya öğesini çalıştırabileceksiniz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

Şablon belirtimini dağıtmak için aşağıdaki biçimde bir URL de açabilirsiniz:

```url
https://portal.azure.com/#create/Microsoft.Template/templateSpecVersionId/%2fsubscriptions%2f{subscription-id}%2fresourceGroups%2f{resource-group-name}%2fproviders%2fMicrosoft.Resources%2ftemplateSpecs%2f{template-spec-name}%2fversions%2f{template-spec-version}
```

## <a name="parameters"></a>Parametreler

Parametreleri Template spec 'e geçirmek, parametreleri bir ARM şablonuna geçirme gibidir. Parametre değerlerini satır içi veya bir parametre dosyasına ekleyin.

Bir parametreyi satır içine geçirmek için şunu kullanın:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Yerel bir parametre dosyası oluşturmak için şunu kullanın:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

Ve bu parametre dosyasını şu ile geçirin:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="versioning"></a>Sürüm Oluşturma

Bir şablon belirtimi oluşturduğunuzda, bunun için bir sürüm adı sağlarsınız. Şablon kodu üzerinde yineleme yaparken, var olan bir sürümü güncelleştirebilir (düzeltmeler için) veya yeni bir sürüm yayımlayabilirsiniz. Sürüm bir metin dizesidir. Anlamsal sürüm oluşturma da dahil olmak üzere tüm sürüm oluşturma sistemini izlemeyi tercih edebilirsiniz. Şablon belirtiminin kullanıcıları, dağıtma sırasında kullanmak istedikleri sürüm adını sağlayabilir.

## <a name="use-tags"></a>Etiketleri kullanma

[Etiketler](../management/tag-resources.md) , kaynaklarınızı mantıksal olarak düzenlemenize yardımcı olur. Azure PowerShell ve Azure CLı kullanarak şablon özelliklerine Etiketler ekleyebilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts update \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

Bir şablon belirtimini oluşturma veya değiştirme sırasında, belirtilen sürüm parametresiyle, ancak Tag/Tags parametresi olmadan:

- Şablon belirtimi varsa ve etiketler içeriyorsa, ancak sürüm yoksa, yeni sürüm mevcut şablon belirtimi ile aynı etiketleri devralır.

Hem Tag/Tags parametresi hem de belirtilen sürüm parametresi ile bir şablon belirtimini oluştururken veya değiştirirken:

- Şablon belirtiminin ve sürümü yoksa, Etiketler hem yeni şablon belirtimine hem de yeni sürüme eklenir.
- Şablon belirtimi varsa, ancak sürüm yoksa, Etiketler yalnızca yeni sürüme eklenir.
- Hem şablon belirtimi hem de sürüm varsa, Etiketler yalnızca sürüm için geçerlidir.

Etiket/etiket parametresi belirtilmiş ancak belirtilen sürüm parametresi olmadan bir şablonu değiştirirken, Etiketler yalnızca şablon belirtimine eklenir.

## <a name="create-a-template-spec-with-linked-templates"></a>Bağlantılı şablonlarla şablon belirtimi oluşturma

Şablon belirtiminin ana şablonu bağlantılı şablonlara başvuruyorsa, PowerShell ve CLı komutları, bağlantılı şablonları yerel sürücünüzden otomatik olarak bulabilir ve paketleyebilir. Şablon özelliklerini barındırmak için depolama hesaplarını veya depoları el ile yapılandırmanız gerekmez; şablon belirtimi kaynağında her şey kendi kendine dahil edilir.

Aşağıdaki örnek, iki bağlantılı şablonla bir ana şablondan oluşur. Örnek yalnızca şablonun bir alıntısıdır. Diğer şablonlara bağlamak için adlı bir özellik kullandığına dikkat edin `relativePath` . `apiVersion` `2020-06-01` Dağıtımlar kaynağı için veya sonraki bir sürümünü kullanmanız gerekir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

Önceki örnekte şablon belirtimini oluşturmak için PowerShell veya CLı komutu yürütüldüğünde, komut üç dosya bulur-ana şablon, Web uygulaması şablonu ( `webapp.json` ) ve veritabanı şablonu ( `database.json` ) ve bunları şablon belirtimine göre paketler.

Daha fazla bilgi için bkz. [öğretici: bağlantılı şablonlarla şablon belirtimi oluşturma](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Şablon belirtimini bağlantılı şablon olarak dağıtma

Şablon belirtimini oluşturduktan sonra, bir ARM şablonundan veya başka bir şablon belirtiminde yeniden kullanmak kolaydır. Şablon belirtimine, kaynak KIMLIĞINI şablonunuza ekleyerek bağlarsınız. Bağlı şablon belirtimi, ana şablonu dağıtırken otomatik olarak dağıtılır. Bu davranış, modüler şablon özellikleri geliştirmenize ve bunları gerektiği gibi yeniden kullanmanıza olanak tanır.

Örneğin, ağ kaynaklarını dağıtan bir şablon belirtimi ve depolama kaynaklarını dağıtan başka bir şablon özelliği oluşturabilirsiniz. ARM şablonlarında, her zaman ağ veya depolama kaynaklarını yapılandırmak için bu iki şablon özelliklerine bağlantı oluşturmanız gerekir.

Aşağıdaki örnek, önceki örneğe benzerdir, ancak `id` özelliğini kullanarak bir `relativePath` Yerel şablona bağlanacak özelliği yerine bir şablon belirtimine bağlayabilirsiniz. `2020-06-01`Dağıtımlar kaynağı IÇIN API sürümü için kullanın. Örnekte, şablon özellikleri **Templatespecsrg** adlı bir kaynak grubunda bulunur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

Şablon özelliklerinin bağlanması hakkında daha fazla bilgi için bkz. [öğretici: şablon belirtimini bağlantılı şablon olarak dağıtma](template-specs-deploy-linked-template.md).

## <a name="next-steps"></a>Sonraki adımlar

* Bir şablon belirtimi oluşturup dağıtmak için bkz. [hızlı başlangıç: Create and Deploy Template spec](quickstart-create-template-specs.md).

* Şablon özelliklerine bağlama şablonları hakkında daha fazla bilgi için bkz. [öğretici: bağlantılı şablonlarla şablon belirtimi oluşturma](template-specs-create-linked.md).

* Şablon belirtimini bağlantılı şablon olarak dağıtma hakkında daha fazla bilgi için bkz. [öğretici: şablon belirtimini bağlantılı şablon olarak dağıtma](template-specs-deploy-linked-template.md).

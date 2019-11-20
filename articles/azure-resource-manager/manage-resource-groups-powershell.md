---
title: Kaynak gruplarını yönetme-Azure PowerShell
description: Kaynak gruplarınızı Azure Resource Manager kullanarak yönetmek için Azure PowerShell kullanın. Kaynak grupları oluşturma, listeleme ve silme işlemlerinin nasıl yapılacağını gösterir.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5d0d7394de2763c785640b0981de9017cf0a7ae7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149801"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure PowerShell kullanarak Azure Resource Manager kaynak gruplarını yönetme

Azure Kaynak gruplarınızı yönetmek için [Azure Resource Manager](resource-group-overview.md) Azure PowerShell nasıl kullanacağınızı öğrenin. Azure kaynaklarını yönetmek için bkz. [Azure PowerShell kullanarak Azure kaynaklarını yönetme](./manage-resources-powershell.md).

Kaynak gruplarını yönetme hakkında diğer makaleler:

- [Azure portal kullanarak Azure kaynak gruplarını yönetme](./manage-resources-portal.md)
- [Azure CLı kullanarak Azure kaynak gruplarını yönetme](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Kaynak grubu nedir?

Kaynak grubu, bir Azure çözümü için ilgili kaynakları bir arada tutan kapsayıcıdır. Kaynak grubu bir çözümün tüm kaynaklarını veya yalnızca grup olarak yönetmek istediğiniz kaynakları içerebilir. Kuruluş için önemli olan faktörleri temel alarak kaynakları kaynak gruplarına nasıl ayıracağınıza siz karar verirsiniz. Genellikle, bunları bir grup olarak kolayca dağıtabilmeniz, güncelleştirebilmeniz ve silebilmeniz için aynı yaşam döngüsünü paylaşan kaynakları ekleyin.

Kaynak grubu, kaynaklarla ilgili meta verileri depolar. Bu nedenle, kaynak grubu için bir konum belirttiğinizde meta verilerin nereye depolanacağını belirtirsiniz. Uyumluluk nedeniyle verilerinizin belirli bir bölgeye depolandığından emin olmanız gerekebilir.

Kaynak grubu, kaynaklarla ilgili meta verileri depolar. Kaynak grubu için bir konum belirttiğinizde, meta verilerin nerede depolandığını belirtirsiniz.

## <a name="create-resource-groups"></a>Kaynak grupları oluşturma

Aşağıdaki PowerShell betiği bir kaynak grubu oluşturur ve ardından kaynak grubunu gösterir.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Kaynak gruplarını listeleme

Aşağıdaki PowerShell betiği, aboneliğinizin altındaki kaynak gruplarını listeler.

```azurepowershell-interactive
Get-AzResourceGroup
```

Bir kaynak grubu almak için:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Kaynak gruplarını silme

Aşağıdaki PowerShell betiği bir kaynak grubunu siler:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Kaynakların silinmesini Azure Resource Manager nasıl sipariş Azure Resource Manager hakkında daha fazla bilgi için bkz. [kaynak grubu silme](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Kaynakları var olan bir kaynak grubuna dağıtma

Bkz. [kaynakları var olan bir kaynak grubuna dağıtma](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Bir kaynak grubu dağıtımını doğrulamak için bkz. [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Kaynak grubu ve kaynakları dağıtma

Kaynak Yöneticisi şablonu kullanarak bir kaynak grubu oluşturabilir ve gruba kaynak dağıtabilirsiniz. Daha fazla bilgi için bkz. [kaynak grubu oluşturma ve kaynakları dağıtma](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Dağıtım başarısız olduğunda yeniden Dağıt

Bu özellik *hata durumunda geri alma*olarak da bilinir. Daha fazla bilgi için bkz. [dağıtım başarısız olduğunda yeniden dağıtma](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Başka bir kaynak grubuna veya aboneliğe taşıma

Gruptaki kaynakları başka bir kaynak grubuna taşıyabilirsiniz. Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](./resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Kaynak gruplarını kilitle

Kilitleme, kuruluşunuzdaki diğer kullanıcıların Azure aboneliği, kaynak grubu veya kaynak gibi önemli kaynakları yanlışlıkla silmesini veya değiştirmelerini engeller. 

Aşağıdaki betik bir kaynak grubunu kilitleyerek kaynak grubu silinemez.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Aşağıdaki betik, bir kaynak grubu için tüm kilitleri alır:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Kaynak gruplarını etiketleme

Varlıklarınızı mantıksal olarak düzenlemek için kaynak gruplarına ve kaynaklarına Etiketler uygulayabilirsiniz. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Kaynak gruplarını şablonlara aktarma

Kaynak grubunuzu ayarladıktan sonra kaynak grubu için bir Kaynak Yöneticisi şablonu görüntüleyebilirsiniz. Şablonu dışarı aktarmak iki avantaj sunar:

- Şablon, tüm altyapıyı içerdiğinden çözümün gelecekteki dağıtımlarını otomatikleştirin.
- Çözümünüzü temsil eden JavaScript Nesne Gösterimi (JSON) bakarak Şablon sözdizimini öğrenin.

Bir kaynak grubundaki tüm kaynakları dışarı aktarmak için [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet 'ini kullanın ve kaynak grubu adını sağlayın.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Şablonu yerel bir dosya olarak kaydeder.

Kaynak grubundaki tüm kaynakları dışarı aktarmak yerine, hangi kaynakların dışarı aktarılacağını seçebilirsiniz.

Bir kaynağı dışarı aktarmak için bu kaynak KIMLIĞINI geçirin.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Birden fazla kaynağı dışarı aktarmak için, kaynak kimliklerini bir dizide geçirin.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Şablonu dışarı aktarırken, şablonda parametrelerin kullanılıp kullanılmayacağını belirtebilirsiniz. Varsayılan olarak, kaynak adları için parametreler dahil edilir ancak varsayılan bir değere sahip değildir. Dağıtım sırasında bu parametre değerini geçirmeniz gerekir.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

Kaynağında, adı için parametresi kullanılır.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Şablonu dışarı aktarırken `-IncludeParameterDefaultValue` parametresini kullanırsanız, şablon parametresi geçerli değere ayarlanmış bir varsayılan değer içerir. Bu varsayılan değeri kullanabilir ya da farklı bir değere geçirerek varsayılan değerin üzerine yazabilirsiniz.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Şablonu dışarı aktarırken `-SkipResourceNameParameterization` parametresini kullanırsanız, kaynak adları için parametreler şablona dahil edilmez. Bunun yerine, kaynak adı kaynak üzerinde doğrudan geçerli değerine ayarlanır. Dağıtım sırasında adı özelleştiremezsiniz.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Daha fazla bilgi için, [Azure Portal ' de tek ve çoklu kaynak verme şablonuna](./export-template-portal.md)bakın.

## <a name="manage-access-to-resource-groups"></a>Kaynak gruplarına erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md), Azure'daki kaynaklara erişimi yönetmek için kullanılan sistemdir. Daha fazla bilgi için bkz. [RBAC ve Azure PowerShell kullanarak erişimi yönetme](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Resource Manager öğrenmek için bkz. [Azure Resource Manager genel bakış](./resource-group-overview.md).
- Kaynak Yöneticisi Şablon sözdizimini öğrenmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](./resource-group-authoring-templates.md).
- Şablon geliştirmeyi öğrenmek için [adım adım öğreticiler](/azure/azure-resource-manager/)bölümüne bakın.
- Azure Resource Manager şablonu şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).
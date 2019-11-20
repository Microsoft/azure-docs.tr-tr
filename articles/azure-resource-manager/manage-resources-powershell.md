---
title: Kaynakları yönetme-Azure PowerShell
description: Kaynaklarınızı yönetmek için Azure PowerShell ve Azure Resource Manager kullanın. Kaynakların nasıl dağıtılacağını ve silineceğini gösterir.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: d9714c90af326c34a64ac13b0d10e21fbe5469d4
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146521"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure PowerShell kullanarak Azure kaynaklarını yönetme

Azure kaynaklarınızı yönetmek için [Azure Resource Manager](resource-group-overview.md) Azure PowerShell nasıl kullanacağınızı öğrenin. Kaynak gruplarını yönetmek için bkz. [Azure PowerShell kullanarak Azure kaynak gruplarını yönetme](./manage-resource-groups-powershell.md).

Kaynakları yönetme hakkında diğer makaleler:

- [Azure portal kullanarak Azure kaynaklarını yönetme](./manage-resources-portal.md)
- [Azure CLı kullanarak Azure kaynaklarını yönetme](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Kaynakları var olan bir kaynak grubuna dağıtma

Azure kaynaklarını Azure PowerShell kullanarak doğrudan dağıtabilir veya Azure kaynakları oluşturmak için bir Kaynak Yöneticisi şablonu dağıtabilirsiniz.

### <a name="deploy-a-resource"></a>Kaynak dağıtma

Aşağıdaki betik bir depolama hesabı oluşturur.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Şablon dağıtma

Aşağıdaki betik, bir depolama hesabı oluşturmak için hızlı başlangıç şablonu dağıt oluşturur. Daha fazla bilgi için bkz. [hızlı başlangıç: Visual Studio Code kullanarak Azure Resource Manager şablonları oluşturma](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure PowerShell ile kaynak dağıtma](./resource-group-template-deploy.md).

## <a name="deploy-a-resource-group-and-resources"></a>Kaynak grubu ve kaynakları dağıtma

Bir kaynak grubu oluşturabilir ve gruba kaynak dağıtabilirsiniz. Daha fazla bilgi için bkz. [kaynak grubu oluşturma ve kaynakları dağıtma](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Kaynakları birden çok aboneliğe veya kaynak grubuna dağıtma

Genellikle, şablonunuzda bulunan tüm kaynakları tek bir kaynak grubuna dağıtırsınız. Ancak, bir kaynak kümesini birlikte dağıtmak ve bunları farklı kaynak gruplarına veya aboneliklerine yerleştirmek istediğiniz senaryolar vardır. Daha fazla bilgi için bkz. [Azure kaynaklarını birden çok aboneliğe veya kaynak grubuna dağıtma](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Kaynakları silme

Aşağıdaki betik bir depolama hesabının nasıl silineceğini gösterir.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Kaynakların silinmesini Azure Resource Manager nasıl sipariş Azure Resource Manager hakkında daha fazla bilgi için bkz. [kaynak grubu silme](./resource-group-delete.md).

## <a name="move-resources"></a>Kaynakları taşıma

Aşağıdaki betik, bir depolama hesabının bir kaynak grubundan başka bir kaynak grubuna nasıl kaldırılacağını gösterir.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).

## <a name="lock-resources"></a>Kaynakları kilitleme

Kilitleme, kuruluşunuzdaki diğer kullanıcıların Azure aboneliği, kaynak grubu veya kaynak gibi önemli kaynakları yanlışlıkla silmesini veya değiştirmelerini engeller. 

Aşağıdaki betik, bir depolama hesabını kilitleyerek hesabın silinememesi sağlanır.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Aşağıdaki betik bir depolama hesabı için tüm kilitleri alır:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Aşağıdaki betik bir depolama hesabının bir kilidini siler:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Kaynakları etiketleme

Etiketleme, kaynak grubunuzun ve kaynaklarınızın mantıksal olarak düzenlenmesine yardımcı olur. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](./resource-group-using-tags.md#powershell).

## <a name="manage-access-to-resources"></a>Kaynaklara erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md), Azure'daki kaynaklara erişimi yönetmek için kullanılan sistemdir. Daha fazla bilgi için bkz. [RBAC ve Azure PowerShell kullanarak erişimi yönetme](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Resource Manager öğrenmek için bkz. [Azure Resource Manager genel bakış](./resource-group-overview.md).
- Kaynak Yöneticisi Şablon sözdizimini öğrenmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](./resource-group-authoring-templates.md).
- Şablon geliştirmeyi öğrenmek için [adım adım öğreticiler](/azure/azure-resource-manager/)bölümüne bakın.
- Azure Resource Manager şablonu şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).

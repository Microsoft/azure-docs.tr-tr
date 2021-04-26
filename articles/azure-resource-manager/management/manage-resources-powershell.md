---
title: Kaynakları yönetme-Azure PowerShell
description: Kaynaklarınızı yönetmek için Azure PowerShell ve Azure Resource Manager kullanın. Kaynakların nasıl dağıtılacağını ve silineceğini gösterir.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3b1d9f8d01e722e4ec5078d92ef4148072da25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185751"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure PowerShell kullanarak Azure kaynaklarını yönetme

Azure kaynaklarınızı yönetmek için [Azure Resource Manager](overview.md) Azure PowerShell nasıl kullanacağınızı öğrenin. Kaynak gruplarını yönetmek için bkz. [Azure PowerShell kullanarak Azure kaynak gruplarını yönetme](manage-resource-groups-powershell.md).

Kaynakları yönetme hakkında diğer makaleler:

- [Azure portal kullanarak Azure kaynaklarını yönetme](manage-resources-portal.md)
- [Azure CLı kullanarak Azure kaynaklarını yönetme](manage-resources-cli.md)

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

Aşağıdaki betik, bir depolama hesabı oluşturmak için hızlı başlangıç şablonu dağıt oluşturur. Daha fazla bilgi için bkz. [hızlı başlangıç: Visual Studio Code kullanarak Azure Resource Manager şablonları oluşturma](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure PowerShell ile kaynak dağıtma](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Kaynak grubu ve kaynakları dağıtma

Bir kaynak grubu oluşturabilir ve gruba kaynak dağıtabilirsiniz. Daha fazla bilgi için bkz. [kaynak grubu oluşturma ve kaynakları dağıtma](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Kaynakları birden çok aboneliğe veya kaynak grubuna dağıtma

Genellikle, şablonunuzda bulunan tüm kaynakları tek bir kaynak grubuna dağıtırsınız. Ancak, bir kaynak kümesini birlikte dağıtmak ve bunları farklı kaynak gruplarına veya aboneliklerine yerleştirmek istediğiniz senaryolar vardır. Daha fazla bilgi için bkz. [Azure kaynaklarını birden çok aboneliğe veya kaynak grubuna dağıtma](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Kaynakları silme

Aşağıdaki betik bir depolama hesabının nasıl silineceğini gösterir.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Kaynakların silinmesini Azure Resource Manager nasıl sipariş Azure Resource Manager hakkında daha fazla bilgi için bkz. [kaynak grubu silme](delete-resource-group.md).

## <a name="move-resources"></a>Kaynakları taşıma

Aşağıdaki betik, bir depolama hesabının bir kaynak grubundan başka bir kaynak grubuna nasıl kaldırılacağını gösterir.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).

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

Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](lock-resources.md).

## <a name="tag-resources"></a>Kaynakları etiketleme

Etiketleme, kaynak grubunuzun ve kaynaklarınızın mantıksal olarak düzenlenmesine yardımcı olur. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](tag-resources.md#powershell).

## <a name="manage-access-to-resources"></a>Kaynaklara erişimi yönetme

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) , Azure 'daki kaynaklara erişimi yönetme yöntemidir. Daha fazla bilgi için bkz. [Azure PowerShell kullanarak Azure rol atamaları ekleme veya kaldırma](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Resource Manager öğrenmek için bkz. [Azure Resource Manager genel bakış](overview.md).
- Kaynak Yöneticisi Şablon sözdizimini öğrenmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](../templates/template-syntax.md).
- Şablon geliştirmeyi öğrenmek için [adım adım öğreticiler](../index.yml)bölümüne bakın.
- Azure Resource Manager şablonu şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).
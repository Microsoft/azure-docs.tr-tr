---
title: Kaynakları yönetme-Azure CLı
description: Kaynaklarınızı yönetmek için Azure CLı ve Azure Resource Manager kullanın. Kaynakların nasıl dağıtılacağını ve silineceğini gösterir.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: a9148ada37886d8d9ee6d8e314b36f9b259082d8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146783"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Azure CLı kullanarak Azure kaynaklarını yönetme

Azure kaynaklarınızı yönetmek için Azure CLı 'yı [Azure Resource Manager](resource-group-overview.md) ile nasıl kullanacağınızı öğrenin. Kaynak gruplarını yönetmek için bkz. Azure [CLI kullanarak Azure kaynak gruplarını yönetme](./manage-resource-groups-cli.md).

Kaynakları yönetme hakkında diğer makaleler:

- [Azure portal kullanarak Azure kaynaklarını yönetme](./manage-resources-portal.md)
- [Azure PowerShell kullanarak Azure kaynaklarını yönetme](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Kaynakları var olan bir kaynak grubuna dağıtma

Azure kaynaklarını doğrudan Azure CLı kullanarak dağıtabilir veya Azure kaynakları oluşturmak için bir Kaynak Yöneticisi şablonu dağıtabilirsiniz.

### <a name="deploy-a-resource"></a>Kaynak dağıtma

Aşağıdaki betik bir depolama hesabı oluşturur.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Şablon dağıtma

Aşağıdaki betik, bir depolama hesabı oluşturmak için hızlı başlangıç şablonu dağıt oluşturur. Daha fazla bilgi için bkz. [hızlı başlangıç: Visual Studio Code kullanarak Azure Resource Manager şablonları oluşturma](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure CLI ile kaynak dağıtma](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Kaynak grubu ve kaynakları dağıtma

Bir kaynak grubu oluşturabilir ve gruba kaynak dağıtabilirsiniz. Daha fazla bilgi için bkz. [kaynak grubu oluşturma ve kaynakları dağıtma](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Kaynakları birden çok aboneliğe veya kaynak grubuna dağıtma

Genellikle, şablonunuzda bulunan tüm kaynakları tek bir kaynak grubuna dağıtırsınız. Ancak, bir kaynak kümesini birlikte dağıtmak ve bunları farklı kaynak gruplarına veya aboneliklerine yerleştirmek istediğiniz senaryolar vardır. Daha fazla bilgi için bkz. [Azure kaynaklarını birden çok aboneliğe veya kaynak grubuna dağıtma](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Kaynakları silme

Aşağıdaki betik bir depolama hesabının nasıl silineceğini gösterir.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Kaynakların silinmesini Azure Resource Manager nasıl sipariş Azure Resource Manager hakkında daha fazla bilgi için bkz. [kaynak grubu silme](./resource-group-delete.md).

## <a name="move-resources"></a>Kaynakları taşıma

Aşağıdaki betik, bir depolama hesabının bir kaynak grubundan başka bir kaynak grubuna nasıl kaldırılacağını gösterir.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).

## <a name="lock-resources"></a>Kaynakları kilitleme

Kilitleme, kuruluşunuzdaki diğer kullanıcıların Azure aboneliği, kaynak grubu veya kaynak gibi önemli kaynakları yanlışlıkla silmesini veya değiştirmelerini engeller. 

Aşağıdaki betik, bir depolama hesabını kilitleyerek hesabın silinememesi sağlanır.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Aşağıdaki betik bir depolama hesabı için tüm kilitleri alır:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Aşağıdaki betik bir depolama hesabının bir kilidini siler:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Kaynakları etiketleme

Etiketleme, kaynak grubunuzun ve kaynaklarınızın mantıksal olarak düzenlenmesine yardımcı olur. Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Kaynaklara erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md), Azure'daki kaynaklara erişimi yönetmek için kullanılan sistemdir. Daha fazla bilgi için bkz. [RBAC ve Azure CLI kullanarak erişimi yönetme](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Resource Manager öğrenmek için bkz. [Azure Resource Manager genel bakış](./resource-group-overview.md).
- Kaynak Yöneticisi Şablon sözdizimini öğrenmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](./resource-group-authoring-templates.md).
- Şablon geliştirmeyi öğrenmek için [adım adım öğreticiler](/azure/azure-resource-manager/)bölümüne bakın.
- Azure Resource Manager şablonu şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).

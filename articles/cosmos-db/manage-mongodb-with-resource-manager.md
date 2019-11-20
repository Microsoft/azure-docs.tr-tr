---
title: MongoDB için Azure Cosmos DB API 'SI Azure Resource Manager şablonları
description: MongoDB için Azure Cosmos DB API 'SI oluşturmak ve yapılandırmak üzere Azure Resource Manager şablonlarını kullanın.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: e0d28de67082c4b4d582f62653ad474e9ad5dadd
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960575"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Azure Cosmos DB MongoDB API kaynaklarını yönetme

Bu makalede, Azure Resource Manager şablonları kullanarak Azure Cosmos DB hesaplarınızın, veritabanlarınızın ve kapsayıcılarınızın yönetimini otomatikleştirmek için farklı işlemlerin nasıl gerçekleştirileceği açıklanır. Bu makalede, yalnızca MongoDB için Azure Cosmos DB API 'sine örnek verilmiştir. diğer API türü hesaplara yönelik örnekler bulmak için bkz. [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md)ve [tablo](manage-table-with-resource-manager.md) makaleleri için Azure Cosmos DB API 'si ile Azure Resource Manager şablonları kullanma.

## MongoDB hesabı, veritabanı ve koleksiyonu için Azure Cosmos DB API oluşturma<a id="create-resource"></a>

Azure Resource Manager şablonu kullanarak Azure Cosmos DB kaynakları oluşturun. Bu şablon, veritabanı düzeyinde 400 RU/sn aktarım hızını paylaşan iki koleksiyon ile MongoDB API 'SI için bir Azure Cosmos hesabı oluşturur. Şablonu kopyalayın ve aşağıda gösterildiği gibi dağıtın veya [Azure hızlı başlangıç Galerisi](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) ' ni ziyaret edin ve Azure Portal dağıtın. Ayrıca, şablonu yerel bilgisayarınıza indirebilir veya yeni bir şablon oluşturup `--template-file` parametresiyle yerel yolu belirtebilirsiniz.

> [!NOTE]
> Hesap adları küçük ve 44 ya da daha az karakter olmalıdır.
> RU/s 'yi güncelleştirmek için, şablonu güncelleştirilmiş işleme özelliği değerleriyle yeniden gönderin.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-the-azure-cli"></a>Azure CLı aracılığıyla dağıtma

Azure CLı kullanarak Azure Resource Manager şablonunu dağıtmak için, betiği **kopyalayın** ve Azure Cloud Shell açmak için **dene** ' yi seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` komutu, yeni oluşturulan Azure Cosmos hesabını, sağlandıktan sonra gösterir. Cloud Shell kullanmak yerine Azure CLı 'nın yerel olarak yüklü bir sürümünü kullanmayı tercih ederseniz, bkz. [Azure CLI](/cli/azure/) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

Bazı ek kaynaklar aşağıda verilmiştir:

- [Azure Resource Manager belgeleri](/azure/azure-resource-manager/)
- [Azure Cosmos DB kaynak sağlayıcısı şeması](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Ortak Azure Resource Manager Dağıtım hatalarını giderme](../azure-resource-manager/resource-manager-common-deployment-errors.md)
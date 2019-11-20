---
title: Azure Resource Manager şablonları kullanarak Azure Cosmos DB oluşturma ve yönetme
description: SQL (Core) API için Azure Cosmos DB oluşturmak ve yapılandırmak üzere Azure Resource Manager şablonları kullanma
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 0cb6e80bafca3bb0bfc339552facae5bd16aced4
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960540"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Azure Cosmos DB SQL (çekirdek) API kaynaklarını yönetme

Bu makalede, Azure Resource Manager şablonları kullanarak Azure Cosmos DB hesaplarınızın, veritabanlarınızın ve kapsayıcılarınızın yönetimini otomatikleştirmek için farklı işlemlerin nasıl gerçekleştirileceği açıklanır. Bu makalede yalnızca SQL API hesapları için örnekler bulunur, diğer API türü hesaplara yönelik örnekler bulunur. bkz. [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [tablo](manage-table-with-resource-manager.md) makaleleri için Azure Cosmos DB API 'siyle Azure Resource Manager şablonları kullanma.

MongoDB, Gremlin, Cassandra ve Tablo API'si için Cosmos DB hesapları, veritabanlarını ve kapsayıcıları oluşturma ve yönetme.

## Azure Cosmos hesabı, veritabanı ve kapsayıcısı oluşturma<a id="create-resource"></a>

Azure Resource Manager şablonu kullanarak Azure Cosmos DB kaynakları oluşturun. Bu şablon, veritabanı düzeyinde 400 RU/sn aktarım hızını paylaşan iki kapsayıcıyla bir Azure Cosmos hesabı ve adanmış 400 RU/sn aktarım hızı ile tek bir kapsayıcı oluşturur. Şablonu kopyalayın ve aşağıda gösterildiği gibi dağıtın veya [Azure hızlı başlangıç Galerisi](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) ' ni ziyaret edin ve Azure Portal dağıtın. Ayrıca, şablonu yerel bilgisayarınıza indirebilir veya yeni bir şablon oluşturup `--template-file` parametresiyle yerel yolu belirtebilirsiniz.

> [!NOTE]
>
> - Azure Cosmos hesabına eş zamanlı olarak konum ekleyemez veya bunları kaldıramaz ve diğer özellikleri değiştirebilirsiniz. Bunların ayrı işlemler olarak yapılması gerekir.
> - Hesap adları küçük ve 44 ya da daha az karakter olmalıdır.
> - RU/s 'yi güncelleştirmek için, şablonu güncelleştirilmiş işleme özelliği değerleriyle yeniden gönderin.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Büyük bölüm anahtarı olan bir kapsayıcı oluşturmak için, önceki şablondaki `partitionKey` nesnesine `"version":2` özelliğini ekleyin.

### <a name="deploy-via-powershell"></a>PowerShell aracılığıyla dağıtma

PowerShell kullanarak Azure Resource Manager şablonunu dağıtmak için betiği **kopyalayın** ve Azure Cloud Shell açmak için **dene** ' yi seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell yerine yerel olarak yüklü bir PowerShell sürümü kullanmayı seçerseniz, Azure PowerShell modülünü [yüklemelisiniz](/powershell/azure/install-az-ps) . Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın.

### <a name="deploy-via-azure-cli"></a>Azure CLı aracılığıyla dağıtma

Azure CLı kullanarak Azure Resource Manager şablonunu dağıtmak için, Azure Cloud Shell açmak üzere **deneyin** ' i seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` komutu, yeni oluşturulan Azure Cosmos hesabını, sağlandıktan sonra gösterir. CloudShell kullanmak yerine yerel olarak yüklenmiş bir Azure CLı sürümü kullanmayı seçerseniz, bkz. [Azure komut satırı arabirimi (CLI)](/cli/azure/) makalesi.

## Sunucu tarafı işlevleriyle Azure Cosmos DB kapsayıcısı oluşturma<a id="create-sproc"></a>

Bir Azure Resource Manager şablonu kullanarak saklı yordam, tetikleyici ve Kullanıcı tanımlı işlev ile Azure Cosmos DB kapsayıcısı oluşturun. Şablonu kopyalayın ve aşağıda gösterildiği gibi dağıtın veya [Azure hızlı başlangıç Galerisi](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) ' ni ziyaret edin ve Azure Portal dağıtın. Ayrıca, şablonu yerel bilgisayarınıza indirebilir veya yeni bir şablon oluşturup `--template-file` parametresiyle yerel yolu belirtebilirsiniz.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-stored-procedure-template-via-powershell"></a>Saklı yordam şablonunu PowerShell aracılığıyla dağıtma

PowerShell kullanarak Kaynak Yöneticisi şablonunu dağıtmak için betiği **kopyalayın** ve Azure Cloud Shell açmak için **dene** ' yi seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Azure Cloud Shell yerine yerel olarak yüklü bir PowerShell sürümü kullanmayı seçerseniz, Azure PowerShell modülünü [yüklemelisiniz](/powershell/azure/install-az-ps) . Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın.

### <a name="deploy-stored-procedure-template-via-azure-cli"></a>Azure CLı ile saklı yordam şablonu dağıtma

Azure CLı kullanarak Azure Resource Manager şablonunu dağıtmak için, Azure Cloud Shell açmak üzere **deneyin** ' i seçin. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Sonraki Adımlar

Bazı ek kaynaklar aşağıda verilmiştir:

- [Azure Resource Manager belgeleri](/azure/azure-resource-manager/)
- [Azure Cosmos DB kaynak sağlayıcısı şeması](/azure/templates/microsoft.documentdb/allversions)
- [Azure Cosmos DB hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Ortak Azure Resource Manager Dağıtım hatalarını giderme](../azure-resource-manager/resource-manager-common-deployment-errors.md)

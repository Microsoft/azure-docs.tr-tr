---
title: Azure CLı kullanarak Azure Cosmos DB kaynaklarını yönetme
description: Azure Cosmos DB hesabınızı, veritabanınızı ve Kapsayıcılarınızı yönetmek için Azure CLı 'yi kullanın.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 06df85c73b6060bf166df37679457715522f80d8
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385779"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure CLı kullanarak Azure Cosmos kaynaklarını yönetme

Aşağıdaki kılavuzda, Azure CLı kullanarak Azure Cosmos DB hesaplarınızın, veritabanlarınızın ve kapsayıcılarınızın yönetimini otomatik hale getirmek için kullanılan komutlar açıklanmaktadır. Tüm Azure Cosmos DB CLI komutlarına ait başvuru sayfalarına [Azure CLI Başvurusu](https://docs.microsoft.com/cli/azure/cosmosdb) sayfasından erişilebilir. Ayrıca, MongoDB, Gremlin, Cassandra ve Tablo API'si için Cosmos DB hesapları, veritabanları ve kapsayıcıları oluşturma ve yönetme dahil olmak üzere [Azure Cosmos DB Için Azure CLI örneklerinde](cli-samples.md)daha fazla örnek bulabilirsiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu konu, Azure CLI 2.0 veya sonraki bir sürümünü kullanmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı oluşturma

Batı ABD 2 ve Doğu ABD 2 bölgelerinde SQL API 'SI, oturum tutarlılığı Azure Cosmos DB bir hesap oluşturun:

> [!IMPORTANT]
> Azure Cosmos hesap adı küçük ve 31 karakterden az olmalıdır.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 31 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

## <a name="add-or-remove-regions"></a>Bölge ekleme veya kaldırma

İki bölgeyle bir Azure Cosmos hesabı oluşturun, bölge ekleyin ve bölge kaldırın.

> [!NOTE]
> Aynı anda bir Azure Cosmos hesabı için @no__t bölge ekleyemez ve kaldıramazsınız ve diğer özellikleri değiştirebilirsiniz. Bölgeleri değiştirmek, hesap kaynağında yapılan diğer değişiklikten farklı bir işlem olarak gerçekleştirilmelidir.
> [!NOTE]
> Bu komut, bölge eklemenize ve kaldırmanıza izin verir ancak yük devretme önceliklerini değiştirmenize veya el ile yük devretme tetiklemesine izin vermez. Bkz. [Yük devretme önceliğini ayarlama](#set-failover-priority) ve [El Ile yük devretmeyi tetikleme](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName= "South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>Birden çok yazma bölgesini etkinleştir

Cosmos hesabı için çoklu yönetici 'yi etkinleştirme

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>Yük devretme önceliğini ayarla

Otomatik yük devretme için yapılandırılmış bir Azure Cosmos hesabının yük devretme önceliğini ayarlama

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a>Otomatik yük devretmeyi etkinleştir

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>El ile yük devretmeyi Tetikle

> [!CAUTION]
> Öncelik = 0 olan bölgenin değiştirilmesi, bir Azure Cosmos hesabı için el ile yük devretmeyi tetikler. Diğer herhangi bir öncelik değişikliği, yük devretmeyi tetiklemez.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a id="list-account-keys"></a>Tüm hesap anahtarlarını Listele

Cosmos hesabı için tüm anahtarları alın.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>Salt okuma hesap anahtarlarını listeleme

Cosmos hesabı için salt okuma anahtarları alın.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

## <a name="list-connection-strings"></a>Bağlantı dizelerini listeleme

Cosmos hesabı için bağlantı dizelerini alın.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="regenerate-account-key"></a>Hesap anahtarını yeniden oluştur

Cosmos hesabı için yeni bir anahtar oluşturun.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="create-a-database"></a>Veritabanı oluşturun

Cosmos veritabanı oluşturma.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

## <a name="create-a-database-with-shared-throughput"></a>Paylaşılan verimlilik ile veritabanı oluşturma

Paylaşılan aktarım hızı ile Cosmos veritabanı oluşturma.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

## <a name="change-the-throughput-of-a-database"></a>Bir veritabanının verimini değiştirme

Cosmos veritabanının verimini 1000 RU/s ile artırın.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
increaseRU=1000

originalRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query throughput -o tsv)

newRU=$((originalRU + increaseRU))

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Varsayılan dizin ilkesi, bölüm anahtarı ve 400 RU/s ile Cosmos kapsayıcısı oluşturun.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

## <a name="create-a-container-with-ttl"></a>TTL ile kapsayıcı oluşturma

TTL etkin olan bir Cosmos kapsayıcısı oluşturun.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'
databaseName = 'database1'
containerName = 'container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl = 86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>Özel dizin ilkesiyle kapsayıcı oluşturma

Özel dizin ilkesi, uzamsal dizin, bileşik dizin, bölüm anahtarı ve 400 RU/sn ile Cosmos kapsayıcısı oluşturun.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

## <a name="change-the-throughput-of-a-container"></a>Kapsayıcının verimini değiştirme

Cosmos kapsayıcısının verimini 1000 RU/s ile artırın.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
increaseRU=1000

originalRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query throughput -o tsv)

newRU=$((originalRU + increaseRU))

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı hakkında daha fazla bilgi için bkz.

- [Azure CLI'yı yükleme](/cli/azure/install-azure-cli)
- [Azure CLI Başvurusu](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Azure Cosmos DB için ek Azure CLı örnekleri](cli-samples.md)

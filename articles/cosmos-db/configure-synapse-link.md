---
title: Azure Cosmos DB için Azure Synapse Link'i yapılandırma ve kullanma
description: Azure Cosmos DB hesapları için SYNAPSE bağlantısının nasıl etkinleştirileceğini, analitik deponun etkinleştirildiği bir kapsayıcı oluşturmayı, Azure Cosmos veritabanını SYNAPSE çalışma alanına bağlamayı ve sorguları çalıştırmayı öğrenin.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: references_regions, synapse-cosmos-db
ms.openlocfilehash: 24886ff3e01e9d9b4c01eabc917ced433599c0fa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727153"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure Synapse Link'i yapılandırma ve kullanma
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Cosmos DB Için Azure SYNAPSE bağlantısı](synapse-link.md) , Azure Cosmos DB ' de işletimsel veriler üzerinde neredeyse gerçek zamanlı analizler çalıştırmanıza olanak tanıyan, bulut tabanlı bir karma işlem ve analitik Işleme (htap) özelliğidir. SYNAPSE link, Azure Cosmos DB ile Azure SYNAPSE Analytics arasında sıkı sorunsuz bir tümleştirme oluşturur.

Azure SYNAPSE link, Azure Cosmos DB SQL API kapsayıcıları veya Mongo DB koleksiyonları için Azure Cosmos DB API 'SI için kullanılabilir. Azure Cosmos DB için Azure SYNAPSE bağlantısı ile analitik sorgular çalıştırmak için aşağıdaki adımları kullanın:

* [Azure Cosmos DB hesaplarınız için SYNAPSE bağlantısını etkinleştirin](#enable-synapse-link)
* [Bir analitik depo Azure Cosmos DB kapsayıcısı oluşturma](#create-analytical-ttl)
* [İsteğe bağlı-Azure Cosmos DB kapsayıcısı için analitik depoyu güncelleştirme TTL](#update-analytical-ttl)
* [Azure Cosmos DB veritabanınızı bir Synapse çalışma alanına bağlama](#connect-to-cosmos-database)
* [SYNAPSE Spark kullanarak analitik depoyu sorgulama](#query-analytical-store-spark)
* [Sunucusuz SQL havuzu kullanarak analitik depoyu sorgulama](#query-analytical-store-sql-on-demand)
* [Power BI verileri analiz etmek ve görselleştirmek için sunucusuz SQL havuzunu kullanın](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Azure Cosmos DB hesapları için Azure SYNAPSE bağlantısını etkinleştirme

### <a name="azure-portal"></a>Azure portalı

1. [Azure portal](https://portal.azure.com/) oturum açın.

1. [Yeni bir Azure hesabı oluşturun](create-sql-api-dotnet.md#create-account)veya mevcut bir Azure Cosmos DB hesabı seçin.

1. Azure Cosmos DB hesabınıza gidin ve **Özellikler** bölmesini açın.

1. Özellikler listesinden **SYNAPSE bağlantısı** ' nı seçin.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="SYNAPSE Link özelliğini bul":::

1. Bundan sonra hesabınızda SYNAPSE bağlantısını etkinleştirmenizi ister. **Etkinleştir**’i seçin. Bu işlemin tamamlanması 1 ila 5 dakika sürebilir.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="SYNAPSE Link özelliğini etkinleştir":::

1. Hesabınız artık SYNAPSE bağlantısını kullanacak şekilde etkinleştirilmiştir. Ardından, işletimsel verilerinizi işlemsel depodan analitik depoya çoğaltmaya otomatik olarak başlamak için bkz. analitik depo etkin kapsayıcıları oluşturma.

> [!NOTE]
> SYNAPSE bağlantısının etkinleştirilmesi, analitik depoyu otomatik olarak yapmaz. Cosmos DB hesapta SYNAPSE bağlantısını etkinleştirdikten sonra, işlem verilerinizi analitik depoya Çoğaltmaya başlamak için, onları oluşturduğunuz sırada kapsayıcı üzerinde analitik depoyu etkinleştirin. 

### <a name="azure-cli"></a>Azure CLI’si

Aşağıdaki bağlantılarda, SYNAPSE bağlantısının Azure CLı kullanılarak nasıl etkinleştirildiği gösterilmektedir:

* [SYNAPSE bağlantısı etkin olan yeni bir Azure Cosmos DB hesabı oluşturun](/cli/azure/cosmosdb#az_cosmosdb_create-optional-parameters)
* [SYNAPSE bağlantısını etkinleştirmek için mevcut bir Azure Cosmos DB hesabını güncelleştirin](/cli/azure/cosmosdb#az_cosmosdb_update-optional-parameters)

### <a name="powershell"></a>PowerShell

* [SYNAPSE bağlantısı etkin olan yeni bir Azure Cosmos DB hesabı oluşturun](/powershell/module/az.cosmosdb/new-azcosmosdbaccount#description)
* [SYNAPSE bağlantısını etkinleştirmek için mevcut bir Azure Cosmos DB hesabını güncelleştirin](/powershell/module/az.cosmosdb/update-azcosmosdbaccount)


Aşağıdaki bağlantılarda PowerShell kullanılarak SYNAPSE bağlantısının nasıl etkinleştirildiği gösterilmektedir:

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Analitik depo ile Azure Cosmos kapsayıcısı oluşturma

Kapsayıcıyı oluştururken Azure Cosmos kapsayıcısında analitik depoyu açabilirsiniz. Azure portal kullanabilir veya `analyticalTTL` Azure Cosmos DB SDK 'ları kullanarak kapsayıcı oluşturma sırasında özelliğini yapılandırabilirsiniz.

> [!NOTE]
> Şu anda analitik depoyu **Yeni** kapsayıcılar için (hem yeni hem de mevcut hesaplarda) etkinleştirebilirsiniz. [Azure Cosmos DB geçiş araçları](cosmosdb-migrationchoices.md) 'nı kullanarak, mevcut kapsayıcılarınızdaki verileri yeni kapsayıcılara geçirebilirsiniz.

### <a name="azure-portal"></a>Azure portalı

1. [Azure Portal](https://portal.azure.com/) veya [Azure Cosmos DB Gezgininde](https://cosmos.azure.com/)oturum açın.

1. Azure Cosmos DB hesabınıza gidin ve **Veri Gezgini** sekmesini açın.

1. **Yeni kapsayıcı** ' yı seçin ve veritabanınız, Kapsayıcınız, Bölüm anahtarınız ve verimlilik ayrıntılarınız için bir ad girin. **Analitik depo** seçeneğini açın. Analitik depoyu etkinleştirdikten sonra, `AnalyicalTTL` özelliği-1 varsayılan değerine ayarlanmış bir kapsayıcı oluşturur (sonsuz saklama). Kayıtların tüm geçmiş sürümlerini koruyan bu analitik depo.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Azure Cosmos kapsayıcısı için analitik depoyu aç":::

1. Bu hesapta daha önce SYNAPSE bağlantısı etkinleştirilmemişse, analitik depo özellikli bir kapsayıcı oluşturmak için önkoşul olduğundan bunu yapmanız istenir. İstenirse, **SYNAPSE bağlantısını etkinleştir**' i seçin. Bu işlemin tamamlanması 1 ila 5 dakika sürebilir.

1. Azure Cosmos kapsayıcısını etkin bir analitik mağaza oluşturmak için **Tamam**' ı seçin.

1. Kapsayıcı oluşturulduktan sonra, **Ayarlar**' a tıklayıp Veri Gezgini ' deki belgeler ' e tıklayarak analitik deponun etkinleştirildiğini doğrulayın ve **analitik depo süresinin canlı** olup olmadığını denetleyin.

### <a name="net-sdk"></a>.NET SDK

Aşağıdaki kod, .NET SDK kullanarak analitik depolarla bir kapsayıcı oluşturur. Analitik TTL özelliğini gerekli değere ayarlayın. İzin verilen değerler listesi için bkz. [ANALITIK TTL desteklenen değerler](analytical-store-introduction.md#analytical-ttl) makalesi:

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### <a name="java-v4-sdk"></a>Java v4 SDK 'Sı

Aşağıdaki kod, Java v4 SDK 'sını kullanarak analitik depolarla bir kapsayıcı oluşturur. `AnalyticalStoreTimeToLiveInSeconds`Özelliği gerekli değere ayarlayın:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python v4 SDK 'Sı

Python 2,7 ve Azure Cosmos DB SDK 4.1.0, gereken en düşük sürümlerdir ve SDK yalnızca SQL API ile uyumludur.

İlk adım, [Azure Cosmos DB Python SDK 'sının](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)en az sürüm 4.1.0 kullandığınızdan emin olmak için gereklidir:

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
Sonraki adım, Azure Cosmos DB Python SDK 'sını kullanarak analitik depolarla bir kapsayıcı oluşturur:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="azure-cli"></a>Azure CLI’si

Aşağıdaki bağlantılar, Azure CLı kullanarak bir analitik depo etkin kapsayıcıları oluşturmayı göstermektedir:

* [Mongo DB için Azure Cosmos DB API 'SI](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create-examples)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)

### <a name="powershell"></a>PowerShell

Aşağıdaki bağlantılar, PowerShell kullanarak bir analitik depo etkin kapsayıcıları oluşturmayı göstermektedir:

* [Mongo DB için Azure Cosmos DB API 'SI](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection#description)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create)


## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> İsteğe bağlı-analitik depo zamanını canlı olarak güncelleştirin

Analitik depo belirli bir TTL değeri ile etkinleştirildikten sonra, daha sonra farklı bir geçerli değere güncellemek isteyebilirsiniz. Azure portal, Azure CLı, PowerShell veya Cosmos DB SDK 'larını kullanarak değeri güncelleştirebilirsiniz. Çeşitli analitik TTL yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [ANALITIK TTL desteklenen değerler](analytical-store-introduction.md#analytical-ttl) makalesi.


### <a name="azure-portal"></a>Azure portalı

Azure portal aracılığıyla analitik mağaza etkin bir kapsayıcı oluşturduysanız, varsayılan bir analitik TTL-1 ' i içerir. Bu değeri güncelleştirmek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com/) veya [Azure Cosmos DB Gezgininde](https://cosmos.azure.com/)oturum açın.

1. Azure Cosmos DB hesabınıza gidin ve **Veri Gezgini** sekmesini açın.

1. Analitik deponun etkinleştirildiği mevcut bir kapsayıcıyı seçin. Genişletin ve aşağıdaki değerleri değiştirin:

  * **Ölçek & ayarları** penceresini açın.
  * Bul **ayarı** altında, * * gerçek zamanlı analitik depolama süresi * *.
  * **Açık (varsayılan)** **seçeneğini belirleyin veya SEÇIN ve bir** TTL değeri ayarlayın
  * Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.

### <a name="net-sdk"></a>.NET SDK

Aşağıdaki kod, .NET SDK kullanarak analitik depo için TTL 'nin nasıl güncelleştirilmesini göstermektedir:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>Java v4 SDK 'Sı

Aşağıdaki kod, Java v4 SDK 'sını kullanarak analitik depo için TTL 'nin nasıl güncelleştirilmesini göstermektedir:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>Python v4 SDK 'Sı

Şu anda desteklenmiyor.


### <a name="azure-cli"></a>Azure CLI’si

Aşağıdaki bağlantılarda, Azure CLı kullanılarak analitik TTL 'nin nasıl güncelleştirilmesi gösterilmektedir:

* [Mongo DB için Azure Cosmos DB API 'SI](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_update)
* [Azure Cosmos DB SQL API](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_update)

### <a name="powershell"></a>PowerShell

Aşağıdaki bağlantılarda PowerShell kullanılarak analitik TTL 'nin nasıl güncelleştirilmesi gösterilmektedir:

* [Mongo DB için Azure Cosmos DB API 'SI](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection)
* [Azure Cosmos DB SQL API](/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Bir Synapse çalışma alanına bağlanma

Azure SYNAPSE link ile Azure SYNAPSE Analytics Studio 'dan bir Azure Cosmos DB veritabanına nasıl eriştiğine ilişkin Azure [SYNAPSE Connect](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) 'teki yönergeleri kullanın.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Azure SYNAPSE Analytics için Apache Spark kullanarak analitik depoyu sorgulama

SYNAPSE Spark ile sorgulama hakkında [analitik depo makalesindeki sorgu Azure Cosmos DB sorgulama](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) bölümündeki yönergeleri kullanın. Bu makalede, SYNAPSE hareketlerinden analitik depoyla nasıl etkileşim kurabileceğine ilişkin bazı örnekler verilmektedir. Bir kapsayıcıya sağ tıkladığınızda bu hareketler görünür. Hareketlerle hızlı bir şekilde kod oluşturabilir ve bunu gereksinimlerinize ince ayar edebilirsiniz. Tek bir tıklama ile verileri bulmak için de mükemmeldir.

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Azure SYNAPSE Analytics 'te sunucusuz SQL havuzunu kullanarak analitik depoyu sorgulama

Sunucusuz SQL havuzu, Azure SYNAPSE bağlantısı ile etkinleştirilen Azure Cosmos DB kapsayıcılarınızdaki verileri sorgulayabilir ve analiz etmenize olanak tanır. İşlemsel iş yüklerinizin performansını etkilemeden verileri neredeyse gerçek zamanlı olarak analiz edebilirsiniz. Analitik depodan veri sorgulama ve T-SQL arabirimi aracılığıyla çok çeşitli bı ve geçici sorgulama araçlarıyla tümleşik bağlantı sunan tanıdık bir T-SQL söz dizimi sunar. Daha fazla bilgi edinmek için bkz. [sunucusuz SQL havuzu kullanarak analitik depoyu sorgulama](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) makalesi.

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Power BI verileri analiz etmek ve görselleştirmek için sunucusuz SQL havuzunu kullanın

Azure Cosmos DB için SYNAPSE link üzerinden sunucusuz bir SQL havuzu veritabanı ve görünümleri oluşturabilirsiniz. Daha sonra, Azure Cosmos kapsayıcılarını sorgulayabilir ve ardından bu görünümler üzerinde Power BI olan ve bu sorguyu yansıtan bir model oluşturabilirsiniz. Daha fazla bilgi edinmek için bkz. [SYNAPSE link ile Azure Cosmos DB verileri çözümlemek Için sunucusuz SQL havuzu](synapse-link-power-bi.md) kullanma makalesi.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

[Azure Resource Manager şablonu](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) , SQL API 'si için etkin bir Synapse bağlantısı Azure Cosmos DB hesabı oluşturur. Bu şablon, analitik TTL etkin olarak yapılandırılmış bir kapsayıcı içeren bir bölgede bir çekirdek (SQL) API hesabı oluşturur ve el ile veya otomatik ölçeklendirme üretimi kullanma seçeneği sağlar. Bu şablonu dağıtmak için Benioku sayfasında **Azure 'A dağıt** ' a tıklayın.

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Azure SYNAPSE bağlantısı ile çalışmaya başlama-örnekler

[GitHub](https://aka.ms/cosmosdb-synapselink-samples)'Da Azure SYNAPSE bağlantısı ile çalışmaya başlama örneklerini bulabilirsiniz. Bu, IoT ve perakende senaryolarıyla uçtan uca çözümler sergileme. MongoDB için Azure Cosmos DB API 'sine karşılık gelen örnekleri [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) klasörü altındaki aynı depoda da bulabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki belgelere bakın:

* [Azure Cosmos DB için Azure SYNAPSE bağlantısı.](synapse-link.md)

* [Azure Cosmos DB analiz deposuna genel bakış.](analytical-store-introduction.md)

* [Azure Cosmos DB için SYNAPSE bağlantısı hakkında sık sorulan sorular.](synapse-link-frequently-asked-questions.md)

* [Azure SYNAPSE Analytics 'te Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md).

* [Azure SYNAPSE Analytics 'Te sunucusuz SQL havuzu çalışma zamanı desteği](../synapse-analytics/sql/on-demand-workspace-overview.md).

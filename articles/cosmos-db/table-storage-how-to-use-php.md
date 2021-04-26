---
title: PHP 'den Azure Storage tablo hizmeti veya Azure Cosmos DB Tablo API'si kullanma
description: Azure Tablo depolama veya PHP 'den Azure Cosmos DB Tablo API'si kullanarak yapılandırılmış verileri bulutta depolayın.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 07/23/2020
ms.openlocfilehash: 9d059c899e4a64d4d2c1b880b2a1d0f89258f33b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93079640"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>PHP’den Azure Depolama Tablo hizmeti veya Azure Cosmos DB Tablo API'sini kullanma
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Bu makalede, tablolar oluşturma, verilerinizi depolama ve veri üzerinde CRUD işlemleri gerçekleştirme işlemleri gösterilir. Azure Tablo hizmeti 'ni veya Azure Cosmos DB Tablo API'si seçin. örnekler PHP’de yazılmıştır ve [Azure Depolama Tablo PHP İstemci Kitaplığını][download] kullanır. Kapsamdaki senaryolara **tablo oluşturma ve silme**, **tablodaki varlıkları ekleme, silme ve sorgulama** dahildir. Azure Tablo hizmeti hakkında daha fazla bilgi için bkz: [Sonraki adımlar](#next-steps) bölümü.

## <a name="create-an-azure-service-account"></a>Azure hizmet hesabı oluşturma

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure depolama hesabı oluşturma**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB Tablo API’si hesabı oluşturma**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>PHP uygulaması oluşturma

Azure Cosmos DB Tablo API’si veya Depolama Tablosu hizmetine erişmek için bir PHP uygulaması oluşturmak için tek gereksinim, kodunuzdan PHP için azure depolama tablosu SDK’sindeki sınıflara başvurmaktır. Uygulamanızı oluşturmak için Not Defteri dahil olmak üzere tüm geliştirme araçlarını kullanabilirsiniz.

Bu kılavuzda, PHP uygulamanızda yerel olarak veya bir Azure web rolü, çalışan rolü veya web sitesinde çalışan kod içinde çağrılabilen Depolama Tablo Hizmeti veya Azure Cosmos DB özelliklerini kullanırsınız.

## <a name="get-the-client-library"></a>İstemci kitaplığını alma

1. Projenizin kökünde Composer.JSON adlı bir dosya oluşturun ve aşağıdaki kodu ekleyin:
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. Kökünüzde [composer.phar](https://getcomposer.org/composer.phar) dosyasını indirin. 
3. Bir komut istemi açın ve proje kök dizininde aşağıdaki komutu yürütün:
   ```
   php composer.phar install
   ```
   Alternatif olarak, GitHub'da kaynak kodunu kopyalamak için [Azure Depolama Tablo PHP İstemci Kitaplığına](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) gidin.

## <a name="add-required-references"></a>Gerekli referansları ekleme

Depolama Tablo hizmetini ya da Azure Cosmos DB API'lerini kullanmak için:

* [require_once][require_once] bildirimini kullanarak otomatik yükleme dosyasına başvurun ve
* Kullandığınız tüm sınıflara başvurun.

Aşağıdaki örnekte otomatik yükleyici dosyasını nasıl dahil edeceğiniz **TableRestProxy** sınıfını nasıl dahil edeceğiniz gösterilmektedir.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

Aşağıdaki örneklerde `require_once` deyimi her zaman gösterilir, ancak yalnızca örneğin yürütülmesi için gerekli olan sınıflara başvurulur.

## <a name="add-your-connection-string"></a>Bağlantı dizenizi ekleyin

Azure depolama hesabına veya Azure Cosmos DB Tablo API'si hesabına bağlanabilirsiniz. Bağlantı dizesini, kullanmakta olduğunuz hesap türüne göre alır.

### <a name="add-a-storage-table-service-connection"></a>Bir Depolama Tablosu hizmeti bağlantısı ekleme

Bir Depolama Tablosu hizmet istemcisi örneği oluşturmak için öncelikle geçerli bir bağlantı dizesi olması gerekir. Depolama Tablosu hizmeti bağlantı dizesinin biçimi şöyledir:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

### <a name="add-a-storage-emulator-connection"></a>Depolama öykünücüsü bağlantısı ekleme

Öykünücü depolamasına erişmek için:

```php
UseDevelopmentStorage = true
```

### <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB bağlantısını ekleme

Bir Azure Cosmos DB Tablo istemcisi oluşturmak için öncelikle geçerli bir bağlantı dizesi olması gerekir. Azure Cosmos DB bağlantı dizesi biçimi şöyledir:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

Bir Azure Tablo Depolama istemcisi veya Azure Cosmos DB istemcisi oluşturmak için **TableRestProxy** sınıfını kullanmanız gerekir. Seçenekleriniz şunlardır:

* Bağlantı dizesini doğrudan geçirebilirsiniz veya
* Bağlantı dizesi için birden fazla harici kaynağı denetlemek için **CloudConfigurationManager (CCM)** kullanabilirsiniz:
  * Varsayılan olarak, bir dış kaynak için destek içerir (ortam değişkenleri).
  * `ConnectionStringSource` sınıfını genişleterek yeni kaynaklar ekleyebilirsiniz.

Burada özetlenen örnekler için bağlantı dizesi doğrudan geçirilir.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Bir tablo oluşturma

**TableRestProxy** nesnesi, **createTable** yöntemini içeren bir tablo oluşturmanıza olanak tanır. Bir tablo oluştururken, Tablo hizmeti zaman aşımını ayarlayabilirsiniz. (Tablo hizmeti zaman aşımı hakkında daha fazla bilgi için bkz: [Tablo Hizmeti İşlemleri için Zaman Aşımlarını Ayarlama][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Tablo adlarındaki kısıtlamalar hakkında daha fazla bilgi için bkz. [Tablo Hizmeti Veri Modelini anlama][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Tabloya bir varlık ekleme

Bir tabloya varlık eklemek için yeni bir **Varlık** nesnesi oluşturun ve bunu **TableRestProxy -> insertEntity**’ye geçirin. Bir varlık oluşturduğunuzda bir `PartitionKey` ve `RowKey` belirtmeniz gerektiğini unutmayın. Bunlar bir varlık için benzersiz tanımlayıcılardır ve diğer varlık özelliklerinden çok daha hızlı sorgulanabilir değerlerdir. Sistem tablonun varlıklarını birden çok depolama düğümlerine otomatik olarak dağıtmak için `PartitionKey` kullanır. Aynı `PartitionKey` değerine sahip varlıklar aynı düğümde depolanır. (Aynı düğümde depolanan birden çok varlık üzerindeki işlemler, farklı düğümlerde depolanan varlıklardan daha iyi gerçekleştirilir.) , `RowKey` Bir bölüm içindeki bir varlığın BENZERSIZ kimliğidir.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Tablo özellikleri ve türleri hakkında bilgi için bkz. [Tablo Hizmeti Veri Modelini anlama][table-data-model].

**TableRestProxy** sınıfı, varlık eklemek için iki alternatif yöntem sunar: **insertOrMergeEntity** ve **insertOrReplaceEntity**. Bu yöntemleri kullanmak için yeni bir **varlık** oluşturun ve her iki yöntem için parametre olarak geçirin. Bu yöntemler, varlık yoksa varlığı ekler. Varlık zaten varsa **insertOrMergeEntity**, özellikler zaten varsa özellik değerlerini güncelleştirir ve yoksa yeni özellikleri ekler. **insertOrReplaceEntity** ise var olan bir varlığı tamamen değiştirir. Aşağıdaki örnekte **insertOrMergeEntity**’nin nasıl kullanılacağı gösterilmiştir. `PartitionKey` "tasksSeattle" ve `RowKey` "1" içeren varlık zaten yoksa eklenir. Ancak daha önce eklendiyse (yukarıdaki örnekte gösterildiği gibi), `DueDate` özelliği güncelleştirilir ve `Status` özelliği eklenir. `Description` ve `Location` özellikleri de güncelleştirilir, ancak yerlerine geçen değerler onları gerçek anlamda değiştirmez. Bu son iki özellik örnekte gösterildiği gibi eklenmediyse ancak hedef varlık üzerinde varsa mevcut değerleri değişmeden kalır.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Tek bir varlık alma

**TableRestProxy -> getEntity** yöntemi`PartitionKey` ve `RowKey` değerlerini sorgulayarak tek bir varlığı almanıza olanak tanır. Aşağıdaki örnekte bölüm anahtarı `tasksSeattle` ve satır anahtarı `1`, **getEntity** yöntemine geçirilir.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Tüm varlıkları bir bölüme alma

Varlık sorguları filtreler kullanılarak oluşturulur (daha fazla bilgi için bkz: [sorgulama tabloları ve varlıkları][filters]). Bölümdeki tüm varlıkları almak için "PartitionKey eq *bölüm_adı*" filtresini kullanın. Aşağıdaki örnekte, **queryEntities** yöntemine bir filtre geçirerek `tasksSeattle` bölümündeki tüm varlıkları alma işlemi gösterilmiştir.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Bir bölümdeki varlıkların alt kümesini alma

Önceki örnekte kullanılan aynı düzen, bir alt bölümdeki tüm varlıkların bir alt kümesini almak için kullanılabilir. Aldığınız varlıkların alt kümesi, kullandığınız filtre tarafından belirlenir (daha fazla bilgi için bkz. [Sorgulama Tabloları ve Varlıklar][filters]). Aşağıdaki örnekte belirli bir `Location` ve belirtilen bir tarihten önceki `DueDate` içindeki tüm varlıkları almak için bir filtre kullanmayı gösterir.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Varlık özellikleri alt kümesi alma

Bir sorgu, varlık özelliklerinin bir alt kümesini alabilir. *Projeksiyon* olarak adlandırılan bu teknik, bant genişliğini azaltır ve özellikle büyük varlıklar için sorgu performansını iyileştirebilir. Alınacak bir özelliği belirtmek için özelliğin adını **Query > addSelectField** yöntemine geçirin. Daha fazla özellik eklemek için bu yöntemi birden çok kez çağırabilirsiniz. **TableRestProxy -> queryEntities**’i yürüttükten sonra, döndürülen varlıklar yalnızca seçilen özelliklere sahip olur. (Tablo varlıklarının bir alt kümesini döndürmek istiyorsanız yukarıda gösterildiği gibi bir filtre sorgusu kullanın.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Varlığı güncelleştirme

**Entity->setProperty** ve **Entity->addProperty** yöntemlerini kullanarak ve ardından **TableRestProxy->updateEntity**.yöntemini çağırarak var olan bir varlığı güncelleştirebilirsiniz. Aşağıdaki örnekte bir varlık getirilir, bir özellik değiştirilir, bir başka özellik kaldırılır ve yeni bir özellik eklenir. Bir özelliği, değerini **null** olarak ayarlayarak kaldırabileceğini unutmayın.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Bir varlığı silme

Bir varlığı silmek için tablo adını ve varlığın `PartitionKey` ve `RowKey` değerlerini **TableRestProxy -> deleteEntity** yöntemine geçirin.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Eşzamanlılık denetimleri için **DeleteEntityOptions -> setEtag** yöntemini kullanıp dördüncü bir parametre olarak **DeleteEntityOptions** nesnesini **deleteEntity**’ye geçirerek varlığın Etag değerini silinmeye ayarlayabilirsiniz.

## <a name="batch-table-operations"></a>Toplu tablo işlemleri

**TableRestProxy -> Toplu** yöntemi birden çok işlemin tek bir istekte yürütülmesine olanak tanır. Buradaki örnekte **BatchRequest** nesnesne işlemler eklenir ve ardından **BatchRequest** nesnesi **TableRestProxy -> Toplu** yöntemine geçirilir. Bir **BatchRequest** nesnesine işlem eklemek için aşağıdaki yöntemlerden herhangi birini birden çok kez çağırabilirsiniz:

* **addInsertEntity** (bir insertEntity işlemi ekler)
* **addUpdateEntity** (bir updateEntity işlemi ekler)
* **addMergeEntity** (bir mergeEntity işlemi ekler)
* **addInsertOrReplaceEntity** (bir insertOrReplaceEntity işlemi ekler)
* **addInsertOrMergeEntity** (bir insertOrMergeEntity işlemi ekler)
* **addDeleteEntity** (bir deleteEntity işlemi ekler)

Aşağıdaki örnekte **insertEntity** ve **deleteEntity** işlemlerinin tek bir istekte nasıl yürütüleceği gösterilmektedir. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Toplu tablo işlemleri hakkında daha fazla bilgi için bkz. [Varlık Grup işlemleri gerçekleştirme][entity-group-transactions].

## <a name="delete-a-table"></a>Bir tablo silme

Son olarak, bir tabloyu silmek için tablo adını **TableRestProxy -> deleteTable** yöntemine geçirin.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Sonraki adımlar

Artık Tablo hizmeti ve Azure Cosmos DB’ye ilişkin temel bilgileri öğrendiniz, daha fazla bilgi edinmek için aşağıdaki bağlantıları izleyin.

* [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.

* [Php Geliştirici Merkezi](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: /rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: /rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: /rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: /rest/api/storageservices/Performing-Entity-Group-Transactions

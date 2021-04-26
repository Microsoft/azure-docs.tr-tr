---
title: Azure Tablo depolama veya Azure Cosmos DB Tablo API'si kullanın Node.js
description: Yapılandırılmış verileri Azure Tablo Depolamayı kullanarak bulutta depolayın veya Node.js Azure Cosmos DB Tablo API'si.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93079720"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Node.js uygulamasından Azure Tablo depolama veya Azure Cosmos DB Tablo API’sini kullanma
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Bu makalede, tablolar oluşturma, verilerinizi depolama ve veri üzerinde CRUD işlemleri gerçekleştirme işlemleri gösterilir. Azure Tablo hizmeti 'ni veya Azure Cosmos DB Tablo API'si seçin. Örnekler Node.js yazılır.

## <a name="create-an-azure-service-account"></a>Azure hizmet hesabı oluşturma

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure depolama hesabı oluşturma**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB Tablo API’si hesabı oluşturma**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Azure Depolama veya Azure Cosmos DB Tablo API’sine erişmek için uygulamanızı yapılandırma

Azure Depolama veya Azure Cosmos DB’yi kullanmak için, Depolama REST hizmetleriyle iletişim kuran bir dizi kitaplık içeren Node.js için Azure Depolama SDK’sı gerekir.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Paketi yüklemek için Düğüm Paket Yöneticisi’ni (NPM) kullanma

1. **PowerShell** (Windows), **Terminal** (Mac) veya **Bash** (Unix) gibi bir komut satırı arabirimi kullanın ve uygulamanızı oluşturduğunuz klasöre gidin.
2. Komut penceresine **npm install azure-storage** yazın. Komutun çıktısı aşağıdaki örneğe benzer.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. Bir **node_modules** klasörünün oluşturulduğunu doğrulamak için **ls** komutunu kendiniz çalıştırabilirsiniz. Bu klasörün içinde, depolama alanına erişmek için ihtiyaç duyduğunuz kitaplıkları içeren **azure-storage** paketini bulacaksınız.

### <a name="import-the-package"></a>Paketi içeri aktarma

Uygulamanızda **server.js** dosyasının üst kısmına aşağıdaki kodu ekleyin:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Bağlantı dizenizi ekleyin

Azure depolama hesabına veya Azure Cosmos DB Tablo API'si hesabına bağlanabilirsiniz. Bağlantı dizesini, kullanmakta olduğunuz hesap türüne göre alır.

### <a name="add-an-azure-storage-connection"></a>Azure Depolama bağlantısı ekleme

Azure modülü, Azure Depolama hesabınıza bağlanmak için gerekli bilgiler için AZURE_STORAGE_ACCOUNT ve AZURE_STORAGE_ACCESS_KEY veya AZURE_STORAGE_CONNECTION_STRING ortam değişkenlerini okur. Bu ortam değişkenleri ayarlanmamışsa, çağırırken hesap bilgilerini belirtmeniz gerekir `TableService` . Örneğin, aşağıdaki kod bir `TableService` nesnesi oluşturur:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB bağlantısını ekleme

Azure Cosmos DB bir bağlantı eklemek için bir nesne oluşturun `TableService` ve hesap adınızı, birincil anahtarınızı ve uç noktasını belirtin. Bu değerleri   >  , Cosmos DB hesabınız için Azure Portal ayarlar **bağlantı dizesinden** kopyalayabilirsiniz. Örnek:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Bir tablo oluşturma

Aşağıdaki kod bir nesnesi oluşturur `TableService` ve yeni bir tablo oluşturmak için onu kullanır.

```javascript
var tableSvc = azure.createTableService();
```

' A çağrı, `createTableIfNotExists` zaten mevcut değilse, belirtilen ada sahip yeni bir tablo oluşturur. Aşağıdaki örnek, henüz yoksa, 'mytable' adlı yeni bir tablo oluşturur:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

`result.created`, yeni bir tablo oluşturulursa `true`, tablo zaten mevcutsa `false` olur. `response`, istekle ilgili bilgileri içerir.

### <a name="apply-filters"></a>Filtreleri uygulama

Kullanılarak gerçekleştirilen işlemlere isteğe bağlı filtre uygulayabilirsiniz `TableService` . Filtreleme işlemleri, günlüğe kaydetme, otomatik yeniden denemeler vb. içerebilir. Filtreler imzaya sahip bir yöntemi uygulayan nesnelerdir:

```javascript
function handle (requestOptions, next)
```

İstek seçeneklerinde önişlemi yapıldıktan sonra yöntem, aşağıdaki imza ile bir geri çağrı geçirerek **next** çağrısı yapmalıdır:

```javascript
function (returnObject, finalCallback, next)
```

Bu geri çağırmada ve `returnObject` (istekten sunucuya olan yanıt) işlendikten sonra geri çağırma, `next` diğer filtreleri işlemeye devam etmek için varsa veya `finalCallback` hizmet çağrısını sonlandırmak için Aksi takdirde çağırmak gerekir.

Yeniden deneme mantığını uygulayan iki filtre, Node.js için Azure SDK ile birlikte gelir `ExponentialRetryPolicyFilter` `LinearRetryPolicyFilter` . Aşağıdakiler `TableService` , şunu kullanan bir nesnesi oluşturur `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Tabloya bir varlık ekleme

Bir varlık eklemek için ilk olarak varlık özelliklerinizi tanımlayan bir nesne oluşturun. Tüm varlıklar, varlık için benzersiz tanımlayıcılar olan **PartitionKey** ve **RowKey** içermelidir.

* **PartitionKey**: Varlığın depolandığı bölümü belirler.
* **RowKey**: Bölüm içindeki varlığı benzersiz şekilde tanımlar.

Hem **PartitionKey** hem de **RowKey** dize değerleri olmalıdır. Daha fazla bilgi için bkz. [Tablo Hizmeti Veri Modelini anlama](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Aşağıda, bir varlığın tanımlanmasına örnek verilmiştir. **DueDate** , türü olarak tanımlanır `Edm.DateTime` . Tür belirtme isteğe bağlıdır ve türler belirtilmezse çıkarsanır.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Ayrıca `Timestamp` , bir varlık eklendiğinde veya güncelleştirilirken Azure tarafından ayarlanan her kayıt için bir alan de vardır.

`entityGenerator`Varlıklarını oluşturmak için de kullanabilirsiniz. Aşağıdaki örnek, kullanarak aynı görev varlığını oluşturur `entityGenerator` .

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Tablonuza bir varlık eklemek için, varlık nesnesini `insertEntity` metoduna geçirin.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

İşlem başarılı olursa `result`, eklenen kaydın [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) öğesini içerir ve `response`, işlemle ilgili bilgileri içerir.

Örnek yanıt:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Varsayılan olarak, `insertEntity` ekli varlığı bilgilerin bir parçası olarak döndürmez `response` . Bu varlık üzerinde başka işlemler gerçekleştirmeyi planlıyor veya bilgileri önbelleğe almak istiyorsanız, `result` değerinin parçası olarak bunun döndürülmesini sağlamak yararlı olabilir. Bunu `echoContent` aşağıdaki gibi etkinleştirerek yapabilirsiniz:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Varlığı güncelleştirme

Mevcut bir varlığı güncelleştirmenin birçok yolu vardır:

* `replaceEntity` -Var olan bir varlığı değiştirerek güncelleştirir.
* `mergeEntity` -Yeni özellik değerlerini mevcut varlığa birleştirerek mevcut bir varlığı güncelleştirir.
* `insertOrReplaceEntity` -Var olan bir varlığı değiştirerek güncelleştirir. Bir varlık yoksa, yenisi eklenir.
* `insertOrMergeEntity` -Varolan bir varlığı yeni özellik değerlerini mevcut bir ile birleştirerek güncelleştirir. Bir varlık yoksa, yenisi eklenir.

Aşağıdaki örnek kullanarak bir varlığın güncelleştirilmesini göstermektedir `replaceEntity` :

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Varsayılan olarak, bir varlık güncelleştirildiğinde, güncelleştirilmekte olan verilerin önceden başka bir işlem tarafından değiştirilip değiştirilmediği denetlenmez. Eş zamanlı güncelleştirmeleri desteklemek için:
>
> 1. Güncelleştirilmekte olan nesnenin ETag öğesini alın. Bu, herhangi bir varlıkla ilgili işlemin `response` değerinin parçası olarak döndürülür ve `response['.metadata'].etag` aracılığıyla alınabilir.
> 2. Bir varlık üzerinde bir güncelleştirme işlemi gerçekleştirirken, önceden alınan ETag bilgilerini yeni varlığa ekleyin. Örnek:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Güncelleştirme işlemini gerçekleştirin. Varlık, ETag değerini almanızın ardından değiştirildiyse, (örn. uygulamanızın başka bir örneği), istekte belirtilen güncelleştirme koşulunun karşılanmadığını belirten bir `error` döndürülür.
>
>

`replaceEntity`Ve ile `mergeEntity` , güncelleştirilmekte olan varlık yoksa, güncelleştirme işlemi başarısız olur; bu nedenle, zaten mevcut olup olmadığına bakılmaksızın bir varlığı depolamak istiyorsanız, `insertOrReplaceEntity` veya kullanın `insertOrMergeEntity` .

Başarılı güncelleştirme işlemleri için `result`, güncelleştirilen varlığın **Etag** değerini içerir.

## <a name="work-with-groups-of-entities"></a>Varlık gruplarıyla çalışma

Bazen sunucu tarafından atomik işleme sağlamak için bir toplu işte birden fazla işlemin bir arada gönderilmesi mantıklıdır. Bunu gerçekleştirmek için, `TableBatch` bir Batch oluşturmak üzere sınıfını kullanın ve sonra toplu `executeBatch` `TableService` işlemleri gerçekleştirmek için yöntemini kullanın.

 Aşağıdaki örnekte, bir toplu işte iki varlığın gönderilmesi gösterilmektedir:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

Başarılı toplu işlemler için `result`, toplu işteki her bir işlemin bilgilerini içerir.

### <a name="work-with-batched-operations"></a>Toplu işlemlerle çalışma

`operations` özelliğini görüntüleyerek bir toplu işe eklenen işlemleri inceleyebilirsiniz. İşlemlerle çalışmak için aşağıdaki yöntemleri de kullanabilirsiniz:

* **clear**: Bir toplu işteki tüm işlemleri temizler.
* **getOperations**: Toplu işten bir işlem alır.
* **hasOperations**: Toplu iş, işlemler içeriyorsa true değerini döndürür.
* **removeOperations**: Bir işlemi kaldırır.
* **size**: Toplu işteki işlem sayısını döndürür.

## <a name="retrieve-an-entity-by-key"></a>Anahtara göre bir varlık alma

**PartitionKey** ve **RowKey** değerine dayalı belirli bir varlık döndürmek için **retrieveEntity** yöntemini kullanın.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Bu işlem tamamlandıktan sonra `result`, varlığı içerir.

## <a name="query-a-set-of-entities"></a>Varlık kümesini sorgulama

Bir tabloyu sorgulamak için, **TableQuery** nesnesini kullanarak aşağıdaki yan tümceleri kullanıp bir sorgu ifadesi oluşturun:

* **select**: Sorgudan döndürülecek alanlar.
* **where**: Where yan tümcesi.

  * **and**: Bir `and` where koşulu.
  * **or**: Bir `or` where koşulu.
* **top**: Getirilecek öğe sayısı.

Aşağıdaki örnek, bir 'hometasks' PartitionKey ile ilk beş öğeyi döndüren bir sorgu derler.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

**select** kullanılmadığından, tüm alanlar döndürülür. Bir tabloya karşı sorguyu gerçekleştirmek için **queryEntities** kullanın. Aşağıdaki örnekte, 'mytable' içinden varlıkları döndürmek için bu sorgu kullanılır.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Başarılı olursa `result.entries`, sorguyla eşleşen varlık dizisini içerir. Sorgu tüm varlıkları döndüremediyse `result.continuationToken`, *null* değildir ve daha fazla sonuç almak için **queryEntities** öğesinin üçüncü parametresi olarak kullanılabilir. İlk sorguda, üçüncü parametre için *null* değerini kullanın.

### <a name="query-a-subset-of-entity-properties"></a>Giriş özellikleri alt kümesi sorgulama

Bir tabloya yapılan sorgu, bir varlıktan yalnızca birkaç alan alabilir.
Bu, bant genişliğini azaltır ve özellikle büyük varlıklar için sorgu performansını iyileştirebilir. **select** yan tümcesini kullanın ve döndürülecek alanların adlarını geçirin. Örneğin, aşağıdaki sorgu yalnızca **description** ve **dueDate** alanlarını döndürür.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Bir varlığı silme

Bölüm ve satır anahtarlarını kullanarak bir varlığı silebilirsiniz. Bu örnekte **task1** nesnesi, silinecek varlığın **RowKey** ve **PartitionKey** değerlerini içerir. Daha sonra nesne, **deleteEntity** yöntemine geçirilir.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Öğeleri silerken, öğenin başka bir işlem tarafından değiştirilmediğinden emin olmak için ETag’leri kullanın. ETag’leri kullanma hakkında bilgi için bkz. [Varlığı güncelleştirme](#update-an-entity).
>
>

## <a name="delete-a-table"></a>Bir tablo silme

Aşağıdaki kod, bir depolama hesabından tabloyu siler.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Tablonun mevcut olup olmadığından emin değilseniz, **deleteTableIfExists** komutunu kullanın.

## <a name="use-continuation-tokens"></a>Devamlılık belirteçlerini kullanma

Büyük miktarda sonuçlar için tabloları sorguluyorsanız devamlılık belirteçlerine bakın. Bir devamlılık belirtecinin mevcut olup olmadığını belirlemek için derleme yapmıyorsanız, sorgunuz için fark etmeyebileceğiniz büyük miktarlarda veriler olabilir.

Varlıkları sorgulama sırasında döndürülen **results** nesnesi, böyle bir belirteç mevcut olduğunda bir `continuationToken` özelliği ayarlar. Daha sonra bölüm ve tablo varlıkları arasında hareket etmeye devam etmek için bir sorgu gerçekleştirirken bunu kullanabilirsiniz.

Sorgulama sırasında, sorgu nesnesi örneği ile geri çağrı işlevi arasında bir `continuationToken` parametresi sağlayabilirsiniz:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

`continuationToken` nesnesini incelerseniz, tüm sonuçlar arasında yineleme yapmak için kullanılabilecek `nextPartitionKey`, `nextRowKey` ve `targetLocation` gibi özellikleri bulursunuz.

Ayrıca, `top` `continuationToken` sayfa boyutunu ayarlamak için ile birlikte kullanabilirsiniz.

## <a name="work-with-shared-access-signatures"></a>Paylaşılan erişim imzaları ile çalışma

Paylaşılan erişim imzaları (SAS), Depolama hesabı adınızı veya anahtarlarınızı sağlamadan tablolara ayrıntılı erişim sağlamanın güvenli bir yoludur. SAS çoğu zaman verilerinize sınırlı erişim sağlamak (örneğin, bir mobil uygulamanın kayıtları sorgulamasına izin verme) için kullanılır.

Bulut tabanlı hizmet gibi güvenilir bir uygulama, **TableService** için **generateSharedAccessSignature** öğesini kullanarak bir SAS oluşturur ve bunu mobil uygulama gibi güvenilir olmayan veya yarı güvenilir uygulamaya sağlar. SAS’ın geçerli olduğu başlangıç ve bitiş tarihlerini ve SAS sahibine verilen erişim düzeyini açıklayan bir ilke kullanılarak SAS oluşturulur.

Aşağıdaki örnekte, SAS sahibinin tabloyu sorgulamasına ('r') olanak sağlayacak ve oluşturulduktan 100 dakika sonra geçerlilik süresi sona erecek yeni bir paylaşılan erişim ilkesi oluşturulur.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

SAS sahibi tabloya erişmeye çalıştığında gerekli olacağı için ana bilgisayar bilgilerini de sağlamanız gerektiğini unutmayın.

Daha sonra istemci uygulaması, tabloya karşı işlemleri gerçekleştirmek için **TableServiceWithSAS** ile SAS’ı kullanır. Aşağıdaki örnek, tabloya bağlanır ve bir sorgu gerçekleştirir. Bkz. tableSAS biçimi için [paylaşılan erişim imzaları (SAS) makalesini kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../storage/common/storage-sas-overview.md) .

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

SAS yalnızca sorgu erişimiyle oluşturulduğundan, varlıklar eklemeye, güncelleştirmeye veya silmeye çalışırsanız bir hata döndürülür.

### <a name="access-control-lists"></a>Erişim Denetim Listeleri

Ayrıca SAS için erişim ilkesini ayarlamak istediğinizde de bir Erişim Denetim Listesi (ACL) kullanabilirsiniz. Birden çok istemcinin tabloya erişmesini, ancak her istemci için farklı erişim ilkeleri sağlamak istiyorsanız bu yararlıdır.

Her politikayla ilişkilendirilmiş bir kimlik ile, bir erişim ilkeleri dizisi kullanılarak ACL uygulanır. Aşağıdaki örnekte, biri 'user1' için ve biri de 'user2' için olmak üzere iki ilke tanımlanmaktadır:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Aşağıdaki örnek, **hometasks** tablosu için geçerli ACL’yi alır ve sonra **setTableAcl** kullanarak yeni ilkeler ekler. Bu yaklaşım aşağıdakilere olanak sağlar:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

ACL ayarlandıktan sonra, bir ilke için kimliğe dayalı bir SAS oluşturabilirsiniz. Aşağıdaki örnek, 'user2' için yeni bir SAS oluşturur:

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın.

* [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.
* GitHub’da [Node.js için Azure Depolama SDK’sı](https://github.com/Azure/azure-storage-node) deposu.
* [Node.js Geliştiricileri için Azure](/azure/developer/javascript/)
* [Azure App Service'te Node.js web uygulaması oluşturma](../app-service/quickstart-nodejs.md)
* [Bir Node.js uygulaması derleme ve Azure Cloud Service’e dağıtma](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (Windows PowerShell kullanarak)
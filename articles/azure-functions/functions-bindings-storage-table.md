---
title: Azure Işlevleri için Azure Tablo depolama bağlamaları
description: Azure Işlevleri 'nde Azure Tablo depolama bağlamalarını nasıl kullanacağınızı anlayın.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure işlevleri, İşlevler, olay işleme dinamik işlem, sunucusuz mimari
ms.service: azure-functions
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 464c1a8ab27f6615fdffd8efa6ab20d75e10a7c1
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171190"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Işlevleri için Azure Tablo depolama bağlamaları

Bu makalede Azure Işlevleri 'nde Azure Tablo depolama bağlamalarıyla nasıl çalışılacağı açıklanmaktadır. Azure Işlevleri, Azure Tablo depolaması için giriş ve çıkış bağlamalarını destekler.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paketler - 1.x işlevleri

Tablo depolama bağlamaları [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet paketi, sürüm 2. x ' de verilmiştir. Paket için kaynak kodu konusu [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub deposu.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paketler - 2.x işlevleri

Tablo depolama bağlamaları [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet paketi, sürüm 3. x içinde verilmiştir. Paket için kaynak kodu konusu [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub deposu.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Girdi

Azure depolama hesabındaki bir tabloyu okumak için Azure Tablo depolama girişi bağlamasını kullanın.

## <a name="input---example"></a>Giriş-örnek

Dile özgü örneğe bakın:

* [C#bir varlığı oku](#input---c-example---one-entity)
* [C#IQueryable 'a bağla](#input---c-example---iqueryable)
* [C#CloudTable 'a bağlama](#input---c-example---cloudtable)
* [C#bir varlık okuma betiği](#input---c-script-example---one-entity)
* [C#IQueryable 'a betik bağlama](#input---c-script-example---iqueryable)
* [C#CloudTable 'a betik bağlama](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Giriş- C# örnek-bir varlık

Aşağıdaki örnek, tek bir tablo satırını okuyan bir [ C# işlevi](functions-dotnet-class-library.md) gösterir. 

"{QueueTrigger}" satır anahtarı değeri, satır anahtarının kuyruk ileti dizesinden geldiğini gösterir.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example---iqueryable"></a>Giriş- C# örnek-IQueryable

Aşağıdaki örnek, birden çok tablo satırını okuyan bir [ C# işlevi](functions-dotnet-class-library.md) gösterir. `MyPoco` Sınıfının öğesinden`TableEntity`türetildiğine unutmayın.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-example---cloudtable"></a>Input- C# example-cloudtable

`IQueryable`[Functions v2 çalışma zamanında](functions-versions.md)desteklenmez. Alternatif olarak, Azure depolama SDK `CloudTable` 'sını kullanarak tabloyu okumak için bir yöntem parametresi kullanılır. Aşağıda, bir Azure Işlevleri günlük tablosunu sorgulayan 2. x işlevine bir örnek verilmiştir:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

CloudTable 'ın nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Tablo Depolamayı kullanmaya başlama](../cosmos-db/table-storage-how-to-use-dotnet.md).

' A bağlanıp `CloudTable` bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

### <a name="input---c-script-example---one-entity"></a>Giriş C# betiği örneği-bir varlık

Aşağıdaki örnek, bir *function. JSON* dosyası ve [ C# ](functions-reference-csharp.md) bağlamayı kullanan betik kodu içindeki tablo girişi bağlamasını gösterir. İşlevi, tek bir tablo satırını okumak için bir kuyruk tetikleyicisi kullanır. 

*Function. JSON* dosyası bir `partitionKey` ve `rowKey`belirtir. "{Queuetrigger}" değeri,satıranahtarınınkuyrukiletidizesindengeldiğinigösterir.`rowKey`

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#input---configuration) bölümde, bu özellikleri açıklanmaktadır.

C# betik kodunu şu şekildedir:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---iqueryable"></a>Giriş C# betiği örneği-IQueryable

Aşağıdaki örnek, bir *function. JSON* dosyası ve [ C# ](functions-reference-csharp.md) bağlamayı kullanan betik kodu içindeki tablo girişi bağlamasını gösterir. İşlevi, bir kuyruk iletisinde belirtilen bölüm anahtarı için varlıkları okur.

İşte *function.json* dosyası:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#input---configuration) bölümde, bu özellikleri açıklanmaktadır.

C# Betik kodu, varlık türünün türeyebilmesi `TableEntity`için Azure depolama SDK 'sına bir başvuru ekler:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---cloudtable"></a>Giriş C# betiği örneği-cloudtable

`IQueryable`[Functions v2 çalışma zamanında](functions-versions.md)desteklenmez. Alternatif olarak, Azure depolama SDK `CloudTable` 'sını kullanarak tabloyu okumak için bir yöntem parametresi kullanılır. Aşağıda, bir Azure Işlevleri günlük tablosunu sorgulayan 2. x işlevine bir örnek verilmiştir:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

CloudTable 'ın nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Tablo Depolamayı kullanmaya başlama](../cosmos-db/table-storage-how-to-use-dotnet.md).

' A bağlanıp `CloudTable` bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

### <a name="input---f-example"></a>Giriş- F# örnek

Aşağıdaki örnek, bir *function. JSON* dosyası ve [ F# ](functions-reference-fsharp.md) bağlamayı kullanan betik kodu içindeki tablo girişi bağlamasını gösterir. İşlevi, tek bir tablo satırını okumak için bir kuyruk tetikleyicisi kullanır. 

*Function. JSON* dosyası bir `partitionKey` ve `rowKey`belirtir. "{Queuetrigger}" değeri,satıranahtarınınkuyrukiletidizesindengeldiğinigösterir.`rowKey`

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#input---configuration) bölümde, bu özellikleri açıklanmaktadır.

İşte F# kod:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Input-JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* dosyası ve bağlamayı kullanan [JavaScript kodundaki](functions-reference-node.md) tablo girişi bağlamasını gösterir. İşlevi, tek bir tablo satırını okumak için bir kuyruk tetikleyicisi kullanır. 

*Function. JSON* dosyası bir `partitionKey` ve `rowKey`belirtir. "{Queuetrigger}" değeri,satıranahtarınınkuyrukiletidizesindengeldiğinigösterir.`rowKey`

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#input---configuration) bölümde, bu özellikleri açıklanmaktadır.

JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Input-Java örneği

Aşağıdaki örnek, tablo depolamada belirtilen bir bölümdeki öğelerin toplam sayısını döndüren HTTP ile tetiklenen bir işlev gösterir.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Giriş - öznitelikleri
 
[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda, bir tablo girişi bağlamasını yapılandırmak için aşağıdaki öznitelikleri kullanın:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Özniteliğin Oluşturucusu tablo adını, Bölüm anahtarını ve satır anahtarını alır. Aşağıdaki örnekte gösterildiği gibi, bir out parametresi veya işlevin dönüş değeri üzerinde kullanılabilir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Aşağıdaki örnekte gösterildiği gibi `Connection` , kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Tam bir örnek için bkz. giriş- C# örnek.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Kullanılacak depolama hesabını belirtmek için başka bir yol sağlar. Oluşturucu, depolama bağlantı dizesi içeren bir uygulama ayarının adını alır. Öznitelik parametre, yöntemi veya sınıf düzeyinde uygulanabilir. Aşağıdaki örnek, sınıf ve yöntem düzeyindeki gösterir:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Kullanılacak depolama hesabı aşağıdaki sırayla belirlenir:

* `Table` Özniteliğin `Connection` özelliği.
* `StorageAccount` Özniteliği aynı parametresine uygulanan `Table` özniteliği.
* `StorageAccount` İşleve uygulanmış bir öznitelik.
* `StorageAccount` Sınıfına uygulanan bir öznitelik.
* İşlev uygulaması için varsayılan depolama hesabı ("AzureWebJobsStorage" uygulama ayarı).

## <a name="input---java-annotations"></a>Giriş-Java ek açıklamaları

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değeri tablo `@TableInput` depolamadan gelen parametrelerde ek açıklama kullanın.  Bu ek açıklama, isteğe bağlı\<T > kullanan yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir. 

## <a name="input---configuration"></a>Giriş - yapılandırma

Aşağıdaki tabloda ayarladığınız bağlama yapılandırma özelliklerini açıklayan *function.json* dosya ve `Table` özniteliği.

|Function.JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | yok | Ayarlanmalıdır `table`. Azure portal bağlama oluşturduğunuzda bu özellik otomatik olarak ayarlanır.|
|**direction** | yok | Ayarlanmalıdır `in`. Azure portal bağlama oluşturduğunuzda bu özellik otomatik olarak ayarlanır. |
|**name** | yok | İşlev kodundaki tabloyu veya varlığı temsil eden değişkenin adı. | 
|**tableName** | **TableName** | Tablonun adı.| 
|**partitionKey** | **partitionKey** |İsteğe bağlı. Okunacak tablo varlığının bölüm anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#input---usage) bölümüne bakın.| 
|**rowKey** |**RowKey** | İsteğe bağlı. Okunacak tablo varlığının satır anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#input---usage) bölümüne bakın.| 
|**take** |**Almanız** | İsteğe bağlı. JavaScript 'te okunacak en fazla varlık sayısı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#input---usage) bölümüne bakın.| 
|**filtreyle** |**Filtre** | İsteğe bağlı. JavaScript 'te tablo girişi için bir OData filtre ifadesi. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#input---usage) bölümüne bakın.| 
|**bağlantı** |**bağlantı** | Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, "Mystorage" `connection` olarak ayarlarsanız, işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. Boş bırakırsanız `connection` , işlevler çalışma zamanı adlı `AzureWebJobsStorage`uygulama ayarında varsayılan depolama bağlantı dizesini kullanır.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Giriş - kullanım

Tablo depolama girişi bağlama, aşağıdaki senaryoları destekler:

* **Bir satırı C# veya C# betiği okuyun**

  `partitionKey` Ve`rowKey`ayarlayın. Bir yöntem parametresi `T <paramName>`kullanarak tablo verilerine erişin. C# Komut dosyasında, `paramName` *function. JSON* `name` özelliğinde belirtilen değerdir. `T`genellikle uygulayan `ITableEntity` veya ondan `TableEntity`türetilen bir türdür. `filter` Ve`take` özellikleri bu senaryoda kullanılmaz. 

* **Veya C# C# betikteki bir veya daha fazla satırı okuyun**

  Bir yöntem parametresi `IQueryable<T> <paramName>`kullanarak tablo verilerine erişin. C# Komut dosyasında, `paramName` *function. JSON* `name` özelliğinde belirtilen değerdir. `T`uygulayan `ITableEntity` veya ondan `TableEntity`türetilen bir tür olmalıdır. Yöntemleri, gerekli `IQueryable` filtrelemeleri yapmak için kullanabilirsiniz. ,, Ve özellikleri`take` Bu senaryoda kullanılmaz. `rowKey` `partitionKey` `filter`  

  > [!NOTE]
  > `IQueryable`[Functions v2 çalışma zamanında](functions-versions.md)desteklenmez. Alternatif olarak, Azure depolama SDK 'sını kullanarak tabloyu okumak için [CloudTable paramName yöntemi parametresi kullanılır](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) . ' A bağlanıp `CloudTable` bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

* **JavaScript 'te bir veya daha fazla satırı okuma**

  `filter` Ve`take` özelliklerini ayarlayın. `partitionKey` Veya`rowKey`ayarlama. Kullanarak `context.bindings.<BINDING_NAME>`giriş tablosu varlığına (veya varlıklara) erişin. Seri durumdan çıkarılan nesneler `RowKey` ve `PartitionKey` özellikleri.

## <a name="output"></a>Output

Azure depolama hesabındaki bir tabloya varlıklar yazmak için bir Azure Tablo depolama çıkış bağlaması kullanın.

> [!NOTE]
> Bu çıkış bağlaması mevcut varlıkların güncelleştirilmesini desteklemiyor. Mevcut bir varlığı güncelleştirmek için [Azure depolama SDK 'sının](https://docs.microsoft.com/azure/cosmos-db/tutorial-develop-table-dotnet#delete-an-entity) işleminikullanın.`TableOperation.Replace`   

## <a name="output---example"></a>Çıkış - örnek

Dile özgü örneğe bakın:

* [C#](#output---c-example)
* [C# betiği (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Çıkış - C# örneği

Aşağıdaki örnek, tek bir tablo satırı yazmak için http tetikleyicisi kullanan bir [ C# işlevi](functions-dotnet-class-library.md) gösterir. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Çıkış - C# betiği örneği

Aşağıdaki örnek, bir *function. JSON* dosyası ve [ C# ](functions-reference-csharp.md) bağlamayı kullanan betik kodu içindeki bir tablo çıkış bağlamasını gösterir. İşlev birden çok tablo varlığı yazar.

İşte *function.json* dosyası:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#output---configuration) bölümde, bu özellikleri açıklanmaktadır.

C# betik kodunu şu şekildedir:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### <a name="output---f-example"></a>Çıkış - F# örneği

Aşağıdaki örnek, bir *function. JSON* dosyası ve [ F# ](functions-reference-fsharp.md) bağlamayı kullanan betik kodu içindeki bir tablo çıkış bağlamasını gösterir. İşlev birden çok tablo varlığı yazar.

İşte *function.json* dosyası:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#output---configuration) bölümde, bu özellikleri açıklanmaktadır.

İşte F# kod:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Çıkış - JavaScript örneği

Aşağıdaki örnek, bir *function. JSON* dosyasındaki bir tablo çıkış bağlamasını ve bağlamayı kullanan bir [JavaScript işlevini](functions-reference-node.md) gösterir. İşlev birden çok tablo varlığı yazar.

İşte *function.json* dosyası:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Yapılandırma](#output---configuration) bölümde, bu özellikleri açıklanmaktadır.

JavaScript kod aşağıdaki gibidir:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes"></a>Çıkış - öznitelikleri

[ C# Sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)' u kullanın.

Özniteliğin Oluşturucusu tablo adını alır. Aşağıdaki örnekte gösterildiği gibi, bir `out` parametrede veya işlevin dönüş değerinde kullanılabilir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Aşağıdaki örnekte gösterildiği gibi `Connection` , kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Tam bir örnek için bkz. [çıkış - C# örneği](#output---c-example).

Sınıfı, yöntemi veya `StorageAccount` parametre düzeyinde depolama hesabını belirtmek için özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. [giriş-öznitelikler](#input---attributes).

## <a name="output---configuration"></a>Çıkış - yapılandırma

Aşağıdaki tabloda ayarladığınız bağlama yapılandırma özelliklerini açıklayan *function.json* dosya ve `Table` özniteliği.

|Function.JSON özelliği | Öznitelik özelliği |Açıklama|
|---------|---------|----------------------|
|**type** | yok | Ayarlanmalıdır `table`. Azure portal bağlama oluşturduğunuzda bu özellik otomatik olarak ayarlanır.|
|**direction** | yok | Ayarlanmalıdır `out`. Azure portal bağlama oluşturduğunuzda bu özellik otomatik olarak ayarlanır. |
|**name** | yok | Tablo veya varlığı temsil eden işlev kodunda kullanılan değişken adı. `$return` İşlev dönüş değerine başvuracak şekilde ayarlanır.| 
|**tableName** |**TableName** | Tablonun adı.| 
|**partitionKey** |**partitionKey** | Yazılacak tablo varlığının bölüm anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım bölümüne](#output---usage) bakın.| 
|**rowKey** |**RowKey** | Yazılacak tablo varlığının satır anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım bölümüne](#output---usage) bakın.| 
|**bağlantı** |**bağlantı** | Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, "Mystorage" `connection` olarak ayarlarsanız, işlevler çalışma zamanı "AzureWebJobsMyStorage" adlı bir uygulama ayarı arar. Boş bırakırsanız `connection` , işlevler çalışma zamanı adlı `AzureWebJobsStorage`uygulama ayarında varsayılan depolama bağlantı dizesini kullanır.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Çıkış - kullanım

Tablo depolama çıkış bağlaması aşağıdaki senaryoları destekler:

* **Herhangi bir dilde bir satır yazın**

  C# Ve C# komut dosyasında, `out T paramName` veya işlev dönüş değeri gibi bir yöntem parametresi kullanarak çıkış tablosu varlığına erişin. C# Komut dosyasında, `paramName` *function. JSON* `name` özelliğinde belirtilen değerdir. `T`bölüm anahtarı ve satır anahtarı *function. JSON* dosyası ya `Table` da özniteliği tarafından sağlanmışsa, serileştirilebilir herhangi bir tür olabilir. Aksi halde `T` , `PartitionKey` ve`RowKey` özelliklerini içeren bir tür olmalıdır. Bu senaryoda, `T` genellikle `TableEntity`öğesini uygular `ITableEntity` veya türetiliyor, ancak bunu yapmak zorunda değildir.

* **C# Veya C# betiğe bir veya daha fazla satır yazın**

  C# Ve C# komut dosyasında, bir yöntem parametresi `ICollector<T> paramName` veya `IAsyncCollector<T> paramName`kullanarak çıkış tablosu varlığına erişin. C# Komut dosyasında, `paramName` *function. JSON* `name` özelliğinde belirtilen değerdir. `T`eklemek istediğiniz varlıkların şemasını belirtir. Genellikle, veya uygular `ITableEntity`, ancak bu, öğesine sahip değildir. `TableEntity` `T` *Function. JSON* veya `Table` Attribute oluşturucusunda bölüm anahtarı ve satır anahtarı değerleri bu senaryoda kullanılmıyor.

  Alternatif olarak, Azure depolama SDK `CloudTable` 'sını kullanarak tabloya yazmak için bir yöntem parametresi kullanılır. ' A bağlanıp `CloudTable` bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun. `CloudTable`' A bağlanan bir kod örneği için, bu makalenin önceki kısımlarında yer alan giriş [C#](#input---c-example---cloudtable) bağlama örneklerine veya [ C# betiğine](#input---c-script-example---cloudtable) bakın.

* **JavaScript 'te bir veya daha fazla satır yazma**

  JavaScript işlevleri ' nde, kullanarak `context.bindings.<BINDING_NAME>`tablo çıktısına erişin.

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlama | Başvuru |
|---|---|
| Tablo | [Tablo hata kodları](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, tablo, kuyruk | [Depolama hatası kodları](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tablo, kuyruk | [Sorun giderme](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

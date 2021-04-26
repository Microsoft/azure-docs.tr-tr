---
title: Azure Işlevleri için Azure Tablo depolama Giriş bağlamaları
description: Azure Işlevleri 'nde Azure Tablo depolama girişi bağlamalarını nasıl kullanacağınızı anlayın.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98033787"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Azure Işlevleri için Azure Tablo depolama Giriş bağlamaları

Azure depolama hesabındaki bir tabloyu okumak için Azure Tablo depolama girişi bağlamasını kullanın.

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Bir varlık

Aşağıdaki örnekte, tek bir tablo satırını okuyan bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir. Sıraya gönderilen her ileti için, işlev tetiklenecektir.

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

### <a name="cloudtable"></a>CloudTable

`CloudTable` yalnızca 2. ve [ve sonraki çalışma zamanları](functions-versions.md)için desteklenir.

`CloudTable`Azure depolama SDK 'sını kullanarak tabloyu okumak için bir yöntem parametresi kullanın. Aşağıda, Azure Işlevleri günlük tablosunu sorgulayan bir işleve örnek verilmiştir:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
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

CloudTable 'ın nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Tablo Depolamayı kullanmaya başlama](../cosmos-db/tutorial-develop-table-dotnet.md).

' `CloudTable` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

### <a name="iqueryable"></a>IQueryable

`IQueryable` yalnızca sürüm [v1 çalışma zamanında](functions-versions.md)desteklenir.

Aşağıdaki örnek, sınıfın türetildiği birden çok tablo satırını okuyan bir [C# işlevini](functions-dotnet-class-library.md) gösterir `MyPoco` `TableEntity` .

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

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

### <a name="one-entity"></a>Bir varlık

Aşağıdaki örnek, bağlama kullanan bir *function.js* dosya ve [C# betik](functions-reference-csharp.md) kodundaki tablo girişi bağlamasını gösterir. İşlevi, tek bir tablo satırını okumak için bir kuyruk tetikleyicisi kullanır. 

Dosya *function.js* , bir ve belirtir `partitionKey` `rowKey` . `rowKey`"{QueueTrigger}" değeri, satır anahtarının kuyruk ileti dizesinden geldiğini gösterir.

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu aşağıda verilmiştir:

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

### <a name="cloudtable"></a>CloudTable

`IQueryable`[2. x ve üzeri sürümler](functions-versions.md)için işlevler çalışma zamanı 'nda desteklenmez. Alternatif olarak, `CloudTable` Azure depolama SDK 'sını kullanarak tabloyu okumak için bir yöntem parametresi kullanılır. Aşağıda, Azure Işlevleri günlük tablosunu sorgulayan bir işleve örnek verilmiştir:

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

CloudTable 'ın nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Tablo Depolamayı kullanmaya başlama](../cosmos-db/tutorial-develop-table-dotnet.md).

' `CloudTable` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

### <a name="iqueryable"></a>IQueryable

Aşağıdaki örnek, bağlama kullanan bir *function.js* dosya ve [C# betik](functions-reference-csharp.md) kodundaki tablo girişi bağlamasını gösterir. İşlevi, bir kuyruk iletisinde belirtilen bölüm anahtarı için varlıkları okur.

Dosyada *function.js* :

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu, varlık türünün türeyebilmesi için Azure depolama SDK 'sına bir başvuru ekler `TableEntity` :

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

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki örnek, tablo depolamada belirtilen bir bölümde bulunan kişi nesnelerinin bir listesini döndüren HTTP ile tetiklenen bir işlev gösterir. Örnekte, bölüm anahtarı http rotasına ayıklanır ve tableName ve Connection işlevi işlev ayarlarından çıkarılır. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

Aşağıdaki örnekte gösterildiği gibi, Tableınput ek açıklaması isteğin JSON gövdesinden bağlamaları da ayıklayabilir.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Aşağıdaki örnek, bir Azure tablosunda belirli bir ada sahip kişileri sorgulamak için filtreyi kullanır ve olası eşleşmelerin sayısını 10 sonuçla sınırlandırır.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnek, bir *function.js* dosya ve bağlamayı kullanan [JavaScript kodundaki](functions-reference-node.md) bir tablo girişi bağlamasını gösterir. İşlevi, tek bir tablo satırını okumak için bir kuyruk tetikleyicisi kullanır. 

Dosya *function.js* , bir ve belirtir `partitionKey` `rowKey` . `rowKey`"{QueueTrigger}" değeri, satır anahtarının kuyruk ileti dizesinden geldiğini gösterir.

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki işlev, tek bir tablo satırını bir işleve giriş olarak okumak için bir kuyruk tetikleyicisi kullanır.

Bu örnekte, bağlama yapılandırması tablo için açık bir değer belirtir ve ' a `partitionKey` geçirilecek bir ifade kullanır `rowKey` . `rowKey`İfadesi, `{queueTrigger}` satır anahtarının kuyruk ileti dizesinden geldiğini gösterir.

Yapılandırma yapılandırmasında _function.js_:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

_run.ps1_ içindeki PowerShell kodu:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki işlev, tek bir tablo satırını bir işleve giriş olarak okumak için bir kuyruk tetikleyicisi kullanır.

Bu örnekte, bağlama yapılandırması tablo için açık bir değer belirtir ve ' a `partitionKey` geçirilecek bir ifade kullanır `rowKey` . `rowKey`İfadesi, `{id}` satır anahtarının kuyruk ileti dizesinden geldiğini gösterir.

_function.js_ dosyadaki yapılandırma yapılandırması:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

*\_ \_ İnit \_ \_ . Kopyala* dosyasındaki Python kodu:

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

 [C# sınıf kitaplıklarında](functions-dotnet-class-library.md), tablo girişi bağlamasını yapılandırmak için aşağıdaki öznitelikleri kullanın:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Özniteliğin Oluşturucusu tablo adını, Bölüm anahtarını ve satır anahtarını alır. Öznitelik, `out` Aşağıdaki örnekte gösterildiği gibi, bir parametre veya işlevin dönüş değeri üzerinde kullanılabilir:

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

  `Connection`Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

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

  Tüm örnek için bkz. C# örneği.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Kullanılacak depolama hesabını belirtmek için başka bir yol sağlar. Oluşturucu, depolama bağlantı dizesi içeren bir uygulama ayarının adını alır. Özniteliği parametresi, yöntemi veya sınıf düzeyinde uygulanabilir. Aşağıdaki örnek, sınıf düzeyi ve Yöntem düzeyini gösterir:

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

* `Table`Özniteliğin `Connection` özelliği.
* Özniteliği `StorageAccount` ile aynı parametreye uygulanan öznitelik `Table` .
* `StorageAccount`İşleve uygulanan öznitelik.
* `StorageAccount`Sınıfına uygulanan öznitelik.
* İşlev uygulaması için varsayılan depolama hesabı ("AzureWebJobsStorage" uygulama ayarı).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, `@TableInput` değeri tablo depolamadan gelen parametrelerde ek açıklama kullanın.  Bu ek açıklama, kullanılarak yerel Java türleri, POJOs veya null atanabilir değerlerle kullanılabilir `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Öznitelikler JavaScript tarafından desteklenmez.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Öznitelikler PowerShell tarafından desteklenmez.

# <a name="python"></a>[Python](#tab/python)

Öznitelikler Python tarafından desteklenmez.

---

## <a name="configuration"></a>Yapılandırma

Aşağıdaki tabloda, dosyasında ve özniteliğinde *function.js* ayarladığınız bağlama yapılandırma özellikleri açıklanmaktadır `Table` .

|function.jsözelliği | Öznitelik özelliği |Description|
|---------|---------|----------------------|
|**türüyle** | yok | Olarak ayarlanmalıdır `table` . Azure portal bağlama oluşturduğunuzda bu özellik otomatik olarak ayarlanır.|
|**Görünüm** | yok | Olarak ayarlanmalıdır `in` . Azure portal bağlama oluşturduğunuzda bu özellik otomatik olarak ayarlanır. |
|**ada** | yok | İşlev kodundaki tabloyu veya varlığı temsil eden değişkenin adı. | 
|**tableName** | **TableName** | Tablonun adı.| 
|**partitionKey** | **PartitionKey** |İsteğe bağlı. Okunacak tablo varlığının bölüm anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#usage) bölümüne bakın.| 
|**rowKey** |**RowKey** | İsteğe bağlı. Okunacak tablo varlığının satır anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#usage) bölümüne bakın.| 
|**almanız** |**Almanız** | İsteğe bağlı. JavaScript 'te okunacak en fazla varlık sayısı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#usage) bölümüne bakın.| 
|**filtreyle** |**Filtrele** | İsteğe bağlı. JavaScript 'te tablo girişi için bir OData filtre ifadesi. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım](#usage) bölümüne bakın.| 
|**bağlanma** |**Bağlantı** | Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Bu ayar bir "AzureWebJobs" önekli uygulama ayarının veya bağlantı dizesi adının adı olabilir. Örneğin, ayar adınız "AzureWebJobsMyStorage" ise burada "MyStorage" belirtebilirsiniz. Işlevler çalışma zamanı, "AzureWebJobsMyStorage" adlı bir uygulama ayarını otomatik olarak arar. `connection`Boş bırakırsanız, işlevler çalışma zamanı adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

* **İçindeki bir satırı okuyun**

  `partitionKey`Ve ayarlayın `rowKey` . Bir yöntem parametresi kullanarak tablo verilerine erişin `T <paramName>` . C# komut dosyasında, `paramName` `name` *üzerindefunction.js* özelliğinde belirtilen değerdir. `T` genellikle uygulayan `ITableEntity` veya ondan türetilen bir türdür `TableEntity` . `filter`Ve `take` özellikleri bu senaryoda kullanılmaz.

* **Bir veya daha fazla satırı okuyun**

  Bir yöntem parametresi kullanarak tablo verilerine erişin `IQueryable<T> <paramName>` . C# komut dosyasında, `paramName` `name` *üzerindefunction.js* özelliğinde belirtilen değerdir. `T` uygulayan `ITableEntity` veya ondan türetilen bir tür olmalıdır `TableEntity` . `IQueryable`Yöntemleri, gerekli filtrelemeleri yapmak için kullanabilirsiniz. `partitionKey`,, `rowKey` `filter` Ve `take` özellikleri bu senaryoda kullanılmaz.  

  > [!NOTE]
  > `IQueryable`[Functions v2 çalışma zamanında](functions-versions.md)desteklenmez. Alternatif olarak, Azure depolama SDK 'sını kullanarak tabloyu okumak için [CloudTable paramName yöntemi parametresi kullanılır](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) . ' `CloudTable` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

* **İçindeki bir satırı okuyun**

  `partitionKey`Ve ayarlayın `rowKey` . Bir yöntem parametresi kullanarak tablo verilerine erişin `T <paramName>` . C# komut dosyasında, `paramName` `name` *üzerindefunction.js* özelliğinde belirtilen değerdir. `T` genellikle uygulayan `ITableEntity` veya ondan türetilen bir türdür `TableEntity` . `filter`Ve `take` özellikleri bu senaryoda kullanılmaz.

* **Bir veya daha fazla satırı okuyun**

  Bir yöntem parametresi kullanarak tablo verilerine erişin `IQueryable<T> <paramName>` . C# komut dosyasında, `paramName` `name` *üzerindefunction.js* özelliğinde belirtilen değerdir. `T` uygulayan `ITableEntity` veya ondan türetilen bir tür olmalıdır `TableEntity` . `IQueryable`Yöntemleri, gerekli filtrelemeleri yapmak için kullanabilirsiniz. `partitionKey`,, `rowKey` `filter` Ve `take` özellikleri bu senaryoda kullanılmaz.  

  > [!NOTE]
  > `IQueryable`[Functions v2 çalışma zamanında](functions-versions.md)desteklenmez. Alternatif olarak, Azure depolama SDK 'sını kullanarak tabloyu okumak için [CloudTable paramName yöntemi parametresi kullanılır](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) . ' `CloudTable` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

# <a name="java"></a>[Java](#tab/java)

[Tableınput](/java/api/com.microsoft.azure.functions.annotation.tableinput) özniteliği, işlevi tetikleyen tablo satırına erişmenizi sağlar.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`filter`Ve özelliklerini ayarlayın `take` . Veya ayarlama `partitionKey` `rowKey` . Kullanarak giriş tablosu varlığına (veya varlıklara) erişin `context.bindings.<BINDING_NAME>` . Seri durumdan çıkarılan nesneler `RowKey` ve `PartitionKey` özellikleri.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Veriler, `name` *function.js* dosyadaki anahtar tarafından belirtilen giriş parametresine geçirilir. `partitionKey`' İ belirtme ve `rowKey` belirli kayıtlara filtrelemenize izin verir. Daha fazla ayrıntı için bkz. [PowerShell örneği](#example) .

# <a name="python"></a>[Python](#tab/python)

Tablo verileri, işleve JSON dizesi olarak geçirilir. `json.loads`Giriş [örneğinde](#example)gösterildiği gibi çağırarak iletiyi seri durumdan kaldırın.

---

## <a name="next-steps"></a>Sonraki adımlar

* [Bir işlevden tablo depolama verileri yazma](./functions-bindings-storage-table-output.md)

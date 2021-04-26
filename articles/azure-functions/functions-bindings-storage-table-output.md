---
title: Azure Işlevleri için Azure Tablo depolama çıkış bağlamaları
description: Azure Işlevleri 'nde Azure Tablo depolama çıkış bağlamalarının nasıl kullanılacağını anlayın.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f793f96f55a258b2d7cb11f214984416557618df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453011"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Azure Işlevleri için Azure Tablo depolama çıkış bağlamaları

Azure depolama hesabındaki bir tabloya varlıklar yazmak için bir Azure Tablo depolama çıkış bağlaması kullanın.

> [!NOTE]
> Bu çıkış bağlaması mevcut varlıkların güncelleştirilmesini desteklemiyor. `TableOperation.Replace`Mevcut bir varlığı güncelleştirmek Için [Azure depolama SDK 'sının](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) işlemini kullanın.

## <a name="example"></a>Örnek

# <a name="c"></a>[C#](#tab/csharp)

Aşağıdaki örnekte, tek bir tablo satırı yazmak için HTTP tetikleyicisi kullanan bir [C# işlevi](functions-dotnet-class-library.md) gösterilmektedir.

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

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Aşağıdaki örnek, bağlama kullanan bir *function.js* dosya ve [C# betik](functions-reference-csharp.md) kodundaki tablo çıkış bağlamasını gösterir. İşlev birden çok tablo varlığı yazar.

Dosyada *function.js* :

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

C# betik kodu aşağıda verilmiştir:

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

# <a name="java"></a>[Java](#tab/java)

Aşağıdaki örnekte, tek bir tablo satırı yazmak için HTTP tetikleyicisi kullanan bir Java işlevi gösterilmektedir.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Aşağıdaki örnekte, birden çok tablo satırı yazmak için HTTP tetikleyicisi kullanan bir Java işlevi gösterilmektedir.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aşağıdaki örnekte, bir *function.js* dosyadaki bir tablo çıkış bağlaması ve bağlamayı kullanan bir [JavaScript işlevi](functions-reference-node.md) gösterilmektedir. İşlev birden çok tablo varlığı yazar.

Dosyada *function.js* :

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

[Yapılandırma](#configuration) bölümünde bu özellikler açıklanmaktadır.

JavaScript kodu aşağıda verilmiştir:

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aşağıdaki örnek, bir işlevden bir tabloya birden çok varlığın nasıl yazılacağını gösterir.

Yapılandırma yapılandırmasında _function.js_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

_run.ps1_ içindeki PowerShell kodu:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Aşağıdaki örnek, tablo depolama çıkış bağlamasının nasıl kullanılacağını göstermektedir. Bağlama,,, ve ' a `table` değer atayarak *üzerindefunction.js* yapılandırılır `name` `tableName` `partitionKey` `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Aşağıdaki işlev değer için benzersiz bir UUı oluşturur `rowKey` ve Iletiyi tablo depolamaya devam ettirir.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

---

## <a name="attributes-and-annotations"></a>Öznitelikler ve ek açıklamalar

# <a name="c"></a>[C#](#tab/csharp)

[C# sınıf kitaplıkları](functions-dotnet-class-library.md)' nda [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)' u kullanın.

Özniteliğin Oluşturucusu tablo adını alır. Öznitelik, `out` Aşağıdaki örnekte gösterildiği gibi, bir parametre veya işlevin dönüş değeri üzerinde kullanılabilir:

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

`Connection`Aşağıdaki örnekte gösterildiği gibi, kullanılacak depolama hesabını belirtmek için özelliğini ayarlayabilirsiniz:

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

Tüm örnek için bkz. [C# örneği](#example).

`StorageAccount`Sınıfı, yöntemi veya parametre düzeyinde depolama hesabını belirtmek için özniteliğini kullanabilirsiniz. Daha fazla bilgi için bkz. [giriş-öznitelikler](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Öznitelikler C# betiği tarafından desteklenmez.

# <a name="java"></a>[Java](#tab/java)

[Java işlevleri çalışma zamanı kitaplığı](/java/api/overview/azure/functions/runtime)'nda, değerleri tablo depolamaya yazmak Için parametrelerde [tableoutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) ek açıklamasını kullanın.

[Daha fazla ayrıntı için örneğe](#example)bakın.

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
|**Görünüm** | yok | Olarak ayarlanmalıdır `out` . Azure portal bağlama oluşturduğunuzda bu özellik otomatik olarak ayarlanır. |
|**ada** | yok | Tablo veya varlığı temsil eden işlev kodunda kullanılan değişken adı. `$return`İşlev dönüş değerine başvuracak şekilde ayarlanır.| 
|**tableName** |**TableName** | Tablonun adı.| 
|**partitionKey** |**PartitionKey** | Yazılacak tablo varlığının bölüm anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım bölümüne](#usage) bakın.| 
|**rowKey** |**RowKey** | Yazılacak tablo varlığının satır anahtarı. Bu özelliğin nasıl kullanılacağına ilişkin yönergeler için [kullanım bölümüne](#usage) bakın.| 
|**bağlanma** |**Bağlantı** | Bu bağlama için kullanılacak depolama bağlantı dizesini içeren bir uygulama ayarının adı. Uygulama ayarı adı "AzureWebJobs" ile başlıyorsa, burada yalnızca adının geri kalanını belirtebilirsiniz. Örneğin, `connection` "MyStorage" olarak ayarlarsanız, işlevler çalışma zamanı "MyStorage" adlı bir uygulama ayarı arar. `connection`Boş bırakırsanız, işlevler çalışma zamanı adlı uygulama ayarında varsayılan depolama bağlantı dizesini kullanır `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Kullanım

# <a name="c"></a>[C#](#tab/csharp)

Bir yöntem parametresi kullanarak çıkış tablosu varlığına erişin `ICollector<T> paramName` veya `IAsyncCollector<T> paramName` `T` , `PartitionKey` ve `RowKey` özelliklerini içerir. Bu özellikler genellikle uygulama `ITableEntity` veya devralma ile birlikte sunulur `TableEntity` .

Alternatif olarak, `CloudTable` Azure Storage SDK kullanarak tabloya yazmak için bir yöntem parametresi kullanabilirsiniz. ' `CloudTable` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

# <a name="c-script"></a>[C# betiği](#tab/csharp-script)

Bir yöntem parametresi kullanarak çıkış tablosu varlığına erişin `ICollector<T> paramName` veya `IAsyncCollector<T> paramName` `T` , `PartitionKey` ve `RowKey` özelliklerini içerir. Bu özellikler genellikle uygulama `ITableEntity` veya devralma ile birlikte sunulur `TableEntity` . `paramName`Değer, `name` *üzerindefunction.js* özelliğinde belirtilir.

Alternatif olarak, `CloudTable` Azure Storage SDK kullanarak tabloya yazmak için bir yöntem parametresi kullanabilirsiniz. ' `CloudTable` A bağlanıp bir hata iletisi almaya çalışırsanız, [doğru depolama SDK sürümüne](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

# <a name="java"></a>[Java](#tab/java)

[Tablestorageoutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) ek açıklamasını kullanarak bir işlevden tablo depolama satırını almak için iki seçenek vardır:

- **Dönüş değeri**: ek açıklamanın işleve uygulanması için, işlevin dönüş değeri tablo depolama satırı olarak kalıcıdır.

- **Kesinlik**: ileti değerini açık olarak ayarlamak için, ek açıklamayı türünün belirli bir parametresine uygulayın [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) ; burada `T` `PartitionKey` ve özellikleri de bulunur `RowKey` . Bu özellikler genellikle uygulama `ITableEntity` veya devralma ile birlikte sunulur `TableEntity` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Çıkış olayına, `context.bindings.<name>` `<name>` `name` *üzerindefunction.js* özelliğinde belirtilen değerin nerede olduğunu kullanarak erişin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Tablo verilerine yazmak için `Push-OutputBinding` cmdlet 'ini kullanın, `-Name TableBinding` parametresini ve `-Value` parametresini satır verilerine eşit olarak ayarlayın. Daha fazla ayrıntı için bkz. [PowerShell örneği](#example) .

# <a name="python"></a>[Python](#tab/python)

Bir işlevden tablo depolama satırı iletisi çıktısı için iki seçenek vardır:

- **Dönüş değeri**: `name` *üzerindefunction.js* özelliğini olarak ayarlayın `$return` . Bu yapılandırmayla, işlevin dönüş değeri tablo depolama satırı olarak kalıcıdır.

- **Zorunludur**: bir değeri, [Out](/python/api/azure-functions/azure.functions.out) türü olarak belirtilen parametresinin [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) yöntemine geçirin. Geçirilen değer `set` bir olay hub 'ı iletisi olarak kalıcıdır.

---

## <a name="exceptions-and-return-codes"></a>Özel durumlar ve dönüş kodları

| Bağlama | Başvuru |
|---|---|
| Tablo | [Tablo hata kodları](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, tablo, kuyruk | [Depolama hatası kodları](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tablo, kuyruk | [Sorun giderme](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure işlevleri Tetikleyicileri ve bağlamaları hakkında daha fazla bilgi edinin](functions-triggers-bindings.md)

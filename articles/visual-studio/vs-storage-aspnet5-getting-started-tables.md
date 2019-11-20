---
title: Visual Studio kullanarak tablo depolamayı kullanmaya başlama (ASP.NET Core)
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da bir ASP.NET Core projesinde Azure Tablo depolama ile çalışmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298822"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Azure Tablo depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Bu makalede, Visual Studio **bağlı hizmetler** özelliğini kullanarak bir ASP.NET Core projesinde Azure Storage hesabı oluşturduktan veya başvurduktan sonra Visual Studio 'Da Azure Table Storage 'ı kullanmaya nasıl başlacağınız açıklanır. **Bağlı hizmetler** işlemi, projenizde Azure depolama 'ya erişmek ve depolama hesabı için bağlantı dizesini proje yapılandırma dosyalarınıza eklemek üzere uygun NuGet paketlerini yükleyerek ekler. (Azure depolama hakkında genel bilgi için bkz. [depolama belgeleri](https://azure.microsoft.com/documentation/services/storage/) .)

Azure Tablo depolama hizmeti, büyük miktarlarda yapılandırılmış verileri depolamanıza olanak sağlar. Hizmet, Azure bulutunun içinden ve dışından kimliği doğrulanmış çağrılar kabul eden bir NoSQL veri deposudur. Azure tabloları, yapılandırılmış ve ilişkisel olmayan verilerin depolanması için idealdir. Azure Tablo Depolamayı kullanma hakkında daha fazla genel bilgi için bkz. [.NET kullanarak Azure Tablo Depolamayı kullanmaya başlama](../storage/storage-dotnet-how-to-use-tables.md).

Başlamak için önce depolama hesabınızda bir tablo oluşturun. Bu makale daha sonra ' de C# bir tablo oluşturmayı ve tablo girişlerini ekleme, değiştirme, okuma ve kaldırma gibi temel tablo işlemlerini gerçekleştirmeyi gösterir.  Kod, .NET için Azure Storage Istemci kitaplığı 'nı kullanır. ASP.NET hakkında daha fazla bilgi için bkz. [ASP.net](https://www.asp.net).

Bazı Azure depolama API 'Leri zaman uyumsuzdur ve bu makaledeki kodda zaman uyumsuz yöntemlerin kullanıldığı varsayılır. Daha fazla bilgi için bkz. [zaman uyumsuz programlama](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-tables-in-code"></a>Koddaki erişim tabloları

ASP.NET Core projelerindeki tablolara erişmek için, Azure Tablo depolamaya erişen tüm C# kaynak dosyalarına aşağıdaki öğeleri eklemeniz gerekir.

1. Gerekli `using` deyimlerini ekleyin:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Depolama hesabı bilgilerinizi temsil eden `CloudStorageAccount` nesnesi alın. Depolama hesabınızın adını ve appSettings. JSON içindeki depolama bağlantı dizesinde bulabileceğiniz hesap anahtarını kullanarak aşağıdaki kodu kullanın:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Depolama hesabınızdaki tablo nesnelerine başvurmak için `CloudTableClient` nesnesi alın:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Belirli bir tablo ve varlığa başvurmak için `CloudTable` başvuru nesnesi alın:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Kod içinde tablo oluşturma

Azure tablosu oluşturmak için zaman uyumsuz bir yöntem oluşturun ve içinde `CreateIfNotExistsAsync()` ' ı çağırın:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Tabloya bir varlık ekleme

Bir tabloya varlık eklemek için, varlığınızın özelliklerini tanımlayan bir sınıf oluşturursunuz. Aşağıdaki kod, satır anahtarı olarak müşterinin adını ve bölüm anahtarı olarak soyadı ' nı kullanan `CustomerEntity` adlı bir varlık sınıfını tanımlar.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Varlıklar ile ilgili tablo işlemleri, [kod Içindeki erişim tablolarında](#access-tables-in-code)daha önce oluşturduğunuz `CloudTable` nesnesini kullanır. @No__t-0 nesnesi, yapılacak işlemi temsil eder. Aşağıdaki kod örneği, `CloudTable` nesnesinin ve `CustomerEntity` nesnesinin nasıl oluşturulacağını gösterir. İşlemi hazırlamak için, müşteri varlığını tabloya eklemek üzere bir `TableOperation` oluşturulur. Son olarak, işlem `CloudTable.ExecuteAsync` çağırarak yürütülür.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Toplu işlem varlık yerleştirme

Tek bir yazma işleminde bir tabloya birden çok varlık ekleyebilirsiniz. Aşağıdaki kod örneği iki varlık nesnesi oluşturur ("Jeff Smith" ve "Ben Smith"), `Insert` metodunu kullanarak bunları `TableBatchOperation` nesnesine ekler ve sonra işlemi `CloudTable.ExecuteBatchAsync` çağırarak başlatır.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Bir bölümdeki tüm varlıkları al

Bir bölümdeki tüm varlıklar için bir tabloyu sorgulamak üzere `TableQuery` nesnesi kullanın. Aşağıdaki kod örneği, ‘Smith’in bölüm anahtarı olduğu varlıklar için bir filtre belirtir. Bu örnek sorgu sonuçlarındaki her varlığın alanlarını konsola yazdırır.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Tek bir varlık alın

Tek, belirli bir varlığı almak için bir sorgu yazabilirsiniz. Aşağıdaki kod, ' Ben Smith ' adlı bir müşteriyi belirtmek için `TableOperation` nesnesini kullanır. Yöntemi bir koleksiyon yerine yalnızca bir varlık döndürür ve `TableResult.Result` ' daki döndürülen değer bir `CustomerEntity` nesnesidir. Bir sorguda hem bölüm hem de satır anahtarlarının belirtilmesi, `Table` hizmetinden tek bir varlık almanın en hızlı yoludur.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Bir varlığı silme

Bulduğunuz bir varlığı, bulduktan sonra silebilirsiniz. Aşağıdaki kod, "Ben Smith" adlı bir müşteri varlığını arar ve siler:

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

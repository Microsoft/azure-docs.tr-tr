---
title: Visual Studio kullanarak tablo depolamayı kullanmaya başlama (bulut Hizmetleri)
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da bir bulut hizmeti projesinde Azure Tablo Depolamayı kullanmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5e687b172b49fec5f77615e332d0a2204162c43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545791"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Azure tablo depolama ve Visual Studio’ya bağlı hizmetleri kullanmaya başlama (bulut hizmeti projeleri)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Genel Bakış
Bu makalede, Visual Studio **bağlı hizmetler Ekle** iletişim kutusunu kullanarak bir bulut hizmetleri projesinde Azure depolama hesabı oluşturduktan veya başvurduktan sonra Visual Studio 'da Azure Table Storage 'ı kullanmaya nasıl başlacağınız açıklanır. **Bağlı hizmetler ekleme** işlemi, projenizdeki Azure depolama 'ya erişmek Için uygun NuGet paketlerini yükleyerek depolama hesabı için bağlantı dizesini proje yapılandırma dosyalarınıza ekler.

Azure Tablo depolama hizmeti, büyük miktarlarda yapılandırılmış verileri depolamanıza olanak sağlar. Hizmet, Azure bulutu içinden ve dışından kimliği doğrulanmış çağrıları kabul eden bir NoSQL veri deposu olur. Azure tabloları, yapılandırılmış ve ilişkisel olmayan verilerin depolanması için idealdir.

Başlamak için önce depolama hesabınızda bir tablo oluşturmanız gerekir. Kodda bir Azure tablosu oluşturmayı ve ayrıca tablo varlıklarını ekleme, değiştirme, okuma ve okuma gibi temel tablo ve varlık işlemlerini gerçekleştirmeyi göstereceğiz. Örnekler C \# koduna yazılır ve [.net için Microsoft Azure depolama istemci kitaplığı](/previous-versions/azure/dn261237(v=azure.100))' nı kullanır.

**Note:** Azure depolama 'ya çağrı gerçekleştiren bazı API 'Ler zaman uyumsuzdur. Daha fazla bilgi için bkz. [Async ve await Ile zaman uyumsuz programlama](/previous-versions/hh191443(v=vs.140)) . Aşağıdaki kod zaman uyumsuz programlama yöntemlerinin kullanıldığını varsayar.

* Tabloları programlama yoluyla düzenleme hakkında daha fazla bilgi için bkz. [.NET kullanarak Azure Tablo Depolamayı kullanmaya başlama](../cosmos-db/tutorial-develop-table-dotnet.md) .
* Azure depolama hakkında genel bilgi için bkz. [depolama belgeleri](https://azure.microsoft.com/documentation/services/storage/) .
* Azure Cloud Services hakkında genel bilgi için bkz. [Cloud Services belgeleri](https://azure.microsoft.com/documentation/services/cloud-services/) .
* ASP.NET uygulamalarını programlama hakkında daha fazla bilgi için bkz. [ASP.net](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Koddaki erişim tabloları
Bulut hizmeti projelerindeki tablolara erişmek için, Azure Tablo depolamaya erişen herhangi bir C# kaynak dosyasına aşağıdaki öğeleri eklemeniz gerekir.

1. C# dosyasının en üstündeki ad alanı bildirimlerinin bu **using** deyimlerini içerdiğinden emin olun.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Depolama hesabı bilgilerinizi temsil eden bir **Cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>
    _AzureStorageConnectionString"));
    ```
   > [!NOTE]
   > Aşağıdaki örneklerde kodun önünde yukarıdaki kodu kullanın.
   > 
   > 
3. Depolama hesabınızdaki tablo nesnelerine başvuracak bir **Cloudtableclient** nesnesi alın.
   
    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```
4. Belirli bir tabloya ve varlıklara başvuracak bir **Cloudtable** başvuru nesnesi alın.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Kod içinde tablo oluşturma
Azure tablosu oluşturmak için, "kodda erişim tabloları" bölümünde açıklandığı gibi bir **Cloudtable** nesnesi aldıktan sonra bir **Createifnotexistsasync** öğesine çağrı eklemeniz yeterlidir.

```csharp
// Create the CloudTable if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Tabloya bir varlık ekleme
Tabloya bir varlık eklemek için varlığınızın özelliklerini tanımlayan bir sınıf oluşturun. Aşağıdaki kod, müşterinin ilk adını satır anahtarı olarak ve bölüm anahtarı olarak soyadı ' nı kullanan **customerentity** adlı bir varlık sınıfını tanımlar.

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

Varlıklar ile ilgili tablo işlemleri, daha önce "koddaki erişim tabloları" içinde oluşturduğunuz **Cloudtable** nesnesi kullanılarak yapılır. **Tableoperation** nesnesi yapılacak işlemi temsil eder. Aşağıdaki kod örneği, **Cloudtable** nesnesinin ve **customerentity** nesnesinin nasıl oluşturulduğunu gösterir. İşlemi hazırlamak için, müşteri varlığını tabloya eklemek üzere bir **Tableoperation** oluşturulur. Son olarak, işlem **CloudTable.ExecuteAsync** çağırarak yürütülür.

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
Tek bir yazma işleminde bir tabloya birden çok varlık ekleyebilirsiniz. Aşağıdaki kod örneği iki varlık nesnesi oluşturur ("Jeff Smith" ve "Ben Smith"), bunları Insert metodunu kullanarak bir **Tablebatchoperation** nesnesine ekler ve sonra **CloudTable.ExecuteBatchAsync** öğesini çağırarak işlemi başlatır.

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
Bir bölümdeki tüm varlıklar için bir tabloyu sorgulamak üzere bir **Tablequery** nesnesi kullanın. Aşağıdaki kod örneği, ‘Smith’in bölüm anahtarı olduğu varlıklar için bir filtre belirtir. Bu örnek sorgu sonuçlarındaki her varlığın alanlarını konsola yazdırır.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

return View();
```


## <a name="get-a-single-entity"></a>Tek bir varlık alın
Tek, belirli bir varlığı almak için bir sorgu yazabilirsiniz. Aşağıdaki kod, ' Ben Smith ' adlı bir müşteriyi belirtmek için bir **Tableoperation** nesnesi kullanır. Bu yöntem bir koleksiyon yerine yalnızca bir varlık döndürür ve **Tableresult. Result** içindeki döndürülen değer bir **customerentity** nesnesidir. Bir sorguda hem bölüm hem de satır anahtarlarının belirtilmesi, **tablo** hizmetinden tek bir varlık almanın en hızlı yoludur.

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
Bulduğunuz bir varlığı, bulduktan sonra silebilirsiniz. Aşağıdaki kod, "Ben Smith" adlı bir müşteri varlığına bakar ve bulursa onu siler.

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

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
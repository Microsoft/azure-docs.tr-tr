---
title: .NET Standard SDK kullanarak Azure Cosmos DB Tablo API'si kullanmaya başlama
description: Yapılandırılmış verileri Azure Cosmos DB Tablo API'si kullanarak bulutta depolayın.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 05/20/2019
ms.openlocfilehash: 677ea48244f8417670a2645ab67fa08c1f869f1a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142594"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>.NET SDK kullanarak Azure Cosmos DB Tablo API'si ve Azure Tablo depolama ile çalışmaya başlama

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Yapılandırılmış NoSQL verilerini bulutta depolamak için Azure Cosmos DB Tablo API'si veya Azure Tablo depolama alanını kullanabilir, böylece şema daha az tasarıma sahip bir anahtar/öznitelik deposu sağlayabilirsiniz. Azure Cosmos DB Tablo API'si ve tablo depolaması şema daha az olduğundan, uygulamanızın ihtiyaçları geliştikçe verilerinizi kolayca uyarlayabilirsiniz. Web uygulamaları için Kullanıcı verileri, adres defterleri, cihaz bilgileri veya hizmetinizin gerektirdiği diğer meta veri türleri gibi esnek veri kümelerini depolamak için Azure Cosmos DB Tablo API'si veya tablo depolama alanını kullanabilirsiniz. 

Bu öğretici, Azure Cosmo DB Tablo API'si ve Azure Tablo depolama senaryolarıyla [.NET için Microsoft Azure Cosmos db tablo kitaplığının](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) nasıl kullanılacağını gösteren bir örneği açıklar. Azure hizmetine özel bağlantıyı kullanmanız gerekir. Bu senaryolar, tablo oluşturmayı C# , veri eklemeyi/güncelleştirmeyi, verileri sorgulamayı ve tabloları silmeyi gösteren örnekler kullanılarak araştırılabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu örneği başarıyla tamamlamak için aşağıdakiler gerekir:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [.Net Için CosmosDB tablo kitaplığı Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) -bu kitaplık, .NET Standard ve .NET Framework 'te Şu anda kullanılabilir. 

* [Azure Cosmos DB tablo API'si hesabı](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Tablo API’si hesabı oluşturma

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>.NET konsol projesi oluşturma

Visual Studio 'da yeni bir .NET konsol uygulaması oluşturun. Aşağıdaki adımlarda, Visual Studio 2019 ' de bir konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. Azure Cosmos DB tablo kitaplığını, bir Azure bulut hizmeti veya Web uygulaması, masaüstü ve mobil uygulamalar dahil olmak üzere herhangi bir türde .NET uygulamasında kullanabilirsiniz. Bu kılavuzda, sadeleştirmek için konsol uygulaması kullanmaktayız.

1. **Dosya** > **Yeni** > **Proje**’yi seçin.

1. **Konsol uygulaması (.NET Core)** öğesini seçin ve ardından **İleri**' yi seçin.

1. **Proje adı** alanında, uygulamanız Için **Cosmostablesamples**gibi bir ad girin. (Gerektiğinde farklı bir ad sağlayabilirsiniz.)

1. **Oluştur**’u seçin.

Bu örnekteki tüm kod örnekleri konsol uygulamanızın **program.cs** dosyasının Main () yöntemine eklenebilir.

## <a name="install-the-required-nuget-package"></a>Gerekli NuGet paketini yükler

NuGet paketini edinmek için aşağıdaki adımları izleyin:

1. **Çözüm Gezgini**'nde projenize sağ tıklayın ve **NuGet Paketlerini Yönet**’i seçin.

1. , `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration` Ve için çevrimiçi `Microsoft.Extensions.Configuration.Json`olarak arama yapın ve Microsoft Azure Cosmos db tablo kitaplığını yüklemek için yüklemeyi seçin. `Microsoft.Extensions.Configuration.Binder`

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

1. [Azure Portal](https://portal.azure.com/)Azure Cosmos hesabınıza veya tablo depolama hesabına gidin. 

1. **Bağlantı dizesini** veya **erişim tuşları** bölmesini açın. Pencerenin sağ tarafındaki kopyala düğmesini kullanarak **PRIMARY CONNECTION STRING**'i kopyalayın.

   ![Bağlantı Dizesi bölmesindeki PRIMARY CONNECTION STRING’i görüntüleyin ve kopyalayın](./media/create-table-dotnet/connection-string.png)
   
1. Bağlantı dizenizi yapılandırmak için, Visual Studio 'dan projenizin **Cosmostablesamples**öğesine sağ tıklayın.

1. **Ekle** ' yi ve ardından **Yeni öğe**' yi seçin. **TYPESCRIPT JSON yapılandırma** dosyası olarak dosya türüne sahip yeni bir dosya **Settings. JSON** oluşturun. 

1. Settings. JSON dosyasındaki kodu aşağıdaki kodla değiştirin ve birincil Bağlantı dizenizi atayın:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Projenizin **Cosmostablesamples**öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **appSettings.cs**adlı bir sınıf ekleyin.

1. Aşağıdaki kodu AppSettings.cs dosyasına ekleyin. Bu dosya, Settings. json dosyasından bağlantı dizesini okur ve onu yapılandırma parametresine atar:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Bağlantı ayrıntılarını ayrıştırma ve doğrulama 

1. Projenizin **Cosmostablesamples**öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **Common.cs**adlı bir sınıf ekleyin. Bağlantı ayrıntılarını doğrulamak ve bu sınıf içinde bir tablo oluşturmak için kod yazacaksınız.

1. Aşağıda gösterildiği gibi `CreateStorageAccountFromConnectionString` bir yöntem tanımlayın. Bu yöntem, bağlantı dizesi ayrıntılarını ayrıştırır ve "Settings. JSON" dosyasında sunulan hesap adı ve hesap anahtarı ayrıntılarının geçerli olduğunu doğrular. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Tablo oluşturma 

[CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) sınıfı, Table Storage’da depolanan tabloları ve varlıkları almanızı sağlar. Cosmos db tablo API'si hesabında hiç tablo olmadığı için, bir tablo oluşturmak üzere `CreateTableAsync` yöntemi **Common.cs** sınıfına ekleyelim:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>Varlığı tanımlama 

Varlıklar, C# [tableentity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)'dan türetilmiş özel bir sınıf kullanarak nesnelerle eşlenir. Tabloya bir varlık eklemek için varlığınızın özelliklerini tanımlayan bir sınıf oluşturun.

Projenizin **Cosmostablesamples**öğesine sağ tıklayın. **Ekle**, **Yeni klasör** ' ü seçin ve **model**olarak adlandırın. Model klasörü içinde **CustomerEntity.cs** adlı bir sınıf ekleyin ve bu sınıfa aşağıdaki kodu ekleyin.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Bu kod, satır anahtarı olarak müşterinin adını ve bölüm anahtarı olarak soyadı ' nı kullanan bir varlık sınıfını tanımlar. Birlikte, bir varlığın bölüm ve sıra anahtarı varlığı tabloda benzersiz şekilde tanımlar. Aynı bölüm anahtarına sahip varlıklar farklı bölüm anahtarları olan varlıklardan daha hızlı sorgulanabilir ancak farklı bölüm anahtarlarının kullanılması paralel işlemler için daha fazla ölçeklenebilirlik sağlar. Tablolarda depolanacak varlıkların, örneğin [Tableentity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) sınıfından türetilmiş, desteklenen bir türde olması gerekir. Bir tabloda depolamak istediğiniz varlık özellikleri, türün genel özellikleri olmalı ve değerleri hem almayı hem de ayarlamayı desteklemelidir. Ayrıca, varlık türü bir parametre-daha az Oluşturucu kullanıma sunmalıdır.

## <a name="insert-or-merge-an-entity"></a>Varlık ekleme veya birleştirme

Aşağıdaki kod örneği bir varlık nesnesi oluşturur ve onu tabloya ekler. [Tableoperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) sınıfı Içindeki ınsertormerge yöntemi bir varlık eklemek veya birleştirmek için kullanılır. İşlemi yürütmek için [Cloudtable. ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) yöntemi çağırılır. 

Projenizin **Cosmostablesamples**öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **SamplesUtils.cs**adlı bir sınıf ekleyin. Bu sınıf, varlıklarda CRUD işlemleri gerçekleştirmek için gereken tüm kodu depolar. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Bir bölümden varlık edinme

[Tableoperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) sınıfının altındaki Retrieve metodunu kullanarak bir bölümden varlık elde edebilirsiniz. Aşağıdaki kod örneği, bir müşteri varlığının bölüm anahtarı satır anahtarını, e-posta ve telefon numarasını alır. Bu örnek ayrıca varlık için sorgulamak üzere tüketilen istek birimlerini de yazdırır. Bir varlığı sorgulamak için, **SamplesUtils.cs** dosyasına aşağıdaki kodu ekleyin: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Bir varlığı silme

Bir varlığı güncelleştirmek için gösterilen aynı yöntemi kullanarak, bir varlığı aldıktan sonra kolayca silebilirsiniz. Aşağıdaki kod bir müşteri girişini alır ve siler. Bir varlığı silmek için, **SamplesUtils.cs** dosyasına aşağıdaki kodu ekleyin: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Örnek verilerde CRUD işlemlerini yürütün

Tablo oluşturma, varlık ekleme veya birleştirme yöntemlerini tanımladıktan sonra, bu yöntemleri örnek verilerde çalıştırın. Bunu yapmak için, projenizin **Cosmostablesamples**öğesine sağ tıklayın. **Ekle**, **Yeni öğe** ' yi seçin ve **BasicSamples.cs** adlı bir sınıf ekleyin ve bu sınıfa aşağıdaki kodu ekleyin. Bu kod, bir tablo oluşturur ve ona varlıklar ekler. Projenin sonundaki varlığı ve tabloyu silmek isterseniz, aşağıdaki koddan açıklamaları `table.DeleteIfExistsAsync()` ve `SamplesUtils.DeleteEntityAsync(table, customer)` yöntemleri kaldırın:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Önceki kod, "Demo" ile başlayan bir tablo oluşturur ve oluşturulan GUID tablo adına eklenir. Ardından, ilk ve son adı "Harp Walter" olarak bir müşteri varlığı ekler ve daha sonra bu kullanıcının telefon numarasını günceller. 

Bu öğreticide, Tablo API'si hesapta depolanan veriler üzerinde temel CRUD işlemlerini gerçekleştirmek için kod derlediniz. Ayrıca, – toplu veri ekleme, bir bölümdeki tüm verileri sorgulama, bir bölüm içindeki veri aralığını sorgulama ve adları belirtilen önekle başlayan hesaptaki tabloları listeleyen gibi gelişmiş işlemleri de gerçekleştirebilirsiniz. Tam örnek form olan [Azure-Cosmos-Table-DotNet-Core-Başlarken](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub deposunu indirebilirsiniz. [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) sınıfı, veri üzerinde gerçekleştirebileceğiniz daha fazla işlem içerir.  

## <a name="run-the-project"></a>Projeyi çalıştırma

Proje **Cosmostablesamples**. **Program.cs** adlı sınıfı açın ve proje çalışırken basicsamples ' i çağırmak için aşağıdaki kodu ekleyin.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Şimdi Çözümü derleyin ve F5 tuşuna basarak projeyi çalıştırın. Proje çalıştırıldığında, komut isteminde aşağıdaki çıktıyı görürsünüz:

![Komut isteminden çıkış](./media/tutorial-develop-table-standard/output-from-sample.png)

Projeyi çalıştırırken Settings. json dosyasının bulunamadığını bildiren bir hata alırsanız, proje ayarlarına aşağıdaki XML girişini ekleyerek bu sorunu çözebilirsiniz. CosmosTableSamples öğesine sağ tıklayın, CosmosTableSamples. csproj öğesini Düzenle ' yi seçin ve şu ItemGroup 'u ekleyin: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Artık Azure portal oturum açabilir ve verilerin tabloda var olduğunu doğrulayabilirsiniz. 

![Portalda sonuçlar](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık sonraki öğreticiye devam edebilir ve verileri Azure Cosmos DB Tablo API'si hesabına geçirmeyi öğrenebilirsiniz. 

> [!div class="nextstepaction"]
>[Verileri sorgulama](../cosmos-db/table-import.md)

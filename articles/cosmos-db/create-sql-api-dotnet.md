---
title: Azure Cosmos DB SQL API kaynaklarını yönetmek için bir .NET konsol uygulaması oluşturma
description: Azure Cosmos DB SQL API hesabı kaynaklarını yönetmek için bir .NET konsol uygulaması oluşturmayı öğrenin.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 109fe3bc19aeaee6e0790d351d9981f2dc878561
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486083"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Hızlı başlangıç: Azure Cosmos DB SQL API kaynaklarını yönetmek için bir .NET konsol uygulaması oluşturma

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

.NET için Azure Cosmos DB SQL API istemci kitaplığı ile çalışmaya başlayın. .NET paketini yüklemek, bir uygulama derlemek ve Azure Cosmos DB depolanan veriler üzerinde temel CRUD işlemleri için örnek kodu denemek üzere bu belgedeki adımları izleyin. 

Azure Cosmos DB, Microsoft'un genel olarak dağıtılmış çok modelli veritabanı hizmetidir. Anahtar/değer, belge ve grafik veritabanlarını hızlıca oluşturmak ve sorgulamak için Azure Cosmos DB kullanabilirsiniz. .NET için Azure Cosmos DB SQL API istemci kitaplığı 'nı kullanarak şunları yapın:

* Azure Cosmos veritabanı ve kapsayıcısı oluşturma
* Kapsayıcıya örnek veri ekleme
* Verileri sorgulama 
* Veritabanını silme

[API başvuru belgeleri](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-cosmos-dotnet-v3) | [paketi (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/) veya Azure aboneliği olmadan [ücretsiz Azure Cosmos DB deneyebilir](https://azure.microsoft.com/try/cosmosdb/) , ücretsiz ve taahhütlere sahip olabilirsiniz. 
* [.NET Core 2,1 SDK veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, kaynakları yönetmek için .NET için Azure Cosmos DB SQL API istemci kitaplığı 'nı kullanan bir projeyi ayarlama ve Azure Cosmos hesabı oluşturma işlemi adım adım açıklanmaktadır. Bu makalede açıklanan örnek kod, bu veritabanı içindeki bir `FamilyDatabase` veritabanı ve aile üyeleri (her aile üyesi bir öğedir) oluşturur. Her aile üyesinin `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`gibi özellikleri vardır. `LastName` özelliği, kapsayıcının bölüm anahtarı olarak kullanılır. 

### <a id="create-account"></a>Azure Cosmos hesabı oluşturma

Azure Cosmos hesabı oluşturmak için [ücretsiz deneme Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) seçeneğini kullanırsanız, **SQL apı**türünde bir Azure Cosmos DB hesabı oluşturmanız gerekir. Sizin için bir Azure Cosmos DB test hesabı zaten oluşturulmuş. Hesabı açıkça oluşturmanız gerekmez; bu sayede bu bölümü atlayabilir ve sonraki bölüme geçebilirsiniz.

Kendi Azure aboneliğiniz varsa veya ücretsiz bir abonelik oluşturduysanız, açıkça bir Azure Cosmos hesabı oluşturmanız gerekir. Aşağıdaki kod, oturum tutarlılığı olan bir Azure Cosmos hesabı oluşturacaktır. Hesap `South Central US` ve `North Central US`çoğaltılır.  

Azure Cosmos hesabını oluşturmak için Azure Cloud Shell kullanabilirsiniz. Azure Cloud Shell, Azure kaynaklarını yönetmeye yönelik etkileşimli, kimliği doğrulanmış, tarayıcıda erişilebilen bir kabuktur. Bu, bash veya PowerShell gibi çalıştığınız yönteme en uygun kabuk deneyimini seçme esnekliğini sağlar. Bu hızlı başlangıçta **Bash** modu ' nu seçin. Azure Cloud Shell Ayrıca bir depolama hesabı gerektirir, istendiğinde bir tane oluşturabilirsiniz.

Aşağıdaki kodun yanındaki **deneyin** düğmesini seçin, **Bash** modu ' nu seçin, **depolama hesabı oluştur** ' u seçin ve Cloud Shell için oturum açın. Ardından aşağıdaki kodu kopyalayın ve Azure Cloud Shell 'e yapıştırın ve çalıştırın. Azure Cosmos hesap adı genel olarak benzersiz olmalıdır, komutu çalıştırmadan önce `mysqlapicosmosdb` değerini güncelleştirdiğinizden emin olun.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Azure Cosmos hesabını oluşturma işlemi biraz zaman alır, işlem başarılı olduktan sonra onay çıkışını görebilirsiniz. Komut başarıyla tamamlandıktan sonra, [Azure Portal](https://portal.azure.com/) oturum açın ve belirtilen ada sahip Azure Cosmos hesabının mevcut olduğundan emin olun. Kaynak oluşturulduktan sonra Azure Cloud Shell penceresini kapatabilirsiniz. 

### <a id="create-dotnet-core-app"></a>Yeni bir .NET uygulaması oluşturun

Tercih ettiğiniz düzenleyicide veya IDE 'de yeni bir .NET uygulaması oluşturun. Yerel bilgisayarınızdan Windows komut istemi veya bir Terminal penceresi açın. Komut istemi veya terminalden sonraki bölümlerde bulunan tüm komutları çalıştıracaksınız.  `todo`adlı yeni bir uygulama oluşturmak için aşağıdaki DotNet New komutunu çalıştırın. --LangVersion parametresi, oluşturulan proje dosyasındaki LangVersion özelliğini ayarlar.

```console
dotnet new console --langVersion 7.1 -n todo
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. Uygulamayı ile oluşturabilirsiniz:

```console
cd todo
dotnet build
```

Derlemeden beklenen çıktı şuna benzemelidir:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Azure Cosmos DB paketini yükler

Hala uygulama dizininde, DotNet Add Package komutunu kullanarak .NET Core için Azure Cosmos DB istemci kitaplığını yükleyebilirsiniz.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Azure Cosmos hesabı kimlik bilgilerinizi Azure portal kopyalayın

Örnek uygulamanın Azure Cosmos hesabınızda kimlik doğrulaması yapması gerekir. Kimlik doğrulaması yapmak için Azure Cosmos hesabı kimlik bilgilerini uygulamaya geçirmeniz gerekir. Aşağıdaki adımları izleyerek Azure Cosmos hesabı kimlik bilgilerinizi alın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Azure Cosmos hesabınıza gidin.

1. **Anahtarlar** bölmesini açın ve hesabınızın **URI** ve **birincil anahtarını** kopyalayın. Bir sonraki adımda bir ortam değişkenine URI ve anahtar değerleri ekleyeceksiniz.

### <a name="set-the-environment-variables"></a>Ortam değişkenlerini ayarlama

Hesabınızın **URI** 'Sini ve **birincil anahtarını** kopyaladıktan sonra, uygulamayı çalıştıran yerel makinede yeni bir ortam değişkenine kaydedin. Ortam değişkenini ayarlamak için bir konsol penceresi açın ve aşağıdaki komutu çalıştırın. `<Your_Azure_Cosmos_account_URI>` ve `<Your_Azure_Cosmos_account_PRIMARY_KEY>` değerlerini değiştirdiğinizden emin olun.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**MacOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a id="object-model"></a>Nesne modeli

Uygulamayı oluşturmaya başlamadan önce, Azure Cosmos DB içindeki kaynak hiyerarşisine ve bu kaynakları oluşturmak ve bunlara erişmek için kullanılan nesne modeline göz atalım. Azure Cosmos DB, kaynakları aşağıdaki sırayla oluşturur:

* Azure Cosmos hesabı 
* Veritabanları 
* Kapsayıcılar 
* Öğeler

Farklı varlıkların hiyerarşisi hakkında daha fazla bilgi edinmek için, [Azure Cosmos DB makalesinde veritabanları, kapsayıcılar ve öğelerle çalışma](databases-containers-items.md) makalesine bakın. Şu kaynaklarla etkileşim kurmak için aşağıdaki .NET sınıflarını kullanacaksınız:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) -bu sınıf Azure Cosmos DB hizmeti için istemci tarafı mantıksal temsili sağlar. İstemci nesnesi, hizmete yönelik istekleri yapılandırmak ve yürütmek için kullanılır.

* [Createdatabaseifnotexistsasync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) -bu Yöntem (yoksa) oluşturur veya bir veritabanı kaynağını zaman uyumsuz bir işlem olarak alır (varsa). 

* [Createcontainerıfnotexistsasync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)--bu Yöntem (yoksa) oluşturur veya bir kapsayıcıyı zaman uyumsuz bir işlem olarak (zaten varsa) alır. Kapsayıcının yeni oluşturulup oluşturulmayacağını (201) veya var olan bir kapsayıcının döndürülüp döndürülmediğini (200) öğrenmek için yanıttan durum kodunu kontrol edebilirsiniz. 
* [Createitemasync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) -bu yöntem kapsayıcı içinde bir öğe oluşturur. 

* [Upsertitemasync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) -bu yöntem, zaten yoksa kapsayıcı içinde bir öğe oluşturur veya zaten varsa öğeyi değiştirir. 

* [Getıtemqueryyineleyici](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) -bu yöntem, parametreli değerler IÇEREN bir SQL ifadesini kullanarak bir Azure Cosmos veritabanındaki kapsayıcı altındaki öğeler için bir sorgu oluşturur. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) -belirtilen veritabanını Azure Cosmos hesabınızdan siler. `DeleteAsync` Yöntem yalnızca veritabanını siler. `Cosmosclient` örneği elden atılırken ayrı olarak gerçekleşmelidir (DeleteDatabaseAndCleanupAsync yönteminde olduğu gibi). 

 ## <a id="code-examples"></a>Kod örnekleri

Bu makalede açıklanan örnek kod, Azure Cosmos DB bir aile veritabanı oluşturur. Aile veritabanı ad, adres, konum, ilişkili üst öğeler, alt öğeler ve Evcil hayvan gibi aile ayrıntılarını içerir. Verileri Azure Cosmos hesabınıza doldurmadan önce, bir aile öğesinin özelliklerini tanımlayın. Örnek uygulamanızın kök düzeyinde `Family.cs` adlı yeni bir sınıf oluşturun ve buna aşağıdaki kodu ekleyin:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>İstemci nesnesini tanımlamak & using yönergelerini ekleyin

Proje dizininden `Program.cs` dosyasını Düzenleyicinizde açın ve uygulamanızın en üstüne aşağıdaki kullanım yönergelerini ekleyin:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

**Program.cs** dosyasına, önceki adımda ayarlamış olduğunuz ortam değişkenlerini okumak için kod ekleyin. `CosmosClient`, `Database`ve `Container` nesnelerini tanımlayın. Daha sonra, Azure Cosmos hesap kaynaklarını yönettiğiniz `GetStartedDemoAsync` yöntemi çağıran Main yöntemine kod ekleyin. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Veritabanı oluşturma 

`program.cs` sınıfında `CreateDatabaseAsync` yöntemini tanımlayın. Bu yöntem, zaten yoksa `FamilyDatabase` oluşturur.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Bir kapsayıcı oluşturma

`program.cs` sınıfında `CreateContainerAsync` yöntemini tanımlayın. Bu yöntem, zaten yoksa `FamilyContainer` oluşturur. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Öğe oluşturma

Aşağıdaki kodla `AddItemsToContainerAsync` yöntemini ekleyerek bir aile öğesi oluşturun. `CreateItemAsync` veya `UpsertItemAsync` yöntemlerini kullanarak bir öğe oluşturabilirsiniz:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

try
{
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}
}

```

### <a name="query-the-items"></a>Öğeleri sorgulama

Bir öğeyi ekledikten sonra, "Andersen" ailesinin ayrıntılarını almak için bir sorgu çalıştırabilirsiniz. Aşağıdaki kod, SQL sorgusunu doğrudan kullanarak sorgunun nasıl yürütüleceğini gösterir. "Anderson" Aile ayrıntılarını almak için SQL sorgusu: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. `program.cs` sınıfında `QueryItemsAsync` yöntemi tanımlayın ve buna aşağıdaki kodu ekleyin:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Veritabanını silme 

Son olarak, aşağıdaki kodla `DeleteDatabaseAndCleanupAsync` yöntemini ekleyerek veritabanını silebilirsiniz:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>CRUD işlemlerini yürütün

Tüm gerekli yöntemleri tanımladıktan sonra, `GetStartedDemoAsync` yönteminde ile yürütün. `DeleteDatabaseAndCleanupAsync` yöntemi bu kodda açıklama yazdı çünkü bu yöntem yürütülürse hiçbir kaynak görmezsiniz. Azure Cosmos DB kaynaklarınızın Azure portal oluşturulduğunu doğruladıktan sonra açıklamayı kaldırın. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Gerekli tüm yöntemleri ekledikten sonra `Program.cs` dosyasını kaydedin. 

## <a name="run-the-code"></a>Kodu çalıştırma

Sonra Azure Cosmos DB kaynaklarını oluşturmak için uygulamayı derleyin ve çalıştırın. Yeni bir komut istemi penceresi seçtiğinizden emin olun, ortam değişkenlerini ayarlamak için kullandığınız örneği kullanmayın. Ortam değişkenleri geçerli açık pencerede ayarlanmadığından. Güncelleştirmeleri görmek için yeni bir komut istemi açmanız gerekir. 

```console
dotnet build
```

```console
dotnet run
```

Uygulamayı çalıştırdığınızda aşağıdaki çıktı oluşturulur. Ayrıca Azure portal oturum açabilir ve kaynakların oluşturulduğunu doğrulayabilirsiniz:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Azure portal oturum açıp Azure Cosmos hesabınızda gerekli öğeleri görmek için verilerin oluşturulduğunu doğrulayabilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmayan Azure Cosmos hesabını ve ilgili kaynak grubunu kaldırmak için Azure CLı veya Azure PowerShell kullanabilirsiniz. Aşağıdaki komut, Azure CLı kullanılarak kaynak grubunun nasıl silineceğini göstermektedir:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure Cosmos hesabı oluşturmayı, bir veritabanı ve bir .NET Core uygulamasını kullanarak kapsayıcıyı oluşturmayı öğrendiniz. Artık aşağıdaki makaledeki yönergeler int ile Azure Cosmos hesabınıza ek veri aktarabilirsiniz. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB hesabınıza veri aktarma](import-data.md)

---
title: C# öğreticisi birden çok Azure veri kaynağını dizinleyen
titleSuffix: Azure Cognitive Search
description: Çoklu veri kaynaklarından verileri Dizin oluşturucular kullanarak tek bir Azure Bilişsel Arama dizinine aktarmayı öğrenin. Bu öğretici ve örnek kod C# ' de bulunur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: df7dcf9859b6942662ae447af836f59985e2d11a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509511"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Öğretici: .NET SDK kullanarak birden çok veri kaynağından Dizin

Azure Bilişsel Arama, birden çok veri kaynağından alınan verileri tek bir birleştirilmiş arama dizininde içeri aktarabilir, çözümleyebilir ve dizine alabilir. 

Bu öğretici, bir Azure Cosmos DB örnek otel verilerini indekslemek ve Azure Blob depolama belgelerinden alınan otel odası ayrıntıları ile birleştirmek üzere .NET için Azure SDK 'da C# ve [Azure.Search.Documstalar](/dotnet/api/overview/azure/search) istemci kitaplığını kullanır. Sonuç, bir karmaşık veri türleri olarak odalar içeren otel belgelerini içeren bir birleştirilmiş otel arama dizini olacaktır.

Bu öğreticide, aşağıdaki görevleri gerçekleştirirsiniz:

> [!div class="checklist"]
> * Örnek verileri karşıya yükleme ve veri kaynaklarını oluşturma
> * Belge anahtarını tanımla
> * Dizini tanımlama ve oluşturma
> * Azure Cosmos DB otel verilerinin dizinini oluştur
> * BLOB depolama alanından otel odası verilerini birleştirme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="overview"></a>Genel Bakış

Bu öğretici, birden çok Dizin Oluşturucu oluşturmak ve çalıştırmak için yeni istemci kitaplığı [Azure.Search.Documstalar](/dotnet/api/overview/azure/search), sürüm 11. x kullanır. Bu öğreticide, tek bir arama dizinini doldurmak üzere iki Azure veri kaynağı ayarlayacaksınız. Birleştirmeyi desteklemek için iki veri kümesi ortak bir değere sahip olmalıdır. Bu örnekte, bu alan bir KIMLIĞIDIR. Eşlemeyi desteklemek için ortak bir alan olduğu sürece, bir Dizin Oluşturucu farklı kaynaklardan verileri birleştirebilir: Azure SQL 'den yapılandırılmış veriler, blob depolamadan yapılandırılmamış veriler veya Azure 'da [desteklenen veri kaynaklarının](search-indexer-overview.md#supported-data-sources) herhangi bir birleşimi.

Bu öğreticideki kodun tamamlanmış bir sürümü aşağıdaki projede bulunabilir:

* [çoklu veri kaynakları/v11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Bu öğretici, Azure.Search.Documstalar (sürüm 11) paketini kullanacak şekilde güncelleştirilmiştir. .NET SDK 'sının önceki bir sürümü için GitHub 'da [Microsoft. Azure. Search (sürüm 10) kod örneğine](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10) bakın.

## <a name="prerequisites"></a>Önkoşullar

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Depolama](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Azure Bilişsel Arama (sürüm 11. x) NuGet paketi](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Mevcut bir arama hizmeti](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [oluşturun](search-create-service-portal.md) veya bulun 

> [!Note]
> Bu öğretici için ücretsiz hizmeti kullanabilirsiniz. Ücretsiz arama hizmeti, sizi üç Dizin, üç Dizin Oluşturucu ve üç veri kaynağı ile sınırlandırır. Bu öğreticide hepsinden birer tane oluşturulur. Başlamadan önce, hizmetinize yeni kaynakları kabul etmek için yeriniz olduğundan emin olun.

## <a name="1---create-services"></a>1-hizmet oluşturma

Bu öğretici, dizin oluşturma ve sorgular için Azure Bilişsel Arama, bir veri kümesi için Azure Cosmos DB ve ikinci veri kümesi için Azure Blob depolama kullanır. 

Mümkünse, yakınlık ve yönetilebilirlik için aynı bölgedeki ve kaynak grubundaki tüm hizmetleri oluşturun. Uygulamada, hizmetleriniz herhangi bir bölgede olabilir.

Bu örnek, yedi kurgusal oteli tanımlayan iki küçük veri kümesini kullanır. Bir küme, otelleri tanımlar ve bir Azure Cosmos DB veritabanına yüklenir. Diğer küme, otel odası ayrıntılarını içerir ve Azure Blob depolamaya yüklenecek yedi ayrı JSON dosyası olarak sağlanır.

### <a name="start-with-cosmos-db"></a>Cosmos DB kullanmaya başlayın

1. [Azure Portal](https://portal.azure.com)oturum açın ve Azure Cosmos DB hesabınıza genel bakış sayfasına gidin.

1. **Veri Gezgini** ' yi seçin ve ardından **Yeni veritabanı**' nı seçin.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Yeni veritabanı oluşturma" border="false":::

1. **Otel-odalar-DB** adını girin. Geri kalan ayarlar için varsayılan değerleri kabul edin.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Veritabanını yapılandır" border="false":::

1. Yeni bir kapsayıcı oluşturun. Yeni oluşturduğunuz mevcut veritabanını kullanın. Kapsayıcı adı için **oteller** girin ve bölüm anahtarı için **/Hotelıd** kullanın.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Kapsayıcı ekleme" border="false":::

1. **Oteller** altındaki **öğeleri** seçin ve ardından komut çubuğunda **öğeyi karşıya yükle** ' ye tıklayın. Öğesine gidin ve proje klasöründe **cosmosdb/HotelsDataSubset_CosmosDb.js** dosyasını seçin.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Azure Cosmos DB koleksiyonuna yükle" border="false":::

1. Oteller koleksiyonundaki öğelerin görünümünü yenilemek için Yenile düğmesini kullanın. Yedi yeni veritabanı belgesi listelendiğini görmeniz gerekir.

1. **Anahtarlar** sayfasından bir bağlantı dizesini Not defteri 'ne kopyalayın. Daha sonraki bir adımda **appsettings.js** için buna ihtiyacınız olacaktır. Önerilen "Otel-Odalar-DB" veritabanı adını kullanmıyorsanız, veritabanı adını da kopyalayın.

### <a name="azure-blob-storage"></a>Azure Blob depolama

1. [Azure Portal](https://portal.azure.com)oturum açın, Azure depolama hesabınıza gidin, **Bloblar**' a tıklayın ve ardından **+ Container**' a tıklayın.

1. Örnek otel odası JSON dosyalarını depolamak için **otel odaları** adlı [bir blob kapsayıcısı oluşturun](../storage/blobs/storage-quickstart-blobs-portal.md) . Ortak erişim düzeyini geçerli değerlerinden herhangi birine ayarlayabilirsiniz.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Blob kapsayıcısı oluşturma" border="false":::

1. Kapsayıcı oluşturulduktan sonra açın ve komut çubuğunda **karşıya yükle** ' yi seçin. Örnek dosyaları içeren klasöre gidin. Tümünü seçip **karşıya yükle**' ye tıklayın.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Dosyaları karşıya yükleme" border="false":::

1. Depolama hesabı adını ve bir bağlantı dizesini **erişim tuşları** sayfasından Not defteri 'ne kopyalayın. Sonraki adımda **appsettings.js** için her iki değere de ihtiyaç duyarsınız.

### <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

Üçüncü bileşen, [portalda oluşturabileceğiniz](search-create-service-portal.md)Azure bilişsel arama. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Bilişsel Arama için bir yönetici API-anahtarı ve URL 'SI kopyalama

Arama hizmetinize kimlik doğrulaması yapmak için, hizmet URL 'SI ve erişim anahtarı gerekir.

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Hizmet adı ve yönetici ve sorgu anahtarlarını alın" border="false":::

İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="2---set-up-your-environment"></a>2-ortamınızı ayarlama

1. Visual Studio 'Yu başlatın ve **Araçlar** menüsünde **NuGet Paket Yöneticisi** ' ni ve ardından **çözüm için NuGet Paketlerini Yönet...** seçeneğini belirleyin. 

1. **Araştır** sekmesinde **Azure.Search.Documtları** (sürüm 11,0 veya üzeri) bulun ve ardından bu Yüklemeyi tamamlaması için ek iletişim kutularına tıklamacaksınız.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Azure kitaplıklarını eklemek için NuGet kullanma" border="false":::

1. NuGet paketlerinde **Microsoft.Extensions.Configurlama** ve **Microsoft.Extensions.Configuration.Js** arayın ve bunları da birlikte yüklersiniz.

1. Çözüm dosyasını **/v11/AzureSearchMultipleDataSources.sln** açın.

1. Çözüm Gezgini, bağlantı bilgilerini eklemek için dosya **appsettings.js** düzenleyin.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

İlk iki giriş, bir arama hizmetinin URL ve yönetici anahtarlarıdır. Tam bitiş noktasını kullanın, örneğin: `https://mydemo.search.windows.net` .

Sonraki girişler, Azure Blob depolama ve Azure Cosmos DB veri kaynakları için hesap adlarını ve bağlantı dizesi bilgilerini belirtir.

## <a name="3---map-key-fields"></a>3-anahtar alanlarını eşleme

İçerik birleştirme, her iki veri akışının de arama dizininde aynı belgeleri hedeflemesini gerektirir. 

Azure Bilişsel Arama 'de, anahtar alanı her belgeyi benzersiz bir şekilde tanımlar. Her arama dizininin türünde tam olarak bir anahtar alanı olmalıdır `Edm.String` . Bu anahtar alanı, dizine eklenen bir veri kaynağındaki her belge için mevcut olmalıdır. (Aslında, tek gerekli alandır.)

Birden çok veri kaynağından veri dizinlerken, her gelen satır veya belgenin, iki fiziksel farklı kaynak belgesinden verileri Birleşik dizindeki yeni bir arama belgesiyle birleştirmek için ortak bir belge anahtarı içerdiğinden emin olun. 

Genellikle dizininiz için anlamlı bir belge anahtarı belirlemek için bazı önde bir planlama gerektirir ve her iki veri kaynağında de bulunduğundan emin olun. Bu gösteride, `HotelId` Cosmos DB içindeki her bir otelin anahtarı blob depolamada yer aldığı odalar JSON Bloblarında de bulunur.

Azure Bilişsel Arama Dizinleyicileri, dizin oluşturma işlemi sırasında veri alanlarını yeniden adlandırmak ve hatta yeniden biçimlendirmek için alan eşlemelerini kullanarak kaynak verilerin doğru Dizin alanına yönlendirilebilmesi için kullanılabilir. Örneğin, Cosmos DB, otel tanımlayıcısı çağırılır **`HotelId`** . Ancak, otel odalarının JSON blob dosyalarında, otel tanımlayıcısı olarak adlandırılır **`Id`** . Program bunu, **`Id`** alanı Blobların **`HotelId`** Dizin oluşturucudaki anahtar alanı ile eşleyerek işler.

> [!NOTE]
> Çoğu durumda, bazı Dizin oluşturucular tarafından varsayılan olarak oluşturulanlar gibi otomatik olarak oluşturulan belge anahtarları, birleştirilmiş dizinler için iyi belge anahtarları oluşturmazlar. Genel olarak, içinde zaten bulunan veya veri kaynaklarınıza kolayca eklenebilen anlamlı, benzersiz bir anahtar değeri kullanmak isteyeceksiniz.

## <a name="4---explore-the-code"></a>4-kodu keşfet

Veriler ve yapılandırma ayarları olduktan sonra, **/v11/AzureSearchMultipleDataSources.sln** 'deki örnek program, derleme ve çalıştırmaya hazırlanmalıdır.

Bu basit C#/.NET konsol uygulaması aşağıdaki görevleri gerçekleştirir:

* C# otel sınıfının veri yapısına dayalı yeni bir dizin oluşturur (Ayrıca adres ve oda sınıflarına de başvurur).
* Yeni bir veri kaynağı ve dizin alanları Azure Cosmos DB verileri eşleyen bir dizin oluşturucu oluşturur. Bunlar her iki nesne de Azure Bilişsel Arama.
* Cosmos DB ' dan otel verilerini yüklemek için Dizin oluşturucuyu çalıştırır.
* İkinci bir veri kaynağı ve JSON blob verilerini Dizin alanlarıyla eşleyen bir dizin oluşturucu oluşturur.
* Blob depolamadan Oda verilerini yüklemek için ikinci dizin oluşturucuyu çalıştırır.

 Programı çalıştırmadan önce, bu örneğe ilişkin kodu ve dizin ve Dizin Oluşturucu tanımlarını incelemek için bir dakikanızı alın. İlgili kod iki dosyada yer alır:

  + **Otel. cs** , dizini tanımlayan şemayı içerir
  + **Program. cs** , Azure bilişsel arama dizinini, veri kaynaklarını ve Dizin oluşturucuyu oluşturan ve Birleşik sonuçları dizine yükleyen işlevleri içerir.

### <a name="create-an-index"></a>Dizin oluşturma

Bu örnek program, Azure Bilişsel Arama dizini tanımlamak ve oluşturmak için [Createındexasync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) kullanır. Bir C# veri modeli sınıfından dizin yapısı oluşturmak için [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) sınıfından yararlanır.

Veri modeli, Otel Sınıfı tarafından tanımlanır ve bu da adres ve oda sınıflarına başvurular içerir. FieldBuilder, dizin için karmaşık bir veri yapısı oluşturmak üzere birden fazla sınıf tanımı aracılığıyla ayrıntıya gider. Meta veri etiketleri, her alanın, aranabilir veya sıralanabilir olup olmadığı gibi özniteliklerini tanımlamak için kullanılır.

Program, bu örneği birden çok kez çalıştırmak istemeniz durumunda yenisini oluşturmadan önce aynı ada sahip mevcut herhangi bir dizini siler.

**Otel. cs** dosyasındaki aşağıdaki kod parçacıkları tek alanları ve ardından başka bir veri modeli sınıfına yönelik bir başvuruyu, yani oda **. cs** dosyasında (gösterilmez) tanımlanmış olan oda [] öğesini gösterir.

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

**Program. cs** dosyasında, bir [searchındex](/dotnet/api/azure.search.documents.indexes.models.searchindex) yöntemi tarafından oluşturulan bir ad ve alan koleksiyonuyla tanımlanır `FieldBuilder.Build` ve sonra aşağıdaki gibi oluşturulur:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB veri kaynağı ve Dizin Oluşturucu oluşturma

Ardından ana program, otel verileri için Azure Cosmos DB veri kaynağı oluşturma mantığını içerir.

İlk olarak, Azure Cosmos DB veritabanı adını bağlantı dizesine ekler. Sonra bir [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) nesnesi tanımlar.

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

Veri kaynağı oluşturulduktan sonra program, **otel-odalar-Cosmos-Indexer** adlı bir Azure Cosmos DB Dizin Oluşturucu ayarlar.

Program, Yukarıdaki kodun içeriğiyle mevcut dizin oluşturucunun üzerine yazarak aynı ada sahip mevcut dizin oluşturucularının güncelleştirilmesini sağlayacaktır. Ayrıca, bu örneği birden çok kez çalıştırmak istemeniz durumunda sıfırlama ve çalıştırma eylemlerini de içerir.

Aşağıdaki örnek, Dizin Oluşturucu için bir zamanlama tanımlar, böylece günde bir kez çalıştırılır. Dizin oluşturucunun daha sonra otomatik olarak yeniden çalışmasını istemiyorsanız bu çağrıdan Schedule özelliğini kaldırabilirsiniz.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Bu örnek, yürütme sırasında ortaya çıkabilecek hataları raporlamak için basit bir try-catch bloğu içerir.

Azure Cosmos DB Dizin Oluşturucu çalıştıktan sonra, arama dizini bir örnek otel belgeleri kümesi içerir. Ancak, Azure Cosmos DB veri kaynağı Oda ayrıntılarını atladığından, her otel için Odalar alanı boş bir dizi olacaktır. Daha sonra program, Oda verilerini yüklemek ve birleştirmek için BLOB depolama alanından çekmesini sağlar.

### <a name="create-blob-storage-data-source-and-indexer"></a>BLOB depolama veri kaynağı ve Dizin Oluşturucu oluştur

Oda ayrıntılarını almak için, program önce tek bir JSON blob dosyası kümesine başvurmak üzere bir BLOB depolama veri kaynağı ayarlar.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

Veri kaynağı oluşturulduktan sonra program, aşağıda gösterildiği gibi **otel-odalar-blob-Indexer** adlı bir blob Dizin Oluşturucu ayarlar.

JSON blob 'ları yerine adında bir anahtar alanı içerir **`Id`** **`HotelId`** . Kod, Dizin `FieldMapping` oluşturucudan **`Id`** alan değerini **`HotelId`** dizindeki belge anahtarına yönlendirecek şekilde söylemek için sınıfını kullanır.

BLOB depolama Dizin oluşturucular, ayrıştırma modunu belirtmek için [ındexingparameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) kullanabilir. Blobların tek bir belgeyi veya aynı blob içindeki birden çok belgeyi temsil ettiğini bağlı olarak farklı ayrıştırma modları ayarlamanız gerekir. Bu örnekte, her blob tek bir JSON belgesini temsil ettiğinden kod `json` ayrıştırma modunu kullanır. JSON Blobları için Dizin Oluşturucu ayrıştırma parametreleri hakkında daha fazla bilgi için bkz. [DIZIN JSON blob 'ları](search-howto-index-json-blobs.md).

Bu örnek, Dizin Oluşturucu için bir zamanlama tanımlar, böylece günde bir kez çalıştırılır. Dizin oluşturucunun daha sonra otomatik olarak yeniden çalışmasını istemiyorsanız bu çağrıdan Schedule özelliğini kaldırabilirsiniz.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
    Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

Dizin Azure Cosmos DB veritabanından otel verileriyle doldurulduğundan, blob Indexer dizindeki mevcut belgeleri güncelleştirir ve Oda ayrıntılarını ekler.

> [!NOTE]
> Her iki veri kaynağınız de aynı anahtar olmayan alanlara sahipseniz ve bu alanlardaki veriler eşleşmiyorsa, dizin, dizin oluşturucunun en son çalıştırıldığı değerleri içerir. Örneğimizde, her iki veri kaynağı da bir **Hotelname** alanı içerir. Bazı nedenlerle bu alandaki veriler farklıysa, aynı anahtar değerine sahip belgeler için, en son dizinlenen veri kaynağından gelen **barındırma adı** verileri dizinde depolanan değer olacaktır.

## <a name="5---search"></a>5-arama

Program çalıştırıldıktan sonra, portalda [**Arama Gezgini**](search-explorer.md) 'ni kullanarak doldurulmuş arama dizinini inceleyebilirsiniz.

Azure portal ' de, arama hizmeti **genel bakış** sayfasını açın ve **dizinler** listesinde **otel-odalar-Sample** dizinini bulun.

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Azure Bilişsel Arama dizinlerinin listesi" border="false":::

Listedeki otel-odalar-örnek dizinine tıklayın. Dizin için bir arama Gezgini arabirimi görürsünüz. "Merkezlerini" gibi bir terim için bir sorgu girin. Sonuçlarda en az bir belge görmeniz gerekir ve bu belge, Oda dizisindeki bir oda nesnelerinin bir listesini göstermelidir.

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

Geliştirmede erken deneysel aşamalarda, tasarım yinelemesi için en pratik yaklaşım, nesneleri Azure Bilişsel Arama silmek ve kodunuzun bunları yeniden oluşturması için izin verir. Kaynak adları benzersizdir. Bir nesneyi sildiğinizde, aynı adı kullanarak nesneyi yeniden oluşturabilirsiniz.

Örnek kod, mevcut nesneleri denetler ve programı yeniden çalıştırabilmeniz için onları siler veya güncelleştirir.

Ayrıca, dizinleri, Dizin oluşturucuyu ve veri kaynaklarını silmek için portalını de kullanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir projenin sonunda kendi aboneliğinizde çalışırken, artık ihtiyaç duyulmadığınızda kaynakları kaldırmak iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki tüm kaynaklar veya kaynak grupları bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık birden çok kaynaktan veri alma kavramı hakkında bilgi sahibi olduğunuza göre, Cosmos DB başlayarak Dizin Oluşturucu yapılandırmasına daha yakından bakalım.

> [!div class="nextstepaction"]
> [Azure Cosmos DB Dizin Oluşturucu yapılandırma](search-howto-index-cosmosdb.md)

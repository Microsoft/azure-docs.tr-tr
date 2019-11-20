---
title: 'Hızlı başlangıç: .NET C# kullanarak bir arama dizini oluşturma'
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama .NET SDK kullanarak C# Dizin oluşturmayı, verileri yüklemeyi ve sorguları çalıştırmayı açıklar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 45d24286a511ff60db33e149627932b0768c543b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112032"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Hızlı başlangıç: .NET SDK C# kullanarak Azure bilişsel arama dizini oluşturma
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Visual Studio ve C# [Azure bilişsel arama .NET SDK](https://aka.ms/search-sdk)kullanarak bir Azure bilişsel arama dizini oluşturan, yükleyen ve sorgulayan bir .NET Core konsol uygulaması oluşturun. Bu makalede, uygulamanın adım adım nasıl oluşturulacağı açıklanmaktadır. Alternatif olarak, [tüm uygulamayı indirebilir ve çalıştırabilirsiniz](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!NOTE]
> Bu makaledeki tanıtım kodu, basitlik için Azure Bilişsel Arama .NET SDK 'sının zaman uyumlu yöntemlerini kullanır. Ancak, üretim senaryolarında bunları ölçeklenebilir ve hızlı bir şekilde korumak için kendi uygulamalarınızda zaman uyumsuz yöntemleri kullanmanızı öneririz. Örneğin, `Create` ve `Delete`yerine `CreateAsync` ve `DeleteAsync` kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç için aşağıdaki hizmetler ve araçlar gereklidir.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), tüm sürüm. Örnek kod ve yönergeler ücretsiz topluluk sürümünde sınanmıştır.

+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

Hizmete yapılan çağrılar, her istekte bir URL uç noktası ve erişim anahtarı gerektirir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

2. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   Sorgu anahtarını da alın. Salt okuma erişimiyle sorgu istekleri vermek en iyi uygulamadır.

![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-postman/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

Visual Studio 'Yu açıp .NET Core üzerinde çalışabilen yeni bir konsol uygulama projesi oluşturarak başlayın.

### <a name="install-nuget-packages"></a>NuGet paketlerini yükleme

[Azure bilişsel arama .NET SDK](https://aka.ms/search-sdk) , NuGet paketleri olarak dağıtılan birkaç istemci kütüphanelerinin oluşur.

Bu proje için, `Microsoft.Azure.Search` NuGet paketinin 9. sürümünü ve en son `Microsoft.Extensions.Configuration.Json` NuGet paketini kullanın.

1. **NuGet Paket Yöneticisi** **Araçlar** > , **çözüm için NuGet Paketlerini Yönet...** seçeneğini belirleyin. 

1. **Gözat**’a tıklayın.

1. `Microsoft.Azure.Search` arayın ve sürüm 9.0.1 veya üzerini seçin.

1. Derlemeyi projenize ve çözümünüze eklemek için **sağdaki aç '** a tıklayın.

1. `Microsoft.Extensions.Configuration.Json`, sürüm 2.2.0 veya üstünü seçerek tekrarlayın.


### <a name="add-azure-cognitive-search-service-information"></a>Azure Bilişsel Arama hizmet bilgilerini ekleme

1. Çözüm Gezgini, projeye sağ tıklayın ve > **Yeni öğe Ekle...** seçeneğini belirleyin. 

1. Yeni öğe Ekle ' de, "JSON" sözcüğünü, JSON ile ilgili öğe türlerinin bir listesini döndürecek şekilde aratın.

1. **JSON dosyası**' nı seçin, "appSettings. JSON" dosyasını adlandırın ve **Ekle**' ye tıklayın. 

1. Dosyayı çıkış dizininize ekleyin. AppSettings. JSON öğesine sağ tıklayın ve **Özellikler**' i seçin. **Çıkış Dizinine Kopyala**' da, **daha yeniyse kopyala**' yı seçin.

1. Aşağıdaki JSON 'ı yeni JSON dosyanıza kopyalayın. Arama hizmeti adı 'nı (-SEARCH-SERVICE-NAME) ve yönetici API anahtarınızı (-ADMIN-API-KEY) geçerli değerlerle değiştirin. Hizmet uç noktanız `https://mydemo.search.windows.net`, hizmet adı "mydemo" olacaktır.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Sınıf Ekle ". Yöntem "projenize dosya

Sonuçlar konsol penceresine yazdırılırken, otel nesnesinden bağımsız alanlar dizeler olarak döndürülmelidir. Bu görevi gerçekleştirmek için [ToString ()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) öğesini uygulayabilir, gerekli kodu iki yeni dosyaya kopyalayabilirsiniz.

1. Projenize iki boş sınıf tanımı ekleyin: Address.Methods.cs, Hotel.Methods.cs

1. Address.Methods.cs ' de, varsayılan içeriğin üzerine aşağıdaki kodu, 1-32. [satırları](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32)yazın.

1. Hotel.Methods.cs ' de, [1-66 satırlarını](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66)kopyalayın.


## <a name="1---create-index"></a>1-Dizin oluşturma

Oteller dizini basit ve karmaşık alanlardan oluşur; burada basit bir alan "HotelName" veya "Description" ya da karmaşık alanlar alt alanlarla veya bir oda koleksiyonu olan bir adrestir. Bir dizin karmaşık türler içerdiğinde, karmaşık alan tanımlarını ayrı sınıflarda yalıtın.

1. Projenize iki boş sınıf tanımı ekleyin: Address.cs, Hotel.cs

1. Address.cs ' de, varsayılan içeriklerin üzerine aşağıdaki kodu yazın:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. Hotel.cs içinde sınıfı, adres sınıfına başvurular da dahil olmak üzere dizinin genel yapısını tanımlar.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Alanındaki öznitelikler bir uygulamada nasıl kullanıldığını belirleme. Örneğin, `IsSearchable` özniteliği tam metin aramasına dahil edilecek her alana atanmalıdır. 
    
    > [!NOTE]
    > .NET SDK 'sında, alanlar açıkça [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet), [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet), [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet)ve [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet)olarak adlandırılmalıdır. Bu davranış, veri türüne göre (örneğin, basit dize alanları otomatik olarak aranabilir), açıkça atısyonu sağlayan REST API farklıdır.

    `string` dizininizdeki tek bir alan, her belgeyi benzersiz bir şekilde tanımlamak için *anahtar* alanı olmalıdır. Bu şemada, anahtar `HotelId`.

    Bu dizinde, açıklama alanları varsayılan standart Lucene Çözümleyicisi 'ni geçersiz kılmak istediğinizde belirtilen isteğe bağlı [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) özelliğini kullanır. `description_fr` alanı Fransızca metin depoladığından Fransızca Lucene Çözümleyicisi 'ni ([frlucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) kullanıyor. `description`, isteğe bağlı Microsoft dil Çözümleyicisi 'ni ([enmicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)) kullanıyor.

1. Program.cs ' de, uygulamanın yapılandırma dosyasında (appSettings. JSON) depolanan değerleri kullanarak hizmete bağlanmak için [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) sınıfının bir örneğini oluşturun. 

   `SearchServiceClient`, Azure Bilişsel Arama dizinleri oluşturmak, listelemek, güncelleştirmek veya silmek için ihtiyaç duyduğunuz tüm yöntemleri sağlayan bir [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) özelliğine sahiptir. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Mümkünse, çok fazla bağlantı açmamak için uygulamanızdaki tek bir `SearchServiceClient` örneğini paylaşabilirsiniz. Sınıf yöntemleri, bu tür paylaşımı etkinleştirmek için iş parçacığı güvenlidir.

   Sınıfta birkaç Oluşturucu vardır. İstediğiniz oluşturucu, arama hizmeti adınızı ve `SearchCredentials` nesnesini parametre olarak alır. `SearchCredentials`, api anahtarınızı sarmalar.

    Dizin tanımında, `Field` nesnelerini oluşturmanın en kolay yolu, tür parametresi için bir model sınıfı geçirerek `FieldBuilder.BuildForType` yöntemini çağırmaktan biridir. Bir model sınıfında dizininizin alanlarıyla eşlenen özellikler mevcuttur. Bu eşleme, arama dizininizdeki belgeleri model sınıfınızın örneklerine bağlamanıza olanak tanır.

    > [!NOTE]
    > Bir model sınıfı kullanmayı planlamıyorsanız, `Field` nesnelerini doğrudan oluşturarak dizininizi tanımlayabilirsiniz. Oluşturucuya alan adını veri türü (veya dize alanları çözümleyicisi) ile birlikte sağlayabilirsiniz. `IsSearchable`, `IsFilterable`gibi diğer özellikleri de bir kaç kez adlandırmak için ayarlayabilirsiniz.
    >

1. Uygulamayı derlemek ve dizini oluşturmak için F5 tuşuna basın. 

    Proje başarıyla derlemeseniz, bir konsol penceresi açılır ve dizini silmek ve oluşturmak için ekrana durum iletileri yazıyor. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-belge yükleme

Azure Bilişsel Arama 'de belgeler, sorguların dizin oluşturma ve çıkışlara yönelik giriş olan veri yapılarıdır. Bir dış veri kaynağından elde edilen belge girişleri bir veritabanındaki satırlar, blob depolamada Bloblar veya diskteki JSON belgeleri olabilir. Bu örnekte, bir kısayol çekiyoruz ve JSON belgelerini kodun kendisinde dört otel için katıştırıyoruz. 

Belgeler karşıya yüklenirken bir [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) nesnesi kullanmanız gerekir. `IndexBatch`, her biri bir belge ve Azure 'a ([karşıya yükleme, birleştirme, silme ve mergeOrUpload](search-what-is-data-import.md#indexing-actions)) hangi eylemin gerçekleştirileceğini bilişsel arama söyleyen bir özellik içeren bir [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) nesneleri koleksiyonu içerir.

1. Program.cs içinde bir dizi belge ve Dizin eylemi oluşturun ve sonra diziyi `IndexBatch`geçirin. Aşağıdaki belgeler, otel ve adres sınıfları tarafından tanımlanan otel-hızlı başlangıç dizinine uygundur.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    `IndexBatch` nesnesini başlattığınızda, [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) nesnenizin [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) çağırarak dizine gönderebilirsiniz. `Documents`, dizininizdeki belgeleri eklemek, değiştirmek, silmek veya sorgulamak için yöntemler sağlayan bir `SearchIndexClient` özelliğidir.

    `try`/`catch` `Index` yöntemine yapılan çağrıyı kapsayan dizin oluşturma hatalarının, hizmetiniz ağır yük altında olduğunda meydana gelebileceği bir. Üretim kodunda, başarısız olan belgelerin dizinlenmesini erteleyebilir ve yeniden deneyebilir veya örnek olarak oturum açıp devam edebilir ya da uygulamanızın veri tutarlılığı gereksinimlerini karşılayan başka bir şekilde işleyebilir.

    2 saniyelik gecikme, zaman uyumsuz olan dizin oluşturma için dengelenir, böylece sorgular yürütülmeden önce tüm belgelerin dizini oluşturulabilir. Yalnızca gösteriler, testler ve örnek uygulamalarda yalnızca bir gecikmeden kodlama gereklidir.

1. Program.cs içinde, ana, "2-yükleme belgeleri" için satırların açıklamasını kaldırın. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Uygulamayı yeniden derlemek için F5 tuşuna basın. 

    Proje başarıyla oluþturulup, bir konsol penceresi açılır ve durum iletileri, bu kez belge yükleme hakkında bir iletiyle birlikte görüntülenir. Azure portal, arama hizmeti **genel bakış** sayfasında, otel-hızlı başlangıç dizininde artık 4 belge olmalıdır.

Belge işleme hakkında daha fazla bilgi için, [".NET SDK belgeleri nasıl Işler?](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)" başlığına bakın.

## <a name="3---search-an-index"></a>3 - Dizin arama

İlk belge dizine eklendiğinde sorgu sonuçları elde edebilirsiniz, ancak dizininizin gerçek testi tüm belgelerin dizinlenene kadar beklemeniz gerekir. 

Bu bölüm iki işlev parçasını ekler: Sorgu mantığı ve sonuçları. Sorgular için [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) yöntemini kullanın. Bu yöntem, arama metninin yanı sıra diğer [parametreleri](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet)de alır. 

[`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) sınıfı sonuçları temsil eder.


1. Program.cs içinde, arama sonuçlarını konsola yazdıran bir WriteDocuments yöntemi oluşturun.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Sorguları yürütmek ve sonuçları döndürmek için bir RunQueries yöntemi oluşturun. Sonuçlar otel nesneleridir. Belirli alanları tek tek yüzey için Select parametresini kullanabilirsiniz. Select parametresinde bir alan yoksa, karşılık gelen otel özelliği null olur.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    [Bir sorgudaki terimlerle eşleşen iki yol](search-query-overview.md#types-of-queries)vardır: tam metin araması ve filtreler. Tam metin arama sorgusu, dizininizdeki `IsSearchable` alanlarında bir veya daha fazla terimi arar. Filtre, bir dizindeki `IsFilterable` alanları üzerinde değerlendirilen Boolean bir ifadedir. Tam metin arama ve filtreleri birlikte veya ayrı olarak kullanabilirsiniz.

    Arama ve filtrelerin her ikisi de `Documents.Search` yöntemi kullanılarak gerçekleştirilir. Bir arama sorgusu `searchText` parametresinde geçirilebilirken bir filtre ifadesi `Filter` sınıfının `SearchParameters` özelliğinden geçirilebilir. Arama yapmadan filtrelemek üzere `"*"` parametresi için `searchText` geçirmeniz yeterlidir. Filtrelemeden arama yapmak için `Filter` özelliğini ayarlamadan bırakmanız veya bir `SearchParameters` örneği geçirmemeniz yeterlidir.

1. Program.cs ' de, ana, "3-arama" satırının açıklamasını kaldırın. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Çözüm artık tamamlanmıştır. Uygulamayı yeniden derlemek ve programı tamamen çalıştırmak için F5 tuşuna basın. 

    Çıktı, sorgu bilgilerinin ve sonuçların eklenmesiyle birlikte, daha önceki ile aynı iletileri içerir.

## <a name="clean-up"></a>Temizleme

Kendi aboneliğinizde çalışırken, sizin oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek için bir projenin sonunda iyi bir fikir olur. Çalışan kaynaklar sizin için ücret verebilir. Kaynakları tek tek silebilir veya kaynak grubunu silerek tüm kaynak kümesini silebilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu C# hızlı başlangıçta, bir dizin oluşturmak, belgeler ile yüklemek ve sorguları çalıştırmak için bir dizi görev üzerinden çalıştık. Farklı aşamalarda, okunabilirliği ve kavrama için kodu basitleştirecek kısayollar sunuyoruz. Temel kavramlara rahat bir şekilde yararlandıysanız, bilginizi borçlandırmaya yönelik alternatif yaklaşımların ve kavramların araştırmasını sağlayan bir sonraki makaleye önerilir. 

Örnek kod ve Dizin bunun genişletilmiş sürümleridir. Sonraki örnek, bir oda koleksiyonu ekler, farklı sınıflar ve eylemler kullanır ve işleme nasıl çalıştığına daha yakından bakar.

> [!div class="nextstepaction"]
> [.NET ' te geliştirme](search-howto-dotnet-sdk.md)
---
title: Eski C# hızlı başlangıç
titleSuffix: Azure Cognitive Search
description: Bu C# hızlı başlangıcı, bir arama dizini oluşturmak, yüklemek ve sorgulamak için sürüm 10 istemci kitaplığını (Microsoft. Azure. Search) kullanır.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8dc2eb898c12e374bc503c5a05f00eb20667443b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94701849"
---
# <a name="quickstart-create-a-search-index-using-the-legacy-microsoftazuresearch-v10-client-library"></a>Hızlı başlangıç: eski Microsoft. Azure. Search ile v10 arasındaki istemci kitaplığını kullanarak bir arama dizini oluşturma

Bu makale, eski [**Microsoft. Azure. Search**](/dotnet/api/overview/azure/search/client10) (sürüm 10) istemci kitaplığı için artık [**Azure.Search.Documstalar**](/dotnet/api/overview/azure/search.documents-readme) (sürüm 11) istemci kitaplığı tarafından yerine geçen C# hızlı başlangıcının yerini almıştır.

> [!NOTE]
> Mevcut veya daha açık geliştirme projeleriniz varsa sürüm 10 ' u kullanmaya devam edebilirsiniz. Ancak yeni projeler için veya yeni özellikleri kullanmak için [Yeni kitaplığa](/dotnet/api/overview/azure/search.documents-readme)geçiş yapmanız gerekir.

## <a name="about-this-quickstart"></a>Bu hızlı başlangıç hakkında

C# ' de Visual Studio ve [Microsoft. Azure. Search istemci kitaplıklarını](/dotnet/api/overview/azure/search/client10)kullanarak bir Azure bilişsel arama dizini oluşturan, yükleyen ve sorgulayan bir .NET Core konsol uygulaması oluşturun. 

Bu makalede, uygulamasının nasıl oluşturulacağı açıklanmaktadır. Ayrıca [tüm uygulamayı indirebilir ve çalıştırabilirsiniz](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v10).

> [!NOTE]
> Bu makaledeki tanıtım kodu, basitlik için Azure Bilişsel Arama sürüm 10 .NET SDK 'sının zaman uyumlu yöntemlerini kullanır. Ancak, üretim senaryolarında bunları ölçeklenebilir ve hızlı bir şekilde korumak için kendi uygulamalarınızda zaman uyumsuz yöntemleri kullanmanızı öneririz. Örneğin, ve `CreateAsync` yerine ve kullanabilirsiniz `DeleteAsync` `Create` `Delete` .

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakilere sahip olmanız gerekir:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

+ Bir Azure Bilişsel Arama hizmeti. Geçerli aboneliğiniz kapsamında [bir hizmet oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), tüm sürüm. Örnek kod ve yönergeler ücretsiz topluluk sürümünde sınanmıştır.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

Hizmete yapılan çağrılar, her istekte bir URL uç noktası ve erişim anahtarı gerektirir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

2. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   Sorgu anahtarını da alın. Salt okuma erişimiyle sorgu istekleri vermek en iyi uygulamadır.

![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-rest/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

Visual Studio 'Yu açıp .NET Core üzerinde çalışabilen yeni bir konsol uygulama projesi oluşturarak başlayın.

### <a name="install-nuget-packages"></a>NuGet paketlerini yükleme

[Microsoft. Azure. Search paketi](https://www.nuget.org/packages/Microsoft.Azure.Search/) , NuGet paketleri olarak dağıtılan birkaç istemci kütüphanelerinin oluşur.

Bu proje için NuGet paketinin sürüm 10 ' u `Microsoft.Azure.Search` ve en son `Microsoft.Extensions.Configuration.Json` NuGet paketini kullanın.

1. **Araçlar**  >  **NuGet Paket Yöneticisi**' nde **çözüm için NuGet Paketlerini Yönet...** seçeneğini belirleyin. 

1. **Gözat**’a tıklayın.

1. `Microsoft.Azure.Search`Sürüm 10 ' u arayın ve seçin.

1. Derlemeyi projenize ve çözümünüze eklemek için **sağdaki aç '** a tıklayın.

1. İçin tekrarlayın `Microsoft.Extensions.Configuration.Json` , sürüm 2.2.0 veya üzerini seçin.


### <a name="add-azure-cognitive-search-service-information"></a>Azure Bilişsel Arama hizmet bilgilerini ekleme

1. Çözüm Gezgini, projeye sağ tıklayıp   >  **Yeni öğe Ekle...** seçeneğini belirleyin. 

1. Yeni öğe Ekle ' de, "JSON" sözcüğünü, JSON ile ilgili öğe türlerinin bir listesini döndürecek şekilde aratın.

1. **JSON dosyası** seçin, "appsettings.jsüzerinde" dosyasını adlandırın ve **Ekle**' ye tıklayın. 

1. Dosyayı çıkış dizininize ekleyin. appsettings.jsüzerinde sağ tıklayın ve **Özellikler**' i seçin. **Çıkış Dizinine Kopyala**' da, **daha yeniyse kopyala**' yı seçin.

1. Aşağıdaki JSON 'ı yeni JSON dosyanıza kopyalayın. 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. Arama hizmeti adı 'nı (-SEARCH-SERVICE-NAME) ve yönetici API anahtarınızı (-ADMIN-API-KEY) geçerli değerlerle değiştirin. Hizmet uç noktanız ise `https://mydemo.search.windows.net` , hizmet adı " `mydemo` " olur.

### <a name="add-class-method-files-to-your-project"></a>Sınıf Ekle ". Yöntem "projenize dosya

Bu adım, konsolda anlamlı bir çıktı üretmek için gereklidir. Sonuçlar konsol penceresine yazdırılırken, otel nesnesinden bağımsız alanlar dizeler olarak döndürülmelidir. Bu adım, bu görevi gerçekleştirmek için [ToString ()](/dotnet/api/system.object.tostring) uygular, bu da gerekli kodu iki yeni dosyaya kopyalayarak bunu yapar.

1. Projenize iki boş sınıf tanımı ekleyin: Address. Methods. cs, otel. Methods. cs

1. Address. Methods. cs dosyasında aşağıdaki kod, [satırlar 1-25](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Address.Methods.cs#L1-L25)ile varsayılan içeriklerin üzerine yazın.

1. Otel. Methods. cs ' de, [satırları kopyalayın 1-68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Hotel.Methods.cs#L1-L68).

## <a name="1---create-index"></a>1-Dizin oluşturma

Oteller dizini basit ve karmaşık alanlardan oluşur; burada basit bir alan "HotelName" veya "Description" ya da karmaşık alanlar alt alanlarla veya bir oda koleksiyonu olan bir adrestir. Bir dizin karmaşık türler içerdiğinde, karmaşık alan tanımlarını ayrı sınıflarda yalıtın.

1. Projenize iki boş sınıf tanımı ekleyin: Address. cs, otel. cs

1. Address. cs dosyasında, varsayılan içeriklerin üzerine aşağıdaki kodu yazın:

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

1. Otel. cs ' de, sınıf, adres sınıfına başvurular da dahil olmak üzere dizinin genel yapısını tanımlar.

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

    Alanındaki öznitelikler bir uygulamada nasıl kullanıldığını belirleme. Örneğin, `IsSearchable` özniteliğin tam metin aramasına dahil olması gereken her alana atanması gerekir. 
    
    > [!NOTE]
    > .NET SDK 'sında, alanları,, ve olarak açıkça adlandırılmalıdır [`IsSearchable`](/dotnet/api/microsoft.azure.search.models.field.issearchable) [`IsFilterable`](/dotnet/api/microsoft.azure.search.models.field.isfilterable) [`IsSortable`](/dotnet/api/microsoft.azure.search.models.field.issortable) [`IsFacetable`](/dotnet/api/microsoft.azure.search.models.field.isfacetable) . Bu davranış, veri türüne göre (örneğin, basit dize alanları otomatik olarak aranabilir), açıkça atısyonu sağlayan REST API farklıdır.

    Türünde dizininizdeki tek bir alan `string` , her belgeyi benzersiz bir şekilde tanımlayan *anahtar* alanı olmalıdır. Bu şemada, anahtar `HotelId` .

    Bu dizinde, açıklama alanları [`analyzer`](/dotnet/api/microsoft.azure.search.models.field.analyzer) varsayılan standart Lucene Çözümleyicisi 'ni geçersiz kılmak istediğinizde belirtilen isteğe bağlı özelliği kullanır. `description_fr`Alan, Fransızca metin depoladığı Için Fransızca Lucene Çözümleyicisi ([frlucene](/dotnet/api/microsoft.azure.search.models.analyzername.frlucene)) kullanıyor. , `description` İsteğe bağlı Microsoft dil Çözümleyicisi 'ni ([enmicrosoft](/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft)) kullanıyor.

1. Program. cs ' de, [`SearchServiceClient`](/dotnet/api/microsoft.azure.search.searchserviceclient) uygulamanın yapılandırma dosyasında (appsettings.jsüzerinde) depolanan değerleri kullanarak hizmete bağlanmak için sınıfın bir örneğini oluşturun. 

   `SearchServiceClient` , [`Indexes`](/dotnet/api/microsoft.azure.search.searchserviceclient.indexes) Azure bilişsel arama dizinleri oluşturmak, listelemek, güncelleştirmek veya silmek için ihtiyaç duyduğunuz tüm yöntemleri sağlayan bir özelliğine sahiptir. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
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
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Mümkünse, `SearchServiceClient` çok fazla bağlantı açmamak için uygulamanızdaki tek bir örneğini paylaşabilirsiniz. Sınıf yöntemleri, bu tür paylaşımı etkinleştirmek için iş parçacığı güvenlidir.

   Sınıfta birkaç Oluşturucu vardır. İstediğiniz oluşturucu, arama hizmeti adınızı ve `SearchCredentials` nesnesini parametre olarak alır. `SearchCredentials`, api anahtarınızı sarmalar.

    Dizin tanımında, nesneleri oluşturmanın en kolay yolu `Field` `FieldBuilder.BuildForType` yöntemi çağırarak tür parametresi için bir model sınıfı geçirerek yöntemidir. Bir model sınıfında dizininizin alanlarıyla eşlenen özellikler mevcuttur. Bu eşleme, arama dizininizdeki belgeleri model sınıfınızın örneklerine bağlamanıza olanak tanır.

    > [!NOTE]
    > Bir model sınıfı kullanmayı planlamıyorsanız, `Field` nesnelerini doğrudan oluşturarak dizininizi tanımlayabilirsiniz. Oluşturucuya alan adını veri türü (veya dize alanları çözümleyicisi) ile birlikte sağlayabilirsiniz. Ayrıca, gibi diğer özellikleri `IsSearchable` `IsFilterable` bir ad vermek için de ayarlayabilirsiniz.
    >

1. Uygulamayı derlemek ve dizini oluşturmak için F5 tuşuna basın. 

    Proje başarıyla derlemeseniz, bir konsol penceresi açılır ve dizini silmek ve oluşturmak için ekrana durum iletileri yazıyor. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-belge yükleme

Azure Bilişsel Arama 'de belgeler, sorguların dizin oluşturma ve çıkışlara yönelik giriş olan veri yapılarıdır. Bir dış veri kaynağından elde edilen belge girişleri bir veritabanındaki satırlar, blob depolamada Bloblar veya diskteki JSON belgeleri olabilir. Bu örnekte, bir kısayol çekiyoruz ve JSON belgelerini kodun kendisinde dört otel için katıştırıyoruz. 

Belgeler karşıya yüklenirken bir nesne kullanmanız gerekir [`IndexBatch`](/dotnet/api/microsoft.azure.search.models.indexbatch) . `IndexBatch` [`IndexAction`](/dotnet/api/microsoft.azure.search.models.indexaction) , Her biri bir belge içeren bir nesne ve Azure bilişsel arama hangi eyleme ([karşıya yükleme, birleştirme, silme ve mergeorupload](search-what-is-data-import.md#indexing-actions)) söyleyen bir özelliği içerir.

1. Program. cs ' de, bir dizi belge ve Dizin eylemi oluşturun ve sonra diziyi öğesine geçirin `IndexBatch` . Aşağıdaki belgeler, otel ve adres sınıfları tarafından tanımlanan otel-hızlı başlangıç dizinine uygundur.

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

    `IndexBatch`Nesneyi başlattığınızda, nesneyi çağırarak onu dizine gönderebilirsiniz [`Documents.Index`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index) [`SearchIndexClient`](/dotnet/api/microsoft.azure.search.searchindexclient) . `Documents` , `SearchIndexClient` dizininizdeki belgeleri eklemek, değiştirmek, silmek veya sorgulamak için yöntemler sağlayan öğesinin bir özelliğidir.

    `try` / `catch` Yöntemine yapılan çağrı, `Index` hizmetiniz ağır yük altındaysa meydana gelebilen dizin oluşturma başarısızlıklarını yakalar. Üretim kodunda, başarısız olan belgelerin dizinlenmesini erteleyebilir ve yeniden deneyebilir veya örnek olarak oturum açıp devam edebilir ya da uygulamanızın veri tutarlılığı gereksinimlerini karşılayan başka bir şekilde işleyebilir.

    2 saniyelik gecikme, zaman uyumsuz olan dizin oluşturma için dengelenir, böylece sorgular yürütülmeden önce tüm belgelerin dizini oluşturulabilir. Yalnızca gösteriler, testler ve örnek uygulamalarda yalnızca bir gecikmeden kodlama gereklidir.

1. Program. cs ' de ana, "2-yükleme belgeleri" için satırların açıklamasını kaldırın. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Uygulamayı yeniden derlemek için F5 tuşuna basın. 

    Proje başarıyla oluþturulup, bir konsol penceresi açılır ve durum iletileri, bu kez belge yükleme hakkında bir iletiyle birlikte görüntülenir. Azure portal, arama hizmeti **genel bakış** sayfasında, otel-hızlı başlangıç dizininde artık 4 belge olmalıdır.

Belge işleme hakkında daha fazla bilgi için, [".NET SDK belgeleri nasıl Işler?](search-howto-dotnet-sdk-v10.md#how-dotnet-handles-documents)" başlığına bakın.

## <a name="3---search-an-index"></a>3 - Dizin arama

İlk belge dizine eklendiğinde sorgu sonuçları elde edebilirsiniz, ancak dizininizin gerçek testi tüm belgelerin dizinlenene kadar beklemeniz gerekir. 

Bu bölüm iki işlev parçasını ekler: Sorgu mantığı ve sonuçları. Sorgular için [`Search`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search) yöntemini kullanın. Bu yöntem, arama metninin yanı sıra diğer [parametreleri](/dotnet/api/microsoft.azure.search.models.searchparameters)de alır. 

[`DocumentsSearchResult`](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)Sınıfı sonuçları temsil eder.


1. Program. cs ' de, arama sonuçlarını konsola yazdıran bir WriteDocuments yöntemi oluşturun.

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

    [Bir sorgudaki terimlerle eşleşen iki yol](search-query-overview.md#types-of-queries)vardır: tam metin araması ve filtreler. Tam metin arama sorgusu, dizininizdeki alanlarda bir veya daha fazla terimi arar `IsSearchable` . Filtre, bir dizindeki alanlar üzerinde değerlendirilen bir Boolean ifadedir `IsFilterable` . Tam metin arama ve filtreleri birlikte veya ayrı olarak kullanabilirsiniz.

    Arama ve filtrelerin her ikisi de `Documents.Search` yöntemi kullanılarak gerçekleştirilir. Bir arama sorgusu `searchText` parametresinde geçirilebilirken bir filtre ifadesi `SearchParameters` sınıfının `Filter` özelliğinden geçirilebilir. Arama yapmadan filtrelemek üzere `searchText` parametresi için `"*"` geçirmeniz yeterlidir. Filtrelemeden arama yapmak için `Filter` özelliğini ayarlamadan bırakmanız veya bir `SearchParameters` örneği geçirmemeniz yeterlidir.

1. Program. cs ' de ana, "3-arama" satırının açıklamasını kaldırın. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Çözüm artık tamamlanmıştır. Uygulamayı yeniden derlemek ve programı tamamen çalıştırmak için F5 tuşuna basın. 

    Çıktı, sorgu bilgilerinin ve sonuçların eklenmesiyle birlikte, daha önceki ile aynı iletileri içerir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kendi aboneliğinizde çalışırken, projenin sonunda oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu C# hızlı başlangıçta, bir dizin oluşturmak, belgeler ile yüklemek ve sorguları çalıştırmak için bir dizi görev üzerinden çalıştık. Farklı aşamalarda, okunabilirliği ve kavrama için kodu basitleştirecek kısayollar sunuyoruz. Temel kavramlara rahat bir şekilde yararlandıysanız, bilginizi borçlandırmaya yönelik alternatif yaklaşımların ve kavramların araştırmasını sağlayan bir sonraki makaleye önerilir. 

Örnek kod ve Dizin bunun genişletilmiş sürümleridir. Sonraki örnek, bir oda koleksiyonu ekler, farklı sınıflar ve eylemler kullanır ve işleme nasıl çalıştığına daha yakından bakar.

> [!div class="nextstepaction"]
> [.NET ' te geliştirme](search-howto-dotnet-sdk.md)

Bulut harcamalarınızı iyileştirmek ve kaydetmek istiyor musunuz?

> [!div class="nextstepaction"]
> [Maliyet yönetimi ile maliyetleri çözümlemeye başlayın](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
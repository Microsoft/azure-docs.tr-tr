---
title: JSON Blobları üzerinde arama
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama blob Indexer 'ı kullanarak metin içeriği için Azure JSON bloblarını gezin. Dizin oluşturucular, Azure Blob depolama gibi seçili veri kaynakları için veri alımını otomatik hale getirir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 18f3ed9cb2ef0f700e33e8b643b5e7d167d656a5
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112735"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Azure Bilişsel Arama blob Dizin Oluşturucu kullanarak JSON bloblarını dizin oluşturma

Bu makalede, Azure Blob depolama alanındaki JSON belgelerinden yapılandırılmış içeriği ayıklamak üzere bir Azure Bilişsel Arama blob [dizin oluşturucunun](search-indexer-overview.md) nasıl yapılandırılacağı ve Azure bilişsel arama 'de aranabilir hale getirme gösterilmektedir. Bu iş akışı bir Azure Bilişsel Arama dizini oluşturur ve bunu JSON Bloblarından ayıklanan varolan metinle yükler. 

JSON içeriğini indekslemek için [Portal](#json-indexer-portal), [REST API 'leri](#json-indexer-rest)veya [.NET SDK 'sını](#json-indexer-dotnet) kullanabilirsiniz. Tüm yaklaşımlardan ortak, JSON belgelerinin bir Azure depolama hesabındaki blob kapsayıcısında yer aldığı bir kapsayıcıdır. JSON belgelerini diğer Azure olmayan platformlardan iletme hakkında yönergeler için bkz. [azure bilişsel arama 'Da veri alma](search-what-is-data-import.md).

Azure Blob depolamada JSON blob 'ları genellikle tek bir JSON belgesi (ayrıştırma modu `json`) veya bir JSON varlıkları koleksiyonu olur. Koleksiyonlar için, blob düzgün biçimlendirilmiş bir JSON öğeleri **dizisine** sahip olabilir (ayrıştırma modu `jsonArray`). Blob 'lar aynı zamanda bir yeni satır tarafından ayrılan birden çok bağımsız JSON varlıklarından da oluşabilir (ayrıştırma modu `jsonLines`). İstekteki **Parsingmode** parametresi, çıkış yapılarını belirler.

> [!NOTE]
> Tek bir Blobun birden çok arama belgesini dizine alma hakkında daha fazla bilgi için bkz. [bire çok dizin oluşturma](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Portalı kullanma

JSON belgelerini dizinlemek için en kolay yöntem [Azure Portal](https://portal.azure.com/)bir Sihirbazı kullanmaktır. Azure Blob kapsayıcısında meta verileri ayrıştırarak, [**verileri Içeri aktarma**](search-import-data-portal.md) Sihirbazı varsayılan bir dizin oluşturabilir, kaynak alanları hedef dizin alanlarıyla eşleyebilir ve dizini tek bir işlemde yükleyebilir. Kaynak verilerin boyutuna ve karmaşıklığına bağlı olarak, dakikalar içinde işlemsel bir tam metin arama dizinine sahip olabilirsiniz.

Aynı bölgede, tercihen Azure Bilişsel Arama ve Azure depolama için aynı Azure aboneliğini kullanmanızı öneririz.

### <a name="1---prepare-source-data"></a>1-kaynak verileri hazırlama

[Azure Portal oturum açın](https://portal.azure.com/) ve verilerinizi Içerecek [bir blob kapsayıcısı oluşturun](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) . Genel erişim düzeyi geçerli değerlerinden herhangi birine ayarlanabilir.

Veri **alma** Sihirbazı 'nda verilerinizi almak için depolama hesabı adı, kapsayıcı adı ve erişim anahtarı gerekir.

### <a name="2---start-import-data-wizard"></a>2-veri alma Sihirbazı 'nı başlatma

Arama hizmetinizin Genel Bakış sayfasında Sihirbazı komut çubuğundan [başlatabilirsiniz](search-import-data-portal.md) .

   ![Portalda verileri içeri aktar komutu](./media/search-import-data-portal/import-data-cmd2.png "Veri alma Sihirbazı 'nı başlatma")

### <a name="3---set-the-data-source"></a>3-veri kaynağını ayarlama

**Veri kaynağı** sayfasında, kaynak **Azure Blob depolama**olmalıdır ve aşağıdaki belirtimlerle:

+ **Ayıklanacak veriler** *içerik ve meta veri*olmalıdır. Bu seçeneğin belirlenmesi, sihirbazın bir dizin şemasını çıkarmasını ve içeri aktarma için alanları eşlemenizi sağlar.
   
+ **Ayrıştırma modu** *JSON*, *JSON dizisi* veya *JSON satırları*olarak ayarlanmalıdır. 

  *JSON* , her Blobun arama sonuçlarında bağımsız bir öğe olarak göstererek tek bir arama belgesi olarak ifade edin. 

  *JSON dizisi* , ıyı biçimlendirilmiş JSON verileri içeren bloblara yöneliktir; ıyı biçimlendirilmiş JSON bir nesne dizisine karşılık gelir veya bir nesne dizisi olan bir özelliğine sahiptir ve her öğenin tek başına, bağımsız arama belgesi olarak ifade olmasını istiyorsunuz. Blob 'lar karmaşıktır ve *JSON dizisi* ' ni seçmezseniz blob 'un tamamı tek bir belge olarak alınır.

  *JSON satırları* , her varlığın tek başına bağımsız bir arama belgesi olarak ifade olmasını istediğiniz yeni bir satırla ayrılmış bırden çok JSON varlığında oluşan bloblara yöneliktir. Blob 'lar karmaşıktır ve *JSON satırları* ayrıştırma modunu seçmezseniz, tüm blob tek bir belge olarak alınır.
   
+ **Depolama kapsayıcısı** , depolama hesabınızı ve kapsayıcınızı ya da kapsayıcıya çözümlenen bir bağlantı dizesini belirtmelidir. Blob hizmeti portalı sayfasında bağlantı dizelerini alabilirsiniz.

   ![Blob veri kaynağı tanımı](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-sihirbazdaki "zenginleştirme içerik" sayfasını atlayın

Bilişsel yetenekler (veya zenginleştirme) eklemek bir içeri aktarma gereksinimi değildir. Dizin oluşturma işlem hattınızda [AI zenginleştirme eklemek](cognitive-search-concept-intro.md) için özel bir ihtiyacınız yoksa bu adımı atlamanız gerekir.

Adımı atlamak için, "Ileri" ve "atla" için sayfanın altındaki mavi düğmelere tıklayın.

### <a name="5---set-index-attributes"></a>5-dizin özniteliklerini ayarla

**Dizin** sayfasında, bir veri türü ve Dizin özniteliklerini ayarlamaya yönelik bir dizi onay kutusu içeren alanların listesini görmeniz gerekir. Sihirbaz meta verileri temel alan ve kaynak verileri örnekleyerek bir alanlar listesi oluşturabilir. 

Öznitelik sütununun en üstündeki onay kutusuna tıklayarak öznitelikleri toplu olarak seçebilirsiniz. Bir istemci uygulamasına döndürülmesi gereken her alan için **alınabilir** ve **aranabilir** ' i seçin ve tam metin arama işlemesine tabidir. Tamsayıların tam metin veya benzer aranabilir olduğunu fark edeceksiniz (sayılar tam olarak değerlendirilir ve genellikle filtrelerdeki yararlı olur).

Daha fazla bilgi için [dizin özniteliklerinin](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) ve [dil çözümleyicilerinin](https://docs.microsoft.com/rest/api/searchservice/language-support) açıklamasını gözden geçirin. 

Seçimlerinizi gözden geçirmek için bir dakikanızı ayırın. Sihirbazı çalıştırdığınızda fiziksel veri yapıları oluşturulur ve tüm nesneleri bırakıp yeniden oluşturmadan bu alanları düzenleyemezsiniz.

   ![Blob Dizin tanımı](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6-Dizin Oluşturucu oluştur

Tam olarak belirtilen sihirbaz, arama hizmetinizde üç ayrı nesne oluşturur. Veri kaynağı nesnesi ve dizin nesnesi, Azure Bilişsel Arama hizmetinize adlandırılmış kaynaklar olarak kaydedilir. Son adım bir Dizin Oluşturucu nesnesi oluşturur. Dizin oluşturucunun adlandırılması, aynı sihirbaz dizisinde oluşturulan dizin ve veri kaynağı nesnesinden bağımsız olarak zamanlayabilir ve yönetebileceğiniz tek başına bir kaynak olarak var olmasına izin verir.

Dizin oluşturucular hakkında bilginiz yoksa, *Dizin Oluşturucu* , aranabilir içerik için bir dış veri kaynağında gezinir ve Azure bilişsel arama bir kaynaktır. **Veri alma** Sihirbazı 'nın çıktısı, JSON veri kaynağınıza gezinir, aranabilir içeriği ayıklar ve Azure bilişsel arama üzerindeki bir dizine içeri aktarır.

   ![Blob Dizin Oluşturucu tanımı](media/search-howto-index-json/import-wizard-json-indexer.png)

Sihirbazı çalıştırmak ve tüm nesneleri oluşturmak için **Tamam** ' ı tıklatın. Dizin oluşturma anında yapılır.

Veri içeri aktarmayı Portal sayfalarında izleyebilirsiniz. İlerleme durumu bildirimleri dizin oluşturma durumunu ve kaç belge yüklendiğini gösterir. 

Dizin oluşturma işlemi tamamlandığında, dizini sorgulamak için [Arama Gezgini](search-explorer.md) ' ni kullanabilirsiniz.

> [!NOTE]
> Bekleyen verileri görmüyorsanız, daha fazla alan için daha fazla öznitelik ayarlamanız gerekebilir. Yeni oluşturduğunuz dizin ve Dizin oluşturucuyu silin ve sihirbazda adım adım 5 ' teki dizin öznitelikleri için seçimlerinizi değiştirerek yeniden ilerleyin. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API'lerini kullanma

Azure Bilişsel Arama tüm dizin oluşturucular için ortak olan üç bölümden oluşan bir iş akışını izleyerek JSON bloblarını indekslemek REST API kullanabilirsiniz: veri kaynağı oluşturma, dizin oluşturma, Dizin Oluşturucu oluşturma. Blob depolamadan veri ayıklama, Create Indexer isteği gönderdiğinizde oluşur. Bu istek bittikten sonra, sorgulanabilir bir dizininiz olur. 

Bu bölümün sonunda, her üç nesnenin de nasıl oluşturulacağını gösteren [REST örnek kodunu](#rest-example) gözden geçirebilirsiniz. Bu bölüm ayrıca [JSON ayrıştırma modları](#parsing-modes), [tek blob 'lar](#parsing-single-blobs), [JSON dizileri](#parsing-arrays)ve [iç içe diziler](#nested-json-arrays)hakkındaki ayrıntıları içerir.

Kod tabanlı JSON dizin oluşturma için [Postman](search-get-started-postman.md) ve REST API kullanarak bu nesneleri oluşturun:

+ [indeks](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [veri kaynağı](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [dizinleyic](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

İşlem sırası, nesneleri bu sırayla oluşturmanızı ve çağıraçmanızı gerektirir. Portal iş akışının aksine, bir kod yaklaşımı, Dizin **oluşturma** isteği ISTEĞIYLE gönderilen JSON belgelerini kabul etmek için kullanılabilir bir dizin gerektirir.

Azure Blob depolamada JSON blob 'ları genellikle tek bir JSON belgesi veya bir JSON "dizisi" olacaktır. Azure Bilişsel Arama blob Indexer, istek üzerinde **Parsingmode** parametresini nasıl ayarlayadiğinize bağlı olarak, her iki oluşturmayı de ayrıştırabilirler.

| JSON belgesi | parsingMode | Açıklama | Kullanılabilirlik |
|--------------|-------------|--------------|--------------|
| Blob başına bir tane | `json` | JSON bloblarını tek bir metin öbeği olarak ayrıştırır. Her JSON BLOBU tek bir Azure Bilişsel Arama belgesi haline gelir. | Genellikle [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API ve [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 'sında kullanılabilir. |
| Blob başına birden çok | `jsonArray` | Blob 'ta her bir dizi öğesinin ayrı bir Azure Bilişsel Arama belgesi haline geldiği bir JSON dizisi ayrıştırır.  | Genellikle [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API ve [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 'sında kullanılabilir. |
| Blob başına birden çok | `jsonLines` | Her varlığın ayrı bir Azure Bilişsel Arama belgesi haline geldiği bir yeni satır ile ayrılmış birden çok JSON varlığı ("dizi") içeren bir blobu ayrıştırır. | Genellikle [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API ve [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 'sında kullanılabilir. |

### <a name="1---assemble-inputs-for-the-request"></a>1-istek için girişleri birleştirin

Her istek için, Azure Bilişsel Arama için hizmet adı ve yönetici anahtarı (posta üst bilgisinde) ve BLOB depolama için depolama hesabı adı ve anahtarı sağlamalısınız. Azure Bilişsel Arama HTTP istekleri göndermek için [Postman](search-get-started-postman.md) kullanabilirsiniz.

Aşağıdaki dört değeri not defteri 'ne kopyalayın, böylece bunları bir isteğe yapıştırabilirsiniz:

+ Azure Bilişsel Arama hizmet adı
+ Azure Bilişsel Arama yönetici anahtarı
+ Azure depolama hesabı adı
+ Azure depolama hesabı anahtarı

Bu değerleri portalda bulabilirsiniz:

1. Azure Bilişsel Arama Portal sayfalarında, genel bakış sayfasından arama hizmeti URL 'sini kopyalayın.

2. Sol gezinti bölmesinde **anahtarlar** ' a tıklayın ve ardından birincil ya da ikincil anahtarı kopyalayın (eşdeğerdir).

3. Depolama hesabınız için Portal sayfalarına geçiş yapın. Sol gezinti bölmesindeki **Ayarlar**altında **erişim anahtarları**' na tıklayın. Bu sayfada hem hesap adı hem de anahtar sağlanır. Depolama hesabı adını ve anahtarlardan birini Not defteri 'ne kopyalayın.

### <a name="2---create-a-data-source"></a>2-veri kaynağı oluşturma

Bu adım, Dizin Oluşturucu tarafından kullanılan veri kaynağı bağlantı bilgilerini sağlar. Veri kaynağı, Azure Bilişsel Arama, bağlantı bilgilerini devam eden bir adlandırılmış nesnedir. Veri kaynağı türü `azureblob`, Dizin Oluşturucu tarafından hangi veri ayıklama davranışlarını çağırılacağını belirler. 

Hizmet adı, yönetici anahtarı, depolama hesabı ve hesap anahtarı yer tutucuları için geçerli değerleri değiştirin.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3-hedef arama dizini oluşturma 

Dizin oluşturucular bir dizin şeması ile eşleştirilir. API 'YI kullanıyorsanız (portal yerine), Dizin Oluşturucu işleminde belirleyebilmeniz için bir dizini önceden hazırlayın.

Dizin, Azure Bilişsel Arama 'te aranabilir içeriği depolar. Bir dizin oluşturmak için, bir belge, öznitelikler ve arama deneyimini şekillendirip diğer yapılar içindeki alanları belirten bir şema sağlayın. Kaynak ile aynı alan adlarına ve veri türlerine sahip bir dizin oluşturursanız, Dizin Oluşturucu kaynak ve hedef alanlarla eşleşir ve bu da alanları açık bir şekilde eşlemek için sahip olma çalışmalarınız olur.

Aşağıdaki örnekte bir [Dizin oluşturma](https://docs.microsoft.com/rest/api/searchservice/create-index) isteği gösterilmektedir. Dizinde, bloblardan ayıklanan metnin depolanması için aranabilir bir `content` alanı olacaktır:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4-dizin oluşturucuyu yapılandırma ve çalıştırma

Bir dizin ve veri kaynağında olduğu gibi, Dizin Oluşturucu da Azure Bilişsel Arama hizmetinde oluşturup yeniden kullandığınız adlandırılmış bir nesnedir. Bir dizin oluşturucu oluşturmak için tam olarak belirtilen bir istek aşağıdaki gibi görünebilir:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Dizin Oluşturucu yapılandırması isteğin gövdesinde. Azure Bilişsel Arama 'de zaten var olan bir veri kaynağı ve boş bir hedef dizin gerektirir. 

Zamanlama ve parametreler isteğe bağlıdır. Bunları atlarsanız, Dizin Oluşturucu, ayrıştırma modu olarak `json` kullanarak hemen çalışır.

Bu belirli bir Dizin Oluşturucu alan eşlemelerini içermez. Dizin Oluşturucu tanımı içinde, kaynak JSON belgesinin özellikleri hedef arama dizininizdeki alanlarla eşleşiyorsa **alan eşlemelerini** bırakabilirsiniz. 


### <a name="rest-example"></a>REST örneği

Bu bölüm, nesne oluşturmak için kullanılan tüm isteklerin bir üst ucu olur. Bileşen bölümlerinin bir tartışması için, bu makaledeki önceki bölümlere bakın.

### <a name="data-source-request"></a>Veri kaynağı isteği

Tüm Dizin oluşturucular, mevcut verilere bağlantı bilgilerini sağlayan bir veri kaynağı nesnesi gerektirir. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Dizin isteği

Tüm Dizin oluşturucular, verileri alan bir hedef dizin gerektirir. İsteğin gövdesi, aranabilir bir dizinde istenen davranışları desteklemek için Öznitelikli, alanlardan oluşan Dizin şemasını tanımlar. Dizin oluşturucuyu çalıştırdığınızda bu dizin boş olmalıdır. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Dizin Oluşturucu isteği

Bu istek, tam olarak belirtilen bir dizin oluşturucuyu gösterir. Bu, önceki örneklerde atlanan alan eşlemelerini içerir. Kullanılabilir bir varsayılan değer olduğu sürece "Schedule", "Parameters" ve "fieldMappings" öğesinin isteğe bağlı olduğunu hatırlayın. "Schedule" kullanılmazsa dizin oluşturucunun hemen çalışmasına neden olur. "ParsingMode" kullanılmazsa Dizin "JSON" varsayılanını kullanılmasına neden olur.

Azure Bilişsel Arama Dizin oluşturucuyu oluşturmak, verileri içeri aktarmayı tetikler. Hemen ve ardından bir zamanlama sağladıysanız çalışır.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK kullanma

.NET SDK 'nın REST API tam eşliği vardır. Kavramları, iş akışını ve gereksinimleri öğrenmek için önceki REST API bölümünü incelemenizi öneririz. Daha sonra, yönetilen kodda bir JSON Dizin Oluşturucu uygulamak için aşağıdaki .NET API başvuru belgelerine başvurabilirsiniz.

+ [Microsoft. Azure. Search. modeller. DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft. Azure. Search. modeller. DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft. Azure. Search. modeller. index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft. Azure. Search. modeller. Indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Ayrıştırma modları

JSON blob 'ları birden çok formu kabul edebilir. JSON Dizin oluşturucudaki **Parsingmode** PARAMETRESI, JSON blob Içeriğinin bir Azure bilişsel arama dizininde nasıl ayrıştırılıp yapılandırıldığını belirler:

| parsingMode | Açıklama |
|-------------|-------------|
| `json`  | Her Blobun tek bir belge olarak dizinini oluştur. Bu varsayılandır. |
| `jsonArray` | Bloblarınız JSON dizilerlerinden oluşur ve dizideki her öğeye Azure Bilişsel Arama ayrı bir belge olmak istiyorsanız bu modu seçin. |
|`jsonLines` | Bloblarınız yeni bir satırla ayrılmış birden çok JSON varlığında oluşur ve her bir varlığa Azure Bilişsel Arama 'de ayrı bir belge olması gerekiyorsa bu modu seçin. |

Belgeyi, arama sonuçlarında tek bir öğe olarak düşünebilirsiniz. Dizideki her öğenin arama sonuçlarında bağımsız bir öğe olarak gösterilmesini istiyorsanız, `jsonArray` veya `jsonLines` seçeneğini uygun şekilde kullanın.

Dizin Oluşturucu tanımı içinde, isteğe bağlı olarak, kaynak JSON belgesinin hangi özelliklerinin hedef arama dizininizi doldurmak için kullanıldığını seçmek için [alan eşlemelerini](search-indexer-field-mappings.md) kullanabilirsiniz. `jsonArray` ayrıştırma modu için, dizi alt düzey bir özellik olarak mevcutsa, dizinin blob içine yerleştirileceğini gösteren bir belge kökü ayarlayabilirsiniz.

> [!IMPORTANT]
> `json`, `jsonArray` veya `jsonLines` ayrıştırma modu kullandığınızda Azure Bilişsel Arama, veri kaynağınızdaki tüm Blobların JSON içerdiğini varsayar. Aynı veri kaynağında JSON ve JSON olmayan Blobların bir karışımını desteklemeniz gerekiyorsa [UserVoice sitemizi](https://feedback.azure.com/forums/263029-azure-search)bize bilgilendirin.


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Tek JSON bloblarını ayrıştırma

Varsayılan olarak, [Azure bilişsel arama blob Indexer](search-howto-indexing-azure-blob-storage.md) JSON bloblarını tek bir metin öbeği olarak ayrıştırır. Genellikle, JSON belgelerinizin yapısını korumak istersiniz. Örneğin, Azure Blob depolamada aşağıdaki JSON belgesine sahip olduğunu varsayalım:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob Indexer JSON belgesini tek bir Azure Bilişsel Arama belgesi olarak ayrıştırır. Dizin Oluşturucu, "Text", "Dateyayınlanan" ve "Tags" ile aynı ada ve türü belirtilmiş hedef dizin alanlarına karşılık gelen bir dizini yükler.

Belirtildiği gibi, alan eşlemeleri gerekli değildir. "Text", "Dateyayınlanan ve" Tags "alanları ile bir dizin verildiğinde, blob Indexer istekte yer alan eşleme olmadan doğru eşlemeyi çıkarabilir.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON dizilerini ayrıştırma

Alternatif olarak, JSON dizi seçeneğini de kullanabilirsiniz. Bu seçenek, Bloblar *düzgün biçimlendirilmiş BIR JSON nesneleri dizisi*içerdiğinde ve her bir öğenin ayrı bir Azure bilişsel arama belgesi olmasını istediğinizde yararlıdır. Örneğin, aşağıdaki JSON blobu verildiğinde, her biri "kimlik" ve "metin" alanları ile Azure Bilişsel Arama dizininizi üç ayrı belge ile doldurabilirsiniz.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

JSON dizisi için Dizin Oluşturucu tanımı aşağıdaki örneğe benzer görünmelidir. ParsingMode parametresinin `jsonArray` ayrıştırıcısı belirttiğinden emin olun. Doğru ayrıştırıcının belirtilmesi ve doğru veri girişi olması, JSON bloblarını dizine almanın yalnızca iki diziye özgü gereksinimleridir.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Yine, alan eşlemelerinin atlanabileceğini unutmayın. Aynı "kimlik" ve "metin" alanları ile bir dizin varsayıldığında, blob Indexer açık alan eşleme listesi olmadan doğru eşlemeyi çıkartabilirler.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>İç içe diziler ayrıştırma
İç içe yerleştirilmiş öğeleri olan JSON dizileri için, çok düzeyli bir yapıyı göstermek üzere bir `documentRoot` belirtebilirsiniz. Örneğin, bloblarınız şunun gibi görünür:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

`level2` özelliğinde yer alan diziyi indekslemek için bu yapılandırmayı kullanın:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Newlines ile ayrılmış Blobları Ayrıştır

Blobun, bir yeni satır tarafından ayrılmış birden çok JSON varlığı içeriyorsa ve her bir öğenin ayrı bir Azure Bilişsel Arama belgesi olmasını istiyorsanız JSON satırları seçeneğini kabul edebilirsiniz. Örneğin, aşağıdaki blob (üç farklı JSON varlığı) verildiğinde, her biri "kimlik" ve "metin" alanları ile Azure Bilişsel Arama dizininizi üç ayrı belge ile doldurabilirsiniz.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

JSON satırları için Dizin Oluşturucu tanımı aşağıdaki örneğe benzer görünmelidir. ParsingMode parametresinin `jsonLines` ayrıştırıcısı belirttiğinden emin olun. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Yeniden, `jsonArray` ayrıştırma moduna benzer şekilde alan eşlemelerinin atlanabileceğini unutmayın.

## <a name="add-field-mappings"></a>Alan eşlemeleri Ekle

Kaynak ve hedef alanlar kusursuz hizalı olmadığında, açık alan-alan ilişkilendirmeleri için istek gövdesinde bir alan eşleme bölümü tanımlayabilirsiniz.

Şu anda, yalnızca ilkel veri türlerini, dize dizilerini ve coğrafi JSON noktalarını desteklediğinden Azure Bilişsel Arama doğrudan rastgele JSON belgelerini dizinlemez. Ancak, JSON belgenizin parçalarını seçmek için **alan eşlemelerini** kullanabilir ve bunları arama belgesinin en üst düzey alanlarına "kaldırabilirsiniz". Alan eşlemeleri temelleri hakkında bilgi edinmek için bkz. [Azure bilişsel arama Dizin oluşturucularda alan eşlemeleri](search-indexer-field-mappings.md).

Örnek JSON belgemizi yeniden ziyaret ediyor:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Aşağıdaki alanlarla bir arama dizini olduğunu varsayalım: tür `Edm.String`, `date` türü `Edm.DateTimeOffset`ve `tags` türündeki `text`.`Collection(Edm.String)` Dizindeki kaynak ve `date` alanında "Dateyayýmlandý" arasındaki tutarsızlığı fark edin. JSON 'nizi istenen şekle eşlemek için aşağıdaki alan eşlemelerini kullanın:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Eşlemelerde kaynak alanı adları [JSON işaretçisi](https://tools.ietf.org/html/rfc6901) gösterimi kullanılarak belirtilir. JSON belgenizin köküne başvuracak bir eğik çizgiyle başlayıp, eğik çizgi ile ayrılmış yolu kullanarak istediğiniz özelliği (rastgele iç içe geçme düzeyinde) seçin.

Ayrıca, sıfır tabanlı bir dizin kullanarak ayrı dizi öğelerine de başvurabilirsiniz. Örneğin, yukarıdaki örnekteki "Etiketler" dizisinin ilk öğesini seçmek için, şöyle bir alan eşlemesi kullanın:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Bir alan eşleme yolundaki kaynak alan adı JSON içinde mevcut olmayan bir özelliğe başvuruyorsa, bu eşleme bir hata olmadan atlanır. Bu, farklı bir şemaya sahip belgeleri desteklebilmemiz için yapılır (yaygın kullanım durumu). Doğrulama olmadığından, alan eşleme belirtimindeki yazım hatalarını kullanmaktan kaçınmak için dikkatli olmanız gerekir.
>
>

## <a name="see-also"></a>Ayrıca bkz.

+ [Azure Bilişsel Arama Dizin oluşturucular](search-indexer-overview.md)
+ [Azure Blob depolama dizini oluşturma Bilişsel Arama](search-howto-index-json-blobs.md)
+ [Azure Bilişsel Arama blob Indexer ile CSV bloblarını dizine ekleme](search-howto-index-csv-blobs.md)
+ [Öğretici: Azure Blob depolamadan yarı yapılandırılmış verileri arama](search-semi-structured-data.md)

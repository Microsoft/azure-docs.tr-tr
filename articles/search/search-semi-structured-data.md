---
title: 'Öğretici: JSON Bloblarında yarı strutured verileri dizin oluştur'
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama REST API 'Leri ve Postman kullanarak yarı yapılandırılmış Azure JSON bloblarını dizin oluşturma ve arama hakkında bilgi edinin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d8d3204c8a5ace17ae47a17d4c4ffec2ec7977f2
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112256"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>REST öğreticisi: Azure Bilişsel Arama yarı yapılandırılmış verileri (JSON blob 'ları) dizin ve arama

Azure Bilişsel Arama, yarı yapılandırılmış verilerin nasıl okunacağını bilen bir [Dizin Oluşturucu](search-indexer-overview.md) kullanarak Azure Blob depolamada JSON belgelerini ve dizilerini dizinedebilir. Yarı yapılandırılmış veriler, veriler içindeki içeriği ayıran etiketleri veya işaretleri içerir. Tam olarak dizin oluşturulması gereken yapılandırılmamış veriler arasındaki farkı ve bir ilişkisel veritabanı şeması gibi bir veri modeline bağlı olan, tek başına yapılandırılmış verileri alan temelinde dizinlenebilir şekilde ayırır.

Bu öğreticide, aşağıdaki görevleri gerçekleştirmek için [Azure BILIŞSEL arama REST API 'lerini](https://docs.microsoft.com/rest/api/searchservice/) ve bir rest istemcisini kullanın:

> [!div class="checklist"]
> * Azure Blob kapsayıcısı için Azure Bilişsel Arama veri kaynağı yapılandırma
> * Aranabilir içerik içeren bir Azure Bilişsel Arama dizini oluşturma
> * Kapsayıcıyı okumak ve Azure Blob depolama alanından aranabilir içeriği ayıklamak için bir dizin oluşturucuyu yapılandırma ve çalıştırma
> * Oluşturduğunuz dizini arama

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki hizmetler, Araçlar ve veriler bu hızlı başlangıçta kullanılır. 

Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu öğretici için ücretsiz bir hizmet kullanabilirsiniz. 

Örnek verileri depolamak için [bir Azure depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) .

Azure Bilişsel Arama istek göndermek için [Postman masaüstü uygulaması](https://www.getpostman.com/) .

[Clinical-Trials-JSON. zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) Bu öğreticide kullanılan verileri içerir. Bu dosyayı kendi klasörüne indirip sıkıştırmasını açın. Veriler, bu öğretici için JSON 'a dönüştürüldü, [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)'tan kaynaklanır.

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

REST çağrıları için her istekte hizmet URL'sinin ve bir erişim anahtarının iletilmesi gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-postman/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="prepare-sample-data"></a>Örnek verileri hazırlama

1. [Azure Portal oturum açın](https://portal.azure.com), Azure depolama hesabınıza gidin, **Bloblar**' a tıklayın ve ardından **+ Container**' a tıklayın.

1. Örnek veri içeren [bir blob kapsayıcısı oluşturun](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) . Ortak erişim düzeyini geçerli değerlerinden herhangi birine ayarlayabilirsiniz.

1. Kapsayıcı oluşturulduktan sonra açın ve komut çubuğunda **karşıya yükle** ' yi seçin.

   ![Komut çubuğuna yükle](media/search-semi-structured-data/upload-command-bar.png "Komut çubuğuna yükle")

1. Örnek dosyaları içeren klasöre gidin. Tümünü seçip **karşıya yükle**' ye tıklayın.

   ![Dosyaları karşıya yükleme](media/search-semi-structured-data/clinicalupload.png "Dosyaları karşıya yükleme")

Yükleme tamamlandıktan sonra dosyalar veri kapsayıcısında kendi alt klasöründe görünmelidir.

## <a name="set-up-postman"></a>Postman’i ayarlama

Postman’i başlatın ve bir HTTP isteği ayarlayın. Bu aracı hakkında bilginiz varsa bkz. [Postman kullanarak Azure BILIŞSEL arama REST API 'Lerini araştırma](search-get-started-postman.md).

Bu öğreticideki her çağrının istek yöntemi **gönderi**' dır. Üst bilgi anahtarları "Content-type" ve "api-key"dir. Üst bilgi anahtarlarının değerleri sırayla "application/json" ve "yönetici anahtarınız"dır (yönetici anahtarı, arama birincil anahtarınız için yer tutucudur). Gövde, çağrınızın gerçek içeriklerini yerleştirdiğiniz yerdir. Kullandığınız istemciye bağlı olarak, sorgunuzu oluşturma şeklinizde bazı farklılıklar olabilir. Burada temel bilgiler verilmiştir.

  ![Yarı yapılandırılmış arama](media/search-semi-structured-data/postmanoverview.png)

Arama hizmetinize üç API çağrısı yaparak veri kaynağı, dizin ve dizin oluşturucu oluşturmak için Postman kullanıyoruz. Veri kaynağı, depolama hesabınıza ve JSON verilerinize yönelik bir işaretçi içerir. Arama hizmetiniz, veriler yüklenirken bağlantı kurar.

Sorgu dizeleri bir api sürümü belirtmeli ve her çağrının **oluşturulan bir 201**döndürmesi gerekir. JSON dizilerini kullanmak için genel olarak kullanılabilen api-Version `2019-05-06`.

REST istemcinizden aşağıdaki üç API çağrısını yürütün.

## <a name="create-a-data-source"></a>Veri kaynağı oluşturma

[Veri kaynağı oluşturma API 'si](https://docs.microsoft.com/rest/api/searchservice/create-data-source), hangi verilerin dizine oluşturulacağını belirten bir Azure bilişsel arama nesnesi oluşturur.

Bu çağrının uç noktası: `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. `[service name]` değerini, arama hizmetinizin adıyla değiştirin. 

Bu çağrı için, istek gövdesi depolama hesabınızın adını, depolama hesabı anahtarınızı ve BLOB kapsayıcısı adını içermelidir. Depolama hesabı anahtarı, Azure portalında depolama hesabınızın **Erişim Anahtarları** bölümünde bulunur. Aşağıdaki resimde konum gösterilmektedir:

  ![Yarı yapılandırılmış arama](media/search-semi-structured-data/storagekeys.png)

Çağrıyı yürütmeden önce çağrın gövdesinde `[storage account name]`, `[storage account key]`ve `[blob container name]` değiştirdiğinizden emin olun.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

Yanıt şöyle görünmelidir:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Dizin oluşturun
    
İkinci çağrı, tüm aranabilir verileri depolayan bir Azure Bilişsel Arama dizini oluşturan [Dizin API 'Si oluşturur](https://docs.microsoft.com/rest/api/searchservice/create-indexer). Dizin, tüm parametreleri ve parametrelerin özniteliklerini belirtir.

Bu çağrının URL’si: `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. `[service name]` değerini, arama hizmetinizin adıyla değiştirin.

İlk olarak URL’yi değiştirin. Daha sonra aşağıdaki kodu kopyalayıp gövdeye yapıştırın ve sorguyu çalıştırın.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

Yanıt şöyle görünmelidir:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Dizin Oluşturucu oluşturma ve çalıştırma

Bir dizin oluşturucu veri kaynağını bağlar, verileri hedef arama dizinine aktarır ve isteğe bağlı olarak veri yenilemeyi otomatikleştirmek için bir zamanlama sağlar. REST API [Dizin Oluşturucu oluşturur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Bu çağrının URL’si: `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. `[service name]` değerini, arama hizmetinizin adıyla değiştirin.

İlk olarak URL’yi değiştirin. Ardından aşağıdaki kodu kopyalayıp gövdeye yapıştırın ve isteği gönderin. İstek hemen işlenir. Yanıt geri geldiğinde, tam metin ile aranabilir bir dizininiz olur.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

Yanıt şöyle görünmelidir:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>JSON dosyalarınızı arama

İlk belge yüklendikten hemen sonra aramaya başlayabilirsiniz. Bu görev için portalda [**Arama Gezgini**](search-explorer.md) ' ni kullanın.

Azure portal, arama hizmeti **genel bakış** sayfasını açın, **dizinler** listesinde oluşturduğunuz dizini bulun.

Az önce oluşturduğunuz dizini seçtiğinizden emin olun. 

  ![Yapılandırılmamış arama](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Kullanıcı tanımlı meta veri araması

Önceden olduğu gibi veriler birçok şekilde sorgulanabilir: tam metin araması, sistem özellikleri veya kullanıcı tanımlı meta veriler. Hem sistem özellikleri hem de kullanıcı tanımlı meta veriler yalnızca hedef dizin oluşturulurken `$select`alınabilir**olarak işaretlendiyse** parametresiyle aranabilir. Dizindeki parametreler oluşturulduktan sonra değiştirilemez. Ancak ek parametreler eklenebilir.

Temel bir sorgu örneği `$select=Gender,metadata_storage_size` olup bu, dönüşü bu iki parametreyle sınırlar.

  ![Yarı yapılandırılmış arama](media/search-semi-structured-data/lastquery.png)

Daha karmaşık sorgu örneği `$filter=MinimumAge ge 30 and MaximumAge lt 75` olup bu, yalnızca MinimumAge parametrelerinin 30 değerinden büyük veya bu değere eşit ve MaximumAge parametresinin 75’ten küçük olduğu sonuçları döndürür.

  ![Yarı yapılandırılmış arama](media/search-semi-structured-data/metadatashort.png)

Birkaç sorguyu daha denemek istiyorsanız rahatça bunu yapabilirsiniz. Karşılaştırma işleçlerini (eq, ne, gt, lt, ge, le) değil, Mantıksal işleçleri (and, or, not) kullanabileceğinizi unutmayın. Dize karşılaştırmaları büyük/küçük harfe duyarlıdır.

`$filter` parametresi yalnızca dizininiz oluşturulurken filtrelenebilir olarak işaretlenmiş olan meta verilerle birlikte çalışır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiden sonra temizlemenin en hızlı yolu, Azure Bilişsel Arama hizmetini içeren kaynak grubunu silmelidir. Kaynak grubunu silerek içindeki her şeyi kalıcı olarak silebilirsiniz. Portalda, kaynak grubu adı Azure Bilişsel Arama hizmeti 'nin genel bakış sayfaalıdır.

## <a name="next-steps"></a>Sonraki adımlar

JSON bloblarını dizine almanın çeşitli yaklaşımları ve birden çok seçeneği vardır. Bir sonraki adım olarak, senaryonuza en uygun şeyi görmek için çeşitli seçenekleri gözden geçirin ve test edin.

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama blob Indexer kullanarak JSON bloblarını dizin oluşturma](search-howto-index-json-blobs.md)

---
title: REST kullanarak bilgi deposu (Önizleme) oluşturma
titleSuffix: Azure Cognitive Search
description: Bir AI zenginleştirme ardışık düzeninde kalıcı zenginler için bir Azure Bilişsel Arama bilgi deposu oluşturmak üzere REST API ve Postman kullanın. Bu özellik şu anda genel önizleme aşamasındadır.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 107dcfa9ea312774e679c301ea934255c7b836c0
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720072"
---
# <a name="create-an-azure-cognitive-search-knowledge-store-by-using-rest"></a>REST kullanarak bir Azure Bilişsel Arama bilgi deposu oluşturma

> [!IMPORTANT] 
> bilgi deposu Şu anda genel önizleme aşamasındadır. Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) , Önizleme özellikleri sağlar. Şu anda sınırlı sayıda portal desteği var ve .NET SDK desteği yok.

Azure Bilişsel Arama bilgi deposu özelliği, daha sonraki analizler veya diğer aşağı akış işlemleri için bir AI zenginleştirme ardışık düzeninde çıktı olarak devam eder. Bir AI zenginleştirme işlem hattı, görüntü dosyalarını veya yapılandırılmamış metin dosyalarını kabul eder, Azure Bilişsel Arama kullanarak dizinleri oluşturur, Azure bilişsel hizmetler 'den AI zenginetlerini uygular (görüntü analizi ve doğal dil işleme) ve sonuçları bir Azure depolama 'da bilgi deposu. Bilgi deposunu araştırmak için Azure portal Power BI veya Depolama Gezgini gibi araçları kullanabilirsiniz.

Bu makalede, REST API arabirimini kullanarak bir otel İncelemeleri kümesine AI zenginleştirme, dizin oluşturma ve uygulama. Otel İncelemeleri Azure Blob depolama alanına aktarılır. Sonuçlar, Azure Tablo Depolaması 'nda bir bilgi deposu olarak kaydedilir.

Bilgi deposunu oluşturduktan sonra, [Depolama Gezgini](knowledge-store-view-storage-explorer.md) veya [Power BI](knowledge-store-connect-power-bi.md)kullanarak bilgi deposuna nasıl erişebileceğinizi öğrenebilirsiniz.

## <a name="create-services"></a>Hizmet oluşturma

Aşağıdaki hizmetleri oluşturun:

- Geçerli aboneliğinizde bir [Azure bilişsel arama hizmeti](search-create-service-portal.md) oluşturun veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu öğretici için ücretsiz bir hizmet kullanabilirsiniz.

- Örnek verileri ve bilgi deposunu depolamak için bir [Azure depolama hesabı](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) oluşturun. Depolama hesabınızın Azure Bilişsel Arama hizmetiniz için aynı konumu (ABD-Batı) kullanması gerekir. **Hesap türü** Için değer **StorageV2 (genel amaçlı v2)** (varsayılan) veya **depolama (genel amaçlı v1)** olmalıdır.

- Önerilir: Azure Bilişsel Arama istek göndermek için [Postman masaüstü uygulamasını](https://www.getpostman.com/) alın. REST API, HTTP istekleri ve yanıtları ile çalışmayan herhangi bir araçla birlikte kullanabilirsiniz. Postman REST API 'Leri keşfetmek için iyi bir seçimdir. Bu makalede Postman kullanıyoruz. Ayrıca, bu makaleye ait [kaynak kodu](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) Isteklerin bir Postman koleksiyonunu içerir. 

## <a name="store-the-data"></a>Verileri depolama

Azure Bilişsel Arama Indexer tarafından erişilebilmesi ve AI zenginleştirme işlem hattı üzerinden beslenmesini sağlamak için otel, CSV dosyasını Azure Blob depolama alanına yükleyin.

### <a name="create-a-blob-container-by-using-the-data"></a>Verileri kullanarak blob kapsayıcısı oluşturma

1. Bir CSV dosyasına kaydedilmiş [otel gözden geçirme verilerini](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) Indirin (HotelReviews_Free. csv). Bu veriler Kaggle.com adresinden kaynaklanır ve oteller hakkında müşteri geri bildirimi içerir.
1. [Azure Portal](https://portal.azure.com) oturum açın ve Azure depolama hesabınıza gidin.
1. [BLOB kapsayıcısı](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)oluşturun. Kapsayıcıyı oluşturmak için, depolama hesabınızın sol menüsünde, **Bloblar**' ı seçin ve **kapsayıcı**' yı seçin.
1. Yeni kapsayıcı **adı**için **otel-incelemeler**yazın.
1. **Genel erişim düzeyi**için herhangi bir değer seçin. Varsayılan değer kullandık.
1. Blob kapsayıcısını oluşturmak için **Tamam ' ı** seçin.
1. Yeni **oteller-gözden geçirme** kapsayıcısını açın, **karşıya yükle**' yi seçin ve ardından ilk adımda indirdiğiniz HotelReviews-Free. csv dosyasını seçin.

    ![Verileri karşıya yükleme](media/knowledge-store-create-portal/upload-command-bar.png "Otel incelemelerini karşıya yükleyin")

1. CSV dosyasını Azure Blob depolamaya aktarmak için **karşıya yükle** ' yi seçin. Yeni kapsayıcı gösterilir:

    ![Blob kapsayıcısını oluşturma](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Blob kapsayıcısını oluşturma")

## <a name="configure-postman"></a>Postman'i yapılandırma

Postman yükleme ve ayarlama.

### <a name="download-and-install-postman"></a>Postman indirme ve yükleme

1. [Postman koleksiyonu kaynak kodunu](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)indirin.
1. Kaynak kodu Postman içine aktarmak için **dosya** > **içeri aktar** ' ı seçin.
1. **Koleksiyonlar** sekmesini seçin ve ardından **...** (üç nokta) düğmesini seçin.
1. **Düzenle**’yi seçin. 
   
   ![Gezintiyi gösteren Postman uygulaması](media/knowledge-store-create-rest/postman-edit-menu.png "Postman 'daki düzenleme menüsüne git")
1. **Düzenle** Iletişim kutusunda **değişkenler** sekmesini seçin. 

**Değişkenler** sekmesinde, Postman 'ın her bir çift küme içinde belirli bir değişkenle karşılaştığı her seferinde takas eden değerler ekleyebilirsiniz. Örneğin Postman, symbol `{{admin-key}}` `admin-key`için ayarladığınız geçerli değerle değiştirir. Postman, URL 'Lerde, üst bilgilerde, istek gövdesinde vb. değişiklik yapar. 

`admin-key`değerini almak için Azure Bilişsel Arama hizmetine gidin ve **anahtarlar** sekmesini seçin. `search-service-name` ' i ve `storage-account-name` [Oluştur](#create-services)' u seçtiğiniz değerlere değiştirin. Depolama hesabının **erişim anahtarları** sekmesindeki değeri kullanarak `storage-connection-string` ayarlayın. Diğer değerler için varsayılan değerleri bırakabilirsiniz.

![Postman uygulama değişkenleri sekmesi](media/knowledge-store-create-rest/postman-variables-window.png "Postman 'ın değişkenler penceresi")


| Değişken    | Nereden alınır? |
|-------------|-----------------|
| `admin-key` | Azure Bilişsel Arama hizmetinin **anahtarlar** sayfasında.  |
| `api-version` | **2019-05-06-Preview**olarak bırakın. |
| `datasource-name` | Otel, **incelemeler-DS**olarak bırakın. | 
| `indexer-name` | Otel- **incelemeler-ixR**olarak bırakın. | 
| `index-name` | Otel-, **-x**olarak bırakın. | 
| `search-service-name` | Azure Bilişsel Arama hizmetinin adı. URL `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Otel- **İnceleme**olarak bırakın. | 
| `storage-account-name` | Depolama hesabı adı. | 
| `storage-connection-string` | Depolama hesabında, **erişim anahtarları** sekmesinde, **KEY1** > **bağlantı dizesi**' ni seçin. | 
| `storage-container-name` | **Otel-incelemeler**olarak bırakın. | 

### <a name="review-the-request-collection-in-postman"></a>Postman 'da istek koleksiyonunu gözden geçirin

Bir bilgi deposu oluştururken dört HTTP isteği vermelisiniz: 

- **Dizini oluşturmak için Isteği yerleştir**: Bu dizin, Azure bilişsel arama tarafından kullanılan ve döndürdüğü verileri tutar.
- Veri **kaynağını oluşturmak Için post isteği**: Bu veri kaynağı, Azure bilişsel arama davranışınızı veri ve bilgi deposunun depolama hesabına bağlar. 
- **Beceri oluşturma isteği**: Beceri, verilerinize ve bilgi deposunun yapısına uygulanan zenginleştirme bilgilerini belirtir.
- **Dizin oluşturucuyu oluşturma Isteği koy**: Dizin oluşturucuyu çalıştırmak, verileri okur, Beceri uygular ve sonuçları depolar. Bu isteği en son çalıştırmanız gerekir.

[Kaynak kodu](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) , dört isteği olan bir Postman koleksiyonu içerir. İstekleri vermek için, Postman 'da istek için sekmeyi seçin. Sonra `api-key` ve `Content-Type` istek üst bilgilerini ekleyin. `api-key` değerini `{{admin-key}}`olarak ayarlayın. Değer `Content-type` `application/json`olarak ayarlayın. 

![Üst bilgiler için Postman arabirimini gösteren ekran görüntüsü](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Tüm isteklerinizin `api-key` ve `Content-type` üst bilgilerini ayarlamanız gerekir. Postman bir değişkeni tanırsa, değişken, önceki ekran görüntüsünde `{{admin-key}}` gibi turuncu metinde görünür. Değişken yanlış yazıldığında, kırmızı metinde görünür.
>

## <a name="create-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizini oluşturma

Arama, filtreleme ve geliştirmeleri uygulama hakkında ilgilendiğiniz verileri temsil etmek için bir Azure Bilişsel Arama dizini oluşturun. `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`için bir PUT isteği yayımlayarak dizini oluşturun. Postman, bir çift küme ayracı (`{{search-service-name}}`, `{{index-name}}`ve `{{api-version}}`), [Postman yapılandırma](#configure-postman)bölümünde ayarladığınız değerlerle değiştirir. REST komutlarınızı vermek için farklı bir araç kullanıyorsanız, bu değişkenleri kendiniz yerine kullanmalısınız.

İsteğin gövdesinde Azure Bilişsel Arama dizininizin yapısını ayarlayın. Postman 'da, `api-key` ve `Content-type` üst bilgilerini ayarladıktan sonra, isteğin **gövde** bölmesine gidin. Aşağıdaki JSON 'ı görmeniz gerekir. Aksi takdirde, **ham** > **JSON (Application/JSON)** öğesini seçin ve ardından aşağıdaki kodu gövde olarak yapıştırın:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Bu dizin tanımı, kullanıcıya sunmak istediğiniz verilerin bir birleşimidir (otel adı, içeriği gözden geçirin, tarihi), arama meta verileri ve AI geliştirme verileri (yaklaşım, Keyphrases ve dil).

PUT isteğini vermek için **Gönder** ' i seçin. Durum `201 - Created`görmeniz gerekir. Farklı bir durum görürseniz, **gövde** bölmesinde bir hata ILETISI içeren JSON yanıtı olup olmadığına bakın. 

## <a name="create-the-datasource"></a>Veri kaynağını oluşturma

Ardından, [verileri depolamak](#store-the-data)için Azure bilişsel arama 'yi depoladığınız otel verilerine bağlayın. Veri kaynağını oluşturmak için `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`bir POST isteği gönderin. Daha önce anlatıldığı gibi `api-key` ve `Content-Type` üst bilgilerini ayarlamanız gerekir. 

Postman 'da, **veri kaynağı oluştur** isteğine ve sonra **gövde** bölmesine gidin. Aşağıdaki kodu görmeniz gerekir:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

POST isteğini vermek için **Gönder** ' i seçin. 

## <a name="create-the-skillset"></a>Beceri oluşturma 

Sonraki adım, uygulanacak geliştirmeleri ve sonuçların depolanacağı bilgi deposunu belirten beceri belirtmektir. Postman 'da **beceri oluştur** sekmesini seçin. Bu istek `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`bir PUT gönderir. `api-key` ve `Content-type` üst bilgilerini daha önce yaptığınız gibi ayarlayın. 

İki büyük üst düzey nesne vardır: `skills` ve `knowledgeStore`. `skills` nesne içindeki her nesne bir zenginleştirme hizmetidir. Her bir zenginleştirme hizmeti `inputs` ve `outputs`sahiptir. `LanguageDetectionSkill`, `Language`çıkış `targetName` sahiptir. Bu düğümün değeri, diğer yeteneklerin çoğu tarafından giriş olarak kullanılır. Kaynak `document/Language`. Bir düğümün başka birine girdi olarak çıkışını kullanma yeteneği, verilerin bilgi deposunun tablolarına nasıl akacağını belirten `ShaperSkill`daha da çok daha da fazla bir değer sağlar.

`knowledge_store` nesnesi, `{{storage-connection-string}}` Postman değişkeni aracılığıyla depolama hesabına bağlanır. `knowledge_store`, bilgi deposundaki geliştirilmiş belge ve tablolar ile sütunlar arasında bir eşleme kümesi içerir. 

Beceri oluşturmak için Postman 'daki **Gönder** düğmesini seçerek isteği yerleştirin:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Dizin oluşturucuyu oluşturma

Son adım, Dizin oluşturucuyu oluşturmaktır. Dizin Oluşturucu verileri okur ve beceri etkinleştirir. Postman 'da **Dizin oluşturma Isteği oluştur** ' u seçin ve ardından gövdesini gözden geçirin. Dizin oluşturucunun tanımı, zaten oluşturduğunuz diğer birkaç kaynağa başvurur: veri kaynağı, dizin ve beceri. 

`parameters/configuration` nesnesi, dizin oluşturucunun verileri nasıl geri gediğini denetler. Bu durumda, giriş verileri, üst bilgi satırı ve virgülle ayrılmış değerler içeren tek bir belgedir. Belge anahtarı belge için benzersiz bir tanımlayıcıdır. Kodlamadan önce belge anahtarı kaynak belgenin URL 'sidir. Son olarak, dil kodu, yaklaşım ve anahtar ifadeler gibi beceri çıkış değerleri belgedeki konumlarına eşlenir. `Language`için tek bir değer olsa da, `Sentiment` `pages`dizisindeki her öğeye uygulanır. `Keyphrases`, `pages` dizisindeki her öğe için de uygulanan dizidir.

`api-key` ve `Content-type` üst bilgilerini ayarladıktan ve isteğin gövdesinin aşağıdaki kaynak koda benzediğini doğruladıktan sonra Postman 'da **Gönder** ' i seçin. Postman `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`için bir PUT isteği gönderir. Azure Bilişsel Arama Dizin oluşturucuyu oluşturur ve çalıştırır. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Dizin oluşturucuyu çalıştırma 

Azure portal Azure Bilişsel Arama hizmetinin **genel bakış** sayfasına gidin. **Dizin oluşturucular** sekmesini seçin ve ardından **oteller-incelemeler-ixR**' ı seçin. Dizin Oluşturucu zaten çalıştırılmadıysa **Çalıştır**' ı seçin. Dizin oluşturma görevi, dil tanıma ile ilgili bazı uyarılar oluşturabilir. Veriler, bilişsel yetenekler tarafından henüz desteklenmeyen dillerde yazılmış bazı İncelemeleri içerir. 

## <a name="next-steps"></a>Sonraki adımlar

Bilişsel hizmetler 'i kullanarak verilerinizi zenginleştirdikten ve sonuçları bir bilgi deposuna yansıdığınıza göre, zenginleştirilmiş veri kümesini araştırmak için Depolama Gezgini veya Power BI kullanabilirsiniz.

Bu bilgi deposunu Depolama Gezgini kullanarak nasıl keşfedeceğinizi öğrenmek için şu izlenecek yolu inceleyin:

> [!div class="nextstepaction"]
> [Depolama Gezgini ile görüntüle](knowledge-store-view-storage-explorer.md)

Bu bilgi deposunu Power BI nasıl bağlayacağınızı öğrenmek için şu izlenecek yolu inceleyin:

> [!div class="nextstepaction"]
> [Power BI ile bağlanma](knowledge-store-connect-power-bi.md)

Bu alıştırmayı yinelemek veya farklı bir AI zenginleştirme Kılavuzu denemek istiyorsanız, **otel-incelemeler-ıdxr** Dizin oluşturucuyu silin. Dizin oluşturucunun silinmesi, ücretsiz günlük işlem sayacını sıfıra sıfırlar.

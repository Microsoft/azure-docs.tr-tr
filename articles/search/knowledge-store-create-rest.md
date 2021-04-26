---
title: REST kullanarak bilgi deposu oluşturma
titleSuffix: Azure Cognitive Search
description: Bir AI zenginleştirme ardışık düzeninde kalıcı zenginler için bir Azure Bilişsel Arama bilgi deposu oluşturmak üzere REST API ve Postman kullanın.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/18/2020
ms.openlocfilehash: 6af9b8f97d622ae10cfdbcaa8ca50abb42ec7332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94889063"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>REST ve Postman kullanarak bilgi deposu oluşturma

Bilgi deposu, daha sonraki analizler veya diğer aşağı akış işlemleri için bir Azure Bilişsel Arama zenginleştirme ardışık düzeninin çıktısını içerir. Bir AI zenginleştirme işlem hattı, görüntü dosyalarını veya yapılandırılmamış metin dosyalarını kabul eder, Azure Bilişsel Arama kullanarak dizinleri oluşturur, bilişsel hizmetlerden AI zenginleştirmelerini (görüntü analizi ve doğal dil işleme) uygular ve sonuçları Azure Storage 'daki bir bilgi deposuna kaydeder. Bilgi deposunu araştırmak için Azure portal Power BI veya Depolama Gezgini gibi araçları kullanabilirsiniz.

Bu makalede, REST API arabirimini kullanarak bir otel İncelemeleri kümesine AI zenginleştirme, dizin oluşturma ve uygulama. Otel İncelemeleri Azure Blob depolama alanına aktarılır. Sonuçlar, Azure Tablo Depolaması 'nda bir bilgi deposu olarak kaydedilir.

Bilgi deposunu oluşturduktan sonra, [Depolama Gezgini](knowledge-store-view-storage-explorer.md) veya [Power BI](knowledge-store-connect-power-bi.md)kullanarak bilgi deposuna nasıl erişebileceğinizi öğrenebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

> [!TIP]
> Bu makale için [Postman masaüstü uygulaması](https://www.getpostman.com/) önerilir. Bu makaleye ait [kaynak kodu](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) tüm istekleri Içeren bir Postman koleksiyonu içerir. 

## <a name="create-services-and-load-data"></a>Hizmet oluşturma ve veri yükleme

Bu hızlı başlangıç, AI için Azure Bilişsel Arama, Azure Blob depolama ve Azure bilişsel [Hizmetler](https://azure.microsoft.com/services/cognitive-services/) 'i kullanır. 

İş yükü çok küçük olduğu için bilişsel hizmetler, her gün 20 ' ye kadar işlem için ücretsiz işleme sağlamak üzere arka planda dokunduğunda. Veri kümesi çok küçük olduğundan, bilişsel hizmetler kaynağı oluşturma veya ekleme işlemini atlayabilirsiniz.

1. [HotelReviews_Free.csvindirin ](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Bu veriler, bir CSV dosyasına kaydedilmiş (Kaggle.com kaynaklı) Otel gözden geçirme verileri ve tek bir otel hakkında yaklaşık 19 müşteri geri bildirimi içerir. 

1. Geçerli aboneliğinizde [bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md?tabs=azure-portal) veya [mevcut bir hesabı bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) . Azure Storage 'ı içeri aktarılacak ham içerik ve son sonuç olan bilgi deposu için kullanacaksınız.

   **StorageV2 (genel amaçlı v2)** hesap türünü seçin.

1. Blob Hizmetleri sayfalarını açın ve *otel-incelemeler* adlı bir kapsayıcı oluşturun.

1. **Karşıya Yükle**'ye tıklayın.

    ![Verileri karşıya yükleme](media/knowledge-store-create-portal/upload-command-bar.png "Otel incelemelerini karşıya yükleyin")

1. İlk adımda indirdiğiniz **HotelReviews-Free.csv** dosyasını seçin.

    ![Azure Blob kapsayıcısını oluşturma](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure Blob kapsayıcısını oluşturma")

1. Bu kaynakla neredeyse tamamladınız, ancak bu sayfalardan çıkmadan önce, **erişim tuşları** sayfasını açmak için sol gezinti bölmesindeki bir bağlantıyı kullanın. Blob depolamadan veri almak için bir bağlantı dizesi alın. Bir bağlantı dizesi aşağıdaki örneğe benzer şekilde görünür: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Hala portalda Azure Bilişsel Arama ' ye geçin. [Yeni bir hizmet oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Bu alıştırma için ücretsiz bir hizmet kullanabilirsiniz.

## <a name="configure-postman"></a>Postman'i yapılandırma

Postman yükleme ve ayarlama.

### <a name="download-and-install-postman"></a>Postman indirme ve yükleme

1. [Postman koleksiyonu kaynak kodunu](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)indirin.
1.   >  Kaynak kodu Postman içine aktarmak için dosya **içeri aktarma** ' yı seçin.
1. **Koleksiyonlar** sekmesini seçin ve ardından **...** (üç nokta) düğmesini seçin.
1. **Düzenle**'yi seçin. 
   
   ![Gezintiyi gösteren Postman uygulaması](media/knowledge-store-create-rest/postman-edit-menu.png "Postman 'daki düzenleme menüsüne git")
1. **Düzenle** Iletişim kutusunda **değişkenler** sekmesini seçin. 

**Değişkenler** sekmesinde, Postman 'ın her bir çift küme içinde belirli bir değişkenle karşılaştığı her seferinde takas eden değerler ekleyebilirsiniz. Örneğin Postman, sembolü `{{admin-key}}` için ayarladığınız geçerli değerle değiştirir `admin-key` . Postman, URL 'Lerde, üst bilgilerde, istek gövdesinde vb. değişiklik yapar. 

Değerini almak için `admin-key` Azure bilişsel arama hizmetine gidin ve **anahtarlar** sekmesini seçin. `search-service-name` `storage-account-name` [Hizmetleri oluşturma](#create-services-and-load-data)bölümünde seçtiğiniz değerleri değiştirin. `storage-connection-string`Depolama hesabının **erişim anahtarları** sekmesindeki değeri kullanarak ayarlayın. Diğer değerler için varsayılan değerleri bırakabilirsiniz.

![Postman uygulama değişkenleri sekmesi](media/knowledge-store-create-rest/postman-variables-window.png "Postman 'ın değişkenler penceresi")


| Değişken    | Nereden edinilir: |
|-------------|-----------------|
| `admin-key` | Azure Bilişsel Arama hizmetinin **anahtarlar** sayfasında.  |
| `api-version` | **2020-06-30** olarak bırakın. |
| `datasource-name` | Otel, **incelemeler-DS** olarak bırakın. | 
| `indexer-name` | Otel- **incelemeler-ixR** olarak bırakın. | 
| `index-name` | Otel-, **-x** olarak bırakın. | 
| `search-service-name` | Azure Bilişsel Arama hizmetinin adı. URL `https://{{search-service-name}}.search.windows.net` . | 
| `skillset-name` | Otel- **İnceleme** olarak bırakın. | 
| `storage-account-name` | Azure depolama hesabı adı. | 
| `storage-connection-string` | Depolama hesabında, **erişim anahtarları** sekmesinde **KEY1**  >  **bağlantı dizesi**' ni seçin. | 
| `storage-container-name` | **Otel-incelemeler** olarak bırakın. | 

### <a name="review-the-request-collection-in-postman"></a>Postman 'da istek koleksiyonunu gözden geçirin

Bir bilgi deposu oluştururken dört HTTP isteği vermelisiniz: 

- **Dizini oluşturmak için Isteği yerleştir**: Bu dizin, Azure bilişsel arama tarafından kullanılan ve döndürdüğü verileri tutar.
- Veri **kaynağını oluşturmak Için post isteği**: Bu veri kaynağı, Azure bilişsel arama davranışınızı veri ve bilgi deposunun depolama hesabına bağlar. 
- **Beceri oluşturma isteği**: Beceri, verilerinize ve bilgi deposunun yapısına uygulanan zenginleştirme bilgilerini belirtir.
- **Dizin oluşturucuyu oluşturma Isteği koy**: Dizin oluşturucuyu çalıştırmak, verileri okur, Beceri uygular ve sonuçları depolar. Bu isteği en son çalıştırmanız gerekir.

[Kaynak kodu](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) , dört isteği olan bir Postman koleksiyonu içerir. İstekleri vermek için, Postman 'da istek için sekmeyi seçin. Ardından, `api-key` `Content-Type` üst bilgileri ekleyin ve isteyin. Değerini `api-key` olarak ayarlayın `{{admin-key}}` . Değerini olarak ayarlayın `Content-type` `application/json` . 

![Üst bilgiler için Postman arabirimini gösteren ekran görüntüsü](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> `api-key` `Content-type` Tüm isteklerinizi ve üst bilgileri ayarlamanız gerekir. Postman bir değişkeni tanırsa, değişken önceki ekran görüntüsünde olduğu gibi turuncu metinde görünür `{{admin-key}}` . Değişken yanlış yazıldığında, kırmızı metinde görünür.
>

## <a name="create-an-azure-cognitive-search-index"></a>Azure Bilişsel Arama dizini oluşturma

Arama, filtreleme ve geliştirmeleri uygulama hakkında ilgilendiğiniz verileri temsil etmek için bir Azure Bilişsel Arama dizini oluşturun. Bir PUT isteği vererek dizini oluşturun `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` . Postman, bir çift küme ayracı ( `{{search-service-name}}` , `{{index-name}}` , ve gibi `{{api-version}}` ), [Postman yapılandırma](#configure-postman)bölümünde ayarladığınız değerlerle değiştirilir. REST komutlarınızı vermek için farklı bir araç kullanıyorsanız, bu değişkenleri kendiniz yerine kullanmalısınız.

İsteğin gövdesinde Azure Bilişsel Arama dizininizin yapısını ayarlayın. Postman 'da, `api-key` ve üst bilgilerini ayarladıktan sonra `Content-type` , isteğin **gövde** bölmesine gidin. Aşağıdaki JSON 'ı görmeniz gerekir. Aksi takdirde, **Ham**  >  **JSON (Application/JSON)** öğesini seçin ve ardından aşağıdaki kodu gövde olarak yapıştırın:

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

PUT isteğini vermek için **Gönder** ' i seçin. Durumu görmeniz gerekir `201 - Created` . Farklı bir durum görürseniz, **gövde** bölmesinde bir hata ILETISI içeren JSON yanıtı olup olmadığına bakın. 

## <a name="create-the-datasource"></a>Veri kaynağını oluşturma

Ardından, Azure Bilişsel Arama blob depolamada depoladığınız otel verilerine bağlayın. Veri kaynağını oluşturmak için bir POST isteği gönderin `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` . `api-key`Ve `Content-Type` üst bilgilerini daha önce anlatıldığı gibi ayarlamanız gerekir. 

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

Sonraki adım, uygulanacak geliştirmeleri ve sonuçların depolanacağı bilgi deposunu belirten beceri belirtmektir. Postman 'da **beceri oluştur** sekmesini seçin. Bu istek için bir PUT gönderilir `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` . `api-key`Ve `Content-type` üst bilgilerini daha önce yaptığınız gibi ayarlayın. 

İki büyük üst düzey nesne vardır: `skills` ve `knowledgeStore` . Nesne içindeki her nesne `skills` bir zenginleştirme hizmetidir. Her bir zenginleştirme hizmeti `inputs` ve ' dir `outputs` . , `LanguageDetectionSkill` Bir çıkışına sahiptir `targetName` `Language` . Bu düğümün değeri, diğer yeteneklerin çoğu tarafından giriş olarak kullanılır. Kaynak `document/Language` . Bir düğümün başka birine girdi olarak çıkışını kullanma yeteneği, `ShaperSkill` verilerin bilgi deposunun tablolarına nasıl akacağını belirten daha da daha da açık bir hale gelir.

`knowledge_store`Nesnesi, Postman değişkeni aracılığıyla depolama hesabına bağlanır `{{storage-connection-string}}` . `knowledge_store` bilgi deposundaki geliştirilmiş belge ve tablolar ile sütunlar arasında bir eşleme kümesi içerir. 

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

`parameters/configuration`Nesnesi, dizin oluşturucunun verileri nasıl geri gediğini denetler. Bu durumda, giriş verileri, üst bilgi satırı ve virgülle ayrılmış değerler içeren tek bir belgedir. Belge anahtarı belge için benzersiz bir tanımlayıcıdır. Kodlamadan önce belge anahtarı kaynak belgenin URL 'sidir. Son olarak, dil kodu, yaklaşım ve anahtar ifadeler gibi beceri çıkış değerleri belgedeki konumlarına eşlenir. İçin tek bir değer olsa da `Language` , `Sentiment` dizisindeki her öğeye uygulanır `pages` . `Keyphrases` dizideki her öğeye de uygulanan bir dizidir `pages` .

`api-key`Ve `Content-type` üst bilgilerini ayarladıktan ve isteğin gövdesinin aşağıdaki kaynak koda benzediğini doğruladıktan sonra Postman 'da **Gönder** ' i seçin. Postman öğesine bir PUT isteği gönderir `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` . Azure Bilişsel Arama Dizin oluşturucuyu oluşturur ve çalıştırır. 

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
> [Depolama Gezgini ile görüntüleme](knowledge-store-view-storage-explorer.md)

Bu bilgi deposunu Power BI nasıl bağlayacağınızı öğrenmek için şu izlenecek yolu inceleyin:

> [!div class="nextstepaction"]
> [Power BI ile bağlanma](knowledge-store-connect-power-bi.md)

Bu alıştırmayı yinelemek veya farklı bir AI zenginleştirme Kılavuzu denemek istiyorsanız, **otel-incelemeler-ıdxr** Dizin oluşturucuyu silin. Dizin oluşturucunun silinmesi, ücretsiz günlük işlem sayacını sıfıra sıfırlar.
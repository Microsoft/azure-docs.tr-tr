---
title: "Öğretici: Azure Blob 'ları üzerinde Python ve AI"
titleSuffix: Azure Cognitive Search
description: Bir Jupyter Python Not defteri ve Azure Bilişsel Arama REST API 'Lerini kullanarak blob depolamada içerik üzerinde metin ayıklama ve doğal dil işleme örneğini adım adım yapın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788098"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Öğretici: Azure Bloblarından aranabilir içerik oluşturmak için Python ve AI kullanma

Azure Blob depolamada yapılandırılmamış metin veya görüntü varsa, bir [AI zenginleştirme işlem hattı](cognitive-search-concept-intro.md) bilgileri ayıklayabilir ve tam metin araması veya bilgi araştırma senaryoları için faydalı yeni içerik oluşturabilir. Bir işlem hattı resimleri işleyebilir, ancak sorgular, modeller ve filtrelerde kullanabileceğiniz yeni alanlar oluşturmak için, bu Python öğreticisi metne odaklanır, dil algılama ve doğal dil işleme uygulanıyor.

Bu öğretici aşağıdaki görevleri gerçekleştirmek için Python ve [arama REST API 'lerini](/rest/api/searchservice/) kullanır:

> [!div class="checklist"]
> * Azure Blob depolamada PDF, HTML, DOCX ve PPTX gibi tüm belgeler (yapılandırılmamış metin) ile başlayın.
> * Metin çıkaran, dili algılayan, varlıkları tanıyan ve anahtar tümceleri algılayan bir işlem hattı tanımlayın.
> * Çıktıyı depolamak için bir dizin tanımlayın (ham içerik ve ardışık düzen tarafından oluşturulan ad-değer çiftleri).
> * Dönüşümleri ve Analizi başlatmak ve dizini oluşturmak ve yüklemek için işlem hattını yürütün.
> * Tam metin aramasını ve zengin sorgu söz dizimini kullanarak sonuçları keşfedebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) açın.

## <a name="prerequisites"></a>Önkoşullar

+ [Azure Depolama](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ [Mevcut bir arama hizmeti](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [oluşturun](search-create-service-portal.md) veya bulun 

> [!Note]
> Bu öğretici için ücretsiz hizmeti kullanabilirsiniz. Ücretsiz arama hizmeti, sizi üç Dizin, üç Dizin Oluşturucu ve üç veri kaynağı ile sınırlandırır. Bu öğreticide hepsinden birer tane oluşturulur. Başlamadan önce, hizmetinize yeni kaynakları kabul etmek için yeriniz olduğundan emin olun.

## <a name="download-files"></a>Dosyaları indirme

1. Bu [OneDrive klasörünü](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) açın ve sol üst köşedeki dosyaları bilgisayarınıza kopyalamak için **İndir** ' e tıklayın. 

1. ZIP dosyasına sağ tıklayın ve **Tümünü Ayıkla**' yı seçin. Çeşitli türlerde 14 dosya vardır. Bu alıştırma için 7 kullanacaksınız.

## <a name="1---create-services"></a>1-hizmet oluşturma

Bu öğreticide dizin oluşturma ve sorgular, AI zenginleştirme için arka uçta bilişsel hizmetler ve verileri sağlamak üzere Azure Blob depolama için Azure Bilişsel Arama kullanılmaktadır. Bu öğretici, bilişsel hizmetler 'de her gün Dizin Oluşturucu başına 20 işlem için ücretsiz ayırma kapsamında kalır, bu nedenle yalnızca oluşturmanız gereken hizmetler arama ve depolama amaçlıdır.

Mümkünse, yakınlık ve yönetilebilirlik için aynı bölgede ve kaynak grubunda her ikisini de oluşturun. Uygulamada, Azure depolama hesabınız herhangi bir bölgede olabilir.

### <a name="start-with-azure-storage"></a>Azure Storage 'ı kullanmaya başlama

1. [Azure Portal oturum açın](https://portal.azure.com/) ve **+ kaynak oluştur**' a tıklayın.

1. *Depolama hesabı* araması yapın ve Microsoft 'un depolama hesabı teklifi ' ni seçin.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Depolama hesabı oluştur" border="false":::

1. Temel bilgiler sekmesinde, aşağıdaki öğeler gereklidir. Diğer her şey için varsayılanları kabul edin.

   + **Kaynak grubu**. Mevcut bir tane seçin veya yeni bir tane oluşturun, ancak bunları topluca yönetebilmeniz için tüm hizmetler için aynı grubu kullanın.

   + **Depolama hesabı adı**. Aynı türde birden fazla kaynağınız olabileceğini düşünüyorsanız, tür ve bölgeye göre belirsizliği ortadan kaldırmak için adı kullanın, örneğin *blobstoragewestus*. 

   + **Konum**. Mümkünse, Azure Bilişsel Arama ve bilişsel hizmetler için kullanılan aynı konumu seçin. Tek bir konum, bant genişliği ücretlerini oylar.

   + **Hesap türü**. Varsayılan, *StorageV2 (genel amaçlı v2)* seçeneğini belirleyin.

1. Hizmeti oluşturmak için **gözden geçir + oluştur** ' a tıklayın.

1. Oluşturulduktan sonra genel bakış sayfasını açmak için **Kaynağa Git** ' e tıklayın.

1. **Bloblar** hizmeti ' ne tıklayın.

1. Bir kapsayıcı oluşturmak ve *COG-Search-demo* olarak adlandırmak Için **+ kapsayıcı** ' ya tıklayın.

1. *COG-Search-demo* ' i seçin ve ardından yükleme dosyalarını kaydettiğiniz klasörü açmak Için **karşıya yükle** ' ye tıklayın. Görüntü olmayan dosyaların tümünü seçin. 7 dosyasına sahip olmanız gerekir. Karşıya yüklemek için **Tamam** 'ı tıklatın.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Örnek dosyaları karşıya yükle" border="false":::

1. Azure depolama alanını kapatmadan önce Azure Bilişsel Arama bir bağlantıyı formülleştirmek için bir bağlantı dizesi alın. 

   1. Depolama hesabınızın genel bakış sayfasına geri gidin (örnek olarak *blobstragewestus* kullandık). 
   
   1. Sol gezinti bölmesinde **erişim anahtarları** ' nı seçin ve bağlantı dizelerinden birini kopyalayın. 

   Bağlantı dizesi, aşağıdaki örneğe benzer bir URL 'dir:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Bağlantı dizesini Not defteri 'ne kaydedin. Daha sonra veri kaynağı bağlantısını ayarlarken gerekecektir.

### <a name="cognitive-services"></a>Bilişsel Hizmetler

AI zenginleştirme, doğal dil ve görüntü işleme için Metin Analizi ve Görüntü İşleme dahil bilişsel hizmetler tarafından desteklenir. Amacınız gerçek bir prototipi veya projeyi tamamlayacaksa, bu noktada bilişsel hizmetler sağlama (Azure Bilişsel Arama ile aynı bölgede), böylece dizin oluşturma işlemlerine iliştirebilirsiniz.

Bu öğretici yalnızca 7 işlem kullandığından, Azure Bilişsel Arama, dizin oluşturucunun çalışması başına 20 ücretsiz işlem için bilişsel hizmetlere bağlanabildiğinden kaynak sağlamayı atlayabilirsiniz. Ücretsiz ayırma yeterlidir. Daha büyük projeler için, Kullandıkça öde, bu hizmetleri, Kullandıkça öde, bu hizmetler için sağlama bölümüne planlayın. Daha fazla bilgi için bkz. bilişsel [Hizmetler iliştirme](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

Üçüncü bileşen, [portalda oluşturabileceğiniz](search-create-service-portal.md)Azure bilişsel arama. Bu izlenecek adımları gerçekleştirmek için ücretsiz katmanı kullanabilirsiniz. 

Azure Blob depolamada olduğu gibi, erişim anahtarını toplamak için biraz zaman ayırın. Ayrıca, istekleri raporlamaya başladığınızda, her bir isteğin kimliğini doğrulamak için kullanılan uç nokta ve yönetici API 'si anahtarını sağlamanız gerekir.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Bilişsel Arama yönelik bir yönetici API anahtarı ve URL 'SI alın

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetinize **genel bakış** sayfasında, arama hizmetinizin adını alın. Uç nokta URL 'sini inceleyerek hizmet adınızı doğrulayabilirsiniz. Uç nokta URL 'niz olsaydı `https://mydemo.search.windows.net` , hizmet adınız olur `mydemo` .

2. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   Sorgu anahtarını da alın. Salt okuma erişimiyle sorgu istekleri vermek en iyi uygulamadır.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Hizmet adı ve yönetici ve sorgu anahtarlarını alın" border="false":::

Tüm istekler, hizmetinize gönderilen her isteğin üstbilgisinde bir API anahtarı gerektirir. Geçerli bir anahtar, istek başına, isteği gönderen uygulama ve onu işleyen hizmet arasında güven oluşturur.

## <a name="2---start-a-notebook"></a>2-bir not defteri başlatma

Aşağıdaki yönergeleri kullanarak not defterini oluşturun veya [Azure-Search-Python-Samples deposundan](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)tamamlanmış bir not defteri indirin.

Jupyter Notebook başlatmak ve yeni bir Python 3 Not defteri oluşturmak için Anaconda gezginini kullanın.

Not defterinizde, JSON ile çalışmak için kullanılan kitaplıkları yüklemek ve HTTP isteklerini formüle eklemek için bu betiği çalıştırın.

```python
import json
import requests
from pprint import pprint
```

Aynı not defterinde veri kaynağı, dizin, Dizin Oluşturucu ve beceri için adları tanımlayın. Bu öğreticinin adlarını ayarlamak için bu betiği çalıştırın.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

Aşağıdaki komut dosyasında, arama hizmetinizin (-SEARCH-SERVICE-NAME) ve yönetici API anahtarınızın (-ADMIN-API-KEY) yer tutucuları yerine koyun ve ardından arama hizmeti uç noktasını ayarlamak için çalıştırın.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3-işlem hattını oluşturma

Azure Bilişsel Arama 'de, dizin oluşturma (veya veri alımı) sırasında AI işleme oluşur. İzlenecek yol bu kısmı dört nesne oluşturur: veri kaynağı, Dizin tanımı, Beceri, Dizin Oluşturucu. 

### <a name="step-1-create-a-data-source"></a>1. Adım: Veri kaynağı oluşturma

[Veri kaynağı nesnesi](/rest/api/searchservice/create-data-source) , dosyaları içeren blob kapsayıcısına bağlantı dizesi sağlar.

Aşağıdaki betikte,-BLOB-RESOURCE-CONNECTION-STRING yer tutucusunu, önceki adımda oluşturduğunuz Blobun bağlantı dizesiyle değiştirin. Kapsayıcının yer tutucu metnini değiştirin. Ardından, adlı bir veri kaynağı oluşturmak için betiği çalıştırın `cogsrch-py-datasource` .

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

İstek, başarıyı onaylayan 201 durum kodunu döndürmelidir.

Azure portal, arama hizmeti panosu sayfasında, cogsrch-Kopyala-DataSource ' ın **veri kaynakları** listesinde göründüğünü doğrulayın. Sayfayı güncelleştirmek için **Yenile** ' ye tıklayın.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="Portalda veri kaynakları kutucuğu" border="false":::

### <a name="step-2-create-a-skillset"></a>2. Adım: beceri oluşturma

Bu adımda, verilerinize uygulamak için bir dizi zenginleştirme adımı tanımlayacaksınız. Her zenginleştirme adımını *beceri* olarak ve zenginleştirme adımları kümesini de *beceri kümesi* olarak adlandırabilirsiniz. Bu öğretici, beceri için yerleşik bilişsel [becerileri](cognitive-search-predefined-skills.md) kullanır:

+ Blob kapsayıcısında bulunan içerikten kuruluşların adlarını ayıklamak için [varlık tanıma](cognitive-search-skill-entity-recognition.md) .

+ İçeriğin dilini tanımlamak için [Dil Algılama](cognitive-search-skill-language-detection.md).

+ Anahtar tümcecik ayıklama becerisini çağırmadan önce büyük içeriği daha küçük öbeklere ayırmak için [Metni Böl](cognitive-search-skill-textsplit.md). Anahtar tümcecik ayıklama, 50.000 veya daha az karakterden oluşan girişi kabul eder. Bu sınıra uymak için örnek dosyaların birkaç tanesinin bölünmesi gerekir.

+ Üst anahtar tümcecikleri çekmek için [Anahtar İfade Ayıklama](cognitive-search-skill-keyphrases.md). 

Çağrılan bir beceri oluşturmak için aşağıdaki betiği çalıştırın `cogsrch-py-skillset` .

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

İstek, başarıyı onaylayan 201 durum kodunu döndürmelidir.

Her sayfa için anahtar tümceciği ayıklama yeteneği uygulanır. Bağlamını olarak ayarlayarak `"document/pages/*"` , belge/sayfalar dizisinin her bir üyesi (belgedeki her sayfa için) için bu zengini daha zengin çalıştırırsınız.

Her beceri, belge içeriğinde yürütülür. İşlem sırasında Azure Bilişsel Arama, farklı dosya biçimlerinden içerik okumak için her belgeyi ister. Kaynak dosyada bulunan metin `content` , her belge için bir alana yerleştirilir. Bu nedenle, girişi olarak ayarlayın `"/document/content"` .

Beceri kümesinin grafiksel gösterimi aşağıda gösterilmektedir.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Beceri anlayın" border="false":::

Çıktılar, bir aşağı akış beceriye giriş olarak kullanılan bir dizine veya her ikisi de dil kodu ile ilgili olduğu gibi, her iki durumda da eşleştirilebilir. Dizinde bir dil kodu, filtreleme için yararlıdır. Giriş olarak dil kodu, sözcük bölünmesiyle ilgili dilbilgisi kurallarını bildirmek için metin analizi becerileri tarafından kullanılır.

Beceri kümesi temelleri hakkında daha fazla bilgi için bkz. [Beceri kümesini tanımlama](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>3. Adım: Dizin oluşturma

Bu bölümde, aranabilir dizine dahil edilecek alanları belirterek ve her bir alan için arama özniteliklerini ayarlayarak Dizin şemasını tanımlarsınız. Alanlar bir türe sahiptir ve alanın nasıl kullanıldığını (aranabilir, sıralanabilir vb.) belirleyen öznitelikleri alabilir. Bir dizindeki alan adlarının, kaynaktaki alan adlarıyla tamamen aynı olması gerekmez. Sonraki bir adımda, kaynak-hedef alanlarını bağlamak için dizin oluşturucuda alan eşlemeleri eklersiniz. Bu adım için, arama uygulamanızla ilgili alan adlandırma kurallarını kullanarak dizini tanımlayın.

Bu çalışmada aşağıdaki alanlar ve alan türleri kullanılır:

| alan adları: | ID         | içerik   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Adlı dizini oluşturmak için bu betiği çalıştırın `cogsrch-py-index` .

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

İstek, başarıyı onaylayan 201 durum kodunu döndürmelidir.

Dizin tanımlama hakkında daha fazla bilgi edinmek için bkz. [Dizin oluşturma (Azure Bilişsel Arama REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>4. Adım: Dizin Oluşturucu oluşturma ve çalıştırma

[Dizin Oluşturucu](/rest/api/searchservice/create-indexer) , işlem hattını sürücüler. Şimdiye kadar oluşturduğunuz üç bileşen (veri kaynağı, Beceri, dizin) bir dizin oluşturucunun girdileri olur. Azure Bilişsel Arama dizin oluşturucunun oluşturulması, tüm işlem hattının hareket halinde yer aldığı olaydır. 

Bu nesneleri bir dizin oluşturucuda birlikte bağlamak için alan eşlemelerini tanımlamanız gerekir.

+ , `"fieldMappings"` Beceri önce işlenir, kaynak alanları veri kaynağından bir dizindeki hedef alanlara eşlenir. Alan adları ve türleri her iki uçta da aynıysa, hiçbir eşleme gerekmez.

+ , `"outputFieldMappings"` Beceri sonrasında işlenir ve `"sourceFieldNames"` belge çözme veya zenginleştirme tarafından oluşturuluncaya kadar mevcut değildir. , `"targetFieldName"` Dizindeki bir alandır.

Çıkışlara girişlerin takılmasının yanı sıra, veri yapılarını düzleştirmek için alan eşlemelerini de kullanabilirsiniz. Daha fazla bilgi için bkz. [zenginleştirilmiş alanları aranabilir bir dizine eşleme](cognitive-search-output-field-mapping.md).

Adlı bir dizin oluşturucu oluşturmak için bu betiği çalıştırın `cogsrch-py-indexer` .

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

İstek yakında 201 durum kodunu döndürmelidir, ancak işlemin tamamlanması birkaç dakika sürebilir. Veri kümesi küçük olsa da, görüntü analizi gibi analitik yetenekler, hesaplama açısından yoğun ve zaman alabilir.

Dizin oluşturucunun ne zaman çalıştığını veya bittiğini anlamak için [Dizin Oluşturucu durumunu izleyebilirsiniz](#check-indexer-status) .

> [!TIP]
> Bir dizin oluşturucu oluşturulduğunda, işlem hattı çağrılır. Verilere erişim, girişleri ve çıkışları eşleme veya işlem sırası ile ilgili bir sorun varsa, bu aşamada görüntülenir. İşlem hattını kod veya betik değişiklikleriyle yeniden çalıştırmak için önce nesneleri silmeniz gerekebilir. Daha fazla bilgi için bkz. [Sıfırlama ve yeniden çalıştırma](#reset).

#### <a name="about-the-request-body"></a>İstek gövdesi hakkında

Betik, `"maxFailedItems"` değerini -1 olarak ayarlayarak dizin oluşturma motoruna, veri içeri aktarma sırasında hataları yoksaymasını bildirir. Demo veri kaynağında çok az belge olduğundan bu yararlıdır. Daha büyük bir veri kaynağı için değeri, 0’dan daha büyük bir değere ayarlarsınız.

Ayrıca yapılandırma parametrelerinde `"dataToExtract":"contentAndMetadata"` deyimine de dikkat edin. Bu ifade, Dizin oluşturucudan içeriği farklı dosya biçimlerinden ve her bir dosyayla ilgili meta verilerin ayıklanmasını söyler.

İçerik ayıklandığında, veri kaynağında bulunan görüntülerden metni ayıklamak için `imageAction` değerini ayarlayabilirsiniz. `"imageAction":"generateNormalizedImages"`Yapılandırma, OCR becerisi ve metin birleştirme beceriyle birlikte kullanıldığında, dizin oluşturucunun görüntülerden metin ayıklamasını söyler (örneğin, trafiğin "Durdur" sözcüğünün oturum açmasını durdurur) ve içerik alanının bir parçası olarak katıştırmasını söyler. Bu davranış, belgelerde (PDF içindeki bir görüntünün bir görüntüsünü düşünün) katıştırılmış görüntüler ve veri kaynağında bulunan görüntüler (örneğin, bir JPG dosyası) için geçerlidir.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4-dizin oluşturmayı izleme

Dizin oluşturucu tanımlandıktan sonra, isteği gönderdiğinizde otomatik olarak çalıştırılır. Tanımladığınız bilişsel becerilere bağlı olarak dizin oluşturma beklediğinizden uzun sürebilir. Dizin Oluşturucu işlemenin tamamlanıp tamamlanmadığını öğrenmek için aşağıdaki betiği çalıştırın.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Yanıtta, `"lastResult"` `"status"` ve `"endTime"` değerlerini izleyin. Durumu denetlemek için düzenli aralıklarla betiği çalıştırın. Dizin Oluşturucu tamamlandığında, durum "başarılı" olarak ayarlanır, "BitişZamanı" belirtilir ve yanıt, zenginleştirme sırasında oluşan tüm hataları ve uyarıları içerecektir.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="Dizin Oluşturucu oluşturuldu" border="false":::

Uyarılar bazı kaynak dosya ve beceri birleşimleri için geneldir ve her zaman bir sorunu belirtmez. Birçok uyarı zararsız. Örneğin, metin içermeyen bir JPEG dosyasını dizinlemek istiyorsanız bu ekran görüntüsünde uyarıyı görürsünüz.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Örnek Dizin Oluşturucu uyarısı" border="false":::

## <a name="5---search"></a>5-arama

Dizin oluşturma işlemi tamamlandıktan sonra tek tek alanların içeriklerini döndüren sorgular çalıştırın. Azure Bilişsel Arama, varsayılan olarak ilk 50 sonucunu döndürür. Varsayılan değerin düzgün çalışması için örnek veriler küçüktür. Ancak, büyük veri kümeleriyle çalışırken, daha fazla sonuç döndürmek için sorgu dizesine parametreleri dahil etmeniz gerekebilir. Yönergeler için bkz. [Azure bilişsel arama 'daki sayfa sonuçları](search-pagination-page-layout.md).

Doğrulama adımı olarak, tüm alanları gösteren Dizin tanımını alın.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Sonuçlar aşağıdaki örneğe benzer görünmelidir. Ekran görüntüsü yalnızca yanıtın bir parçasını gösterir.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Tüm alanlar için sorgu dizini" border="false":::

Çıktı, her bir alanın adını, türünü ve özniteliklerini içeren dizin şemasıdır.

`organizations` gibi tek bir alanın tüm içeriklerini döndürmek için ikinci bir `"*"` sorgusu gönderin.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Sonuçlar aşağıdaki örneğe benzer görünmelidir. Ekran görüntüsü yalnızca yanıtın bir parçasını gösterir.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Kuruluşların içeriği için sorgu dizini" border="false":::

Ek alanlar için yineleyin: `content` , `languageCode` , `keyPhrases` , ve `organizations` Bu alıştırmada. Virgülle ayrılmış bir liste kullanarak `$select` aracılığıyla birden fazla alan döndürebilirsiniz.

Sorgu dizesinin karmaşıklık ve uzunluğuna bağlı olarak GET veya POST dizesini kullanabilirsiniz. Daha fazla bilgi için bkz. [REST API kullanarak sorgu](/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

Geliştirmede erken deneysel aşamalarda, tasarım yinelemesi için en pratik yaklaşım, nesneleri Azure Bilişsel Arama silmek ve kodunuzun bunları yeniden oluşturması için izin verir. Kaynak adları benzersizdir. Bir nesneyi sildiğinizde, aynı adı kullanarak nesneyi yeniden oluşturabilirsiniz.

Dizinleri, Dizin oluşturucuyu, veri kaynaklarını ve becerileri silmek için portalı kullanabilirsiniz. Dizin oluşturucuyu sildiğinizde, isteğe bağlı olarak, dizini, beceri ve veri kaynağını aynı anda seçmeli olarak silebilirsiniz.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Portalda arama nesnelerini silme" border="false":::

Ayrıca, bir komut dosyası kullanarak da silebilirsiniz. Aşağıdaki betik, bir beceri nasıl silineceğini gösterir. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Silme işlemi başarılı olduğunda durum kodu 204 döndürülür.

## <a name="takeaways"></a>Paketler

Bu öğreticide, veri kaynağı, beceri kümesi, dizin ve dizin oluşturucu gibi bileşen parçalarının oluşturulması yoluyla zenginleştirilmiş bir dizin oluşturma işlem hattı oluşturmaya yönelik temel adımlar gösterilmektedir.

[Yerleşik yetenekler](cognitive-search-predefined-skills.md) , Beceri tanımları ve girişler ve çıktılar aracılığıyla becerileri zincirlemek için bir yol sunulmuştur. Ayrıca, `outputFieldMappings` bir Azure bilişsel arama hizmetinde işlem hattındaki aranabilir bir dizine yönlendirme değerlerini yönlendirme için Dizin Oluşturucu tanımında gerekli olduğunu öğrenmiş olursunuz.

Son olarak, sonuçları test etme ve daha fazla yineleme için sistemi sıfırlama hakkında öğrenirsiniz. Dizine karşı sorgular düzenlendiğinde, zenginleştirilmiş dizin oluşturma işlem hattı tarafından oluşturulan çıktının döndürüldüğünü öğrendiniz. Bu yayında, iç yapıları (sistem tarafından oluşturulan zenginleştirilmiş belgeler) görüntüleme mekanizması vardır. Ayrıca, bir işlem hattını yeniden çalıştırmadan önce Dizin Oluşturucu durumunu ve hangi nesnelerin silineceğini de öğrenirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir projenin sonunda kendi aboneliğinizde çalışırken, artık ihtiyaç duyulmadığınızda kaynakları kaldırmak iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki tüm kaynaklar veya kaynak grupları bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir AI zenginleştirme işlem hattındaki tüm nesneler hakkında bilgi sahibi olduğunuza göre, Beceri tanımlarına ve bireysel becerilere daha yakından göz atalım.

> [!div class="nextstepaction"]
> [Beceri oluşturma](cognitive-search-defining-skillset.md)
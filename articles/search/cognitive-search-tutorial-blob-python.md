---
title: "Öğretici: REST API 'Leri kullanarak Python 'da beceri oluşturma"
titleSuffix: Azure Cognitive Search
description: Bir Jupyter Python Not defteri kullanarak Azure Bilişsel Arama veri ayıklama, doğal dil ve görüntü AI işleme örneğini adım adım yapın. Ayıklanan veriler, sorgu tarafından dizine alınır ve kolayca erişilir.
manager: nitinme
author: LisaLeib
ms.author: v-lilei
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 1e404998c8f49852248a754e7134f439dcdf5b04
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113686"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>Öğretici: REST ve Python kullanarak bir AI zenginleştirme işlem hattı oluşturma

Bu öğreticide, bilişsel *becerileri*kullanarak Azure bilişsel arama veri zenginleştirmesini programlama mekanizması öğrenirsiniz. Yetenekler, bilişsel hizmetler 'de doğal dil işleme (NLP) ve görüntü analizi özellikleri tarafından desteklenir. Beceri kompozisyonu ve yapılandırması aracılığıyla bir görüntünün veya taranan belge dosyasının metin ve metin gösterimlerini ayıklayabilirsiniz. Ayrıca dili, varlıkları, anahtar tümcecikleri ve daha fazlasını tespit edebilirsiniz. Sonuç, bir dizin oluşturma işlem hattında AI zenginleştirmelerinin oluşturulduğu bir arama dizininde zengin ek içeriğe sahiptir. 

Bu öğreticide, aşağıdaki görevleri yapmak için Python kullanacaksınız:

> [!div class="checklist"]
> * Bir dizine giden yolda örnek verileri zenginleştiren dizin oluşturma işlem hattı oluşturma
> * Yerleşik becerileri uygulama: varlık tanıma, dil algılama, metin işleme, anahtar tümcecik ayıklama
> * Bir beceri kümesindeki çıktılara girişleri eşleyerek becerilerin nasıl birbirine zincirleneceğini öğrenme
> * İstekleri yürütme ve sonuçları gözden geçirme
> * Daha fazla geliştirme için dizini ve dizin oluşturucuları sıfırlama

Çıktı, Azure Bilişsel Arama 'te tam metin aranabilir bir dizindir. [Eş anlamlılar](search-synonyms.md), [puanlama profilleri](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [çözümleyiciler](search-analyzers.md) ve [filtreler](search-filters.md) gibi diğer standart özelliklerle dizini geliştirebilirsiniz. 

Bu öğretici ücretsiz hizmette çalışır, ancak ücretsiz işlem sayısı günde 20 belge ile sınırlıdır. Bu öğreticiyi aynı günde birden çok kez çalıştırmak istiyorsanız, daha fazla çalıştırmaya uyabilmeniz için daha küçük bir dosya kümesi kullanın.

> [!NOTE]
> İşlem sıklığını artırarak, daha fazla belge ekleyerek veya daha fazla AI algoritması ekleyerek kapsamı genişlettikten sonra faturalandırılabilir bilişsel [Hizmetler kaynağı](cognitive-search-attach-cognitive-services.md)eklemeniz gerekir. Bilişsel hizmetlerde API 'Leri çağırırken ve Azure Bilişsel Arama belge çözme aşamasının bir parçası olarak görüntü ayıklama için ücretler tahakkuk eder. Belgelerden metin ayıklama için herhangi bir ücret alınmaz.
>
> Yerleşik yeteneklerin yürütülmesi, mevcut bilişsel [Hizmetler Kullandıkça Öde fiyatı](https://azure.microsoft.com/pricing/details/cognitive-services/)üzerinden ücretlendirilir. Görüntü ayıklama fiyatlandırması, [Azure bilişsel arama fiyatlandırma sayfasında](https://go.microsoft.com/fwlink/?linkid=2042400)açıklanmaktadır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki hizmetler, Araçlar ve veriler bu öğreticide kullanılır. 

+ Örnek verileri depolamak için [bir Azure depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) . Depolama hesabının Azure Bilişsel Arama ile aynı bölgede bulunduğundan emin olun.

+ Python 3. x ve Jupyıter not defterlerini sağlayan [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section).

+ [Örnek veriler](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) , küçük bir dosya kümesi farklı türlerden oluşur. 

+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu öğretici için ücretsiz bir hizmet kullanabilirsiniz.

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

Azure Bilişsel Arama hizmetinize etkileşimde bulunmak için hizmet URL 'SI ve erişim anahtarı gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-postman/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. Geçerli bir anahtar, istek başına, isteği gönderen uygulama ve onu işleyen hizmet arasında güven oluşturur.

## <a name="prepare-sample-data"></a>Örnek verileri hazırlama

Zenginleştirme işlem hattı, Azure veri kaynaklarından çekme işlemi yapar. Kaynak veriler, [Azure bilişsel arama dizin oluşturucunun](search-indexer-overview.md)desteklenen bir veri kaynağı türünden kaynaklanmalıdır. Bu alıştırmada, birden çok içerik türünü göstermek için blob depolama kullanırız.

1. [Azure Portal oturum açın](https://portal.azure.com), Azure depolama hesabınıza gidin, **Bloblar**' a tıklayın ve ardından **+ Container**' a tıklayın.

1. Örnek veri içeren [bir blob kapsayıcısı oluşturun](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) . Ortak erişim düzeyini geçerli değerlerinden herhangi birine ayarlayabilirsiniz.

1. Kapsayıcı oluşturulduktan sonra dosyayı açın ve önceki bir adımda indirdiğiniz örnek dosyaları karşıya yüklemek için komut çubuğunda **karşıya yükle** ' yi seçin.

   ![Azure blob depolamadaki kaynak dosyalar](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Örnek dosyalar yüklendikten sonra Blob depolamanız için bir bağlantı dizesi ve kapsayıcı adını alın. Azure portalda depolama hesabınıza giderek bunu yapabilirsiniz. **Erişim anahtarları**' na tıklayın ve ardından **bağlantı dizesi** alanını kopyalayın.

Bağlantı dizesinde şu biçim olacak: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Bağlantı dizesini kullanışlı tutun. Bu, gelecekteki bir adımda gerekecektir.

Paylaşılan erişim imzası sağlama gibi, bağlantı dizesini belirtmenin başka birçok yolu vardır. Veri kaynağı kimlik bilgileri hakkında daha fazla bilgi edinmek için bkz. [Azure Blob Depolama Alanı dizinini oluşturma](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Jupyter not defteri oluşturma

> [!Note]
> Bu makalede, bir dizi Python komut dosyası kullanarak bir veri kaynağı, dizin, Dizin Oluşturucu ve beceri oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir. Tam not defteri örneğini indirmek için [Azure-Search-Python-Samples depoya](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook)gidin.

Jupyter Notebook başlatmak ve yeni bir Python 3 Not defteri oluşturmak için Anaconda gezginini kullanın.

## <a name="connect-to-azure-cognitive-search"></a>Azure Bilişsel Arama bağlanma

Not defterinizde, JSON ile çalışmak için kullanılan kitaplıkları yüklemek ve HTTP isteklerini formüle eklemek için bu betiği çalıştırın.

```python
import json
import requests
from pprint import pprint
```

Ardından, veri kaynağı, dizin, Dizin Oluşturucu ve beceri için adları tanımlayın. Bu öğreticinin adlarını ayarlamak için bu betiği çalıştırın.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Ücretsiz bir hizmette, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olursunuz. Bu öğreticide hepsinden birer tane oluşturulur. Daha devam etmeden önce yeni nesneler oluşturmak için yeriniz olduğundan emin olun.

Aşağıdaki komut dosyasında, arama hizmetinizin (-SEARCH-SERVICE-NAME) ve yönetici API anahtarınızın (-ADMIN-API-KEY) yer tutucuları yerine koyun ve ardından arama hizmeti uç noktasını ayarlamak için çalıştırın.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Veri kaynağı oluşturma

Hizmetleriniz ve kaynak dosyalarınız hazırlandığına göre şimdi dizin oluşturma işlem hattınızın bileşenlerini derlemeye başlayabilirsiniz. Azure Bilişsel Arama dış kaynak verilerinin nasıl alınacağını bildiren bir veri kaynağı nesnesi ile başlayın.

Aşağıdaki betikte,-BLOB-RESOURCE-CONNECTION-STRING yer tutucusunu, önceki adımda oluşturduğunuz Blobun bağlantı dizesiyle değiştirin. Sonra, `cogsrch-py-datasource`adlı bir veri kaynağı oluşturmak için betiği çalıştırın.

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
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

İstek, başarıyı onaylayan 201 durum kodunu döndürmelidir.

Azure portal, arama hizmeti panosu sayfasında, cogsrch-Kopyala-DataSource ' ın **veri kaynakları** listesinde göründüğünü doğrulayın. Sayfayı güncelleştirmek için **Yenile** ' ye tıklayın.

![Portalda veri kaynakları kutucuğu](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Portalda veri kaynakları kutucuğu")

## <a name="create-a-skillset"></a>Beceri kümesi oluşturma

Bu adımda, verilerinize uygulamak için bir dizi zenginleştirme adımı tanımlayacaksınız. Her zenginleştirme adımını *beceri* olarak ve zenginleştirme adımları kümesini de *beceri kümesi* olarak adlandırabilirsiniz. Bu öğretici, beceri için yerleşik bilişsel [becerileri](cognitive-search-predefined-skills.md) kullanır:

+ İçeriğin dilini tanımlamak için [Dil Algılama](cognitive-search-skill-language-detection.md).

+ Anahtar tümcecik ayıklama becerisini çağırmadan önce büyük içeriği daha küçük öbeklere ayırmak için [Metni Böl](cognitive-search-skill-textsplit.md). Anahtar tümcecik ayıklama, 50.000 veya daha az karakterden oluşan girişi kabul eder. Bu sınıra uymak için örnek dosyaların birkaç tanesinin bölünmesi gerekir.

+ Blob kapsayıcısında bulunan içerikten kuruluşların adlarını ayıklamak için [varlık tanıma](cognitive-search-skill-entity-recognition.md) .

+ Üst anahtar tümcecikleri çekmek için [Anahtar İfade Ayıklama](cognitive-search-skill-keyphrases.md). 

### <a name="python-script"></a>Python betiği
`cogsrch-py-skillset`adlı bir beceri oluşturmak için aşağıdaki betiği çalıştırın.

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
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
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
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
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

Her sayfa için anahtar tümceciği ayıklama yeteneği uygulanır. Bağlamı `"document/pages/*"`olarak ayarlayarak, belge/sayfalar dizisinin her bir üyesi (belgedeki her sayfa için) için bu zengini daha zengin çalıştırırsınız.

Her beceri, belge içeriğinde yürütülür. İşlem sırasında Azure Bilişsel Arama, farklı dosya biçimlerinden içerik okumak için her belgeyi ister. Kaynak dosyada bulunan metin, her belge için bir `content` alanına yerleştirilir. Bu nedenle, girişi `"/document/content"`olarak ayarlayın.

Beceri kümesinin grafiksel gösterimi aşağıda gösterilmektedir.

![Beceri anlayın](media/cognitive-search-tutorial-blob/skillset.png "Beceri anlayın")

Çıktılar, bir aşağı akış beceriye giriş olarak kullanılan bir dizine veya her ikisi de dil kodu ile ilgili olduğu gibi, her iki durumda da eşleştirilebilir. Dizinde bir dil kodu, filtreleme için yararlıdır. Giriş olarak dil kodu, sözcük bölünmesiyle ilgili dilbilgisi kurallarını bildirmek için metin analizi becerileri tarafından kullanılır.

Beceri kümesi temelleri hakkında daha fazla bilgi için bkz. [Beceri kümesini tanımlama](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Dizin oluşturma

Bu bölümde, aranabilir dizine dahil edilecek alanları belirterek ve her bir alan için arama özniteliklerini ayarlayarak Dizin şemasını tanımlarsınız. Alanlar bir türe sahiptir ve alanın nasıl kullanıldığını (aranabilir, sıralanabilir vb.) belirleyen öznitelikleri alabilir. Bir dizindeki alan adlarının, kaynaktaki alan adlarıyla tamamen aynı olması gerekmez. Sonraki bir adımda, kaynak-hedef alanlarını bağlamak için dizin oluşturucuda alan eşlemeleri eklersiniz. Bu adım için, arama uygulamanızla ilgili alan adlandırma kurallarını kullanarak dizini tanımlayın.

Bu çalışmada aşağıdaki alanlar ve alan türleri kullanılır:

| alan adları: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

`cogsrch-py-index`adlı dizini oluşturmak için bu betiği çalıştırın.

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

Dizin tanımlama hakkında daha fazla bilgi edinmek için bkz. [Dizin oluşturma (Azure Bilişsel Arama REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Dizin oluşturucu oluşturma, alanları eşleme ve dönüştürmeler yürütme

Şimdiye kadar, bir veri kaynağı, beceri ve bir dizin oluşturdunuz. Bu üç bileşen, her bir parçayı birlikte tek bir çok aşamalı işleme çeken [dizin oluşturucunun](search-indexer-overview.md) parçası olur. Bu nesneleri bir dizin oluşturucuda birlikte bağlamak için alan eşlemelerini tanımlamanız gerekir.

+ FieldMappings, Beceri öğesinden önce işlenir, kaynak alanları veri kaynağından bir dizindeki hedef alanlara eşleniyor. Alan adları ve türleri her iki uçta da aynıysa, hiçbir eşleme gerekmez.

+ OutputFieldMappings, Beceri sonrasında işlenir ve belge çözme veya zenginleştirene kadar mevcut olmayan sourceFieldNames öğesine başvuruda bulunur. TargetFieldName, dizindeki bir alandır.

Çıkışlara girişlerin takılmasının yanı sıra, veri yapılarını düzleştirmek için alan eşlemelerini de kullanabilirsiniz. Daha fazla bilgi için bkz. [zenginleştirilmiş alanları aranabilir bir dizine eşleme](cognitive-search-output-field-mapping.md).

`cogsrch-py-indexer`adlı bir dizin oluşturucu oluşturmak için bu betiği çalıştırın.

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

İstek hızlı bir şekilde 201 durum kodu döndürmelidir, ancak işlemin tamamlanması birkaç dakika sürebilir. Veri kümesi küçük olsa da, görüntü analizi gibi analitik yetenekler, hesaplama açısından yoğun ve zaman alabilir.

Dizin Oluşturucu işleminin ne zaman tamamlandığını öğrenmek için sonraki bölümde [Dizin Oluşturucu durumunu denetle](#check-indexer-status) betiğini kullanın.

> [!TIP]
> Bir dizin oluşturucu oluşturulduğunda, işlem hattı çağrılır. Verilere erişim, girişleri ve çıkışları eşleme veya işlem sırası ile ilgili bir sorun varsa, bu aşamada görüntülenir. İşlem hattını kod veya betik değişiklikleriyle yeniden çalıştırmak için önce nesneleri silmeniz gerekebilir. Daha fazla bilgi için bkz. [Sıfırlama ve yeniden çalıştırma](#reset).

#### <a name="explore-the-request-body"></a>İstek gövdesini keşfetme

Betik, `"maxFailedItems"` değerini -1 olarak ayarlayarak dizin oluşturma motoruna, veri içeri aktarma sırasında hataları yoksaymasını bildirir. Demo veri kaynağında çok az belge olduğundan bu yararlıdır. Daha büyük bir veri kaynağı için değeri, 0’dan daha büyük bir değere ayarlarsınız.

Ayrıca yapılandırma parametrelerinde `"dataToExtract":"contentAndMetadata"` deyimine de dikkat edin. Bu ifade, Dizin oluşturucudan içeriği farklı dosya biçimlerinden ve her bir dosyayla ilgili meta verilerin ayıklanmasını söyler.

İçerik ayıklandığında, veri kaynağında bulunan görüntülerden metni ayıklamak için `imageAction` değerini ayarlayabilirsiniz. `"imageAction":"generateNormalizedImages"` yapılandırması, OCR becerisi ve metin birleştirme beceriyle birlikte kullanıldığında, dizin oluşturucunun görüntülerden metin ayıklamasını söyler (örneğin, bir trafiğin "Durdur" sözcüğünün oturum açmasını durdurur) ve içerik alanının bir parçası olarak katıştırmasını söyler. Bu davranış, belgelerde (PDF içindeki bir görüntünün bir görüntüsünü düşünün) katıştırılmış görüntüler ve veri kaynağında bulunan görüntüler (örneğin, bir JPG dosyası) için geçerlidir.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Dizin oluşturucu durumunu denetleme

Dizin oluşturucu tanımlandıktan sonra, isteği gönderdiğinizde otomatik olarak çalıştırılır. Tanımladığınız bilişsel becerilere bağlı olarak dizin oluşturma beklediğinizden uzun sürebilir. Dizin Oluşturucu işlemenin tamamlanıp tamamlanmadığını öğrenmek için aşağıdaki betiği çalıştırın.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Yanıtta, "durum" ve "BitişZamanı" değerleri için "lastResult" izleyin. Durumu denetlemek için düzenli aralıklarla betiği çalıştırın. Dizin Oluşturucu tamamlandığında, durum "başarılı" olarak ayarlanır, "BitişZamanı" belirtilir ve yanıt, zenginleştirme sırasında oluşan tüm hataları ve uyarıları içerecektir.

![Dizin Oluşturucu oluşturuldu](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Dizin Oluşturucu oluşturuldu")

Uyarılar bazı kaynak dosya ve beceri birleşimleri için geneldir ve her zaman bir sorunu belirtmez. Bu öğreticide, uyarılar zararsız. Örneğin, metin içermeyen JPEG dosyalarından biri bu ekran görüntüsünde uyarı gösterir.

![Örnek Dizin Oluşturucu uyarısı](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Örnek Dizin Oluşturucu uyarısı")

## <a name="query-your-index"></a>Dizininizi sorgulama

Dizin oluşturma işlemi tamamlandıktan sonra tek tek alanların içeriklerini döndüren sorgular çalıştırın. Azure Bilişsel Arama, varsayılan olarak ilk 50 sonucunu döndürür. Varsayılan değerin düzgün çalışması için örnek veriler küçüktür. Ancak, büyük veri kümeleriyle çalışırken, daha fazla sonuç döndürmek için sorgu dizesine parametreleri dahil etmeniz gerekebilir. Yönergeler için bkz. [Azure bilişsel arama 'daki sayfa sonuçları](search-pagination-page-layout.md).

Doğrulama adımı olarak, tüm alanlar için dizini sorgulayın.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Sonuçlar aşağıdaki örneğe benzer görünmelidir. Ekran görüntüsü yalnızca yanıtın bir parçasını gösterir.

![Tüm alanlar için sorgu dizini](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Tüm alanlar için dizini sorgula")

Çıktı, her bir alanın adını, türünü ve özniteliklerini içeren dizin şemasıdır.

`"*"` gibi tek bir alanın tüm içeriklerini döndürmek için ikinci bir `organizations` sorgusu gönderin.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Sonuçlar aşağıdaki örneğe benzer görünmelidir. Ekran görüntüsü yalnızca yanıtın bir parçasını gösterir.

![Kuruluşların içeriği için sorgu dizini](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Kuruluşların içeriğini döndürmek için dizini sorgulama")

Bu alıştırmada içerik, languageCode, keyPhrases ve kuruluşlar için yineleyin. Virgülle ayrılmış bir liste kullanarak `$select` aracılığıyla birden fazla alan döndürebilirsiniz.

Sorgu dizesinin karmaşıklık ve uzunluğuna bağlı olarak GET veya POST dizesini kullanabilirsiniz. Daha fazla bilgi için bkz. [REST API kullanarak sorgu](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

İşlem hattı geliştirmenin erken deneysel aşamalarında, tasarım yinelemeleri için en pratik yaklaşım, nesneleri Azure Bilişsel Arama silmek ve kodunuzun bunları yeniden oluşturması için izin verir. Kaynak adları benzersizdir. Bir nesneyi sildiğinizde, aynı adı kullanarak nesneyi yeniden oluşturabilirsiniz.

Yeni tanımlarla belgelerinizin yeniden dizinini oluşturmak için:

1. Dizini silerek kalıcı verileri kaldırın. Dizin oluşturucuyu silerek hizmetinizde yeniden oluşturun.
2. Beceri ve Dizin tanımlarını değiştirin.
3. İşlem hattını çalıştırmak için hizmet üzerinde yeniden bir dizin ve dizin oluşturucu oluşturun.

Dizinleri, Dizin oluşturucuyu ve becerileri silmek için portalı kullanabilirsiniz. Dizin oluşturucuyu sildiğinizde, isteğe bağlı olarak, dizini, beceri ve veri kaynağını aynı anda seçmeli olarak silebilirsiniz.

![Arama nesnelerini Sil](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Portalda arama nesnelerini silme")

Ayrıca, bir komut dosyası kullanarak da silebilirsiniz. Aşağıdaki betik oluşturduğumuz beceri silecek. Dizini, Dizin oluşturucuyu ve veri kaynağını silme isteğini kolayca değiştirebilirsiniz.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Kodunuz geliştikçe bir yeniden derleme stratejisini iyileştirmek isteyebilirsiniz. Daha fazla bilgi için bkz. [Yeniden dizin derleme](search-howto-reindex.md).

## <a name="takeaways"></a>Paketler

Bu öğreticide, veri kaynağı, beceri kümesi, dizin ve dizin oluşturucu gibi bileşen parçalarının oluşturulması yoluyla zenginleştirilmiş bir dizin oluşturma işlem hattı oluşturmaya yönelik temel adımlar gösterilmektedir.

[Yerleşik yetenekler](cognitive-search-predefined-skills.md) , Beceri tanımları ve girişler ve çıktılar aracılığıyla becerileri zincirlemek için bir yol sunulmuştur. Ayrıca, işlem hattı tanımındaki `outputFieldMappings`, işlem hattından bir Azure Bilişsel Arama hizmetinde aranabilir dizine yönlendirme için gerekli olduğunu öğrendiniz.

Son olarak, sonuçları test etme ve daha fazla yineleme için sistemi sıfırlama hakkında öğrenirsiniz. Dizine karşı sorgular düzenlendiğinde, zenginleştirilmiş dizin oluşturma işlem hattı tarafından oluşturulan çıktının döndürüldüğünü öğrendiniz. Bu yayında, iç yapıları (sistem tarafından oluşturulan zenginleştirilmiş belgeler) görüntüleme mekanizması vardır. Ayrıca, bir işlem hattını yeniden çalıştırmadan önce Dizin Oluşturucu durumunu ve hangi nesnelerin silineceğini de öğrenirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiden sonra temizlemenin en hızlı yolu, Azure Bilişsel Arama hizmetini ve Azure Blob hizmetini içeren kaynak grubunu silmelidir. Her iki hizmeti de aynı gruba yerleştirdiğinizde, kaynak grubunu silerek hizmetler ve bu öğretici için oluşturduğunuz tüm depolanmış içerikler dahil olmak üzere, içindeki her şeyi kalıcı olarak silebilirsiniz. Portalda kaynak grubu adı, her bir hizmetin Genel Bakış sayfasındadır.

## <a name="next-steps"></a>Sonraki adımlar

Özel becerilerle işlem hattını özelleştirin veya genişletin. Özel bir beceri oluşturup bir beceri kümesine eklemeniz, kendi yazdığınız metin veya görüntü analizini eklemenize olanak sağlar.

> [!div class="nextstepaction"]
> [Örnek: AI zenginleştirme için özel bir yetenek oluşturma](cognitive-search-create-custom-skill-example.md)

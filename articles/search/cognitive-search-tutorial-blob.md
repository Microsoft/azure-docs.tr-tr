---
title: 'Öğretici: JSON Bloblarından metin ve yapıyı ayıklama'
titleSuffix: Azure Cognitive Search
description: Postman ve Azure Bilişsel Arama REST API 'Lerini kullanarak JSON Bloblarındaki içerikler üzerinde metin ayıklama ve doğal dil işleme örneğinde adım adım ilerleyin.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 5dffafba0f0dc0dc108bf2c82929c157018d8dbb
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113664"
---
# <a name="tutorial-extract-text-and-structure-from-json-blobs-in-azure-using-rest-apis-azure-cognitive-search"></a>Öğretici: REST API 'Leri kullanarak Azure 'da JSON Bloblarından metin ve yapı ayıklama (Azure Bilişsel Arama)

Azure Blob depolamada yapılandırılmamış metin veya görüntü içeriğiniz varsa, bir [AI zenginleştirme işlem hattı](cognitive-search-concept-intro.md) , tam metin arama veya bilgi araştırma senaryoları için faydalı olan bilgileri ayıklamanızı ve yeni içerik oluşturmanıza yardımcı olabilir. İşlem hattı görüntü dosyalarını (JPG, PNG, TIFF) işleyebilse de, bu öğretici Word tabanlı içeriğe odaklanmasına karşın sorgularda, modellerde ve filtrelerde kullanabileceğiniz yeni alanlar ve bilgiler oluşturmak için dil algılamayı ve metin analizlerini uygular.

> [!div class="checklist"]
> * Azure Blob depolamada PDF, MD, DOCX ve PPTX gibi tüm belgeler (yapılandırılmamış metin) ile başlayın.
> * Metin çıkaran, dili algılayan, varlıkları tanıyan ve anahtar tümceleri algılayan bir işlem hattı tanımlayın.
> * Çıktıyı depolamak için bir dizin tanımlayın (ham içerik ve ardışık düzen tarafından oluşturulan ad-değer çiftleri).
> * Dönüşümleri ve Analizi başlatmak ve dizini oluşturmak ve yüklemek için işlem hattını yürütün.
> * Tam metin aramasını ve zengin sorgu söz dizimini kullanarak sonuçları keşfedebilirsiniz.

Bu izlenecek yolu tamamlamak için birkaç hizmete, Ayrıca, REST API çağrısı yapmak için [Postman masaüstü uygulamasını](https://www.getpostman.com/) veya başka bir Web Testi aracını kullanmanız gerekir. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) açın.

## <a name="download-files"></a>Dosyaları indirme

1. Bu [OneDrive klasörünü](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) açın ve sol üst köşedeki dosyaları bilgisayarınıza kopyalamak için **İndir** ' e tıklayın. 

1. ZIP dosyasına sağ tıklayın ve **Tümünü Ayıkla**' yı seçin. Çeşitli türlerde 14 dosya vardır. Bu alıştırma için 7 kullanacaksınız.

## <a name="1---create-services"></a>1-hizmet oluşturma

Bu kılavuzda, verileri sağlamak üzere dizin oluşturma ve sorgular, AI zenginleştirme için bilişsel hizmetler ve Azure Blob depolama için Azure Bilişsel Arama kullanılmaktadır. Mümkünse, yakınlık ve yönetilebilirlik için aynı bölgedeki ve kaynak grubundaki tüm üç hizmeti oluşturun. Uygulamada, Azure depolama hesabınız herhangi bir bölgede olabilir.

### <a name="start-with-azure-storage"></a>Azure Storage 'ı kullanmaya başlama

1. [Azure Portal oturum açın](https://portal.azure.com/) ve **+ kaynak oluştur**' a tıklayın.

1. *Depolama hesabı* araması yapın ve Microsoft 'un depolama hesabı teklifi ' ni seçin.

   ![Depolama hesabı oluştur](media/cognitive-search-tutorial-blob/storage-account.png "Depolama hesabı oluştur")

1. Temel bilgiler sekmesinde, aşağıdaki öğeler gereklidir. Diğer her şey için varsayılanları kabul edin.

   + **Kaynak grubu**. Mevcut bir tane seçin veya yeni bir tane oluşturun, ancak bunları topluca yönetebilmeniz için tüm hizmetler için aynı grubu kullanın.

   + **Depolama hesabı adı**. Aynı türde birden fazla kaynağınız olabileceğini düşünüyorsanız, tür ve bölgeye göre belirsizliği ortadan kaldırmak için adı kullanın, örneğin *blobstoragewestus*. 

   + **Konum**. Mümkünse, Azure Bilişsel Arama ve bilişsel hizmetler için kullanılan aynı konumu seçin. Tek bir konum, bant genişliği ücretlerini oylar.

   + **Hesap türü**. Varsayılan, *StorageV2 (genel amaçlı v2)* seçeneğini belirleyin.

1. Hizmeti oluşturmak için **gözden geçir + oluştur** ' a tıklayın.

1. Oluşturulduktan sonra genel bakış sayfasını açmak için **Kaynağa Git** ' e tıklayın.

1. **Bloblar** hizmeti ' ne tıklayın.

1. Bir kapsayıcı oluşturmak ve *COG-Search-demo*olarak adlandırmak Için **+ kapsayıcı** ' ya tıklayın.

1. *COG-Search-demo* ' i seçin ve ardından yükleme dosyalarını kaydettiğiniz klasörü açmak Için **karşıya yükle** ' ye tıklayın. Görüntü olmayan dosyaların tümünü seçin. 7 dosyasına sahip olmanız gerekir. Karşıya yüklemek için **Tamam** 'ı tıklatın.

   ![Örnek dosyaları karşıya yükle](media/cognitive-search-tutorial-blob/sample-files.png "Örnek dosyaları karşıya yükle")

1. Azure depolama alanını kapatmadan önce Azure Bilişsel Arama bir bağlantıyı formülleştirmek için bir bağlantı dizesi alın. 

   1. Depolama hesabınızın genel bakış sayfasına geri gidin (örnek olarak *blobstragewestus* kullandık). 
   
   1. Sol gezinti bölmesinde **erişim anahtarları** ' nı seçin ve bağlantı dizelerinden birini kopyalayın. 

   Bağlantı dizesi, aşağıdaki örneğe benzer bir URL 'dir:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Bağlantı dizesini Not defteri 'ne kaydedin. Daha sonra veri kaynağı bağlantısını ayarlarken gerekecektir.

### <a name="cognitive-services"></a>Bilişsel Hizmetler

AI zenginleştirme, doğal dil ve görüntü işleme için Metin Analizi ve Görüntü İşleme dahil bilişsel hizmetler tarafından desteklenir. Amacınız gerçek bir prototipi veya projeyi tamamlayacaksa, bu noktada bilişsel hizmetler sağlama (Azure Bilişsel Arama ile aynı bölgede), böylece dizin oluşturma işlemlerine iliştirebilirsiniz.

Bununla birlikte, Azure Bilişsel Arama, arka planda bilişsel hizmetlere bağlanıp Dizin Oluşturucu başına 20 ücretsiz işlem sunabileceğinden kaynak sağlamayı atlayabilirsiniz. Bu öğretici 7 işlem kullandığından, ücretsiz ayırma yeterlidir. Daha büyük projeler için, Kullandıkça öde, bu hizmetleri, Kullandıkça öde, bu hizmetler için sağlama bölümüne planlayın. Daha fazla bilgi için bkz. bilişsel [Hizmetler iliştirme](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

Üçüncü bileşen, [portalda oluşturabileceğiniz](search-create-service-portal.md)Azure bilişsel arama. Bu izlenecek yolu tamamlamak için ücretsiz katmanı kullanabilirsiniz. 

Azure Blob depolamada olduğu gibi, erişim anahtarını toplamak için biraz zaman ayırın. Ayrıca, istekleri raporlamaya başladığınızda, her bir isteğin kimliğini doğrulamak için kullanılan uç nokta ve yönetici API 'si anahtarını sağlamanız gerekir.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Bilişsel Arama yönelik bir yönetici API anahtarı ve URL 'SI alın

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetinize **genel bakış** sayfasında, arama hizmetinizin adını alın. Uç nokta URL 'sini inceleyerek hizmet adınızı doğrulayabilirsiniz. Uç nokta URL 'niz `https://mydemo.search.windows.net`, hizmet adınız `mydemo`olur.

2. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

    Sorgu anahtarını da alın. Salt okuma erişimiyle sorgu istekleri vermek en iyi uygulamadır.

![Hizmet adı ve yönetici ve sorgu anahtarlarını alın](media/search-get-started-nodejs/service-name-and-keys.png)

Tüm istekler, hizmetinize gönderilen her isteğin üstbilgisinde bir API anahtarı gerektirir. Geçerli bir anahtar, istek başına, isteği gönderen uygulama ve onu işleyen hizmet arasında güven oluşturur.

## <a name="2---set-up-postman"></a>2-Postman 'ı ayarlama

Postman’i başlatın ve bir HTTP isteği ayarlayın. Bu aracı hakkında bilginiz varsa bkz. [Postman kullanarak Azure BILIŞSEL arama REST API 'Lerini araştırma](search-get-started-postman.md).

Bu öğreticide kullanılan istek metotları **gönderi**, **PUT**ve **Get**. Arama hizmetinize dört API çağrısı yapmak için yöntemlerini kullanacaksınız: bir veri kaynağı, bir beceri, dizin ve Dizin Oluşturucu oluşturun.

Üst bilgilerde, "Content-Type" öğesini `application/json` olarak ayarlayın ve `api-key` Azure Bilişsel Arama hizmetinizin Yönetici API 'si anahtarına ayarlayın. Üst bilgileri ayarladıktan sonra bu alýþtýrmadaki her istek için kullanabilirsiniz.

  ![Postman istek URL 'SI ve üstbilgisi](media/search-get-started-postman/postman-url.png "Postman istek URL 'SI ve üstbilgisi")

## <a name="3---create-the-pipeline"></a>3-işlem hattını oluşturma

Azure Bilişsel Arama 'de, dizin oluşturma (veya veri alımı) sırasında AI işleme oluşur. İzlenecek yolun bu bölümü dört nesne oluşturur: veri kaynağı, Dizin tanımı, Beceri, Dizin Oluşturucu. 

### <a name="step-1-create-a-data-source"></a>1\. Adım: Veri kaynağı oluşturma

[Veri kaynağı nesnesi](https://docs.microsoft.com/rest/api/searchservice/create-data-source) , dosyaları içeren blob kapsayıcısına bağlantı dizesi sağlar.

1. **Post** 'u ve aşağıdaki URL 'yi kullanarak hizmet adını hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. İstek **gövdesi**' nde aşağıdaki JSON tanımını kopyalayın ve `connectionString`, depolama hesabınızın gerçek bağlantısıyla değiştirin. 

   Kapsayıcı adını da düzenlemeyi unutmayın. Önceki bir adımda kapsayıcı adı için "COG-Search-demo" önerilir.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. İsteği gönderin. Başarıyı onaylayan 201 durum kodunu görmeniz gerekir. 

Bir 403 veya 404 hatası aldıysanız, istek yapısını denetleyin: `api-version=2019-05-06`, uç nokta üzerinde olmalıdır, `api-key`, Üst bilgide `Content-Type` öğesinden sonra gelmelidir ve değeri bir arama hizmeti için geçerli olmalıdır. Sözdiziminin doğru olduğundan emin olmak için JSON belgesini çevrimiçi bir JSON doğrulayıcısı aracılığıyla çalıştırmak isteyebilirsiniz. 

### <a name="step-2-create-a-skillset"></a>2\. Adım: beceri oluşturma

[Beceri nesnesi](https://docs.microsoft.com/rest/api/searchservice/create-skillset) , içeriğinize uygulanan bir zenginleştirme adımları kümesidir. 

1. **PUT** ve aşağıdaki URL 'yi kullanın ve hizmet adını hizmetinizin gerçek adıyla değiştirin.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. İstek **gövdesi**' nde, aşağıdaki JSON tanımını kopyalayın. Bu beceri, aşağıdaki yerleşik becerilerden oluşur.

   | İmde                 | Açıklama    |
   |-----------------------|----------------|
   | [Varlık tanıma](cognitive-search-skill-entity-recognition.md) | Kişilerin, kuruluşların ve konumların adlarını blob kapsayıcısındaki içerikten ayıklar. |
   | [Dil algılama](cognitive-search-skill-language-detection.md) | İçeriğin dilini algılar. |
   | [Metin bölme](cognitive-search-skill-textsplit.md)  | Anahtar tümceciği ayıklama becerisi çağrılmadan önce büyük içeriği daha küçük parçalara ayırır. Anahtar tümcecik ayıklama, 50.000 veya daha az karakterden oluşan girişi kabul eder. Bu sınıra uymak için örnek dosyaların birkaç tanesinin bölünmesi gerekir. |
   | [Anahtar ifade ayıklama](cognitive-search-skill-keyphrases.md) | En üstteki anahtar tümceleri çeker. |

   Her beceri, belge içeriğinde yürütülür. İşlem sırasında Azure Bilişsel Arama, farklı dosya biçimlerinden içerik okumak için her belgeyi ister. Kaynak dosyadan gelen, bulunan metin, oluşturulan ```content``` alanına her belge için birer birer yerleştirilir. Bu nedenle, giriş ```"/document/content"```olur.

   Anahtar tümceciği ayıklama için, büyük dosyaları sayfalara bölmek üzere metin Bölümlendirici yeteneği kullandığımızda, anahtar tümceciği ayıklama becerinin bağlamı ```"/document/content"```yerine ```"document/pages/*"``` (belgedeki her sayfa için).

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Beceri kümesinin grafiksel gösterimi aşağıda gösterilmektedir. 

    ![Beceri anlayın](media/cognitive-search-tutorial-blob/skillset.png "Beceri anlayın")

1. İsteği gönderin. Postman, başarıyı onaylayan 201 durum kodunu döndürmelidir. 

> [!NOTE]
> Çıktılar bir dizine eşlenebilir, aşağı akış becerisine yönelik giriş olarak kullanılır veya dil kodunda olduğu gibi her iki şekilde de kullanılabilir. Dizinde bir dil kodu, filtreleme için yararlıdır. Giriş olarak dil kodu, sözcük bölünmesiyle ilgili dilbilgisi kurallarını bildirmek için metin analizi becerileri tarafından kullanılır. Beceri kümesi temelleri hakkında daha fazla bilgi için bkz. [Beceri kümesini tanımlama](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>3\. Adım: Dizin oluşturma

Bir [Dizin](https://docs.microsoft.com/rest/api/searchservice/create-index) , içeriğinizin fiziksel ifadesini, ters dizinler ve Azure bilişsel arama diğer yapıları oluşturmak için kullanılan şemayı sağlar. Bir dizinin en büyük bileşeni, veri türü ve özniteliklerin Azure Bilişsel Arama 'daki içerikleri ve davranışları belirlerken alanlar koleksiyonudur.

1. Dizininizi adlandırmak için, **PUT** ve aşağıdaki URL 'yi kullanarak hizmet adınızı hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. İstek **gövdesi**' nde aşağıdaki JSON tanımını kopyalayın. `content` alanı belgenin kendisini depolar. `languageCode`, `keyPhrases`ve `organizations` için ek alanlar, beceri tarafından oluşturulan yeni bilgileri (alanlar ve değerler) temsil eder.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. İsteği gönderin. Postman, başarıyı onaylayan 201 durum kodunu döndürmelidir. 

### <a name="step-4-create-and-run-an-indexer"></a>4\. Adım: Dizin Oluşturucu oluşturma ve çalıştırma

[Dizin Oluşturucu](https://docs.microsoft.com/rest/api/searchservice/create-indexer) , işlem hattını sürücüler. Şimdiye kadar oluşturduğunuz üç bileşen (veri kaynağı, Beceri, dizin) bir dizin oluşturucunun girdileri olur. Azure Bilişsel Arama dizin oluşturucunun oluşturulması, tüm işlem hattının hareket halinde yer aldığı olaydır. 

1. Dizin oluşturucuyu adlandırmak için **PUT** ve aşağıdaki URL 'yi kullanın ve hizmet adını hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. İstek **gövdesi**' nde, aşağıdaki JSON tanımını kopyalayın. Alan eşleme öğelerine dikkat edin; Bu eşlemeler veri akışını tanımladıklarından önemlidir. 

   `fieldMappings`, bir dizindeki hedef alanlara veri kaynağından içerik gönderen beceri önce işlenir. Dizine var olan, değiştirilmemiş içeriği göndermek için alan eşlemelerini kullanacaksınız. Alan adları ve türleri her iki uçta da aynıysa, hiçbir eşleme gerekmez.

   `outputFieldMappings` yetenekler tarafından oluşturulan alanlara yöneliktir ve bu nedenle beceri çalıştıktan sonra işlenir. Belge çözme veya zenginleştirme onları oluşturuncaya kadar `outputFieldMappings` `sourceFieldNames` başvuruları yok. `targetFieldName` dizin şemasında tanımlanan, dizin içindeki bir alandır.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. İsteği gönderin. Postman, başarılı işlemeyi onaylayan 201 durum kodunu döndürmelidir. 

   Bu adımın tamamlanması birkaç dakika sürebilir. Veri kümesi küçük olsa da, analiz becerileri bilgi işlem açısından yoğundur. 

> [!NOTE]
> Bir dizin oluşturucu oluşturulduğunda, işlem hattı çağrılır. Verilere ulaşılırken, eşleme girişleri ve çıktıları veya işlemlerin sırası ile ilgili sorun olursa bunlar bu aşamada görüntülenir. İşlem hattını, kod veya betik değişiklikleriyle yeniden çalıştırmak için önce nesneleri bırakmanız gerekebilir. Daha fazla bilgi için bkz. [Sıfırlama ve yeniden çalıştırma](#reset).

#### <a name="about-indexer-parameters"></a>Dizin Oluşturucu parametreleri hakkında

Betik, ```"maxFailedItems"``` değerini -1 olarak ayarlayarak dizin oluşturma motoruna, veri içeri aktarma sırasında hataları yoksaymasını bildirir. Bu, tanıtım verileri kaynağında çok az sayıda belge olduğu için kabul edilebilir. Daha büyük bir veri kaynağı için değeri, 0’dan daha büyük bir değere ayarlarsınız.

```"dataToExtract":"contentAndMetadata"``` ifade, dizin oluşturucunun, her dosyayla ilgili meta verilerin yanı sıra farklı dosya biçimlerinden içeriği otomatik olarak ayıklamasını söyler. 

İçerik ayıklandığında, veri kaynağında bulunan görüntülerden metni ayıklamak için ```imageAction``` değerini ayarlayabilirsiniz. ```"imageAction":"generateNormalizedImages"``` yapılandırması, OCR becerisi ve metin birleştirme beceriyle birlikte kullanıldığında, dizin oluşturucunun görüntülerden metin ayıklamasını söyler (örneğin, bir trafiğin "Durdur" sözcüğünün oturum açmasını durdurur) ve içerik alanının bir parçası olarak katıştırmasını söyler. Bu davranış hem belgelerde gömülü olan görüntüler (örneğin, bir PDF’teki görüntü) hem de veri kaynağında bulunan görüntüler (örneğin, bir JPG dosyası) için geçerlidir.

## <a name="4---monitor-indexing"></a>4-dizin oluşturmayı izleme

Dizin oluşturma ve zenginleştirme, oluşturma Dizin Oluşturucu isteğini gönderdikten hemen sonra. Tanımladığınız bilişsel becerilere bağlı olarak, dizin oluşturma biraz zaman alabilir. Dizin oluşturucunun halen çalışıp çalışmadığını öğrenmek için aşağıdaki isteği göndererek dizin oluşturucu durumunu denetleyin.

1. Dizin oluşturucuyu adlandırmak için, **Get** ve aşağıdaki URL 'yi kullanın ve hizmet adını hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Dizin oluşturucunun çalışıp çalışmadığını öğrenmek veya hata ve uyarı bilgilerini görüntülemek için yanıtı gözden geçirin.  

Ücretsiz katmanı kullanıyorsanız, şu ileti bekleniyor: ' "belgenizden içerik veya meta veri ayıklanamadı. Ayıklanan metin ' 32768 ' karakter "olarak kesildi. Bu ileti, boş katmandaki blob dizinlemesi,[karakter ayıklamaya karşı bir 32K sınırına](search-limits-quotas-capacity.md#indexer-limits)sahip olduğundan görüntülenir. Bu ileti, daha yüksek katmanlarda bu veri kümesi için görmezsiniz. 

> [!NOTE]
> Uyarılar bazı senaryolarda ortaktır ve her zaman bir sorun göstermez. Örneğin, bir blob kapsayıcısı görüntü dosyaları içeriyorsa ve işlem hattı görüntüleri işetmez, görüntülerin işlenmediğini belirten bir uyarı alırsınız.

## <a name="5---search"></a>5-arama

Artık yeni alanlar ve bilgiler oluşturduğunuza göre, tipik bir arama senaryosuyla ilişkili olduğu için bilişsel arama değerini anlamak üzere bazı sorgular çalıştıralım.

Tüm belgenin tek bir `content` alanına paketlendiği blob içeriğiyle başladığımızda geri çekin. Bu alanda arama yapabilir ve sorgularınızdaki eşleşmeleri bulabilirsiniz.

1. Bir terim veya tümceciğin örneklerini aramak, `content` alanını ve eşleşen belgelerin sayısını döndürmek için, **Get** ve aşağıdaki URL 'yi kullanın ve hizmet adını hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Bu sorgunun sonuçları, bir blob Dizin oluşturucuyu bilişsel arama işlem hattı olmadan kullandıysanız alacağınız aynı sonucu veren belge içeriğini döndürür. Bu alan aranabilir, ancak modelleri, filtreleri veya otomatik tamamlamayı kullanmak istiyorsanız, kullanılamaz.

   ![İçerik alanı çıkışı](media/cognitive-search-tutorial-blob/content-output.png "İçerik alanı çıkışı")
   
1. İkinci sorgu için, işlem hattı (kişiler, kuruluşlar, konumlar, languageCode) tarafından oluşturulan yeni alanlardan bazılarını döndürün. Kısaltma için keyPhrases yok eteceğiz, ancak bu değerleri görmek istiyorsanız dahil etmelisiniz.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   $Select deyimindeki alanlar bilişsel hizmetler 'in doğal dil işleme özellikleri ' nden oluşturulan yeni bilgiler içerir. Tahmin edebileceğiniz gibi, sonuçlar ve belgelerde çeşitlerdeki bazı gürültü vardır, ancak birçok örnekte analitik modeller doğru sonuçlar üretir.

   Aşağıdaki görüntüde, Microsoft 'ta CEO rolü varsayıldığında Satya Nadella 'nın açık harfinin sonuçları gösterilmektedir.

   ![Ardışık düzen çıkışı](media/cognitive-search-tutorial-blob/pipeline-output.png "Ardışık düzen çıkışı")

1. Bu alanlardan nasıl yararlanabileceği hakkında daha fazla bilgi için, konuma göre eşleşen belge toplamayı döndürmek üzere bir model parametresi ekleyin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   Bu örnekte, her konum için 2 veya 3 eşleşme vardır.

   ![Model çıkışı](media/cognitive-search-tutorial-blob/facet-output.png "Model çıkışı")
   

1. Bu son örnekte, organizasyonlar koleksiyonuna bir filtre uygulayın ve NASDAQ tabanlı filtre ölçütlerine yönelik iki eşleşme döndürerek.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Bu sorgular, bilişsel arama tarafından oluşturulan yeni alanlara sorgu söz dizimi ve filtreler ile çalışmanın birkaç yolunu gösterir. Daha fazla sorgu örneği için bkz. [arama belgeleri REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), [basit sözdizimi sorgu örnekleri](search-query-simple-examples.md)ve [tam Lucene sorgu örnekleri](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

İşlem hattı geliştirmenin erken deneysel aşamalarında, tasarım yinelemeleri için en pratik yaklaşım, nesneleri Azure Bilişsel Arama silmek ve kodunuzun bunları yeniden oluşturması için izin verir. Kaynak adları benzersizdir. Bir nesneyi sildiğinizde, aynı adı kullanarak nesneyi yeniden oluşturabilirsiniz.

Yeni tanımlarla belgelerinizin yeniden dizinini oluşturmak için:

1. Dizin oluşturucuyu, dizini ve beceri silin.
2. Nesneleri Değiştir.
3. İşlem hattını çalıştırmak için hizmetinize yeniden oluşturun. 

Dizinleri, Dizin oluşturucuyu ve becerileri silmek için portalı kullanabilir ya da **Sil** ' i kullanabilir ve her bir nesnenin URL 'sini sağlayabilirsiniz. Aşağıdaki komut bir dizin oluşturucuyu siler.

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

Silme işlemi başarılı olduğunda durum kodu 204 döndürülür.

Kodunuz geliştikçe bir yeniden derleme stratejisini iyileştirmek isteyebilirsiniz. Daha fazla bilgi için bkz. [Yeniden dizin derleme](search-howto-reindex.md).

## <a name="takeaways"></a>Paketler

Bu öğreticide, veri kaynağı, beceri kümesi, dizin ve dizin oluşturucu gibi bileşen parçalarının oluşturulması yoluyla zenginleştirilmiş bir dizin oluşturma işlem hattı oluşturmaya yönelik temel adımlar gösterilmektedir.

[Yerleşik yetenekler](cognitive-search-predefined-skills.md) , Beceri tanımıyla birlikte tanıtılmıştır ve giriş ve çıkışlarla becerilerin zincirlerinin bir araya getirilmiştir. Ayrıca, işlem hattı tanımındaki `outputFieldMappings`, işlem hattından bir Azure Bilişsel Arama hizmetinde aranabilir dizine yönlendirme için gerekli olduğunu öğrendiniz.

Son olarak, daha fazla yineleme için sonuçların nasıl test edileceğini ve sistemin nasıl sıfırlanacağını öğrendiniz. Dizine karşı sorgular düzenlendiğinde, zenginleştirilmiş dizin oluşturma işlem hattı tarafından oluşturulan çıktının döndürüldüğünü öğrendiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiden sonra temizlemenin en hızlı yolu, Azure Bilişsel Arama hizmetini ve Azure Blob hizmetini içeren kaynak grubunu silmelidir. Her iki hizmeti de aynı gruba koyduğunuz varsayılarak, şimdi bu öğreticide oluşturduğunuz depolanan içerikler ve hizmetler de dahil olmak üzere, kaynak grubunun içindeki her şeyi silmek için kaynak grubunu silin. Portalda kaynak grubu adı, her bir hizmetin Genel Bakış sayfasındadır.

## <a name="next-steps"></a>Sonraki adımlar

Özel becerilerle işlem hattını özelleştirin veya genişletin. Özel bir beceri oluşturup bir beceri kümesine eklemeniz, kendi yazdığınız metin veya görüntü analizini eklemenize olanak sağlar. 

> [!div class="nextstepaction"]
> [Örnek: AI zenginleştirme için özel bir yetenek oluşturma](cognitive-search-create-custom-skill-example.md)
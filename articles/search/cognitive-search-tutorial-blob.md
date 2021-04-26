---
title: "Öğretici: Azure Blob 'ları üzerinde REST ve AI"
titleSuffix: Azure Cognitive Search
description: Postman ve Azure Bilişsel Arama REST API 'Lerini kullanarak blob depolamada içerik üzerinde metin ayıklama ve doğal dil işleme örneğini adım adım yapın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: 21f0d141567f17c470732088c6a93a2ae7ed3c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94738059"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Öğretici: Azure Bloblarından aranabilir içerik oluşturmak için REST ve AI kullanma

Azure Blob depolamada yapılandırılmamış metin veya görüntüleriniz varsa, bir [AI zenginleştirme işlem hattı](cognitive-search-concept-intro.md) , tam metin arama veya bilgi araştırma senaryoları için faydalı olan bloblardan yeni içerik oluşturabilir. İşlem hattı görüntüleri işleyebilse de, bu REST öğreticide, sorgularda, modellerle ve filtrelerinizde kullanabileceğiniz yeni alanlar oluşturmak için dil algılama ve doğal dil işleme uygulayarak metin üzerinde odaklanılır.

Bu öğreticide, aşağıdaki görevleri gerçekleştirmek için Postman ve [arama REST API 'leri](/rest/api/searchservice/) kullanılmaktadır:

> [!div class="checklist"]
> * Hizmetleri ve Postman koleksiyonunu ayarlayın.
> * Metin çıkaran, dili algılayan, varlıkları tanıyan ve anahtar tümceleri algılayan bir zenginleştirme işlem hattı oluşturun.
> * Çıktıyı depolamak için bir dizin oluşturun (ham içerik ve ardışık düzen tarafından oluşturulan ad-değer çiftleri).
> * Dönüştürme ve analiz yapmak ve dizini yüklemek için işlem hattını yürütün.
> * Tam metin aramasını ve zengin sorgu söz dizimini kullanarak sonuçları keşfedebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) açın.

## <a name="overview"></a>Genel Bakış

Bu öğretici, bir veri kaynağı, dizin, Dizin Oluşturucu ve beceri oluşturmak için C# ve Azure Bilişsel Arama REST API 'Lerini kullanır. Azure Blob depolamada PDF, HTML, DOCX ve PPTX gibi tüm belgeler (yapılandırılmamış metin) ile başlar ve sonra varlıkları, anahtar ifadeleri ve içerik dosyalarındaki diğer metinleri ayıklamak için bunları bir beceri aracılığıyla çalıştırabilirsiniz.

Bu beceri, Bilişsel Hizmetler API'si dayalı yerleşik becerileri kullanır. İşlem hattının adımları metin, anahtar ifade ayıklama ve varlık tanıma (kuruluşlar) üzerinde dil algılamayı içerir. Yeni bilgiler sorgularda, modellerle ve filtrelerinizde kullanabileceğiniz yeni alanlara depolanır.

## <a name="prerequisites"></a>Önkoşullar

+ [Azure Depolama](https://azure.microsoft.com/services/storage/)
+ [Postman masaüstü uygulaması](https://www.getpostman.com/)
+ [Mevcut bir arama hizmeti](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [oluşturun](search-create-service-portal.md) veya bulun 

> [!Note]
> Bu öğretici için ücretsiz hizmeti kullanabilirsiniz. Ücretsiz arama hizmeti, sizi üç Dizin, üç Dizin Oluşturucu ve üç veri kaynağı ile sınırlandırır. Bu öğreticide hepsinden birer tane oluşturulur. Başlamadan önce, hizmetinize yeni kaynakları kabul etmek için yeriniz olduğundan emin olun.

## <a name="download-files"></a>Dosyaları indirme

1. Bu [OneDrive klasörünü](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) açın ve sol üst köşedeki dosyaları bilgisayarınıza kopyalamak için **İndir** ' e tıklayın. 

1. ZIP dosyasına sağ tıklayın ve **Tümünü Ayıkla**' yı seçin. Çeşitli türlerde 14 dosya vardır. Bu alıştırma için 7 kullanacaksınız.

İsteğe bağlı olarak, bu öğretici için bir Postman koleksiyon dosyası olan kaynak kodunu da indirebilirsiniz. Kaynak kodu adresinde bulabilirsiniz [https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) .

## <a name="1---create-services"></a>1-hizmet oluşturma

Bu öğreticide dizin oluşturma ve sorgular, AI zenginleştirme için arka uçta bilişsel hizmetler ve verileri sağlamak üzere Azure Blob depolama için Azure Bilişsel Arama kullanılmaktadır. Bu öğretici, bilişsel hizmetler 'de her gün Dizin Oluşturucu başına 20 işlem için ücretsiz ayırma kapsamında kalır, bu nedenle yalnızca oluşturmanız gereken hizmetler arama ve depolama amaçlıdır.

Mümkünse, yakınlık ve yönetilebilirlik için aynı bölgede ve kaynak grubunda her ikisini de oluşturun. Uygulamada, Azure depolama hesabınız herhangi bir bölgede olabilir.

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

1. Bir kapsayıcı oluşturmak ve *COG-Search-demo* olarak adlandırmak Için **+ kapsayıcı** ' ya tıklayın.

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

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Bilişsel Arama için bir yönetici API-anahtarı ve URL 'SI kopyalama

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetinize **genel bakış** sayfasında, arama hizmetinizin adını alın. Uç nokta URL 'sini inceleyerek hizmet adınızı doğrulayabilirsiniz. Uç nokta URL 'niz olsaydı `https://mydemo.search.windows.net` , hizmet adınız olur `mydemo` .

2. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   Sorgu anahtarını da alın. Salt okuma erişimiyle sorgu istekleri vermek en iyi uygulamadır.

   ![Hizmet adı ve yönetici ve sorgu anahtarlarını alın](media/search-get-started-javascript/service-name-and-keys.png)

Tüm istekler, hizmetinize gönderilen her isteğin üstbilgisinde bir API anahtarı gerektirir. Geçerli bir anahtar, istek başına, isteği gönderen uygulama ve onu işleyen hizmet arasında güven oluşturur.

## <a name="2---set-up-postman"></a>2-Postman 'ı ayarlama

Postman’i başlatın ve bir HTTP isteği ayarlayın. Bu araçla ilgili bilgi sahibi değilseniz bkz. [Azure BILIŞSEL arama REST API 'Lerini araştırma](search-get-started-rest.md).

Bu öğreticide kullanılan istek metotları **gönderi**, **PUT** ve **Get**. Arama hizmetinize dört API çağrısı yapmak için yöntemlerini kullanacaksınız: bir veri kaynağı, bir beceri, dizin ve Dizin Oluşturucu oluşturun.

Üst bilgiler ' de, "Content-Type" olarak ayarlayın `application/json` ve `api-key` Azure bilişsel arama hizmetinizin yönetim API anahtarı olarak ayarlayın. Üst bilgileri ayarladıktan sonra bu alýþtýrmadaki her istek için kullanabilirsiniz.

  ![Postman istek URL 'SI ve üstbilgisi](media/search-get-started-rest/postman-url.png "Postman istek URL 'SI ve üstbilgisi")

## <a name="3---create-the-pipeline"></a>3-işlem hattını oluşturma

Azure Bilişsel Arama, dizin oluşturma (veya veri alımı) sırasında zenginleştirme oluşur. İzlenecek yolun bu bölümü dört nesne oluşturur: veri kaynağı, Dizin tanımı, Beceri, Dizin Oluşturucu. 

### <a name="step-1-create-a-data-source"></a>1. Adım: Veri kaynağı oluşturma

[Veri kaynağı nesnesi](/rest/api/searchservice/create-data-source) , dosyaları içeren blob kapsayıcısına bağlantı dizesi sağlar.

1. **Post** 'u ve aşağıdaki URL 'yi kullanarak hizmet adını hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2020-06-30
   ```

1. İstek **gövdesi**' nde, aşağıdaki JSON tanımını kopyalayın ve öğesini `connectionString` depolama hesabınızın gerçek bağlantısıyla değiştirin. 

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

Bir 403 veya 404 hatası aldıysanız, istek yapısını denetleyin: `api-version=2020-06-30`, uç nokta üzerinde olmalıdır, `api-key`, Üst bilgide `Content-Type` öğesinden sonra gelmelidir ve değeri bir arama hizmeti için geçerli olmalıdır. Sözdiziminin doğru olduğundan emin olmak için JSON belgesini çevrimiçi bir JSON doğrulayıcısı aracılığıyla çalıştırmak isteyebilirsiniz. 

### <a name="step-2-create-a-skillset"></a>2. Adım: beceri oluşturma

[Beceri nesnesi](/rest/api/searchservice/create-skillset) , içeriğinize uygulanan bir zenginleştirme adımları kümesidir. 

1. **PUT** ve aşağıdaki URL 'yi kullanın ve hizmet adını hizmetinizin gerçek adıyla değiştirin.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-sd?api-version=2020-06-30
    ```

1. İstek **gövdesi**' nde, aşağıdaki JSON tanımını kopyalayın. Bu beceri, aşağıdaki yerleşik becerilerden oluşur.

   | İmde                 | Description    |
   |-----------------------|----------------|
   | [Varlık Tanıma](cognitive-search-skill-entity-recognition.md) | Kişilerin, kuruluşların ve konumların adlarını blob kapsayıcısındaki içerikten ayıklar. |
   | [Dil Algılama](cognitive-search-skill-language-detection.md) | İçeriğin dilini algılar. |
   | [Metin Bölme](cognitive-search-skill-textsplit.md)  | Anahtar tümceciği ayıklama becerisi çağrılmadan önce büyük içeriği daha küçük parçalara ayırır. Anahtar tümcecik ayıklama, 50.000 veya daha az karakterden oluşan girişi kabul eder. Bu sınıra uymak için örnek dosyaların birkaç tanesinin bölünmesi gerekir. |
   | [Anahtar İfade Ayıklama](cognitive-search-skill-keyphrases.md) | En üstteki anahtar tümceleri çeker. |

   Her beceri, belge içeriğinde yürütülür. İşlem sırasında Azure Bilişsel Arama, farklı dosya biçimlerinden içerik okumak için her belgeyi ister. Kaynak dosyadan gelen, bulunan metin, oluşturulan ```content``` alanına her belge için birer birer yerleştirilir. Bu nedenle, girdi olur ```"/document/content"``` .

   Anahtar tümceciği ayıklama için, büyük dosyaları sayfalara bölmek üzere metin Bölümlendirici yeteneği kullandığımızda, anahtar tümceciği ayıklama ```"document/pages/*"``` becerinin bağlamı, yerine (belgedeki her sayfa için) olur ```"/document/content"``` .

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

### <a name="step-3-create-an-index"></a>3. Adım: Dizin oluşturma

Bir [Dizin](/rest/api/searchservice/create-index) , içeriğinizin fiziksel ifadesini, ters dizinler ve Azure bilişsel arama diğer yapıları oluşturmak için kullanılan şemayı sağlar. Bir dizinin en büyük bileşeni, veri türü ve özniteliklerin Azure Bilişsel Arama 'daki içerikleri ve davranışları belirlerken alanlar koleksiyonudur.

1. Dizininizi adlandırmak için, **PUT** ve aşağıdaki URL 'yi kullanarak hizmet adınızı hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2020-06-30
   ```

1. İstek **gövdesi**' nde aşağıdaki JSON tanımını kopyalayın. `content`Alan, belgenin kendisini depolar. , Ve için ek alanlar, `languageCode` `keyPhrases` `organizations` beceri tarafından oluşturulan yeni bilgileri (alanlar ve değerler) temsil eder.

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

### <a name="step-4-create-and-run-an-indexer"></a>4. Adım: Dizin Oluşturucu oluşturma ve çalıştırma

[Dizin Oluşturucu](/rest/api/searchservice/create-indexer) , işlem hattını sürücüler. Şimdiye kadar oluşturduğunuz üç bileşen (veri kaynağı, Beceri, dizin) bir dizin oluşturucunun girdileri olur. Azure Bilişsel Arama dizin oluşturucunun oluşturulması, tüm işlem hattının hareket halinde yer aldığı olaydır. 

1. Dizin oluşturucuyu adlandırmak için **PUT** ve aşağıdaki URL 'yi kullanın ve hizmet adını hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
   ```

1. İstek **gövdesi**' nde, aşağıdaki JSON tanımını kopyalayın. Alan eşleme öğelerine dikkat edin; Bu eşlemeler veri akışını tanımladıklarından önemlidir. 

   , `fieldMappings` Bir dizindeki hedef alanlara veri kaynağından içerik gönderen beceri önce işlenir. Dizine var olan, değiştirilmemiş içeriği göndermek için alan eşlemelerini kullanacaksınız. Alan adları ve türleri her iki uçta da aynıysa, hiçbir eşleme gerekmez.

   , `outputFieldMappings` Yetenekler tarafından oluşturulan alanlara yöneliktir ve bu nedenle beceri çalıştıktan sonra işlenir. `sourceFieldNames` `outputFieldMappings` Belge çözme veya zenginleştirme onları oluşturana kadar ' deki başvuruları yok. , Dizin `targetFieldName` şemasında tanımlanan, dizindeki bir alandır.

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

Bu ```"dataToExtract":"contentAndMetadata"``` ifade, dizin oluşturucunun, her dosyayla ilgili meta verilerin yanı sıra farklı dosya biçimlerinden içeriği otomatik olarak ayıklamasını söyler. 

İçerik ayıklandığında, veri kaynağında bulunan görüntülerden metni ayıklamak için ```imageAction``` değerini ayarlayabilirsiniz. ```"imageAction":"generateNormalizedImages"```Yapılandırma, OCR becerisi ve metin birleştirme beceriyle birlikte kullanıldığında, dizin oluşturucunun görüntülerden metin ayıklamasını söyler (örneğin, trafiğin "Durdur" sözcüğünün oturum açmasını durdurur) ve içerik alanının bir parçası olarak katıştırmasını söyler. Bu davranış hem belgelerde gömülü olan görüntüler (örneğin, bir PDF’teki görüntü) hem de veri kaynağında bulunan görüntüler (örneğin, bir JPG dosyası) için geçerlidir.

## <a name="4---monitor-indexing"></a>4-dizin oluşturmayı izleme

Dizin oluşturma ve zenginleştirme, oluşturma Dizin Oluşturucu isteğini gönderdikten hemen sonra. Tanımladığınız bilişsel becerilere bağlı olarak, dizin oluşturma biraz zaman alabilir. Dizin oluşturucunun halen çalışıp çalışmadığını öğrenmek için aşağıdaki isteği göndererek dizin oluşturucu durumunu denetleyin.

1. Dizin oluşturucuyu adlandırmak için, **Get** ve aşağıdaki URL 'yi kullanın ve hizmet adını hizmetinizin gerçek adıyla değiştirin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2020-06-30
   ```

1. Dizin oluşturucunun çalışıp çalışmadığını öğrenmek veya hata ve uyarı bilgilerini görüntülemek için yanıtı gözden geçirin.  

Ücretsiz katmanı kullanıyorsanız, şu ileti bekleniyor: ' "belgenizden içerik veya meta veri ayıklanamadı. Ayıklanan metin ' 32768 ' karakter "olarak kesildi. Bu ileti, boş katmandaki blob dizinlemesi,[karakter ayıklamaya karşı bir 32K sınırına](search-limits-quotas-capacity.md#indexer-limits)sahip olduğundan görüntülenir. Bu ileti, daha yüksek katmanlarda bu veri kümesi için görmezsiniz. 

> [!NOTE]
> Uyarılar bazı senaryolarda ortaktır ve her zaman bir sorun göstermez. Örneğin, bir blob kapsayıcısı görüntü dosyaları içeriyorsa ve işlem hattı görüntüleri işetmez, görüntülerin işlenmediğini belirten bir uyarı alırsınız.

## <a name="5---search"></a>5-arama

Artık yeni alanlar ve bilgiler oluşturduğunuza göre, tipik bir arama senaryosuyla ilişkili olduğu için bilişsel arama değerini anlamak üzere bazı sorgular çalıştıralım.

Tüm belgenin tek bir alana paketlendiği blob içeriğiyle başladığımızda geri çekin `content` . Bu alanda arama yapabilir ve sorgularınızdaki eşleşmeleri bulabilirsiniz.

1. Bir terim veya tümceciğin örneklerini aramak için, bir terim veya tümcecik örnekleri aramak üzere, **Get** ve aşağıdaki URL 'yi kullanarak, hizmet adını hizmetinizin gerçek adıyla değiştirerek, `content` alan ve eşleşen belgelerin sayısını döndüren bir terim veya tümcecik için arama yapın.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=content&api-version=2020-06-30
   ```
   
   Bu sorgunun sonuçları, bir blob Dizin oluşturucuyu bilişsel arama işlem hattı olmadan kullandıysanız alacağınız aynı sonucu veren belge içeriğini döndürür. Bu alan aranabilir, ancak modelleri, filtreleri veya otomatik tamamlamayı kullanmak istiyorsanız, kullanılamaz.

   ![İçerik alanı çıkışı](media/cognitive-search-tutorial-blob/content-output.png "İçerik alanı çıkışı")
   
1. İkinci sorgu için, işlem hattı (kişiler, kuruluşlar, konumlar, languageCode) tarafından oluşturulan yeni alanlardan bazılarını döndürün. Kısaltma için keyPhrases yok eteceğiz, ancak bu değerleri görmek istiyorsanız dahil etmelisiniz.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2020-06-30
   ```
   $Select deyimindeki alanlar bilişsel hizmetler 'in doğal dil işleme özellikleri ' nden oluşturulan yeni bilgiler içerir. Tahmin edebileceğiniz gibi, sonuçlar ve belgelerde çeşitlerdeki bazı gürültü vardır, ancak birçok örnekte analitik modeller doğru sonuçlar üretir.

   Aşağıdaki görüntüde, Microsoft 'ta CEO rolü varsayıldığında Satya Nadella 'nın açık harfinin sonuçları gösterilmektedir.

   ![Ardışık düzen çıkışı](media/cognitive-search-tutorial-blob/pipeline-output.png "Ardışık düzen çıkışı")

1. Bu alanlardan nasıl yararlanabileceği hakkında daha fazla bilgi için, konuma göre eşleşen belge toplamayı döndürmek üzere bir model parametresi ekleyin.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2020-06-30
   ``` 

   Bu örnekte, her konum için 2 veya 3 eşleşme vardır.

   ![Model çıkışı](media/cognitive-search-tutorial-blob/facet-output.png "Model çıkışı")
   

1. Bu son örnekte, organizasyonlar koleksiyonuna bir filtre uygulayın ve NASDAQ tabanlı filtre ölçütlerine yönelik iki eşleşme döndürerek.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2020-06-30
   ```

Bu sorgular, bilişsel arama tarafından oluşturulan yeni alanlara sorgu söz dizimi ve filtreler ile çalışmanın birkaç yolunu gösterir. Daha fazla sorgu örneği için bkz. [arama belgeleri REST API](/rest/api/searchservice/search-documents#bkmk_examples), [basit sözdizimi sorgu örnekleri](search-query-simple-examples.md)ve [tam Lucene sorgu örnekleri](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

Geliştirmede erken deneysel aşamalarda, tasarım yinelemesi için en pratik yaklaşım, nesneleri Azure Bilişsel Arama silmek ve kodunuzun bunları yeniden oluşturması için izin verir. Kaynak adları benzersizdir. Bir nesneyi sildiğinizde, aynı adı kullanarak nesneyi yeniden oluşturabilirsiniz.

Dizinleri, Dizin oluşturucuyu, veri kaynaklarını ve becerileri silmek için portalı kullanabilirsiniz. Dizin oluşturucuyu sildiğinizde, isteğe bağlı olarak, dizini, beceri ve veri kaynağını aynı anda seçmeli olarak silebilirsiniz.

![Arama nesnelerini Sil](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Portalda arama nesnelerini silme")

Ya da **Sil** ' i kullanın ve her bir nesnenin URL 'sini sağlayın. Aşağıdaki komut bir dizin oluşturucuyu siler.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
```

Silme işlemi başarılı olduğunda durum kodu 204 döndürülür.

## <a name="takeaways"></a>Paketler

Bu öğreticide, veri kaynağı, beceri kümesi, dizin ve dizin oluşturucu gibi bileşen parçalarının oluşturulması yoluyla zenginleştirilmiş bir dizin oluşturma işlem hattı oluşturmaya yönelik temel adımlar gösterilmektedir.

[Yerleşik yetenekler](cognitive-search-predefined-skills.md) , Beceri tanımıyla birlikte tanıtılmıştır ve giriş ve çıkışlarla becerilerin zincirlerinin bir araya getirilmiştir. Ayrıca, `outputFieldMappings` bir Azure bilişsel arama hizmetinde işlem hattındaki aranabilir bir dizine yönlendirme değerlerini yönlendirme için Dizin Oluşturucu tanımında gerekli olduğunu öğrenmiş olursunuz.

Son olarak, daha fazla yineleme için sonuçların nasıl test edileceğini ve sistemin nasıl sıfırlanacağını öğrendiniz. Dizine karşı sorgular düzenlendiğinde, zenginleştirilmiş dizin oluşturma işlem hattı tarafından oluşturulan çıktının döndürüldüğünü öğrendiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir projenin sonunda kendi aboneliğinizde çalışırken, artık ihtiyaç duyulmadığınızda kaynakları kaldırmak iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki tüm kaynaklar veya kaynak grupları bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir AI zenginleştirme işlem hattındaki tüm nesneler hakkında bilgi sahibi olduğunuza göre, Beceri tanımlarına ve bireysel becerilere daha yakından göz atalım.

> [!div class="nextstepaction"]
> [Beceri oluşturma](cognitive-search-defining-skillset.md)

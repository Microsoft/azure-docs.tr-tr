---
title: Beceri kümesi oluşturma
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'da kullanmak üzere verilerinize veri ayıklama, doğal dil işleme veya görüntü analizi adımlarını tanımlayarak verileri zenginleştirin ve verilerinizi ayıklayın.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e9fd4602d661dd4223c8caa2ec02eaf56284735a
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114545"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Azure Bilişsel Arama bir AI zenginleştirme ardışık düzeninde beceri oluşturma 

AI zenginleştirme, Azure Bilişsel Arama 'te aranabilir hale getirmek için verileri ayıklar ve zenginleştirir. Ayıklama ve zenginleştirme adımları bilişsel *becerileri*, dizin oluşturma sırasında başvurulan bir *beceri* birleştirilir. Beceri, [yerleşik becerileri](cognitive-search-predefined-skills.md) veya özel becerileri kullanabilir (daha fazla bilgi için bkz. [bir AI zenginleştirme ardışık düzeninde özel bir yetenek oluşturma](cognitive-search-create-custom-skill-example.md) ).

Bu makalede, kullanmak istediğiniz yetenekler için bir zenginleştirme ardışık düzeni oluşturmayı öğreneceksiniz. Bir Azure Bilişsel Arama [dizin oluşturucusuna](search-indexer-overview.md)bir beceri eklenir. Bu makalede ele alınan işlem hattı tasarımının bir parçası, Beceri kendisini tasarlayabilmektedir. 

> [!NOTE]
> Ardışık düzen tasarımının başka bir bölümü, bir Dizin Oluşturucu belirleyerek bir [sonraki adımda](#next-step)ele alınmıştır. Dizin Oluşturucu tanımı, hedef dizindeki çıkışlara girdileri bağlamak için kullanılan beceri ve alan eşlemelerine yönelik bir başvuru içerir.

Anımsanması gereken önemli noktaları:

+ Dizin Oluşturucu başına yalnızca bir beceri olabilir.
+ Bir beceri en az bir yeteneğe sahip olmalıdır.
+ Aynı türde (örneğin, bir görüntü analizi becerilerinin türevleri) birden çok yetenek oluşturabilirsiniz.

## <a name="begin-with-the-end-in-mind"></a>Son göz önünde bulundurularak başlayın

Önerilen ilk adım, ham verilerinize hangi verilerin ayıklanacağını ve bu verileri bir arama çözümünde nasıl kullanmak istediğinizi saptarken. Tüm zenginleştirme işlem hattının bir resmini oluşturmak, gerekli adımları belirlemenize yardımcı olabilir.

Bir dizi finansal analist açıklamasını işlemek istediğinizi varsayalım. Her dosya için, yorumların şirket adlarını ve genel yaklaşımını ayıklamak istersiniz. Ayrıca şirket hakkında şirket hakkında ek bilgiler bulmak için Bing Varlık Arama hizmetini kullanan özel bir zenginte yazmak isteyebilirsiniz. Temelde, her belge için dizine alınmış aşağıdaki gibi bilgileri ayıklamak istersiniz:

| Kaydet-metin | firması | yaklaşım | Şirket açıklamaları |
|--------|-----|-----|-----|
|örnek kayıt| ["Microsoft", "LinkedIn"] | 0.99 | ["Microsoft Corporation, çok uluslu bir teknoloji şirketidir...", "LinkedIn iş ve işe yönelik olarak çalışan bir sosyal ağ..."]

Aşağıdaki diyagramda bir kuramsal zenginleştirme işlem hattı gösterilmektedir:

![Kuramsal bir zenginleştirme işlem hattı](media/cognitive-search-defining-skillset/sample-skillset.png "Kuramsal bir zenginleştirme işlem hattı")


İşlem hattında istediğiniz kadar fikir sahibi olduktan sonra, bu adımları sağlayan beceri ifade edebilirsiniz. İşlev, Dizin Oluşturucu tanımınızı Azure Bilişsel Arama yüklediğinizde beceri ifade edilir. Dizin oluşturucuyu karşıya yükleme hakkında daha fazla bilgi edinmek için bkz. [Dizin Oluşturucu-belgeler](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Diyagramda *belge çözme* adımı otomatik olarak gerçekleşir. Esas olarak, Azure Bilişsel Arama iyi bilinen dosyaların nasıl açılacağını bilir ve her belgeden ayıklanan metni içeren bir *içerik* alanı oluşturur. Beyaz kutular yerleşik zenginler ve noktalı "Bing Varlık Arama" kutusu oluşturmakta olduğunuz özel bir zengini temsil eder. Gösterildiği gibi, beceri üç yetenek içerir.

## <a name="skillset-definition-in-rest"></a>Beceri tanımı REST

Bir beceri bir yetenek dizisi olarak tanımlanır. Her beceri, girişlerinin kaynağını ve üretilen çıktıların adını tanımlar. [Create beceri REST API](https://docs.microsoft.com/rest/api/searchservice/create-skillset)kullanarak, önceki diyagrama karşılık gelen bir beceri tanımlayabilirsiniz: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Beceri kümesi oluşturma

Bir beceri oluştururken, Beceri kendi kendine belgelerinizi yapmak için bir açıklama sağlayabilirsiniz. Bir açıklama isteğe bağlıdır, ancak beceri ne yaptığını izlemek için yararlıdır. Beceri, açıklamalara izin vermediği bir JSON belgesi olduğundan, bunun için bir `description` öğesi kullanmanız gerekir.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Beceri sonraki parçası bir yetenek dizisidir. Her bir beceriye, zenginleştirme için bir temel olarak düşünebilirsiniz. Her beceri, bu zenginleştirme ardışık düzeninde küçük bir görev gerçekleştirir. Her biri bir giriş (veya bir giriş kümesi) alır ve bazı çıktılar döndürür. Sonraki birkaç bölüm yerleşik ve özel yeteneklerin nasıl belirtildiğiyle ilgili olarak, becerileri giriş ve çıkış başvurularıyla birlikte zincirlemeye odaklanacaktır. Girişler, kaynak verilerden veya başka bir becerilerden gelebilir. Çıktılar, bir arama dizinindeki alanla eşleştirilebilir veya bir aşağı akış beceriye giriş olarak kullanılabilir.

## <a name="add-built-in-skills"></a>Yerleşik yetenekler ekleme

Yerleşik [varlık tanıma becerisi](cognitive-search-skill-entity-recognition.md)olan ilk beceriye bakalım:

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
    }
```

* Her yerleşik beceri `odata.type`, `input`ve `output` özelliklere sahiptir. Beceriye özgü özellikler, bu beceriye uygun ek bilgiler sağlar. Varlık tanıma için `categories`, önceden eğitilen modelin tanıyabileceği sabit bir varlık türleri kümesi arasında bir varlıktır.

* Her yeteneğin bir ```"context"```olması gerekir. Bağlam, işlemlerin gerçekleştiği düzeyi temsil eder. Yukarıdaki becerideki bağlam tüm belgedir, yani varlık tanıma becerisi her belge için bir kez çağrılır. Çıkışlar da bu düzeyde oluşturulur. Daha özel olarak, ```"organizations"``` ```"/document"```üyesi olarak oluşturulur. Aşağı akış becerileri ' nda, yeni oluşturulan bu bilgilere ```"/document/organizations"```olarak başvurabilirsiniz.  ```"context"``` alanı açıkça ayarlanmamışsa, varsayılan bağlam belgedir.

* Yeteneğin, kaynak girişi ```"/document/content"```olarak ayarlanmış "metin" adlı bir giriş vardır. Yetenek (varlık tanıma), Azure Blob Indexer tarafından oluşturulan standart bir alan olan her belgenin *içerik* alanı üzerinde çalışır. 

* Yeteneğin ```"organizations"```adlı bir çıkış vardır. Çıkışlar yalnızca işlem sırasında mevcuttur. Bu çıktıyı bir aşağı akış becerisi girişine zincirlemek için, çıkışa ```"/document/organizations"```olarak başvurun.

* Belirli bir belge için, ```"/document/organizations"``` değeri metinden ayıklanan kuruluşların bir dizisidir. Örneğin:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Bazı durumlar, bir dizinin her öğesine ayrı olarak başvurmak için çağrı yapılır. Örneğin, her bir ```"/document/organizations"``` öğesini başka bir yeteneğe (özel Bing varlık arama daha zengin gibi) ayrı olarak geçirmek istediğinizi varsayalım. Yola bir yıldız işareti ekleyerek dizinin her öğesine başvurabilirsiniz: ```"/document/organizations/*"``` 

Yaklaşım ayıklama için ikinci yetenek, ilk zenginleştirme ile aynı kalıbı izler. ```"/document/content"``` giriş olarak alır ve her içerik örneği için bir yaklaşım puanı döndürür. ```"context"``` alanını açıkça ayarlamazsanız, çıkış (mySentiment) artık ```"/document"```bir alt öğesidir.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Özel bir yetenek ekleyin

Özel Bing varlık arama 'nın yapısını daha zengin bir şekilde geri çekin:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Bu tanım, zenginleştirme sürecinin bir parçası olarak bir Web API 'SI çağıran [özel bir yetentandır](cognitive-search-custom-skill-web-api.md) . Bu yetenek, varlık tanıma tarafından tanımlanan her kuruluş için, bu kuruluşun açıklamasını bulmak için bir Web API 'SI çağırır. Web API 'sinin ne zaman çağrılacağını ve alınan bilgilerin nasıl Flow, enzenginleştirme altyapısı tarafından dahili olarak işlenir. Ancak, bu özel API 'yi çağırmak için gereken başlatma işlemi JSON 'da (URI, httpHeaders ve beklenen girişler gibi) sağlanmalıdır. Zenginleştirme işlem hattı için özel Web API 'SI oluşturma konusunda rehberlik için bkz. [özel bir arabirim tanımlama](cognitive-search-custom-skill-interface.md).

"Bağlam" alanının yıldız işaretiyle ```"/document/organizations/*"``` olarak ayarlandığını, yani ```"/document/organizations"```altındaki *her kuruluş için* de zenginleştirme adımını çağırdığını unutmayın. 

Bu durumda, belirtilen her bir kuruluş için bir şirket açıklaması olan çıktı. Bir aşağı akış adımındaki açıklamaya (örneğin, anahtar tümceciği ayıklama) başvururken, bunu yapmak için ```"/document/organizations/*/description"``` yolunu kullanırsınız. 

## <a name="add-structure"></a>Yapı Ekle

Beceri yapılandırılmamış verilerden yapılandırılmış bilgiler üretir. Aşağıdaki örnek göz önünde bulundurun:

*"Dördüncü çeyrekte, Microsoft, geçen yıl satın aldığı sosyal ağ şirketi olan LinkedIn 'ten gelir üzerinden $1.100.000.000 'e kaydedildi. Alım, Microsoft 'un LinkedIn yeteneklerini kendi CRM ve Office özellikleri ile birleştirmesine olanak sağlar. Stockholders şu ana kadar ilerleme durumuyla heyecanlanır. "*

Olası bir sonuç, aşağıdaki çizime benzer şekilde oluşturulmuş bir yapıdır:

![Örnek çıkış yapısı](media/cognitive-search-defining-skillset/enriched-doc.png "Örnek çıkış yapısı")

Şu anda bu yapı yalnızca iç, yalnızca bellek ve Azure Bilişsel Arama dizinlerinde kullanılır. Bilgi deposunun eklenmesi, arama dışında kullanmak üzere şekillendirilmiş zenginleştirme tasarrufu için bir yol sağlar.

## <a name="add-a-knowledge-store"></a>Bilgi deposu ekleme

[Bilgi deposu](knowledge-store-concept-intro.md) , zenginleştirilmiş belgeyi kaydetmek için Azure bilişsel arama bir önizleme özelliğidir. Azure depolama hesabı tarafından desteklenen, oluşturduğunuz bir bilgi deposu, verileri zenginleştirdiği depodur. 

Bir beceri öğesine bilgi deposu tanımı eklenir. İşlemin tamamına yönelik bir anlatım için bkz. [bilgi deposu ile çalışmaya başlama](knowledge-store-howto.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Zenginleştirilmiş belgeleri, hiyerarşik ilişkileri korunan tablolar olarak veya blob depolamada JSON belgeleri olarak kaydetmeyi seçebilirsiniz. Beceri içindeki yeteneklerin herhangi birinden çıkış, projeksiyonun için giriş olarak kaynak olarak oluşturulabilir. Verileri belirli bir şekle göre projeye eklemek istiyorsanız, [her yetenek](cognitive-search-skill-shaper.md) için güncelleştirilmiş mil, artık kullanmanız için karmaşık türleri modelleyebilir. 

<a name="next-step"></a>

## <a name="next-steps"></a>Sonraki adımlar

Artık enzenginleştirme işlem hattı ve becerileri hakkında bilgi sahibi olduğunuza göre, [bir beceri içindeki ek açıklamaların nasıl başvurulacağını](cognitive-search-concept-annotations-syntax.md) veya [bir dizindeki alanlara çıktıların nasıl eşlenilmeye](cognitive-search-output-field-mapping.md)devam edin. 

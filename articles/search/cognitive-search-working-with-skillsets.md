---
title: Beceri kavramları ve iş akışı
titleSuffix: Azure Cognitive Search
description: Becerileri, Azure Bilişsel Arama bir AI zenginleştirme işlem hattı yazar. Beceri Composition hakkında önemli kavramlar ve ayrıntılar hakkında bilgi edinin.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558122"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure Bilişsel Arama Beceri kavramları

Bu makale, Beceri kavramlarını ve kompozisyonunun daha ayrıntılı bir şekilde anlaşılmasına ve AI zenginleştirme süreciyle ilgili olduğunu varsayan geliştiricilere yöneliktir. Bu kavram hakkında yeni başladıysanız [Azure bilişsel arama 'de AI zenginleştirme](cognitive-search-concept-intro.md)ile başlayın.

## <a name="introducing-skillsets"></a>Becerileri tanıtımı

Beceri, Azure Bilişsel Arama bir dizin oluşturucuya eklenmiş bir yeniden kullanılabilir kaynaktır ve dizin oluşturma sırasında metin veya resim içeriğini çözümlemek, dönüştürmek ve zenginleştirme için kullanılan bir yetenek koleksiyonunu belirtir. Yetenekler, giriş ve çıkışları vardır ve genellikle bir beceriye ait çıktı bir zincirde veya işlem dizisinde başka bir şekilde giriş haline gelir.

Bir beceri üç ana özelliğe sahiptir:

+ `skills`, platformun her bir beceri için gereken girişlere göre yürütme sırasını belirleyen sıralanmamış bir yetenek koleksiyonu.
+ `cognitiveServices`, yerleşik becerileri içeren becerileri için görüntü ve metin işleme gerçekleştiren bilişsel hizmetler kaynağının anahtarı.
+ `knowledgeStore`, (isteğe bağlı) zenginleştirilmiş belgelerinizi tahmin edecek bir Azure depolama hesabı. Zenginleştirilmiş belgeler, arama dizinleri tarafından da kullanılır.

Becerileri, JSON içinde yazılır. Aşağıdaki örnek bu [otelin](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)biraz Basitleştirilmiş sürümüdür. Bu makaledeki kavramları göstermek için kullanılan beceri. 

İlk iki beceri aşağıda gösterilmiştir:

+ Beceri #1, "reviews_text" alanının içeriğini girdi olarak kabul eden ve bu içeriği çıkış olarak 5000 karakterlik "Pages" olarak ayıran [metin bölünmüş bir yetenbedir](cognitive-search-skill-textsplit.md) .
+ Beceri #2, bir yaklaşım [algılama beceriydi](cognitive-search-skill-sentiment.md) "sayfaları" girdi olarak kabul eder ve "yaklaşım" adlı yeni bir alanı, yaklaşım analizinin sonuçlarını içeren çıktı olarak oluşturur.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
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
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> İfadeleri oluşturmak için [koşullu yeteneği](cognitive-search-skill-conditional.md) kullanarak, döngü ve dallandırma ile karmaşık becerileri oluşturabilirsiniz. Sözdizimi, [JSON işaretçisi](https://tools.ietf.org/html/rfc6901) yolu gösterimini temel alır. Bu, zenginleştirme ağacındaki düğümleri belirlemek için birkaç değişiklik ile yapılır. `"/"`, Ağaçta daha düşük bir düzeye geçer ve `"*"` bağlamdaki her bir için bir for-each işleci görevi görür. Bu makaledeki birçok örnek söz dizimini gösterir. 

### <a name="enrichment-tree"></a>Zenginleştirme ağacı

[Bir zenginleştirme ardışık düzeninde adım](cognitive-search-concept-intro.md#enrichment-steps)ilerleme halinde, içerik işleme, metin ve görüntülerin kaynaktan ayıklandığı *belge çözme* aşamasını izler. Görüntü içeriği daha sonra görüntü işlemeyi belirten yeteneklere yönlendirilebilir, ancak metin içeriği metin işleme için sıraya alınır. Büyük miktarlarda metin içeren kaynak belgeler için, Dizin Oluşturucu üzerinde bir *ayrıştırma modunu* , daha iyi işleme sağlamak üzere metin için daha küçük parçalara ayırmak üzere ayarlayabilirsiniz. 

![Ardışık düzen diyagramında bilgi deposu](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Ardışık düzen diyagramında bilgi deposu")

Belge, zenginleştirme ardışık düzeninde olduktan sonra bir içerik ağacı ve ilişkili zenginler olarak temsil edilir. Bu ağaç belge çözme çıktısı olarak oluşturulur.  Zenginleştirme ağacı biçimi, enzenginleştirme işlem hattının meta verileri hatta ilkel veri türlerine iliştirmesine olanak sağlar, bu geçerli bir JSON nesnesi değildir ancak geçerli bir JSON biçiminde yansıtılmalıdır. Aşağıdaki tabloda, zenginleştirme ardışık düzenine giriş bir belgenin durumu gösterilmektedir:

|Veri kaynağı \ ayrıştırma modu|Varsayılan|JSON, JSON satırları & CSV|
|---|---|---|
|Blob Depolama|/Document/Content<br>/Document/normalized_images/*<br>…|/Document/{KEY1}<br>/Document/{key2}<br>…|
|SQL|/Document/{column1}<br>/Document/{Column2}<br>…|Yok |
|Cosmos DB|/Document/{KEY1}<br>/Document/{key2}<br>…|Yok|

 Yetenekler yürütülürken, yeni düğümleri zenginleştirme ağacına ekler. Bu yeni düğümler daha sonra aşağı akış becerileri, bilgi deposuna yansıtma veya dizin alanlarıyla eşleme için giriş olarak kullanılabilir. Enrichments değişebilir değil: oluşturulduktan sonra düğümler düzenlenemez. Becerileri daha karmaşık olsa da, zenginleştirme ağacınızı oluşturur, ancak zenginleştirme ağacındaki tüm düğümlerin dizin veya bilgi deposu üzerinde olması gerekmez. 

Dizine veya bilgi deposuna yalnızca zenginlerin bir alt kümesini seçerek kalıcı hale getirebilirsiniz.

### <a name="context"></a>Bağlam

Her yetenek bir bağlam gerektirir. Bağlam şunları belirler:

+ Seçilen düğümlere göre yeteneğin kaç kez yürütüldüğünü. Koleksiyon türü bağlam değerleri için, sonunda bir eklemek, `/*` niteliğin koleksiyondaki her örnek için bir kez çağrılmasına neden olur. 

+ Enzenginleştirme ağacında, yetenek çıkışları eklenir. Çıktılar, her zaman bağlam düğümünün alt öğeleri olarak ağaca eklenir. 

+ Girişlerin şekli. Çoklu düzey koleksiyonlar için, bağlamı üst koleksiyon olarak ayarlamak, Beceri girişinin şeklini etkiler. Örneğin, ülkelerin/bölgelerin listesi içeren bir zenginleştirme ağacıyla karşılaşırsanız, her biri bir posta kodu listesi içeren bir eyalet listesiyle zenginleştirir.

|Bağlam|Giriş|Giriş şekli|Yetenek çağırma|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Ülke/bölgedeki tüm posta kodlarının listesi |Ülke/bölge başına bir kez |
|`/document/countries/*/states/*` |'/Document/countries/*/States/*/ZipCodes/* ' ' |Durumdaki posta kodlarının listesi | Ülke/bölge ve eyalet birleşimine göre bir kez|

## <a name="generate-enriched-data"></a>Zenginleştirilmiş veriler oluşturma 

[Otel İncelemeleri beceri](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) bir başvuru noktası olarak kullanıldığında şu noktaya bakacağız:

+ Zenginleştirme ağacının her bir yeteneğin yürütülmesiyle nasıl geliştikçe
+ Bağlam ve girdilerin bir yeteneğin kaç kez çalışacağını belirleme
+ Girişin şekli bağlam temelinde

Zenginleştirme işlemindeki bir "belge", hotel_reviews.csv kaynak dosyası içinde tek bir satırı (otel incelemesi) temsil eder.

### <a name="skill-1-split-skill"></a>Yetenek #1: bölünmüş yetenek

Kaynak içerik büyük metin öbeklerinden oluşuyorsa, dilin, yaklaşım ve anahtar tümceciği algılama hakkında daha fazla doğruluk sağlamak için onu daha küçük bileşenlere bölmek faydalı olur. Kullanılabilecek iki giriş vardır: sayfalar ve cümleler. Bir sayfa yaklaşık 5000 karakterden oluşur.

Metin bölünmüş beceri genellikle ilk olarak bir beceri.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

Yetenek bağlamı ile `"/document/reviews_text"` bölünmüş yetenek, için bir kez yürütülür `reviews_text` . Yetenek çıkışı, `reviews_text` 5000 karakter segmentlerinde yer aldığı bir listesidir. Bölünmüş yeteneğin çıktısı adlandırılır `pages` ve bu, zenginleştirme ağacına eklenir. `targetName`Özelliği, bir yetenek çıkışını, zenginleştirme ağacına eklenmeden önce yeniden adlandırmanızı sağlar.

Zenginleştirme ağacının artık yetenek bağlamı altına yerleştirilmiş yeni bir düğümü vardır. Bu düğüm herhangi bir yetenek, projeksiyon veya çıkış alanı eşlemesinde kullanılabilir. Kavramsal olarak, ağaç şöyle görünür:

![Belge çözme sonrasında zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Belge çözme ve yetenek yürütmeden önce zenginleştirme ağacı")

Tüm zenginleştirmeleri için kök düğüm `"/document"` . Blob Dizin oluşturucularla çalışırken `"/document"` düğüm ve alt düğümlerine sahip olur `"/document/content"` `"/document/normalized_images"` . CSV verileriyle çalışırken, bu örnekte olduğu gibi, sütun adları altındaki düğümlerle eşlenir `"/document"` . 

Bir düğüme bir yeteneğe göre eklenen her türlü zenginleştirme için, zenginleştirme için tam yol gereklidir. Örneğin, ```pages``` düğümdeki metni başka bir beceriye girdi olarak kullanmak istiyorsanız, bunu olarak belirtmeniz gerekir ```"/document/reviews_text/pages/*"``` .
 
 ![beceriye #1 sonra zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Beceri #1 yürütüldükten sonra zenginleştirme ağacı")

### <a name="skill-2-language-detection"></a>Beceri #2 dil algılama

Otel gözden geçirme belgeleri, birden çok dilde ifade edilen müşteri geri bildirimini içerir. Dil algılama becerisi, hangi dilin kullanıldığını belirler. Daha sonra sonuç, anahtar tümceciği ayıklama ve yaklaşım algılamasına geçirilir ve yaklaşım ve tümceleri algılamada dil dikkate alınmaz.

Dil algılama becerisi, Beceri içinde tanımlanan üçüncü (yetenek #3) beceriye sahip olsa da, sonraki bir yetenek de yürütülür. Herhangi bir giriş gerektirerek engellenmediğinden, bu, önceki beceriyle paralel olarak yürütülür. Önünde bulunan bölünmüş beceri gibi, dil algılama becerisi de her belge için bir kez çağrılır. Zenginleştirme ağacının artık dil için yeni bir düğümü vardır.

 ![beceriye #2 sonra zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Beceri #2 yürütüldükten sonra zenginleştirme ağacı")
 
 ### <a name="skill-3-key-phrases-skill"></a>Yetenek #3: anahtar tümceleri yeteneği 

Anahtar tümceleri bağlamında belirtilen bağlam, `/document/reviews_text/pages/*` koleksiyondaki her öğe için bir kez çağrılacaktır `pages` . Beceriye ait çıkış, ilişkili sayfa öğesinin altındaki bir düğüm olacaktır. 

 Artık beceri 'deki becerilerin geri kalanında bakabilmeniz ve zenginleştirme ağacının her bir yeteneğin yürütülmesiyle nasıl devam edeceği hakkında görselleştirmeniz gerekir. Birleştirme yeteneği ve mil başına beceri gibi bazı yetenekler de yeni düğümler oluşturur, ancak yalnızca var olan düğümlerdeki verileri kullanır ve net yeni zenginleştirme oluşturmazsınız.

![Tüm becerilerden sonra zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Tüm becerilerden sonra zenginleştirme ağacı")

Yukarıdaki ağaçtaki bağlayıcıların renkleri, zenginleştirmeleri farklı yetenekler tarafından oluşturulduğunu ve düğümlerin ayrı ayrı adreslenmesi gerektiğini ve üst düğümü seçerken döndürülen nesnenin bir parçası olmayacaktır.

## <a name="save-enrichments"></a>Zenginleştirme Kaydet

Azure Bilişsel Arama, Dizin Oluşturucu oluşturduğu çıktıyı kaydeder. Çıktılardan biri her zaman aranabilir bir [dizindir](search-what-is-an-index.md). Bir dizin belirtilmesi bir gereksinimdir ve bir beceri iliştirmeye çalıştığınızda, bir dizin tarafından alınan veriler, enrichments 'in bir kopyasını içerir. Genellikle, anahtar tümceleri veya yaklaşım puanları gibi belirli yeteneklerin çıkışları, bu amaçla oluşturulan bir alandaki dizine alınır.

İsteğe bağlı olarak, bir Dizin Oluşturucu aynı zamanda çıktıyı diğer araç veya süreçlerdeki tüketim için bir [bilgi deposuna](knowledge-store-concept-intro.md) gönderebilir. Bilgi deposu, beceri bir parçası olarak tanımlanmıştır. Bu tanım, zenginleştirilmiş belgelerinizin tablo veya nesne (dosya veya blob) olarak yansıtıldığını belirler. Tablo projeksiyonları Power BI gibi araçlarla etkileşimli analizler için uygundur, ancak dosyalar ve Bloblar genellikle veri bilimi veya benzer işlemlerde kullanılır. Bu bölümde, Beceri kompozisyonunun proje yapmak istediğiniz tabloları veya nesneleri nasıl şekillendirebileceğinizi öğreneceksiniz.

### <a name="projections"></a>Yansıtmalar

Bilgi deposunu hedefleyen içerikler için içeriğin nasıl yapılandırıldığını düşünmek isteyeceksiniz. *Projeksiyon* , enzenginleştirme ağacından düğümleri seçme ve bilgi deposunda bunların fiziksel bir ifadesini oluşturma işlemidir. Projeksiyonlar, tablo veya nesne projeksiyonları olarak çıkış olabilecek belgenin (içerik ve zenginler) özel şekillerinden oluşur. Yansıtmalarda çalışma hakkında daha fazla bilgi edinmek için bkz. [projeksiyonlarla çalışma](knowledge-store-projection-overview.md).

![Alan eşleme seçenekleri](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Zenginleştirme işlem hattı için alan eşleme seçenekleri")

### <a name="sourcecontext"></a>SourceContext

`sourceContext`Öğesi yalnızca yetenek girişlerinde ve projeksiyde kullanılır. Çok düzeyli, iç içe geçmiş nesneler oluşturmak için kullanılır. Bilgi deposuna bir yeteneğe veya projeye giriş olarak geçirmek için yeni bir nesne oluşturmanız gerekebilir. Enzenginleştirme düğümleri, enzenginleştirme ağacında geçerli bir JSON nesnesi olmayabilir ve ağaçtaki bir düğüme başvurmak, Beceri girişleri veya projeksiyonları, iyi biçimlendirilmiş bir JSON nesnesi oluşturmanızı gerektirir. `sourceContext`Yalnızca bağlamını kullanıyorsanız, birden çok yetenek gerektiren hiyerarşik, anonim bir tür nesnesi oluşturmanız mümkün olur. 

`sourceContext`Aşağıdaki örneklerde kullanılması gösterilmiştir. Temel bir tür değil geçerli bir JSON nesnesi olup olmadığını öğrenmek için bir zenginleştirme oluşturan yetenek çıktısına bakın.

### <a name="slicing-projections"></a>Projeksiyonları Dilimleme

Bir tablo projeksiyon grubu tanımlarken, zenginleştirme ağacındaki tek bir düğüm birden çok ilişkili tabloya dilimlenebilir. Varolan bir tablo projeksiyonunun alt öğesi olan bir kaynak yolu içeren bir tablo eklerseniz, sonuçta elde edilen alt düğüm mevcut tablo projeksiyonunun bir alt öğesi olmaz, bunun yerine yeni, ilişkili, tablo olarak yansıtılacaktır. Bu dilimleme tekniği, tüm tablo projeksiyonlarınızın kaynağı olabilecek her bir şekilde bir mil içinde tek bir düğüm tanımlamanızı sağlar. 

### <a name="shaping-projections"></a>Projeksiyonları şekillendirme

Projeksiyon tanımlamanın iki yolu vardır:

+ Her şey için metin Biçimlendiricini kullanarak, yansıtıyorsunuz tüm zenginler için kök düğüm olan yeni bir düğüm oluşturun. Daha sonra, tahminlerinizin her bir yetenek için yalnızca mil çıkışına başvurabilirsiniz.

+ İzdüşüm tanımının içinde projeksiyonu bir satır içi şekil kullanın.

Mil başına her yaklaşım, satır içi şekillendirenden daha ayrıntılıdır, ancak en zenginleştirme ağacının tüm mutasyonların beceriler içinde yer almasını ve çıktının yeniden kullanılabilen bir nesne olmasını sağlar. Buna karşılık satır içi şekillendirme, ihtiyacınız olan şekli oluşturmanıza izin verir, ancak anonim bir nesnedir ve yalnızca tanımlandığı projeksiyon için kullanılabilir. Yaklaşımlar birlikte veya ayrı bir şekilde kullanılabilir. Portal iş akışında sizin için oluşturulan beceri her ikisini de içerir. Tablo projeksiyonlar için bir Shaper kullanır, ancak anahtar tümceleri tablosunu proje için de satır içi şekillendirme kullanır.

Örneği genişletmek için, satır içi şekillendirme 'yı kaldırmayı ve anahtar tümceleri için yeni bir düğüm oluşturmak üzere her beceri için bir mil kullanmayı seçebilirsiniz. ,, Ve gibi üç tablo halinde tasarlanan bir şekil oluşturmak için, `hotelReviewsDocument` `hotelReviewsPages` `hotelReviewsKeyPhrases` iki seçenek aşağıdaki bölümlerde açıklanmıştır.

#### <a name="shaper-skill-and-projection"></a>Beceri ve projeksiyon başına mil

> [!Note]
> Belge tablosundaki sütunlardan bazıları, kısaltma için bu örnekte kaldırılmıştır.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

`tableprojection`Yukarıdaki bölümde tanımlanan düğüm ile `outputs` , artık düğüm parçalarını farklı tablolara proje için dilimleme özelliğini kullanabilirsiniz `tableprojection` :

> [!Note]
> Bu, bilgi deposu yapılandırması içindeki projeksiyonun yalnızca bir parçacığı değildir.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Satır içi şekillendirme projeksiyonlarını

Satır içi şekillendirme yaklaşımına, her türlü şekil gerektiği sırada oluşturulduklarında, hiçbir yetenek için mil gerekmez. Önceki örnekle aynı verileri de proje yapmak için satır içi projeksiyon seçeneği şöyle görünür:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Her iki yaklaşımdan bir gözlem `"Keyphrases"` , değerlerinin kullanılarak yansıtıldır `"sourceContext"` . `"Keyphrases"`Bir dize koleksiyonu içeren düğüm, kendi kendine sayfa metninin bir alt öğesidir. Ancak, projeksiyler bir JSON nesnesi gerektirdiğinden ve sayfa basit (dize) olduğundan, `"sourceContext"` anahtar tümceciğini adlandırılmış bir özelliğe sahip bir nesneye kaydırmak için kullanılır. Bu teknik, Çift temellerden bağımsız olarak yansıtılmalarını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım olarak bilişsel becerilerle ilk beceri oluşturun.

> [!div class="nextstepaction"]
> [İlk beceri oluşturun](cognitive-search-defining-skillset.md).

---
title: Beceri kavramları ve iş akışı
titleSuffix: Azure Cognitive Search
description: Becerileri, Azure Bilişsel Arama bir AI zenginleştirme işlem hattı yazar. Beceri Composition hakkında önemli kavramlar ve ayrıntılar hakkında bilgi edinin.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: acf7305a46e9fc3d19f96f88cf2e9ab5eacddd7c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113653"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Azure Bilişsel Arama Beceri kavramları ve bileşimi

Bu makale, enzenginleştirme ardışık düzeninin nasıl çalıştığını daha ayrıntılı olarak anlamak isteyen geliştiriciler için ve AI zenginleştirme işlemini kavramsal olarak anladığınızı varsayar. Bu kavram hakkında yeni bir kavram varsa şu ile başlayın:
+ [Azure Bilişsel Arama AI zenginleştirme](cognitive-search-concept-intro.md)
+ [Bilgi deposu (Önizleme)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Beceri belirtin
Bir beceri, dizin oluşturma sırasında metin veya resim içeriğini çözümlemek, dönüştürmek ve zenginleştirmek için kullanılan bilişsel yetenekler koleksiyonunu belirten, Azure Bilişsel Arama yeniden kullanılabilir bir kaynaktır. Bir beceri oluşturmak, veri alma aşamasında metin ve görüntü zenginleştirmelerini, ham içerikten yeni bilgi ve yapıları ayıklamanıza ve oluşturmaya olanak sağlar.

Bir beceri üç özelliğe sahiptir:

+   ```skills```, platformun her beceri için gereken girişlere göre yürütme sırasını belirleyen, sıralanmamış bir yetenek koleksiyonu
+   ```cognitiveServices```bilişsel hizmetler anahtarı, çağrılan bilişsel yeteneklerin faturalandırılması için gereklidir
+   ```knowledgeStore```, zenginleştirilmiş belgelerinizi tahmin edilecek depolama hesabı



Becerileri, JSON içinde yazılır. [İfade dilini](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)kullanarak döngü ve [dallanma](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) ile karmaşık becerileri oluşturabilirsiniz. İfade dili, enzenginleştirme ağacındaki düğümleri tanımlamak için birkaç değişiklik ile [JSON işaretçi](https://tools.ietf.org/html/rfc6901) yolu gösterimini kullanır. ```"/"``` ağaçta daha düşük bir düzeye geçer ve ```"*"``` bağlamda her bir for-each işleci olarak davranır. Bu kavramlar bir örnekle en iyi şekilde açıklanmıştır. Kavramların ve yeteneklerin bazılarını göstermek için [otel İncelemeleri örnek beceri gözden geçiririz](knowledge-store-connect-powerbi.md) . Verileri içeri aktarma iş akışını beceri bir kez daha sonra görüntülemek için, [beceri almak](https://docs.microsoft.com/rest/api/searchservice/get-skillset)için bir REST API istemcisi kullanmanız gerekir.

### <a name="enrichment-tree"></a>Zenginleştirme ağacı

Bir beceri, belgenizi aşamalı olarak nasıl bir şekilde zenginleştirmesi için, belgenin herhangi bir zenginleştirme öncesinde nasıl göründüğünü görelim. Belge çözme çıkışı, veri kaynağına ve belirli ayrıştırma moduna bağımlıdır. Bu Ayrıca, arama dizinine veri eklenirken [alan eşlemelerinin](search-indexer-field-mappings.md) içerik kaynağı olarak kullandığı belgenin durumudur.
![Ardışık düzen diyagramında bilgi deposu](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "KŞimdi işlem hattı diyagramında depola ")

Belge, zenginleştirme ardışık düzeninde olduktan sonra bir içerik ağacı ve ilişkili zenginler olarak temsil edilir. Bu ağaç belge çözme çıktısı olarak oluşturulur. Zenginleştirme ağacı biçimi, enzenginleştirme işlem hattının meta verileri hatta ilkel veri türlerine iliştirmesine olanak sağlar, bu geçerli bir JSON nesnesi değildir ancak geçerli bir JSON biçiminde yansıtılmalıdır. Aşağıdaki tabloda, zenginleştirme ardışık düzenine giriş bir belgenin durumu gösterilmektedir:

|Veri kaynağı \ ayrıştırma modu|Varsayılan|JSON, JSON satırları & CSV|
|---|---|---|
|Blob Storage|/Document/Content<br>/Document/normalized_images/*<br>…|/Document/{KEY1}<br>/Document/{key2}<br>…|
|SQL|/Document/{column1}<br>/Document/{Column2}<br>…|Yok |
|Cosmos DB|/Document/{KEY1}<br>/Document/{key2}<br>…|Yok|

 Yetenekler yürütülürken, yeni düğümleri zenginleştirme ağacına ekler. Bu yeni düğümler daha sonra aşağı akış becerileri, bilgi deposuna yansıtma veya dizin alanlarıyla eşleme için giriş olarak kullanılabilir. Enrichments değişebilir değil: oluşturulduktan sonra düğümler düzenlenemez. Becerileri daha karmaşık olsa da, zenginleştirme ağacınızı oluşturur, ancak zenginleştirme ağacındaki tüm düğümlerin dizin veya bilgi deposu üzerinde olması gerekmez. Dizine veya bilgi deposuna yalnızca zenginlerin bir alt kümesini seçerek kalıcı hale getirebilirsiniz.

Dizine veya bilgi deposuna yalnızca zenginlerin bir alt kümesini seçerek kalıcı hale getirebilirsiniz.
Bu belgenin geri kalanında, [otel İncelemeleri örneği](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)ile çalıştık, ancak aynı kavramlar diğer tüm veri kaynaklarından belge zenginleştirilmesi için de geçerlidir.

### <a name="context"></a>Bağlam
Her yetenek bir bağlam gerektirir. Bağlam şunları belirler:
+   Seçilen düğümlere göre yeteneğin kaç kez yürütüldüğünü. Koleksiyon türü bağlam değerleri için, sonunda ```/*``` eklemek, niteliğin koleksiyondaki her örnek için bir kez çağrılmasına neden olur. 
+   Enzenginleştirme ağacında, yetenek çıkışları eklenir. Çıktılar, her zaman bağlam düğümünün alt öğeleri olarak ağaca eklenir. 
+   Girişlerin şekli. Çoklu düzey koleksiyonlar için, bağlamı üst koleksiyon olarak ayarlamak, yeteneğin giriş şeklini etkiler. Örneğin, ülkelerin listesini içeren bir zenginleştirme ağacınızı kullanıyorsanız, her biri bir ZipCodes listesi içeren bir eyalet listesi ile zenginleştirir.

|Bağlam|Girdi|Giriş şekli|Yetenek çağırma|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Ülke içindeki tüm ZipCodes listesi |Ülke başına bir kez |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Durumdaki ZipCodes listesi | Ülke ve eyalet birleşimine göre|

### <a name="sourcecontext"></a>SourceContext

`sourceContext`, beceri ve [projeksiyonlar](knowledge-store-projection-overview.md)için yalnızca [mil](cognitive-search-skill-shaper.md) olarak kullanılır. Çok düzeyli, iç içe geçmiş nesneler oluşturmak için kullanılır. `sourceContext`, yalnızca bağlamını kullanıyorsanız, birden çok yetenek gerektiren hiyerarşik, anonim bir tür nesnesi oluşturmanız mümkün olur. `sourceContext` kullanmak sonraki bölümde gösterilmiştir.

### <a name="projections"></a>Yansıtmalar

Projeksiyon, bilgi deposuna kaydedilecek zenginleştirme ağacından düğümleri seçme işlemidir. Projeksiyonlar, tablo veya nesne projeksiyonları olarak çıkış olabilecek belgenin (içerik ve zenginler) özel şekillerinden oluşur. Yansıtmalarda çalışma hakkında daha fazla bilgi edinmek için bkz. [projeksiyonlarla çalışma](knowledge-store-projection-overview.md).

![Alan eşleme seçenekleri](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Zenginleştirme işlem hattı için alan eşleme seçenekleri")

Yukarıdaki diyagramda, zenginleştirme ardışık düzeninde olduğunuz yere göre çalıştığınız seçici açıklanmaktadır.

## <a name="generate-enriched-data"></a>Zenginleştirilmiş veriler oluşturma 

Şimdi de otel incelemeleriyle Beceri, beceri oluşturmak veya beceri [görüntülemek](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) için [öğreticiyi](knowledge-store-connect-powerbi.md) izleyebilirsiniz. Şurada görüneceğiz:

* zenginleştirme ağacının her bir yeteneğin yürütülmesiyle nasıl geliştikçe 
* bağlam ve girdilerin bir yeteneğin kaç kez çalışacağını belirleme 
* girişin şekli bağlamı temel alır. 

Dizin Oluşturucu için ayrılmış metin ayrıştırma modunu kullandığımızdan, zenginleştirme işlemindeki bir belge CSV dosyası içinde tek bir satırı temsil eder.

### <a name="skill-1-split-skill"></a>Yetenek #1: bölünmüş yetenek 

![Belge çözme sonrasında zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Belge çözme ve yetenek yürütmeden önce zenginleştirme ağacı")

```"/document/reviews_text"```yetenek bağlamı ile, bu beceri `reviews_text`bir kez yürütülür. Yetenek çıkışı, `reviews_text` 5000 karakter segmentlerinde öbekli bir listesidir. Bölünmüş yeteneğin çıktısı `pages` olarak adlandırılır ve zenginleştirme ağacına eklenir. `targetName` özelliği, bir yetenek çıkışını, zenginleştirme ağacına eklenmeden önce yeniden adlandırmanızı sağlar.

Zenginleştirme ağacının artık yetenek bağlamı altına yerleştirilmiş yeni bir düğümü vardır. Bu düğüm herhangi bir yetenek, projeksiyon veya çıkış alanı eşlemesinde kullanılabilir.


Tüm zenginleştirmeleri için kök düğüm `"/document"`. Blob dizinleyicilerle çalışırken, `"/document"` düğümü `"/document/content"` ve `"/document/normalized_images"`alt düğümlerine sahip olur. CSV verileriyle çalışırken, bu örnekte olduğu gibi, sütun adları `"/document"`altındaki düğümlerle eşlenir. Bir düğüme bir yeteneğe göre eklenen her türlü zenginleştirme için, zenginleştirme için tam yol gereklidir. Örneğin, ```pages``` düğümündeki metni başka bir beceriye girdi olarak kullanmak istiyorsanız, bunu ```"/document/reviews_text/pages/*"```olarak belirtmeniz gerekir.
 
 ![beceriye #1 sonra zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Beceri #1 yürütüldükten sonra zenginleştirme ağacı")

### <a name="skill-2-language-detection"></a>Beceri #2 dil algılama
 Dil algılama becerisi, Beceri içinde tanımlanan üçüncü (yetenek #3) beceriye sahip olsa da, sonraki bir yetenek de yürütülür. Herhangi bir giriş gerektirerek engellenmediğinden, bu, önceki beceriyle paralel olarak yürütülür. Önünde bulunan bölünmüş beceri gibi, dil algılama becerisi de her belge için bir kez çağrılır. Zenginleştirme ağacının artık dil için yeni bir düğümü vardır.
 ![beceriye #2 sonra zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "EnBeceri #2 yürütüldükten sonra zenginleştirme ağacı ")
 
 ### <a name="skill-3-key-phrases-skill"></a>Yetenek #3: anahtar tümceleri yeteneği 

```/document/reviews_text/pages/*``` bağlamı verildiğinde, `pages` koleksiyonundaki her öğe için anahtar tümceleri yeteneği bir kez çağrılır. Beceriye ait çıkış, ilişkili sayfa öğesinin altındaki bir düğüm olacaktır. 

 Artık beceri 'deki becerilerin geri kalanında bakabilmeniz ve zenginleştirme ağacının her bir yeteneğin yürütülmesiyle nasıl devam edeceği hakkında görselleştirmeniz gerekir. Birleştirme yeteneği ve mil başına beceri gibi bazı yetenekler de yeni düğümler oluşturur, ancak yalnızca var olan düğümlerdeki verileri kullanır ve net yeni zenginleştirme oluşturmazsınız.

![Tüm becerilerden sonra zenginleştirme ağacı](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Tüm becerilerden sonra zenginleştirme ağacı")

Yukarıdaki ağaçtaki bağlayıcıların renkleri, zenginleştirmeleri farklı yetenekler tarafından oluşturulduğunu ve düğümlerin ayrı ayrı adreslenmesi gerektiğini ve üst düğümü seçerken döndürülen nesnenin bir parçası olmayacaktır.

## <a name="save-enrichments-in-a-knowledge-store"></a>Bilgi deposuna zenginleştirme kaydetme 

Becerileri Ayrıca, zenginleştirilmiş belgelerin tablo veya nesne olarak yansıtılabileceği bir bilgi deposu da tanımlar. Zenginleştirilmiş verilerinizi bilgi deposuna kaydetmek için zenginleştirilmiş belgenin bir kümesini tanımlarsınız. Bilgi deposu hakkında daha fazla bilgi edinmek için bkz. [bilgi deposuna genel bakış](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Projeksiyonları Dilimleme

Bir tablo projeksiyon grubu tanımlarken, zenginleştirme ağacındaki tek bir düğüm birden çok ilişkili tabloya dilimlenebilir. Varolan bir tablo projeksiyonunun alt öğesi olan bir kaynak yolu içeren bir tablo eklerseniz, sonuçta elde edilen alt düğüm mevcut tablo projeksiyonunun bir alt öğesi olmaz, bunun yerine yeni, ilişkili, tablo olarak yansıtılacaktır. Bu dilimleme tekniği, tüm tablo projeksiyonlarınızın kaynağı olabilecek her bir şekilde bir mil içinde tek bir düğüm tanımlamanızı sağlar. 

### <a name="shaping-projections"></a>Projeksiyonları şekillendirme

Projeksiyon tanımlamanın iki yolu vardır. Her türlü beceriye, yansıtıyorsunuz tüm zenginler için kök düğüm olan yeni bir düğüm oluşturmak için bir mil kullanabilirsiniz. Daha sonra, tahminlerinizin her bir yetenek için yalnızca mil çıkışına başvurabilirsiniz. Ayrıca, İzdüşüm tanımının içinde bir projeksiyonun satır içi şeklini de oluşturabilirsiniz.

Mil başına her yaklaşım, satır içi şekillendirenden daha ayrıntılıdır, ancak en zenginleştirme ağacının tüm mutasyonların beceriler içinde yer almasını ve çıktının yeniden kullanılabilen bir nesne olmasını sağlar. Satır içi şekillendirme, ihtiyacınız olan şekli oluşturmanıza izin verir, ancak anonim bir nesnedir ve yalnızca tanımlandığı projeksiyon için kullanılabilir. Yaklaşımlar birlikte veya ayrı bir şekilde kullanılabilir. Portal iş akışında sizin için oluşturulan beceri her ikisini de içerir. Tablo projeksiyonlar için bir Shaper kullanır, ancak anahtar tümceleri tablosunu proje için de satır içi şekillendirme kullanır.

Örneği genişletmek için, satır içi şekillendirme 'yı kaldırmayı ve anahtar tümceleri için yeni bir düğüm oluşturmak üzere her beceri için bir mil kullanmayı seçebilirsiniz. Üç tabloya (yani `hotelReviewsDocument`, `hotelReviewsPages`ve `hotelReviewsKeyPhrases`tasarlanan bir şekil oluşturmak için, iki seçenek aşağıdaki bölümlerde açıklanmıştır.


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

Yukarıdaki `outputs` bölümünde tanımlanan `tableprojection` düğümü ile, artık `tableprojection` düğümünün parçalarını farklı tablolara proje için dilimleme özelliğini kullanabilirsiniz:

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
  
Her iki yaklaşımdan bir gözlem, `"Keyphrases"` değerlerinin `"sourceContext"`kullanılarak nasıl yansıtıldır. Dizelerin bir koleksiyonunu içeren `"Keyphrases"` düğümü, sayfa metninin bir alt öğesidir. Ancak, projeksiyler bir JSON nesnesi gerektirdiğinden ve sayfa basit (dize) olduğundan, `"sourceContext"` anahtar tümceciğini adlandırılmış bir özelliğe sahip bir nesneye kaydırmak için kullanılır. Bu teknik, Çift temellerden bağımsız olarak yansıtılmalarını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım olarak bilişsel becerilerle ilk beceri oluşturun.

> [!div class="nextstepaction"]
> [İlk beceri oluşturun](cognitive-search-defining-skillset.md).

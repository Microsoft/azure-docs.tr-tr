---
title: Anlam yanıtı döndürme
titleSuffix: Azure Cognitive Search
description: Bir anlam yanıtının birleşimini ve bir sonuç kümesinden nasıl yanıt alınacağını açıklar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722438"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Azure Bilişsel Arama anlam yanıtı döndürme

> [!IMPORTANT]
> Anlamsal arama genel önizlemede, yalnızca önizleme REST API kullanılabilir. Önizleme özellikleri, olduğu gibi, [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)altında sunulur ve genel kullanıma sunulmakta olan uygulamanın garantisi yoktur. Bu özellikler faturalandırılabilir. Daha fazla bilgi için bkz. [kullanılabilirlik ve fiyatlandırma](semantic-search-overview.md#availability-and-pricing).

Bir [anlam sorgusu](semantic-how-to-query-request.md)oluştururken, isteğe bağlı olarak, sorguyu doğrudan "yanıtlar" olan en üst eşleşen belgelerden içeriği ayıklayabilirsiniz. Bir veya daha fazla yanıt yanıta eklenebilir, bu, daha sonra uygulamanızın kullanıcı deneyimini geliştirmek için bir arama sayfasında işleyebilir.

Bu makalede, anlam yanıtı isteme, yanıtı açma ve yüksek kaliteli yanıtlar üreten içerik özelliklerinin en çok ne olduğunu öğreneceğinizi öğrenin.

## <a name="prerequisites"></a>Önkoşullar

[Anlamsal sorgular](semantic-how-to-query-request.md) için uygulanan tüm Önkoşullar, hizmet katmanı ve bölgesi de dahil olmak üzere yanıtlar için de geçerlidir.

+ Sorgu mantığı anlam sorgu parametrelerini ve "yanıtlar" parametresini içermelidir. Gerekli parametreler Bu makalede ele alınmıştır.

+ Kullanıcı tarafından girilen sorgu dizelerinin, bir sorunun (ne, nerede, ne zaman, nasıl) özelliklerine sahip dilde formül oluşturulması gerekir.

+ Arama belgelerinin bir yanıtın özelliklerine sahip bir metin içermesi ve bu metnin "searchFields" bölümünde listelenen alanlardan birinde bulunması gerekir. Örneğin, "karma tablo nedir" sorgusu verildiğinde, searchFields hiçbiri "bir karma tablo..." öğesini içeren bir yanıt içermiyorsa bir yanıtın döndürülmemiş olması beklenmez.

## <a name="what-is-a-semantic-answer"></a>Anlam yanıtı nedir?

Anlamsal yanıt, [anlamsal sorgu yanıtının](semantic-how-to-query-request.md)alt yapısıdır. Bu, bir arama belgesinden bir veya daha fazla tam yanıdan oluşur. Bu, soru gibi görünen bir sorgunun yanıtı olarak ifade alınmıştır. Bir yanıtın döndürülmesi için, bir yanıtın dil özelliklerine sahip bir arama belgesinde ifadeler veya cümleler bulunmalı ve sorgunun kendisi bir soru olarak bildirilmelidir.

Bilişsel Arama en iyi yanıtı seçmek için bir makine okuma kavrama modeli kullanır. Model, kullanılabilir içerikten olası bir yanıt kümesi oluşturur ve yeterince yüksek bir güven düzeyine ulaştığında bir yanıt önerecektir.

Yanıtlar, arama sayfalarında işlemeyi seçebileceğiniz sorgu yanıt yükünde bağımsız, üst düzey bir nesne olarak döndürülür. Yapısal olarak, metin, belge anahtarı ve Güvenirlik puanı içeren yanıt içindeki bir dizi öğesidir.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Sorguda anlam yanıtları isteme

Anlamsal bir yanıt döndürmek için sorgunun anlam "queryType", "queryLanguage", "searchFields" ve "cevaplar" parametresine sahip olması gerekir. "Yanıtlar" parametresinin belirtilmesi, bir yanıt alınacağını garanti etmez, ancak yanıt işleme her seferinde çağrılması durumunda istek bu parametreyi içermelidir.

"SearchFields" parametresi, hem içerik hem de sıra bakımından yüksek kaliteli bir yanıt döndürmek için önemlidir (aşağıya bakın). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Sorgu dizesi null olmamalı ve soru olarak formüle eklenmelidir. Bu önizlemede, "queryType" ve "queryLanguage" tam olarak örnekte gösterildiği gibi ayarlanmalıdır.

+ "SearchFields" parametresi, hangi dize alanlarının ayıklama modeline belirteç sağlamasını belirler. Açıklamalı altyazı üreten alanlar da yanıt üretir. Bu alanı, hem açıklamalı alt yazılar hem de yanıtlar için çalışacak şekilde ayarlamaya yönelik kesin yönergeler için bkz. [searchFields set](semantic-how-to-query-request.md#searchfields). 

+ "Yanıtlar" için, `"answers": "extractive"` döndürülen varsayılan yanıt sayısının bir olması, parametre oluşturma ' dır. En fazla beş adede kadar yukarıdaki örnekte gösterildiği gibi bir sayı ekleyerek yanıt sayısını artırabilirsiniz.  Birden fazla yanıta ihtiyacınız olup olmadığı, uygulamanızın kullanıcı deneyimine ve sonuçları nasıl işlemek istediğinize bağlıdır.

## <a name="deconstruct-an-answer-from-the-response"></a>Yanıtın yanıtını kaldırma

Yanıtlar, @search.answers yanıtta ilk görüntülenen dizide verilmiştir. Yanıt belirsiz ise, yanıt bu şekilde görünür `"@search.answers": []` . Yanıtları içeren bir arama sonuçları sayfası tasarlarken, yanıtların bulunamadığı durumları işlediğinizden emin olun.

İçinde @search.answers , "anahtar" eşleşme belge anahtarıdır veya kimliğidir. Belge anahtarı [verildiğinde arama belgesi API 'sini](/rest/api/searchservice/lookup-document) kullanarak arama sayfasına veya bir ayrıntı sayfasına dahil edebilirsiniz.

Hem "metin" hem de "vurgular" aynı içeriğe, hem düz metin hem de vurgularla birlikte sahiptir. Varsayılan olarak, `<em>` var olan highlightPreTag ve highlightPostTag parametrelerini kullanarak geçersiz kılabileceğiniz olarak, vurgular olarak stillendirilmiş. Başka bir yerde de belirtildiği gibi, bir yanıtın üyesi bir arama belgesinden tam içerik olur. Ayıklama modeli, ilgili içeriği bulmak için bir yanıtın özelliklerine bakar, ancak yanıtta yeni bir dil oluşturmaz.

"Puan", yanıtın gücünü yansıtan bir güvenilirlik puandır. Yanıtta birden çok yanıt varsa, bu puan sırayı belirlemede kullanılır. En iyi yanıtlar ve en üst başlıklar farklı arama belgelerinden türetilebilir, burada üst yanıt bir belgeden kaynaklanmaktadır, ancak genel olarak her bir dizide en üstteki konumlarda aynı belgeleri görürsünüz.

Yanıt, her zaman puanlarını, açıklamalı alt yazıları ve varsayılan olarak alınabilir alanları içeren "Value" dizisi tarafından izlenir. Select parametresini belirttiyseniz, "Value" dizisi belirttiğiniz alanlarla sınırlıdır. Yanıttaki öğeler hakkında daha fazla bilgi için bkz. [anlamsal sorgu oluşturma](semantic-how-to-query-request.md).

"Bulutları nasıl oluşturur?" sorgusu verildiğinde, yanıtta aşağıdaki yanıt döndürülür:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Yüksek kaliteli yanıtlar oluşturmaya yönelik ipuçları

En iyi sonuçlar için, aşağıdaki özelliklere sahip bir belge corpile anlam yanıtları döndürün:

+ "searchFields", bir yanıtın bulunma olasılığı olan yeterli metin sunan alanlar sağlamalıdır. Yalnızca bir belgedeki tam metin yanıt olarak görünebilir.

+ sorgu dizeleri null olmamalı (Search = `*` ) ve dize, anahtar sözcük aramasının aksine (rastgele terimlerin veya deyimlerin sıralı bir listesi) bir sorunun özelliklerine sahip olmalıdır. Sorgu dizesi yanıt olarak görünmezse, istek bir sorgu parametresi olarak "yanıtlar" belirtse bile yanıt işleme atlanır.

+ Anlamsal ayıklama ve özetleme, her belge için kaç belirtece zamanında çözümlenebilecek sınırlara sahiptir. Pratik koşullarda, yüzlerce sayfada çalışan büyük belgeleriniz varsa, önce içeriği daha küçük belgelere kesmeyi denemelisiniz.

## <a name="next-steps"></a>Sonraki adımlar

+ [Anlamsal aramaya genel bakış](semantic-search-overview.md)
+ [Anlam derecelendirmesi algoritması](semantic-ranking.md)
+ [Benzerlik derecelendirme algoritması](index-ranking-similarity.md)
+ [Anlamsal sorgu oluşturma](semantic-how-to-query-request.md)
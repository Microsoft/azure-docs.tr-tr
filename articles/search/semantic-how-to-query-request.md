---
title: Anlamsal sorgu oluşturma
titleSuffix: Azure Cognitive Search
description: Derin öğrenme modellerini, arama derecesi ve ilgisi kapsamında bir amaç ve bağlamı göstermek üzere sorgu işlemeye eklemek için anlamsal bir sorgu türü ayarlayın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654671"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Bilişsel Arama anlam başlıkları için bir sorgu oluşturma

> [!IMPORTANT]
> Anlamsal arama, önizleme REST API ve Azure portal aracılığıyla kullanılabilen genel önizlemededir. Önizleme özellikleri, olduğu gibi, [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)altında sunulur. Bu özellikler faturalandırılabilir. Daha fazla bilgi için bkz. [kullanılabilirlik ve fiyatlandırma](semantic-search-overview.md#availability-and-pricing).

Bu makalede, anlam derecelendirmesi kullanan bir arama isteğini nasıl ifade edeceğinizi ve anlamlı açıklamalı alt yazılar (ve isteğe bağlı olarak [anlamsal yanıtlar](semantic-answers.md)), en ilgili hüküm ve tümceciklerin üzerine vurgularla birlikte nasıl ifade edeceğinizi öğrenin. Hem açıklamalı alt yazılar hem de yanıtlar, "anlam" sorgu türü kullanılarak formül oluşturulan sorgularda döndürülür.

Açıklamalı alt yazılar ve yanıtlar, arama belgesinde metinden yazılır. Anlamsal alt sistem, içeriğinizin bir başlık veya yanıtın özelliklerine sahip olduğunu belirler, ancak yeni tümceler veya ifadeler oluşturmaz. Bu nedenle, açıklamaları veya tanımları içeren içerik anlamsal arama için en iyi şekilde çalışır.

## <a name="prerequisites"></a>Önkoşullar

+ Şu bölgelerden birinde bulunan standart katmanda (S1, S2, S3) bir arama hizmeti: Orta Kuzey ABD, Batı ABD, Batı ABD 2, Doğu ABD 2, Kuzey Avrupa, Batı Avrupa. Bu bölgelerden birinde mevcut bir S1 veya daha fazla hizmetiniz varsa, yeni bir hizmet oluşturmak zorunda kalmadan erişim isteyebilirsiniz.

+ Anlamsal arama önizlemesine erişim: [kaydolma](https://aka.ms/SemanticSearchPreviewSignup)

+ Ingilizce içerik içeren mevcut bir arama dizini

+ Sorgu göndermek için bir arama istemcisi

  Arama istemcisinin, sorgu isteğinde önizleme REST API 'Leri desteklemesi gerekir. Önizleme API 'Lerine REST çağrıları yapan [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)veya kodu kullanabilirsiniz. Bir anlamsal sorgu göndermek için Azure portal [Arama Gezgini](search-explorer.md) 'ni de kullanabilirsiniz.

+ Bir [sorgu isteği](/rest/api/searchservice/preview-api/search-documents) , bu makalede açıklanan anlam seçeneğini ve diğer parametreleri içermelidir.

## <a name="whats-a-semantic-query"></a>Anlamsal sorgu nedir?

Bilişsel Arama bir sorgu, sorgu işlemeyi ve yanıtın şeklini belirleyen parametreli bir istek olur. Bir *anlam sorgusu* , eşleşen sonuçların kapsamını ve anlamını değerlendirebilen, daha fazla ilgili eşleştirmeyi en üst düzeye yükseltebilen ve anlam yanıtlarını ve açıklamalı alt yazıları döndüren anlam yeniden yönlendirme modelini çağıran parametreler ekler.

Aşağıdaki istek, en az anlamsal bir sorgu (yanıt olmadan) temsilcisidir.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Bilişsel Arama tüm sorgularda olduğu gibi, istek tek bir dizinin belgeler koleksiyonunu hedefler. Ayrıca, anlamsal bir sorgu semantik olmayan bir sorgu olarak aynı ayrıştırma, analiz, tarama ve Puanlama dizisine sahiptir. 

Fark, ilgi ve Puanlama ' de yer alır. Bu önizleme sürümünde tanımlandığı gibi anlamsal bir sorgu, sonuçları anlamsal dil modeli kullanılarak tekrar tekrar eden, varsayılan benzerlik derecelendirmesi algoritması tarafından atanan puanlar yerine anlam derecelendirmesiyle ilgili en iyi *sonucu* verilen eşleşmeleri sunmanın bir yolunu sağlayan bir anlam sorgusu olur.

Başlangıçtaki sonuçlardan yalnızca ilk 50 eşleşme anlam olarak derecelendirilir ve yanıtta tüm başlıklar dahil edilebilir. İsteğe bağlı olarak, olası bir **`answer`** yanıtı ayıklamak için istekte bir parametre belirtebilirsiniz. Daha fazla bilgi için bkz. [anlam yanıtları](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Arama gezgini ile sorgulama

[Arama Gezgini](search-explorer.md) anlam sorgularının seçeneklerini içerecek şekilde güncelleştirilmiştir. Bu seçenekler, aşağıdaki adımları tamamladıktan sonra portalda görünür hale gelir:

1. Arama hizmetinizin Önizleme programına [kaydolun](https://aka.ms/SemanticSearchPreviewSignup) ve Admittans yapın

1. Bu söz dizimi ile portalı açın: `https://portal.azure.com/?feature.semanticSearch=true`

Sorgu seçenekleri anlam sorguları, searchFields ve yazım düzeltmesini etkinleştirmek için anahtarlar içerir. Gerekli sorgu parametrelerini sorgu dizesine de yapıştırabilirsiniz.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Arama Gezgininde sorgu seçenekleri" border="true":::

## <a name="query-using-rest"></a>REST kullanarak sorgulama

İsteği programlı bir şekilde oluşturmak için [arama belgelerini (REST Önizleme)](/rest/api/searchservice/preview-api/search-documents) kullanın.

Yanıt, otomatik olarak açıklamalı alt yazılar ve vurgulama içerir. Yanıtın yazım denetimi düzeltmesini veya yanıtlarını içermesini istiyorsanız, isteğe bağlı veya bir parametre ekleyin **`speller`** **`answers`** .

Aşağıdaki örnek, semantik yanıtlar ve açıklamalı alt yazılar ile anlamsal bir sorgu isteği oluşturmak için oteller-Sample-Index ' i kullanır:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Aşağıdaki tablo, bir anlamsal sorgu için kullanılan sorgu parametrelerini özetler, böylece onları holistik olarak görebilirsiniz. Tüm parametrelerin listesi için bkz. [arama belgeleri (REST önizlemesi)](/rest/api/searchservice/preview-api/search-documents)

| Parametre | Tür | Description |
|-----------|-------|-------------|
| queryType | Dize | Geçerli değerler basit, tam ve anlam içerir. Anlam sorguları için "anlam" değeri gereklidir. |
| Sorgu dili | Dize | Anlam sorguları için gereklidir. Şu anda yalnızca "en-US" uygulandı. |
| searchFields | Dize | Aranabilir alanların virgülle ayrılmış listesi. Açıklamalı alt yazıların ve yanıtların ayıklandığı anlam derecelendirmenin gerçekleştiği alanları belirtir. </br></br>Basit ve tam sorgu türlerinin aksine, alanların listelenme sırası öncelik belirler. Daha fazla kullanım yönergeleri için bkz. [2. Adım: searchFields ayarlama](#searchfields). |
| güncelleştirin | Dize | Anlam sorgularına özgü olmayan isteğe bağlı parametre, arama altyapısına ulaşmadan önce yanlış yazılmış koşulları düzeltir. Daha fazla bilgi için bkz. [sorgulara yazım denetimi ekleme](speller-how-to-add.md). |
| acağınız |Dize | Anlam yanıtlarının sonuca dahil edilip edilmeyeceğini belirten isteğe bağlı parametreler. Şu anda yalnızca "extractive" uygulandı. Yanıtlar en fazla beş olacak şekilde yapılandırılabilir. Varsayılan değer bir. Bu örnek, üç yanıt sayısını gösterir: "extractive \| count3" '. Daha fazla bilgi için bkz. [anlam yanıtları döndürme](semantic-answers.md).|

### <a name="formulate-the-request"></a>İsteği formül altına yaz

Bu bölüm, semantik arama için gerekli olan sorgu parametrelerinin adımlarını izleyin.

#### <a name="step-1-set-querytype-and-querylanguage"></a>1. Adım: queryType ve queryLanguage ayarlama

Rest 'e aşağıdaki parametreleri ekleyin. Her iki parametre de gereklidir.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

QueryLanguage, dizin şemasında alan tanımlarına atanan [dil Çözümleyicileri](index-add-language-analyzers.md) ile tutarlı olmalıdır. QueryLanguage "en-US" ise, herhangi bir dil Çözümleyicileri de Ingilizce bir varyant ("en. Microsoft" veya "en. Lucene") olmalıdır. Anahtar sözcük veya basit gibi dilden bağımsız çözümleyiciler, queryLanguage değerleriyle çelişmez.

Bir sorgu isteğinde, [Yazım denetimi](speller-how-to-add.md)de kullanıyorsanız, ayarladığınız QueryLanguage, yazım denetleyicisi, yanıtlar ve açıklamalı alt yazılar için de geçerlidir. Ayrı parçalar için geçersiz kılma yoktur. 

Bir arama dizinindeki içerik birden çok dilde birleştirileken, sorgu girişi büyük olasılıkla birdir. Arama altyapısı, queryLanguage, Language Analyzer ve içeriğin oluştuğu dilin uyumluluğunu kontrol etmez, bu nedenle yanlış sonuçlar üretmemek için sorguları uygun şekilde kapsamdığınızdan emin olun.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>2. Adım: searchFields ayarlama

Searchfields parametresi, sorguya "anlamsal benzerlik" için değerlendirilecek olan metinlerin belirlemek için kullanılır. Önizleme için, modelin işlemek için en önemli olan alanlar için bir ipucu gerektirdiğinden, searchFields 'in boş bırakılması önerilmez.

SearchFields sırası kritik öneme sahiptir. Zaten basit veya tam Lucene sorguları için mevcut kodda searchFields kullanıyorsanız, anlamsal bir sorgu türüne geçiş yaparken alan sırasını denetlemek için bu parametreyi yeniden ziyaret edin.

İki veya daha fazla searchFields için:

+ Koleksiyonlardaki yalnızca dize alanlarını ve en üst düzey dize alanlarını dahil edin. Bir koleksiyonda dize olmayan alanları veya alt düzey alanları dahil etmek için bir hata yoktur, ancak bu alanlar semantik derecelendirmeden kullanılmaz.

+ İlk alan, ideal olarak 25 kelimeyle, her zaman kısa (bir başlık veya ad gibi) olmalıdır.

+ Dizinde metin olan bir URL alanı (örneğin `www.domain.com/name-of-the-document-and-other-details` , gibi makine odaklı şekilde okunabilir) varsa `www.domain.com/?id=23463&param=eis` , bunu bir yere yerleştirin (veya kısa başlık alanı yoksa önce).

+ Bir belgenin ana içeriği gibi anlam sorgularının yanıtının bulunabileceği açıklayıcı alanlarla bu alanları izleyin.

Yalnızca bir alan belirtilmişse, anlam sorgularının yanıtının bulunduğu bir belgenin ana içeriği gibi açıklayıcı bir alan kullanın. 

#### <a name="step-3-remove-orderby-clauses"></a>3. Adım: orderBy yan tümcelerini kaldırma

Mevcut bir istekte varsa, herhangi bir orderBy yan tümcesini kaldırın. Anlamsal puan sonuçları sıralamak için kullanılır ve açık sıralama mantığı eklerseniz bir HTTP 400 hatası döndürülür.

#### <a name="step-4-add-answers"></a>4. Adım: Yanıt ekleme

İsteğe bağlı olarak, yanıt sağlayan ek işlemleri eklemek istiyorsanız "yanıtlar" ı ekleyin. Yanıtlar (ve açıklamalı alt yazılar), searchfields bölümünde listelenen alanlarda bulunan metinlerin 'lerden ayıklanır. Yanıt olarak en iyi yanıtları almak için, searchFields 'e içerik açısından zengin alanlar eklediğinizden emin olun. Daha fazla bilgi için bkz. [anlam yanıtları döndürme](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>5. Adım: başka parametreler ekleme

İstekte istediğiniz diğer parametreleri ayarlayın. [Yazım denetleyicisi](speller-how-to-add.md), [seçme](search-query-odata-select.md)ve sayı gibi parametreler, yanıtın kalitesini ve okunabilirliğini artırır.

İsteğe bağlı olarak, açıklamalı alt yazıların uygulanan vurgu stilini özelleştirebilirsiniz. Açıklamalı alt yazılar, yanıtı özetleyen belgedeki anahtar paslar üzerinde vurgu biçimlendirme uygular. Varsayılan değer: `<em>`. Biçimlendirme türünü (örneğin, sarı arka plan) belirtmek istiyorsanız, highlightPreTag ve highlightPostTag ' i ayarlayabilirsiniz.

## <a name="evaluate-the-response"></a>Yanıtı değerlendirme

Tüm sorgularda olduğu gibi, yanıt alınabilir olarak işaretlenmiş tüm alanlardan veya yalnızca select parametresinde listelenen alanlarla oluşur. Özgün ilgi Puanını içerir ve ayrıca, isteği nasıl formüllerinize bağlı olarak bir sayı veya toplu sonuçlar içerebilir.

Bir anlam sorgusunda, yanıtın ek öğeleri vardır: yeni bir anlamsal olarak sıralanmış bir ilgi puanı, düz metin ve Vurgulamalar ve isteğe bağlı olarak bir yanıt.

Bir istemci uygulamasında, belirli bir alanın tüm içeriği yerine, eşleştirme açıklaması olarak bir resim yazısı eklemek için arama sayfasını yapılandırabilirsiniz. Bu, tek tek alanlar arama sonuçları sayfası için çok yoğun olduğunda faydalıdır.

Yukarıdaki örnek sorgusunun yanıtı, en üstteki seçim olarak aşağıdaki eşleşmeyi döndürür. Açıklamalı alt yazılar, düz metin ve vurgulanmış sürümlerle otomatik olarak döndürülür. Bu belirli sorgu ve Corpus için bir tane belirlenemediği için, örnekte yanıt çıkarılır.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Sonraki adımlar

Anlam derecelendirmenin ve yanıtlarının bir ilk sonuç kümesi üzerinden oluşturulup oluşturulabileceğini hatırlayın. İlk sonuçların kalitesini artıran herhangi bir mantık, anlam aramasına ileri doğru ilerlecektir. Sonraki adım olarak, dizelerin nasıl simgeleştirilmiş olduğunu etkileyen çözümleyiciler, sonuçları ayarlayabilen Puanlama profilleri ve varsayılan ilgi algoritması gibi ilk sonuçlara katkıda bulunan özellikleri gözden geçirin.

+ [Metin işleme için çözümleyiciler](search-analyzers.md)
+ [Benzerlik derecelendirme algoritması](index-similarity-and-scoring.md)
+ [Puanlama modelleri](index-add-scoring-profiles.md)
+ [Anlamsal aramaya genel bakış](semantic-search-overview.md)
+ [Anlam derecelendirmesi algoritması](semantic-ranking.md)

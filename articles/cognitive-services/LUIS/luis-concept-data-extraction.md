---
title: Veri ayıklama-LUSıS
description: Amaç ve varlıklar ile söylenişi metinlerdeki verileri ayıklayın. Language Understanding (LUSıS) öğesinden ne tür verilerin ayıklanabileceği hakkında bilgi edinin.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: dd7d113b1c23a0afec82a346e0f7baa1254ebbed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500150"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Amaç ve varlıklar ile söylenişi metinlerinde veri ayıklama
LUO, kullanıcının doğal dil dıklarından bilgi almanıza olanak tanır. Bilgiler, bir program, uygulama veya sohbet bot tarafından işlem gerçekleştirmek üzere kullanılabilecek şekilde ayıklanır. Aşağıdaki bölümlerde, veri ve varlıklardan JSON örnekleri ile hangi verilerin döndürüldüğünü öğrenin.

Bir tam metin eşleşmesi olmadığından Ayıklanacak en zor veriler makine öğrenimi verileri. Makine öğrenimi [varlıklarının](luis-concept-entity-types.md) veri ayıklamasıyla, bekleyen verileri elde edinceye kadar, [yazma döngüsünün](luis-concept-app-iteration.md) bir parçası olması gerekir.

## <a name="data-location-and-key-usage"></a>Veri konumu ve anahtar kullanımı
LUO, yayımlanan [uç noktada](luis-glossary.md#endpoint)kullanıcının zaman içindeki zaman içindeki verileri ayıklar. **Https isteği** (Post veya Get), hazırlama veya üretim ortamları gibi bazı isteğe bağlı yapılandırmalara sahiptir.

**V2 tahmin uç noktası isteği**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 tahmin uç noktası isteği**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

, Lusıs uygulamanızın `appID` **Ayarlar** sayfasında ve `/apps/` Bu LUIN uygulamasını düzenlediğinizde URL 'nin (sonrasında) bir parçası olarak kullanılabilir. , `subscription-key` Uygulamanızı sorgulamak için kullanılan uç nokta anahtarıdır. LUSıS öğrenirken ücretsiz yazma/başlangıç anahtarınızı kullanabilirsiniz, ancak Endpoint tuşunun [beklenen lusıs kullanımınızı](luis-limits.md#key-limits)destekleyen bir anahtarla değiştirilmesi önemlidir. `timezoneOffset`Birim dakikadır.

**Https yanıtı** , her türlü amaç ve varlık bilgisini içerir. Bu, hazırlama veya üretim uç noktasının geçerli yayımlanmış modeline göre belirlenir. Uç nokta URL 'SI, bu web sitesinde, **anahtarlar ve uç noktalar** sayfasındaki **Yönet** bölümünde bulunan [halsıs](luis-reference-regions.md) Web sitesinde bulunur.

## <a name="data-from-intents"></a>Amaçlardan alınan veriler
Birincil veri, en üst Puanlama **hedefi adıdır**. Uç nokta yanıtı:

#### <a name="v2-prediction-endpoint-response"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * *

|Veri nesnesi|Veri Türü|Veri Konumu|Değer|
|--|--|--|--|
|Amaç|Dize|topScoringIntent. amaç|"GetStoreInfo"|

Sohbet botu veya lu, çağırma uygulamanız birden fazla amaç puanı temelinde bir karar yapıyorsa, tüm amaç puanlarını döndürün.


#### <a name="v2-prediction-endpoint-response"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

QueryString parametresini ayarlayın `verbose=true` . Uç nokta yanıtı:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

QueryString parametresini ayarlayın `show-all-intents=true` . Uç nokta yanıtı:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * *

Amaçlar, en yüksekten en düşük puanla sıralanır.

|Veri nesnesi|Veri Türü|Veri Konumu|Değer|Puan|
|--|--|--|--|:--|
|Amaç|Dize|amaçlar [0]. amaç|"GetStoreInfo"|0,984749258|
|Amaç|Dize|amaçlar [1]. amaç|Seçim|0,0168218873|

Önceden oluşturulmuş etki alanları eklerseniz, amaç adı, etki alanını belirtir, örneğin, `Utilties` ya da `Communication` Amaç:

#### <a name="v2-prediction-endpoint-response"></a>[V2 tahmin uç noktası yanıtı](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 tahmin uç noktası yanıtı](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

[V3 tahmin uç noktası](luis-migration-api-v3.md)hakkında daha fazla bilgi edinin.

* * *

|Etki alanı|Veri nesnesi|Veri Türü|Veri Konumu|Değer|
|--|--|--|--|--|
|Yardımcı Programlar|Amaç|Dize|amaçlar [0]. amaç|"<b>Yardımcı programları</b>. ShowNext "|
|İletişim|Amaç|Dize|amaçlar [1]. amaç|<b>İletişim</b>. StartOver "|
||Amaç|Dize|amaçlar [2]. amaç|Seçim|


## <a name="data-from-entities"></a>Varlıklardan veriler
Çoğu sohbet ve uygulamanın, amaç adından daha fazla ihtiyacı vardır. Bu ek, isteğe bağlı veriler, utterance 'te bulunan varlıklardan gelir. Her varlık türü, eşleşme hakkındaki farklı bilgileri döndürür.

Söylenişi 'teki tek bir sözcük veya tümcecik birden fazla varlıkla eşleşiyor olabilir. Bu durumda, eşleşen her varlık puanı ile döndürülür.

Tüm varlıklar, uç noktadan gelen yanıtın **varlıklar** dizisinde döndürülür

## <a name="tokenized-entity-returned"></a>Simgeleştirilmiş varlık döndürüldü

LUSıS 'deki [belirteç desteğini](luis-language-support.md#tokenization) gözden geçirin.


## <a name="prebuilt-entity-data"></a>Önceden oluşturulmuş varlık verileri
[Önceden oluşturulmuş](luis-concept-entity-types.md) varlıklar, açık kaynaklı [Tanıyıcılar-metin](https://github.com/Microsoft/Recognizers-Text) projesi kullanılarak normal ifade eşleşmesi temel alınarak bulunur. Önceden oluşturulmuş varlıklar, varlıklar dizisinde döndürülür ve önekli tür adını kullanır `builtin::` .

## <a name="list-entity-data"></a>Varlık verilerini listeleme

[Liste varlıkları](reference-entity-list.md) , sabit ve kapalı bir ilgili sözcük kümesini eş anlamlılarıyla birlikte temsil eder. LUSıS, liste varlıkları için ek değerler bulamaz. Geçerli listeye göre yeni kelimelerin önerilerini görmek için **öner** özelliğini kullanın. Aynı değere sahip birden fazla liste varlığı varsa, her bir varlık uç nokta sorgusunda döndürülür.

## <a name="regular-expression-entity-data"></a>Normal ifade varlık verileri

[Normal ifade varlığı](reference-entity-regular-expression.md) , sağladığınız bir normal ifadeye göre bir varlığı ayıklar.

## <a name="extracting-names"></a>Adları ayıklama
Bir ad, neredeyse tüm harflerin ve sözcüklerin bir birleşimi olabileceğinden, bir tanınaradan adların alınması zordur. Hangi tür adı ayıkladığınıza bağlı olarak, birkaç seçeneğiniz vardır. Aşağıdaki öneriler kurallar değildir ancak daha fazla yönerge değildir.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Önceden oluşturulmuş PersonName ve GeographyV2 varlıklarını ekleyin

[PersonName](luis-reference-prebuilt-person.md) ve [GeographyV2](luis-reference-prebuilt-geographyV2.md) varlıkları bazı [dil kültürleri](luis-reference-prebuilt-entities.md)içinde kullanılabilir.

### <a name="names-of-people"></a>Kişilerin adları

Kişilerin adı, dile ve kültüre bağlı olarak bazı hafif biçimlendirmeye sahip olabilir. Önceden oluşturulmuş bir **[PersonName](luis-reference-prebuilt-person.md)** varlığı veya ad ve soyadı rollerine sahip **[basit bir varlık](luis-concept-entity-types.md)** kullanın.

Basit varlığı kullanırsanız, farklı uzunluklarda Farklı uzunluklardaki ilk ve son adı kullanan örneklere ve hiçbir türlü amaç dahil olmak üzere tüm amaçlar genelinde dikkat edilecek örneklere sahip olun. Doğru tahmin edilemeyen adların etiketlenmesi için düzenli olarak uç nokta utslarını [gözden geçirin](./luis-how-to-review-endpoint-utterances.md) .

### <a name="names-of-places"></a>Yerlerin adları

Konum adları, şehir, ilçe, eyalet, eyalet ve ülke/bölge gibi bir şekilde ayarlanır ve bilinmektedir. Konum bilgilerini ayıklamak için önceden oluşturulan **[geographyV2](luis-reference-prebuilt-geographyv2.md)** varlığını kullanın.

### <a name="new-and-emerging-names"></a>Yeni ve gelişen adlar

Bazı uygulamaların, ürünler veya şirketler gibi yeni ve gelişmekte olan adları bulabilmeleri gerekir. Bu tür adlar, veri ayıklamanın en zor türüdür. **[Basit bir varlıkla](luis-concept-entity-types.md)** başlayın ve [tümcecik listesi](luis-concept-feature.md)ekleyin. Doğru tahmin edilemeyen adların etiketlenmesi için düzenli olarak uç nokta utslarını [gözden geçirin](./luis-how-to-review-endpoint-utterances.md) .

## <a name="patternany-entity-data"></a>Model. tüm varlık verileri

[Desen. any](reference-entity-pattern-any.md) , varlığın nerede başladığını ve bittiğini işaretlemek için yalnızca bir desen şablonunda kullanılan değişken uzunluklu bir yer tutucudur. Düzenin uygulanması için, düzende kullanılan varlığın bulunması gerekir.

## <a name="sentiment-analysis"></a>Yaklaşım analizi
[Yayımlama](luis-how-to-publish-app.md#sentiment-analysis)sırasında yaklaşım Analizi YAPıLANDıRıLıRSA, lusıs JSON yanıtı yaklaşım analizini içerir. [Metin analizi](../text-analytics/index.yml) belgelerindeki yaklaşım analizi hakkında daha fazla bilgi edinin.

## <a name="key-phrase-extraction-entity-data"></a>Anahtar tümceciği ayıklama varlığı verileri
[Anahtar tümceciği ayıklama varlığı](luis-reference-prebuilt-keyphrase.md) , [metin analizi](../text-analytics/index.yml)tarafından sunulan, utterance 'teki anahtar tümceleri döndürür.

## <a name="data-matching-multiple-entities"></a>Birden çok varlıkla eşleşen veriler

LUSıS, utterance 'te bulunan tüm varlıkları döndürür. Sonuç olarak, sohbet botunuzun sonuçlara göre bir karar vermesini gerekebilir.

## <a name="data-matching-multiple-list-entities"></a>Birden çok liste varlığı ile eşleşen veriler

Bir sözcük veya tümcecik birden fazla liste varlığıyla eşleşiyorsa, uç nokta sorgusu her bir liste varlığını döndürür.

Sorgu için `when is the best time to go to red rock?` ve uygulamanın sözcüğe birden `red` fazla listede sahip olması, lusıs tüm varlıkları TANıR ve JSON uç noktası yanıtının bir parçası olarak varlıkların bir dizisini döndürür.

## <a name="next-steps"></a>Sonraki adımlar

LUSıS uygulamanıza varlık ekleme hakkında daha fazla bilgi edinmek için bkz. [varlık ekleme](luis-how-to-add-entities.md) .

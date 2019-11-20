---
title: V3 API 'sindeki tahmin uç noktası değişiklikleri
titleSuffix: Azure Cognitive Services
description: Sorgu tahmin uç noktası v3 API 'Leri değişti. Sürüm 3 uç nokta API 'Lerine nasıl geçiş yapılacağını anlamak için bu kılavuzu kullanın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bb2255a9a68a499ff3e77c1fbd35081a2474cf1d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961945"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3 için tahmin uç noktası değişiklikleri

Sorgu tahmin uç noktası v3 API 'Leri değişti. Sürüm 3 uç nokta API 'Lerine nasıl geçiş yapılacağını anlamak için bu kılavuzu kullanın. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Genel olarak kullanılabilen durum** -bu v3 API, v2 API 'SINDEN önemli JSON isteği ve yanıt değişiklikleri içerir.

V3 API 'SI aşağıdaki yeni özellikleri sağlar:

* [Dış varlıklar](#external-entities-passed-in-at-prediction-time)
* [Dinamik listeler](#dynamic-lists-passed-in-at-prediction-time)
* [Önceden oluşturulmuş varlık JSON değişiklikleri](#prebuilt-entity-changes)

Tahmin uç noktası [isteği](#request-changes) ve [yanıtı](#response-changes) , yukarıda listelenen yeni özellikleri desteklemek için aşağıdakiler de dahil önemli değişikliklere sahiptir:

* [Yanıt nesnesi değişiklikleri](#top-level-json-changes)
* [Varlık adı yerine varlık rolü adı başvuruları](#entity-role-name-instead-of-entity-name)
* [Varlıkları utterde olarak işaretlemek için Özellikler](#marking-placement-of-entities-in-utterances)

[Başvuru belgeleri](https://aka.ms/luis-api-v3) v3 için kullanılabilir.

## <a name="v3-changes-from-preview-to-ga"></a>V3, önizlemeden GA 'ye değişir

V3, GA 'ya taşıma kapsamında aşağıdaki değişiklikleri yaptı: 

* Aşağıdaki önceden oluşturulmuş varlıkların farklı JSON yanıtları vardır: 
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * `units` ile `unit` arasında ölçülebilir birim anahtar adı

* İstek gövdesi JSON değişikliği:
    * `preferExternalEntities` `preferExternalEntities`
    * dış varlıklar için isteğe bağlı `score` parametresi

* Yanıt gövdesi JSON değişiklikleri:
    * `normalizedQuery` kaldırıldı

## <a name="suggested-adoption-strategy"></a>Önerilen benimseme stratejisi

Bot Framework kullanıyorsanız, v7 Bing Yazım Denetimi veya LUSıS uygulama Authoring-yalnızca geçiş yapmak istiyorsanız v2 uç noktasını kullanmaya devam edin. 

İstemci uygulamanız veya tümleştirmelerinizin (bot Framework ve Bing Yazım Denetimi v7) etkilenmediğini biliyorsanız ve LUSıS uygulama Authoring ve tahmin uç noktanızı aynı anda geçiriyorsanız, v3 tahmin uç noktasını kullanmaya başlayın. V2 tahmin uç noktası hala kullanılabilir olmaya devam eder ve iyi bir geri dönüş stratejisidir. 

## <a name="not-supported"></a>Desteklenmiyor

* Bing Yazım Denetimi API'si v3 tahmin uç noktasında desteklenmez-yazım düzeltmeleri için v2 API tahmini uç noktasını kullanmaya devam edin

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework ve Azure bot hizmeti istemci uygulamaları

Bot çerçevesinin V 4.7 'i serbest bırakılana kadar v2 API tahmini uç noktasını kullanmaya devam edin. 

## <a name="v2-api-deprecation"></a>V2 API 'sinin kullanımdan kaldırılması 

V2 tahmin API 'SI v3 önizlemesi sonrasında 8 Haziran 2020 ' den sonra en az 9 ay boyunca kullanım dışı olmayacaktır. 

## <a name="endpoint-url-changes"></a>Uç nokta URL 'SI değişiklikleri 

### <a name="changes-by-slot-name-and-version-name"></a>Yuva adı ve sürüm adına göre değişiklikler

V3 uç nokta HTTP çağrısının biçimi değişti.

Sürümüne göre sorgulamak istiyorsanız, önce `"directVersionPublish":true`ile [API aracılığıyla yayımlamanız](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) gerekir. Yuva adı yerine sürüm KIMLIĞINE başvuran uç noktayı sorgulayın.

|TAHMIN APı SÜRÜMÜ|YÖNTEMIDIR|URL'si|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>tahmin</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{slot-adı}</b>/tahmin? sorgu =<b>{Query}</b>|
|V3|POST|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>tahmin</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{yuva-adı}</b>/tahmin|
|V2|GET|https://<b>{REGION}</b><b>. api.Cognitive.Microsoft.com/Luis/tahmine</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{sürüm-kimliği}</b>/tahmin? sorgu =<b>{Query}</b>|
|V2|POST|https://<b>{REGION}</b>. api.Cognitive.Microsoft.com/Luis/<b>Tahmini</b><b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{sürüm-kimliği}</b>/tahmin|

|`SLOT-NAME` için geçerli değerler|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Değişiklikleri isteme 

### <a name="query-string-changes"></a>Sorgu dizesi değişiklikleri

V3 API 'sinde farklı sorgu dizesi parametreleri vardır.

|Param adı|Tür|Sürüm|Varsayılan|Amaç|
|--|--|--|--|--|
|`log`|boole|V2 & v3|yanlış|Sorguyu günlük dosyasında sakla. Varsayılan değer false 'dur.| 
|`query`|string|Yalnızca v3|Varsayılan değer yok-GET isteğinde gerekli|**V2 'de**, tahmin edilecek olan söylik `q` parametredir. <br><br>**V3 'de**, işlev `query` parametresine geçirilir.|
|`show-all-intents`|boole|Yalnızca v3|yanlış|**Tahmin. amaçlar** nesnesindeki karşılık gelen puanı içeren tüm hedefleri döndürün. Amaçlar bir üst `intents` nesnesinde nesneler olarak döndürülür. Bu, bir dizide amacı bulmaya gerek kalmadan programlı erişime izin verir: `prediction.intents.give`. V2 'de, bunlar bir dizide döndürülür. |
|`verbose`|boole|V2 & v3|yanlış|**V2 sürümünde**, true olarak ayarlandığında, tüm tahmin edilen amaçlar döndürülür. Tahmin edilen tüm amaçlar için gerekiyorsa `show-all-intents`v3 param ' ı kullanın.<br><br>**V3 'de**, bu parametre yalnızca varlık tahmini varlık meta veri ayrıntılarını sağlar.  |
|`timezoneOffset`|string|V2|-|DatetimeV2 varlıklara uygulanan saat dilimi.|
|`datetimeReference`|string|V3|-|DatetimeV2 varlıklara uygulanan [saat dilimi](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) . V2 'deki `timezoneOffset` değiştirir.|


### <a name="v3-post-body"></a>V3 posta gövdesi

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Özellik|Tür|Sürüm|Varsayılan|Amaç|
|--|--|--|--|--|
|`dynamicLists`|array|Yalnızca v3|Gerekli değildir.|[Dinamik listeler](#dynamic-lists-passed-in-at-prediction-time) , zaten lusıs uygulamasında var olan eğitilen ve yayımlanmış bir liste varlığını genişletmenizi sağlar.|
|`externalEntities`|array|Yalnızca v3|Gerekli değildir.|[Dış varlıklar](#external-entities-passed-in-at-prediction-time) , lusıs uygulamasına çalışma zamanı sırasında varlıkları tanımlayabilir ve etiketleyebilir, bu da mevcut varlıkların özellikleri olarak kullanılabilir. |
|`options.datetimeReference`|string|Yalnızca v3|Varsayılan değer yok|[DatetimeV2 sapmasını](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)belirlemede kullanılır. DatetimeReference biçimi [ıso 8601](https://en.wikipedia.org/wiki/ISO_8601)' dir.|
|`options.preferExternalEntities`|boole|Yalnızca v3|yanlış|Kullanıcının [dış varlığının (var olan varlıkla aynı ada sahip)](#override-existing-model-predictions) kullanıldığını veya modeldeki mevcut varlığın tahmin için kullanıldığını belirtir. |
|`query`|string|Yalnızca v3|Gereklidir.|**V2 'de**, tahmin edilecek olan söylik `q` parametredir. <br><br>**V3 'de**, işlev `query` parametresine geçirilir.|



## <a name="response-changes"></a>Yanıt değişiklikleri

Sorgu yanıtı JSON, en sık kullanılan verilere daha fazla programlı erişim sağlamak için değişti. 

### <a name="top-level-json-changes"></a>Üst düzey JSON değişiklikleri



V2 için en üstteki JSON özellikleri, `verbose` true olarak ayarlandığında, `intents` özelliğindeki tüm amaçları ve puanlarını döndüren ' dir:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 için en üstteki JSON özellikleri şunlardır:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`intents` nesnesi sıralanmamış bir liste. `intents` `topIntent`karşılık gelen ilk alt öğenin kabul edilmez. Bunun yerine, puanı bulmak için `topIntent` değerini kullanın:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Response JSON şeması değişiklikleri için izin ver:

* Orijinal utterance, `query`ve döndürülen tahmin `prediction`arasındaki ayrımı temizleyin.
* Tahmin edilen verilere programlı erişim daha kolay. V2 'deki bir dizi aracılığıyla listelemek yerine, her iki amaç ve **varlık için değerlere** göre değerlere erişebilirsiniz. Tahmin edilen varlık rolleri için, rol adı tüm uygulama genelinde benzersiz olduğundan döndürülür.
* Saptandığı takdirde veri türleri dikkate alınır. Numerics artık dizeler olarak döndürülmez.
* `$instance` nesnesinde döndürülen ilk öncelikli tahmin bilgileri ve ek meta veriler arasındaki ayrım. 

### <a name="entity-response-changes"></a>Varlık yanıtı değişiklikleri

#### <a name="marking-placement-of-entities-in-utterances"></a>Varlıkların, utterlerdeki yerleşimini işaretleme

**V2 'de**, bir varlık `startIndex` ve `endIndex`ile utterlik olarak işaretlendi. 

**V3 'de**varlık `startIndex` ve `entityLength`olarak işaretlenir.

#### <a name="access-instance-for-entity-metadata"></a>Varlık meta verileri için erişim `$instance`

Varlık meta verileri gerekiyorsa, sorgu dizesinin `verbose=true` bayrağını kullanması gerekir ve yanıt `$instance` nesnesinde meta verileri içerir. Aşağıdaki bölümlerde JSON yanıtlarında örnekler gösterilmektedir.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Tahmin edilen her varlık bir dizi olarak temsil edilir

Her varlık, utterance 'de birden çok kez tahmin edilebileceği için `prediction.entities.<entity-name>` nesnesi bir dizi içerir. 

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Önceden oluşturulmuş varlık değişiklikleri

V3 yanıtı nesnesi, önceden oluşturulmuş varlıklarda yapılan değişiklikleri içerir. Daha fazla bilgi edinmek için [önceden oluşturulmuş özel varlıkları](luis-reference-prebuilt-entities.md) gözden geçirin. 

#### <a name="list-entity-prediction-changes"></a>Varlık tahmini değişikliklerini Listele

Bir liste varlık tahmini için JSON, dizi dizileri olacak şekilde değiştirilmiştir:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Her iç dizi, utterance içindeki metne karşılık gelir. Aynı metin bir liste varlığının birden fazla alt listesinde görünebildiğinden iç nesne bir dizidir. 

`entities` nesnesi arasında `$instance` nesnesi arasında eşleme yaparken, nesne sırası liste varlık tahminleri için korunur.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Varlık adı yerine varlık rolü adı 

V2 'de `entities` dizisi, benzersiz tanımlayıcı olan varlık adına sahip tüm tahmin edilen varlıkları döndürdü. V3 'de, varlık roller kullanıyorsa ve tahmin bir varlık rolü için ise, birincil tanımlayıcı rol adıdır. Bu, varlık rolü adlarının diğer model (amaç, varlık) adları da dahil olmak üzere tüm uygulama genelinde benzersiz olması gerektiğinden mümkündür.

Aşağıdaki örnekte: `Yellow Bird Lane`metnini içeren bir söylenişi düşünün. Bu metin, özel bir `Location` varlığının `Destination`rolü olarak tahmin edilir.

|Utterance metni|Varlık adı|Rol adı|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V2 'de, varlık _varlık adı_ tarafından nesnenin özelliği olarak rolüyle tanımlanır:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

V3 'de, rolün tahmini rol için olması durumunda varlığa _varlık rolü_başvurulur:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3 'de, varlık meta verilerini döndürmek için `verbose` bayrağıyla aynı sonuç:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Tahmin sırasında geçirilen dış varlıklar

Dış varlıklar, LUSıS uygulamasına çalışma zamanı sırasında varlıkları tanımlayabilir ve etiketleyebilir, bu da mevcut varlıkların özellikleri olarak kullanılabilir. Bu, sorguları tahmin uç noktanıza göndermeden önce kendi ayrı ve özel varlıklarınızı kullanmanıza olanak sağlar ayıklayıcıları. Bu sorgu tahmini uç noktasında yapıldığından, modelinizi yeniden eğitmeniz ve yayımlamanız gerekmez.

İstemci uygulaması, varlık eşleşmesini yönetip, bu eşleşen varlığın içindeki konumu belirleyerek ve sonra bu bilgileri istekle göndererek kendi varlık ayıklayıcısı 'nı sağlıyor. 

Dış varlıklar, hala roller, bileşik ve diğerleri gibi diğer modellere sinyal olarak kullanılmakta olan herhangi bir varlık türünü genişletmeye yönelik mekanizmadır.

Bu, yalnızca sorgu tahmini çalışma zamanında kullanılabilir verileri olan bir varlık için yararlıdır. Bu tür verilerin örnekleri, sürekli olarak verileri değiştirme veya Kullanıcı başına belirli bir örnektir. Bir LUSıS iletişim varlığını, bir kullanıcının kişi listesindeki dış bilgilerle genişletebilirsiniz. 

### <a name="entity-already-exists-in-app"></a>Varlık uygulamada zaten var

Uç nokta istek GÖNDERI gövdesinde geçirilen dış varlık için `entityName` değeri, istek yapıldığı sırada eğitilen ve yayımlanmış uygulamada zaten mevcut olmalıdır. Varlık türü, tüm türler desteklenir.

### <a name="first-turn-in-conversation"></a>İlk olarak konuşmayı açın

Bir kullanıcının aşağıdaki eksik bilgileri girdiği bir sohbet bot görüşmesinde ilk göz önünde bulundurun:

`Send Hazem a new message`

Sohbet bot 'tan LUYA 'ya olan istek, Kullanıcı kişileriyle doğrudan eşleştirilecek şekilde, `Hazem` hakkındaki GÖNDERI gövdesinde bilgi geçirebilir.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Tahmin yanıtı, istek içinde tanımlandığından, diğer tüm öngörülen varlıklar ile bu dış varlığı içerir.  

### <a name="second-turn-in-conversation"></a>İkinci konuşmayı aç

Sohbet bot 'ta bir sonraki Kullanıcı, daha fazla dönemi kullanır:

`Send him a calendar reminder for the party.`

Önceki bir deyişle, söylenişi `Hazem`başvuru olarak `him` kullanır. GÖNDERI gövdesinde konuşma sohbeti bot, `him`, `Hazem`ilk utterden ayıklanan varlık değeriyle eşleyebilir.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Tahmin yanıtı, istek içinde tanımlandığından, diğer tüm öngörülen varlıklar ile bu dış varlığı içerir.  

### <a name="override-existing-model-predictions"></a>Mevcut model tahminlerini geçersiz kıl

`preferExternalEntities` Options özelliği, Kullanıcı aynı ada sahip bir tahmin edilen varlıkla çakışan bir dış varlık gönderiyorsa, LUYA geçirilen varlığı veya modelde var olan varlığı seçer. 

Örneğin, sorguyu `today I'm free`değerlendirin. LUO, aşağıdaki Yanıt ile bir datetimeV2 olarak `today` algılar:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Kullanıcı dış varlığı gönderirse:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

`preferExternalEntities` `false`olarak ayarlanırsa, bir dış varlık gönderilmediği gibi LUSıS bir yanıt döndürür. 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

`preferExternalEntities` `true`olarak ayarlanırsa, LUSıS aşağıdakiler dahil bir yanıt döndürür:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Çözüm

_İsteğe bağlı_ `resolution` özelliği, bir tahmine yanıt vererek, dış varlıkla ilişkili meta verileri geçirmenize ve ardından yanıtta geri almaya izin verir. 

Birincil amaç, önceden oluşturulmuş varlıkların genişletilmesine karşın bu varlık türüyle sınırlı değildir. 

`resolution` özelliği bir sayı, dize, nesne veya dizi olabilir:

* Şubesi
* {"metin": "değer"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Tahmin sırasında geçirilen dinamik listeler

Dinamik listeler, zaten LUSıS uygulamasında var olan eğitilen ve yayımlanmış bir liste varlığını genişletmenizi sağlar. 

Liste varlık değerlerinizin düzenli aralıklarla değiştirilmesi gerektiğinde bu özelliği kullanın. Bu özellik, zaten eğitilen ve yayımlanmış bir liste varlığını genişletmenizi sağlar:

* Sorgu tahmin uç noktası isteği sırasında.
* Tek bir istek için.

Liste varlığı, LUSıS uygulamasında boş olabilir, ancak var olması gerekiyor. LUSıS uygulamasındaki liste varlığı değiştirilmez, ancak uç noktasındaki tahmin yeteneği, yaklaşık 1.000 öğe içeren en fazla 2 liste içerecek şekilde genişletilir.

### <a name="dynamic-list-json-request-body"></a>Dinamik liste JSON istek gövdesi

Aşağıdaki JSON gövdesine göndererek, listeye eş anlamlı olan yeni bir alt liste ekleyin ve `POST` sorgu tahmini isteğiyle `LUIS`metin için liste varlığını tahmin edin:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Tahmin yanıtı, istekte tanımlandığından, diğer tüm öngörülen varlıkların bulunduğu liste varlığını içerir. 

## <a name="deprecation"></a>Kullanımdan kaldırma 

V2 API 'SI, v3 önizlemesi sonrasında en az 9 ay boyunca kullanım dışı olmayacaktır. 

## <a name="next-steps"></a>Sonraki adımlar

LUıS [Endpoint](https://aka.ms/luis-api-v3) API 'LERINE mevcut Rest çağrılarını güncelleştirmek IÇIN v3 API belgelerini kullanın. 

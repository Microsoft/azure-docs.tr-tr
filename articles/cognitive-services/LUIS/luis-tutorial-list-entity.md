---
title: Exkişiyle metin eşleşmesi varlıkları-LUSıS
titleSuffix: Azure Cognitive Services
description: Bir sözcüğün veya tümceciğin BASıS etiket çeşitlemelerine yardımcı olmak için bir liste varlığı eklemeyi öğrenin.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499485"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Varlık algılamayı artırmak için bir liste varlığı kullanma 
Bu makalede, varlık algılamayı artırmak için bir [liste varlığının](luis-concept-entity-types.md) kullanımı gösterilmektedir. Liste varlıklarının, koşullara tam olarak eşleştiği için etiketlenmesi gerekmez.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Liste varlığı oluşturma 
> * Normalleştirilmiş değerler ve eş anlamlılar ekleme
> * Geliştirilmiş varlık kimliğini doğrula

## <a name="prerequisites"></a>Önkoşullar

> [!div class="checklist"]
> * En son [Node. js](https://nodejs.org)
> * [Homeautomation lusıs uygulaması](luis-get-started-create-app.md). Ana Otomasyon uygulaması oluşturmadıysanız, yeni bir uygulama oluşturun ve önceden oluşturulmuş etki alanı **Homeautomation**' ı ekleyin. Uygulamayı eğitin ve yayımlayın. 
> * [Authoringkey](luis-concept-keys.md#authoring-key), [endpointkey](luis-concept-keys.md#endpoint-key) (birçok kez sorgulandığında), uygulama kimliği, sürüm kimliği ve lusıs uygulamasının [bölgesi](luis-reference-regions.md) .

> [!Tip]
> Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)için kaydolabilirsiniz.

Bu makaledeki tüm kod, [Azure-Samples GitHub deposunda](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity)bulunur. 

## <a name="use-homeautomation-app"></a>HomeAutomation uygulamasını kullanma
HomeAutomation uygulaması, ışıklar, eğlence sistemleri ve Isıtma ve soğutma gibi ortam denetimleri gibi cihazlar için denetim sağlar. Bu sistemlerde üretici adları, takma adlar, kısaltmalar ve Slang içerebilen birçok farklı ad vardır. 

Farklı kültürler ve demografik genelinde birçok adı olan bir sistem, termostat ' dir. Bir termostat, bir ev veya bina için hem soğutma hem de ısıtma sistemlerini denetleyebilir.

İdeal olarak, aşağıdaki araslar, önceden oluşturulmuş olan Homeautomation varlığına çözümlenmelidir **. cihaz**:

|#|söylenişi|tanımlanan varlık|ınızı|
|--|--|--|--|
|1|AC 'yi açma|HomeAutomation. Device-"AC"|0,8748562|
|2|ısı 'yi açın|HomeAutomation. Device-"ısı"|0,784990132|
|3|BT Koder yapın|||

İlk iki söz konusu farklı cihazlara eşlenir. Üçüncü utterance, "It Colder" olarak bir cihazla eşleşmez, ancak bunun yerine bir sonuç ister. LUSıS, "Colder" teriminin, termostat 'ın istenen cihaz olduğunu bilmez. En ideal olarak, LUYA bu söyleylerini aynı cihaza çözmelidir. 

## <a name="use-a-list-entity"></a>Liste varlığı kullanma
HomeAutomation. Device varlığı, az sayıda cihaz için veya adların birkaç çeşitiyle harika. Office binası veya kampüs için, cihaz adları HomeAutomation. Device varlığının kullanışlılığını aşacak şekilde artar. 

Bir **liste varlığı** bu senaryo için iyi bir seçimdir çünkü bir yapı veya kampüs içindeki bir cihazın hüküm kümesi, büyük bir küme olsa bile bilinen bir küme. Bir liste varlığı kullanarak, Lua, termostat için küme içindeki olası bir değeri alabilir ve yalnızca tek bir "termostat" cihazına çözümleyebilir. 

Bu makale, termostat ile bir varlık listesi oluşturacağız. Bu makaledeki bir termostat için alternatif adlar şunlardır: 

|Termostat için alternatif adlar|
|--|
| m |
| a/c|
| a-c|
|ısıtıcı|
|Kolay|
|Hotter|
|Soğuk|
|COLDER|

LUTO 'ıN yeni bir alternatif belirlemesi gerekiyorsa, bir [tümcecik listesi](luis-concept-feature.md#how-to-use-phrase-lists) daha iyi bir yanıt olur.

## <a name="create-a-list-entity"></a>Liste varlığı oluşturma
Bir Node. js dosyası oluşturun ve içine aşağıdaki kodu kopyalayın. AuthoringKey, AppID, VersionId ve Region değerlerini değiştirin.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

NPM bağımlılıklarını yüklemek ve liste varlığını oluşturmak için kodu çalıştırmak için aşağıdaki komutu kullanın:

```console
npm install && node add-entity-list.js
```

Çalıştırmanın çıkışı, liste varlığının KIMLIĞIDIR:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Modeli eğitme
Yeni listenin sorgu sonuçlarını etkilemesini sağlamak için LUFOR 'ı eğitme. Eğitim, eğitimin iki bölümden oluşan bir işlemdir ve eğitim yapıldığında durum olup olmadığını denetler. Birçok modelle bir uygulamanın eğitilmesi birkaç dakika sürebilir. Aşağıdaki kod, uygulamayı başardıktan sonra eğitim başarılı olana kadar bekler. Kod, 429 "çok fazla istek" hatası oluşmasını önlemek için bir bekleme ve yeniden deneme stratejisi kullanır. 

Bir Node. js dosyası oluşturun ve içine aşağıdaki kodu kopyalayın. AuthoringKey, AppID, VersionId ve Region değerlerini değiştirin.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Uygulamayı eğiteetmek üzere kodu çalıştırmak için aşağıdaki komutu kullanın:

```console
node train.js
```

Çalıştırmanın çıkışı, LUTÜR modellerinin eğitiminin her bir yinelemesinin durumudur. Aşağıdaki yürütme yalnızca bir eğitim denetimi gerektirdi:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Modeli Yayımla
Liste varlığının uç noktadan kullanılabilir olması için yayımlayın.

Bir Node. js dosyası oluşturun ve içine aşağıdaki kodu kopyalayın. EndpointKey, AppID ve Region değerlerini değiştirin. Bu dosyayı kota sınırınızdan daha fazla çağırmayı planlamıyorsanız authoringKey 'nizi kullanabilirsiniz.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Uygulamayı sorgulamak üzere kodu çalıştırmak için aşağıdaki komutu kullanın:

```console
node publish.js
```

Aşağıdaki çıktı, herhangi bir sorgu için uç nokta URL 'sini içerir. Gerçek JSON sonuçları gerçek AppID 'yi içerir. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Uygulamayı sorgulama 
Liste varlığının, cihaz türünü belirlemesine yardımcı olduğunu kanıtlamak için uygulamayı uç noktadan sorgulayın.

Bir Node. js dosyası oluşturun ve içine aşağıdaki kodu kopyalayın. EndpointKey, AppID ve Region değerlerini değiştirin. Bu dosyayı kota sınırınızdan daha fazla çağırmayı planlamıyorsanız authoringKey 'nizi kullanabilirsiniz.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Kodu çalıştırmak ve uygulamayı sorgulamak için aşağıdaki komutu kullanın:

```console
node train.js
```

Çıktı Sorgu sonuçlarınızda bulunur. Kod, sorgu dizesine **ayrıntılı** ad/değer çifti ekletiğinden, çıkış tüm amaçları ve bunların puanlarını içerir:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Belirli bir **termostat** cihazı, "ısı artırma" sonucu odaklı bir sorgu ile tanımlanır. Özgün HomeAutomation. Device varlığı hala uygulamada olduğundan, onun sonuçlarını da görebilirsiniz. 

Aynı zamanda bir termostat olarak döndürüldüğünden emin olmak için diğer iki kuralı deneyin. 

|#|söylenişi|Varlığının|type|değer|
|--|--|--|--|--|
|1|AC 'yi açma| m | DevicesList | Saatiniz|
|2|ısı 'yi açın|yı| DevicesList |Saatiniz|
|3|BT Koder yapın|COLDER|DevicesList|Saatiniz|

## <a name="next-steps"></a>Sonraki adımlar

Cihaz konumlarını Odalar, katörler veya binalara genişletmek için başka bir liste varlığı oluşturabilirsiniz. 

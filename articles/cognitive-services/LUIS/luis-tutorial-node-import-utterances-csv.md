---
title: Node. js-LUSıS kullanarak uttersları içeri aktarma
titleSuffix: Azure Cognitive Services
description: LUSıS yazma API 'sini kullanarak CSV biçimindeki daha önceden var olan verilerden bir LUO uygulamasını programlı bir şekilde oluşturmayı öğrenin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ef5f6967b7ad9500672d00d93dd8acaca99e5948
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499456"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Node. js kullanarak program aracılığıyla bir LUSıS uygulaması oluşturma

LUSıS, [Luo](luis-reference-regions.md) Web sitesinin yaptığı her şeyi yapan BIR programlı API sağlar. Bu, önceden var olan verileriniz olduğunda zamandan tasarruf edebilir ve el ile bilgi girerek bir LUO uygulamasının programlı bir şekilde oluşturulması daha hızlıdır. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Lusıs](luis-reference-regions.md) Web sitesinde oturum açın ve hesap ayarları ' nda [yazma anahtarınızı](luis-concept-keys.md#authoring-key) bulun. Yazma API 'Lerini çağırmak için bu anahtarı kullanırsınız.
* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* Bu makale, kuramsal bir şirketin kullanıcı isteklerinin günlük dosyaları için bir CSV ile başlar. [Buradan](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv)indirin.
* NPM ile en son Node. js ' i yükler. [Buradan](https://nodejs.org/en/download/)indirin.
* **[Önerilir]** IntelliSense ve hata ayıklama için Visual Studio Code, [buradan](https://code.visualstudio.com/) ücretsiz indirin.

Bu makaledeki tüm kod, [Azure-Samples Language Understanding GitHub deposunda](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv)bulunur. 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Önceden varolan verileri amaçlar ve varlıklara eşleyin
LUıN ile oluşturulmamış bir sisteminiz olsa da, kullanıcıların yapmak istedikleri farklı şeylere eşlenen metin verileri içeriyorsa, bu kullanıcı girişi kategorilerinden LUSıS 'deki amaçlar için bir eşleme ile karşılaşabilirsiniz. Kullanıcıların söyledikleriyle ilişkili önemli sözcükleri veya tümceleri belirleyebiliyorsanız, bu sözcükler varlıklara eşlenir.

[`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) dosyasını açın. Bir kuramsal giriş Otomasyonu hizmetine, kategorilere ayrılmaları, kullanıcının ne söyledikleri ve yararlı bilgiler içeren bazı sütunlarda Kullanıcı sorgularının bir günlüğünü içerir. 

![Önceden var olan verilerin CSV dosyası](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

**RequestType** sütununun amaçları olduğunu ve **istek** sütununda bir örnek olduğunu görürsünüz. Diğer alanlar, utterde oluşduklarında varlık olabilir. Amaçlar, varlıklar ve örnek söyleyler olduğundan, basit, örnek bir uygulama için gereksinimleri vardır.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>LUSıS verisi olmayan bir LUSıS uygulaması oluşturma adımları
CSV dosyasından yeni bir LUO uygulaması oluşturmak için:

* CSV dosyasından verileri ayrıştırın:
    * Yazma API 'sini kullanarak LUYA indirebileceğiniz bir biçime dönüştürün. 
    * Ayrıştırılmış verilerden, amaçlar ve varlıklar hakkında bilgi toplayın. 
* Yazma API 'SI çağrıları yapın:
    * Uygulamayı oluşturun.
    * Ayrıştırılmış verilerden toplanan amaçları ve varlıkları ekleyin. 
    * LUSıS uygulamasını oluşturduktan sonra, ayrıştırılmış verilerden örnek oluşturma işlemi ekleyebilirsiniz. 

Bu programın akışını `index.js` dosyanın son bölümünde görebilirsiniz. Bu kodu kopyalayın veya [indirin](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) ve `index.js`kaydedin.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>CSV 'yi ayrıştırma

CSV 'deki detersliği içeren sütun girdilerinin, LUIN anlayabileceği bir JSON biçiminde ayrıştırılabilmesi gerekir. Bu JSON biçimi, utterance 'in amacını tanımlayan bir `intentName` alanı içermelidir. Ayrıca, bir `entityLabels` alanı içermesi gerekir, bu da bir varlık olmaması halinde boş olabilir. 

Örneğin, "ışıkları aç" girdisi bu JSON ile eşlenir:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

Bu örnekte `intentName`, CSV dosyasındaki **istek** sütunu başlığı altındaki Kullanıcı isteğinden gelir ve `entityName`, önemli bilgiler içeren diğer sütunlardan gelir. Örneğin, **işlem** veya **cihaz**için bir giriş varsa ve bu dize gerçek istekte da gerçekleşirse, bir varlık olarak etiketlenebilir. Aşağıdaki kod bu ayrıştırma sürecini gösterir. Kopyalayabilir veya [indirebilir](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) ve `_parse.js`kaydedebilirsiniz.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>LUSıS uygulamasını oluşturma
Veriler JSON 'a ayrıştırıldıktan sonra bir LUO uygulamasına ekleyin. Aşağıdaki kod LUSıS uygulamasını oluşturur. Kopyalayın veya [indirin](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) ve `_create.js`kaydedin.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Hedef ekleme
Uygulamanız olduktan sonra, bu uygulama için gerekli olan hedefleri gerekir. Aşağıdaki kod LUSıS uygulamasını oluşturur. Kopyalayın veya [indirin](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) ve `_intents.js`kaydedin.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Varlık ekleme
Aşağıdaki kod, varlıkları LUSıS uygulamasına ekler. Kopyalayın veya [indirin](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) ve `_entities.js`kaydedin.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Konuşma ekleme
ASIS uygulamasında varlıklar ve amaçlar tanımlandıktan sonra, bu tür ekleme yapabilirsiniz. Aşağıdaki kod, [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API 'sini kullanır ve bu, bir seferde en fazla 100 utde eklemenize olanak tanır.  Kopyalayın veya [indirin](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) ve `_upload.js`kaydedin.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Kodu çalıştırma


### <a name="install-nodejs-dependencies"></a>Node. js bağımlılıklarını Install
Terminal/komut satırına NPM 'den Node. js bağımlılıklarını yükler.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Yapılandırma ayarlarını değiştir
Bu uygulamayı kullanabilmeniz için index. js dosyasındaki değerleri kendi uç nokta anahtarınızla değiştirmeniz ve uygulamanın sahip olmasını istediğiniz adı sağlamanız gerekir. Ayrıca, uygulamanın kültürünü ayarlayabilir veya sürüm numarasını değiştirebilirsiniz.

İndex. js dosyasını açın ve dosyanın en üstünde bu değerleri değiştirin.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Betiği çalıştırın
Betiği Node. js ile bir terminalden/komut satırından çalıştırın.

```console
> node index.js
```

or

```console
> npm start
```

### <a name="application-progress"></a>Uygulama ilerleme durumu
Uygulama çalışırken, komut satırı ilerlemeyi gösterir. Komut satırı çıktısı, LUSıS yanıtlarının biçimini içerir.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>LUSıS uygulamasını açın
Betik tamamlandıktan sonra, [lusıs](luis-reference-regions.md) 'de oturum açabilir ve **UYGULAMALARıM**altında oluşturduğunuz Luo uygulamasını görebilirsiniz. **TurnOn**, **Turnoff**ve **none** hedefleri altına eklediğiniz söyleymeleri görebilmeniz gerekir.

![TurnOn amacı](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulamanızı LUSıS Web sitesinde test edin ve eğitme](luis-interactive-test.md)

## <a name="additional-resources"></a>Ek kaynaklar

Bu örnek uygulama aşağıdaki LUıS API 'Lerini kullanır:
- [uygulama oluştur](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Amaç Ekle](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [varlık Ekle](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [utterslar ekleme](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)

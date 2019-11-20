---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 54aadd22b8f2bbccaa4c5e4f1444aa08279a4773
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125630"
---
<a name="HOLTop"></a>

[Başvuru belgeleri](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [paketi (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [örnekleri](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Node. js](https://nodejs.org/)' nin geçerli sürümü.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-text-analytics-azure-resource"></a>Metin Analizi Azure kaynağı oluşturma

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp && cd myapp
```

Bir `package.json` dosyası ile bir düğüm uygulaması oluşturmak için `npm init` komutunu çalıştırın. 

```console
npm init
```

`index.js` adlı bir dosya oluşturun ve aşağıdaki kitaplıkları ekleyin:

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Kaynağınızın Azure uç noktası ve abonelik anahtarı için değişkenler oluşturun. `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT`ortam değişkenlerinden bu değerleri alın. Uygulamayı düzenleme başladıktan sonra bu ortam değişkenlerini oluşturduysanız, değişkenlere erişmek için kullandığınız düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

[!code-javascript[Key and endpoint vars](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyVars)]

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

`@azure/ms-rest-js` ve `@azure/cognitiveservices-textanalytics` NPM paketlerini yükler:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Uygulamanızın `package.json` dosyası bağımlılıklarla güncelleştirilecektir.

## <a name="object-model"></a>Nesne modeli

Metin Analizi istemcisi, anahtarınızı kullanarak Azure 'da kimlik doğrulaması yapan bir [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) nesnesidir. İstemci, tek bir dize veya bir toplu iş olarak metin çözümlemek için çeşitli yöntemler sağlar.

Metin, kullanılan yönteme bağlı olarak `id`, `text`ve `language` özniteliklerinin bir birleşimini içeren `dictionary` nesneler olan `documents`bir liste olarak API 'ye gönderilir. `text` özniteliği, kaynak `language`analiz edilecek metni depolar ve `id` herhangi bir değer olabilir. 

Yanıt nesnesi, her belge için analiz bilgilerini içeren bir listesidir. 

## <a name="code-examples"></a>Kod örnekleri

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yaklaşım analizi](#sentiment-analysis)
* [Dil algılama](#language-detection)
* [Varlık tanıma](#entity-recognition)
* [Anahtar tümceciği ayıklama](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

`credentials` ve `endpoint` bir parametre olarak yeni bir [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) nesnesi oluşturun.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>Yaklaşım analizi

Çözümlemek istediğiniz belgeleri içeren sözlük nesnelerinin bir listesini oluşturun. İstemcinin [Sentiment ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) yöntemini çağırın ve döndürülen [Sentimentbatchresult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult)'ı alın. Sonuçlar listesinde yineleme yapın ve her belgenin KIMLIĞINI ve yaklaşım Puanını yazdırın. 0 ' a yakın bir puan negatif bir yaklaşım gösterir, 1 ' e yaklaşarak pozitif bir yaklaşım gösterilir.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Kodunuzu konsol pencerenizde `node index.js` çalıştırın.

### <a name="output"></a>Çıktı

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

## <a name="language-detection"></a>Dil algılama

Belgelerinizi içeren sözlük nesnelerinin bir listesini oluşturun. İstemcinin [DetectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) yöntemini çağırın ve döndürülen [languagebatchresult sonucunu](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult)alın. Sonra sonuçlar arasında yineleme yapın ve her belgenin KIMLIĞINI ve dilini yazdırın.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Kodunuzu konsol pencerenizde `node index.js` çalıştırın.

### <a name="output"></a>Çıktı

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Varlık tanıma

Belgelerinizi içeren bir nesne listesi oluşturun. İstemcinin [Entities ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) yöntemini çağırın ve [Entitiesbatchresult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) nesnesini alın. Sonuçlar listesinde yineleme yapın ve her belge KIMLIĞINI yazdırın. Algılanan her varlık için, bu dosyanın visede adını, tür ve alt türlerini (varsa) ve özgün metindeki konumları yazdırın.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Kodunuzu konsol pencerenizde `node index.js` çalıştırın.

### <a name="output"></a>Çıktı

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Anahtar ifade ayıklama

Belgelerinizi içeren bir nesne listesi oluşturun. İstemcinin [keyPhrases ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) yöntemini çağırın ve döndürülen [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) nesnesini alın. Sonuçlar arasında yineleme yapın ve her belge KIMLIĞINI ve algılanan anahtar tümceleri yazdırın.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Kodunuzu konsol pencerenizde `node index.js` çalıştırın.

### <a name="output"></a>Çıktı

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı hızlı başlangıç dosyanızdaki `node` komutuyla çalıştırın.

```console
node index.js
```

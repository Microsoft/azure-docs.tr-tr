---
title: 'Hızlı başlangıç: Node.js için Metin Analizi v3 istemci kitaplığı | Microsoft Docs'
description: Node.js için v3 Metin Analizi istemci kitaplığı ile çalışmaya başlayın.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 72ca331546d53f85ca82f33ec6a02558d91f1c1e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765104"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[v3 başvuru belgeleri](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  [v3 kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [V3 paketi (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 örnekleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

[v3 başvuru belgeleri](/javascript/api/overview/azure/ai-text-analytics-readme)  |  [v3 kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [V3 paketi (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3 örnekleri](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


---

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Node.js](https://nodejs.org/)geçerli sürümü.
* Azure aboneliğiniz olduktan sonra, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Metin Analizi kaynağı oluşturun metin analizi bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Metin Analizi API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* Çözümle özelliğini kullanmak için standart fiyatlandırma katmanına sahip bir Metin Analizi kaynağına ihtiyacınız olacaktır.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-nodejs-application"></a>Yeni bir Node.js uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. 

```console
mkdir myapp 

cd myapp
```

`npm init`Bir dosya ile bir düğüm uygulaması oluşturmak için komutunu çalıştırın `package.json` . 

```console
npm init
```
### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`@azure/ai-text-analytics`NPM paketlerini yüklerken:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.5
```

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub 'da](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)bulabilirsiniz. 


# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`@azure/ai-text-analytics`NPM paketlerini yüklerken:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub 'da](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)bulabilirsiniz. 

---

Uygulamanızın `package.json` dosyası bağımlılıklarla güncelleştirilir.
Adlı bir dosya oluşturun `index.js` ve aşağıdakileri ekleyin:

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

---

Kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Nesne modeli

Metin Analizi istemcisi, `TextAnalyticsClient` anahtarınızı kullanarak Azure 'da kimlik doğrulayan bir nesnedir. İstemci, tek bir dize veya bir toplu iş olarak metin çözümlemek için çeşitli yöntemler sağlar.

Metin, `documents` `dictionary` `id` `text` `language` kullanılan yöntemine bağlı olarak, ve özniteliklerinin bir birleşimini içeren nesneler olan bir listesi olarak API 'ye gönderilir. `text`Özniteliği, kaynak olarak çözümlenecek metni depolar `language` ve `id` herhangi bir değer olabilir. 

Yanıt nesnesi, her belge için analiz bilgilerini içeren bir listesidir. 

## <a name="code-examples"></a>Kod örnekleri

* [İstemci kimlik doğrulaması](#client-authentication)
* [Yaklaşım Analizi](#sentiment-analysis) 
* [Görüşün madenciliği](#opinion-mining)
* [Dil algılama](#language-detection)
* [Adlandırılmış varlık tanıma](#named-entity-recognition-ner)
* [Varlık bağlama](#entity-linking)
* Kişisel Bilgiler
* [Anahtar tümceciği ayıklama](#key-phrase-extraction)

## <a name="client-authentication"></a>İstemci Kimlik Doğrulaması

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`TextAnalyticsClient`Anahtarınızla ve uç noktanızla parametreler olarak yeni bir nesne oluşturun.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`TextAnalyticsClient`Anahtarınızla ve uç noktanızla parametreler olarak yeni bir nesne oluşturun.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

---

## <a name="sentiment-analysis"></a>Yaklaşım analizi

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `analyzeSentiment()` metodunu çağırın ve döndürülen `SentimentBatchResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve her belgenin KIMLIĞINI, belge düzeyi yaklaşımını güven puanlarına göre yazdırın. Sonuç, her belge için kenar boşlukları, uzunluk ve güvenirlik puanlarıyla birlikte tümce düzeyinde yaklaşım içerir.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Görüşün madenciliği

Fikrinizi inceleme ile yaklaşım analizi yapmak için, çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. `analyzeSentiment()`Seçenek bayrağı ekleyerek istemcinin metodunu çağırın `includeOpinionMining: true` ve döndürülen `SentimentBatchResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve her belgenin KIMLIĞINI, belge düzeyi yaklaşımını güven puanlarına göre yazdırın. Her belge için, sonuç yalnızca yukarıdaki gibi tümce düzeyinde yaklaşım içermez, ayrıca en önemli ve önemli yaklaşım da vardır.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `analyzeSentiment()` metodunu çağırın ve döndürülen `SentimentBatchResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve her belgenin KIMLIĞINI, belge düzeyi yaklaşımını güven puanlarına göre yazdırın. Sonuç, her belge için kenar boşlukları, uzunluk ve güvenirlik puanlarıyla birlikte tümce düzeyinde yaklaşım içerir.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

---

## <a name="language-detection"></a>Dil algılama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `detectLanguage()` metodunu çağırın ve döndürülen geri alın `DetectLanguageResultCollection` . Sonra sonuçlar arasında yineleme yapın ve her belgenin KIMLIĞINI ilgili birincil dille yazdırın.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `detectLanguage()` metodunu çağırın ve döndürülen geri alın `DetectLanguageResultCollection` . Sonra sonuçlar arasında yineleme yapın ve her belgenin KIMLIĞINI ilgili birincil dille yazdırın.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
ID: 0
        Primary Language French
```

---

## <a name="named-entity-recognition-ner"></a>Adlandırılmış varlık tanıma (NER)

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

> [!NOTE]
> Şu sürümde `3.1` :
> * Varlık bağlama, NER 'den ayrı bir istek.

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `recognizeEntities()` metodunu çağırın ve `RecognizeEntitiesResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve varlık adı, türü, alt tür, kaydırma, uzunluk ve puanı yazdırın.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Varlık Bağlama

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `recognizeLinkedEntities()` metodunu çağırın ve `RecognizeLinkedEntitiesResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve varlık adı, KIMLIĞI, veri kaynağı, URL ve eşleşmeleri yazdırın. Dizideki her nesne, `matches` Bu eşleşme için uzaklığa, uzunluğa ve puana sahip olur.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Kişisel tanımlama bilgileri (PII) tanıma

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `recognizePiiEntities()` metodunu çağırın ve `RecognizePIIEntitiesResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve varlık adını, türünü ve Puanını yazdırın.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

> [!NOTE]
> Şu sürümde `3.0` :
> * Varlık bağlama, NER 'den ayrı bir istek.

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `recognizeEntities()` metodunu çağırın ve `RecognizeEntitiesResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve varlık adı, türü, alt tür, kaydırma, uzunluk ve puanı yazdırın.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Varlık Bağlama

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `recognizeLinkedEntities()` metodunu çağırın ve `RecognizeLinkedEntitiesResult` nesneyi alın. Sonuçlar listesinde yineleme yapın ve varlık adı, KIMLIĞI, veri kaynağı, URL ve eşleşmeleri yazdırın. Dizideki her nesne, `matches` Bu eşleşme için uzaklığa, uzunluğa ve puana sahip olur.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```


---

## <a name="key-phrase-extraction"></a>Anahtar ifade ayıklama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `extractKeyPhrases()` metodunu çağırın ve döndürülen `ExtractKeyPhrasesResult` nesneyi alın. Sonuçlar arasında yineleme yapın ve her belge KIMLIĞINI ve algılanan anahtar tümceleri yazdırın.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Çözümlemek istediğiniz belgeyi içeren bir dize dizisi oluşturun. İstemcinin `extractKeyPhrases()` metodunu çağırın ve döndürülen `ExtractKeyPhrasesResult` nesneyi alın. Sonuçlar arasında yineleme yapın ve her belge KIMLIĞINI ve algılanan anahtar tümceleri yazdırın.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kodunuzu `node index.js` konsol pencerenizde çalıştırın.

### <a name="output"></a>Çıktı

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Çözümleme işlemi ile API 'YI zaman uyumsuz olarak kullanma

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

İşlevini çağıran, adlı yeni bir işlev oluşturun `analyze_example()` `beginAnalyze()` . Sonuç, sonuçlar için yoklamak üzere uzun süren bir işlem olacaktır.

```javascript
async function analyze_example(client) {
  const documents = [
    "Microsoft was founded by Bill Gates and Paul Allen.",
  ];

  const actions = {
    recognizeEntitiesActions: [{ modelVersion: "latest" }],
  };
  const poller = await client.beginAnalyzeBatchActions(documents, actions, "en");

  console.log(
    `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
  );
  console.log(
    `The analyze batch actions operation results will expire on ${
      poller.getOperationState().expiresOn
    }`
  );
  const resultPages = await poller.pollUntilDone();
  for await (const page of resultPages) {
    const entitiesAction = page.recognizeEntitiesResults[0];
    if (!entitiesAction.error) {
      for (const doc of entitiesAction.results) {
        console.log(`- Document ${doc.id}`);
        if (!doc.error) {
          console.log("\tEntities:");
          for (const entity of doc.entities) {
            console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
          }
        } else {
          console.error("\tError:", doc.error);
        }
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Çıktı

```console
The analyze batch actions operation was created on Fri Mar 12 2021 09:53:49 GMT-0800 (Pacific Standard Time)
The analyze batch actions operation results will expire on Sat Mar 13 2021 09:53:49 GMT-0800 (Pacific Standard Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

PII 'yi algılamak, bağlı varlıkları tanımak ve anahtar tümceciği ayıklama için Çözümle işlemini de kullanabilirsiniz. GitHub 'da [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript) ve [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) için örnekleri çözümle bölümüne bakın.

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Bu özellik 3,0 sürümünde kullanılamaz.

---

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `node` hızlı başlangıç dosyanızdaki komutla çalıştırın.

```console
node index.js
```

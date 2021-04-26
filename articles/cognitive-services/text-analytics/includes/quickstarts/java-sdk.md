---
title: 'Hızlı başlangıç: Java için Metin Analizi v3 istemci kitaplığı | Microsoft Docs'
description: Java için v3 Metin Analizi istemci kitaplığı ile çalışmaya başlayın.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: d39f40d4059b43246f523b4d01ca92c0360bc574
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765119"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[Başvuru belgeleri](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics)  |  [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.5/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

[Başvuru belgeleri](/java/api/overview/azure/ai-textanalytics-readme)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Örnekler](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) sürüm 8 veya üzeri
* Azure aboneliğiniz olduktan sonra, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Metin Analizi kaynağı oluşturun metin analizi bir kaynak oluşturun.  Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Metin Analizi API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* Çözümle özelliğini kullanmak için standart fiyatlandırma katmanına sahip bir Metin Analizi kaynağına ihtiyacınız olacaktır.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="add-the-client-library"></a>İstemci kitaplığını ekleme

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

Tercih ettiğiniz IDE veya geliştirme ortamınızda Maven projesi oluşturun. Sonra projenizin *pom.xml* dosyasına aşağıdaki bağımlılığı ekleyin. [Diğer derleme araçlarının](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.5) uygulama sözdizimini çevrimiçi olarak bulabilirsiniz.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.5</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Tercih ettiğiniz IDE veya geliştirme ortamınızda Maven projesi oluşturun. Sonra projenizin *pom.xml* dosyasına aşağıdaki bağımlılığı ekleyin. [Diğer derleme araçlarının](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) uygulama sözdizimini çevrimiçi olarak bulabilirsiniz.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub 'da](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)bulabilirsiniz. 

---

Adlı bir Java dosyası oluşturun `TextAnalyticsSamples.java` . Dosyasını açın ve aşağıdaki `import` deyimlerini ekleyin:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Java dosyasında, yeni bir sınıf ekleyin ve aşağıda gösterildiği gibi Azure kaynağınızın anahtarını ve uç noktasını ekleyin.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Aşağıdaki Main yöntemini sınıfına ekleyin. Burada, daha sonra çağrılan yöntemleri tanımlayacaksınız.

# <a name="version-31-preview"></a>[Sürüm 3,1 (Önizleme)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

---


## <a name="object-model"></a>Nesne modeli

Metin Analizi istemcisi, `TextAnalyticsClient` anahtarınızı kullanarak Azure 'da kimlik doğrulayan bir nesnedir ve metni tek dizeler veya toplu işlem olarak kabul etmek için işlevler sağlar. API 'ye eşzamanlı olarak veya zaman uyumsuz olarak metin gönderebilirsiniz. Yanıt nesnesi, göndereceğiniz her belge için analiz bilgilerini içerecektir. 

## <a name="code-examples"></a>Kod örnekleri

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yaklaşım Analizi](#sentiment-analysis) 
* [Görüşün madenciliği](#opinion-mining)
* [Dil algılama](#language-detection)
* [Adlandırılmış varlık tanıma](#named-entity-recognition-ner)
* [Varlık bağlama](#entity-linking)
* [Anahtar tümceciği ayıklama](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

`TextAnalyticsClient`Metin analizi kaynağınız için anahtar ve uç nokta ile nesneyi örneklendirilecek bir yöntem oluşturun. Bu örnek, API 'nin 3,0 ve 3,1 sürümleri için aynıdır.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


Programınızın `main()` yönteminde, istemcisini başlatmak için kimlik doğrulama yöntemini çağırın.

## <a name="sentiment-analysis"></a>Yaklaşım analizi

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

> [!NOTE]
> Şu sürümde `3.1` :
> * Yaklaşım Analizi, isteğe bağlı bayrak olan görüşleriniz araştırma analizini içerir. 
> * Görüşleriniz madenciliği, en boy ve görüş düzeyi yaklaşımı içerir. 

`sentimentAnalysisExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `analyzeSentiment()` işlevini çağırın. Döndürülen `AnalyzeSentimentResult` nesne `documentSentiment` `sentenceSentiments` , başarılı olursa, veya değilse `errorMessage` . 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Çıktı

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>Görüşün madenciliği

Yaklaşım analizini bir yaklaşım analizi yapmak için, daha önce oluşturduğunuz istemciyi alan adlı adlı yeni bir işlev oluşturun `sentimentAnalysisWithOpinionMiningExample()` ve `analyzeSentiment()` işlevini seçenek nesnesi ile çağırın `AnalyzeSentimentOptions` . Döndürülen `AnalyzeSentimentResult` nesne `documentSentiment` `sentenceSentiments` , başarılı olursa, veya değilse `errorMessage` . 


```java
 static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
 {
     // The document that needs be analyzed.
     String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

     System.out.printf("Document = %s%n", document);

     AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
     final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
     SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
     System.out.printf(
             "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
             documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


     documentSentiment.getSentences().forEach(sentenceSentiment -> {
         SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
         System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                 sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
         sentenceSentiment.getOpinions().forEach(opinion -> {
             TargetSentiment targetSentiment = opinion.getTarget();
             System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                     targetSentiment.getText());
             for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                 System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                         assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
             }
         });
     });
 }
```

### <a name="output"></a>Çıktı

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`sentimentAnalysisExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `analyzeSentiment()` işlevini çağırın. Döndürülen `AnalyzeSentimentResult` nesne `documentSentiment` `sentenceSentiments` , başarılı olursa, veya değilse `errorMessage` . 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Çıktı

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

---

## <a name="language-detection"></a>Dil algılama

`detectLanguageExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `detectLanguage()` işlevini çağırın. Döndürülen `DetectLanguageResult` nesne, algılanan birincil dili, başarılı olursa algılanan diğer dillerin bir listesini ya da olmadığını içerir `errorMessage` . Bu örnek, API 'nin 3,0 ve 3,1 sürümleri için aynıdır.

> [!Tip]
> Bazı durumlarda, girişi temel alarak dilleri ayırt etmek zor olabilir. `countryHint`Parametresini 2 harfli bir ülke kodu belirtmek için kullanabilirsiniz. Varsayılan olarak, API varsayılan Countryipucu olarak "US" kullanıyor, bu davranışı kaldırmak için bu değeri boş dize olarak ayarlayarak bu parametreyi sıfırlayabilirsiniz `countryHint = ""` . Farklı bir varsayılan ayarlamak için, özelliği ayarlayın `TextAnalyticsClientOptions.DefaultCountryHint` ve istemcinin başlatılması sırasında geçirin.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Çıktı

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Adlandırılmış varlık tanıma (NER)

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

> [!NOTE]
> Şu sürümde `3.1` :
> * NER kişisel bilgileri algılamak için ayrı yöntemler içerir. 
> * Varlık bağlama, NER 'den ayrı bir istek.

`recognizeEntitiesExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `recognizeEntities()` işlevini çağırın. Döndürülen `CategorizedEntityCollection` nesne `CategorizedEntity` , başarılı olursa bir liste veya değilse bir listesi içerir `errorMessage` .

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Çıktı

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Varlık bağlama

`recognizeLinkedEntitiesExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `recognizeLinkedEntities()` işlevini çağırın. Döndürülen `LinkedEntityCollection` nesne `LinkedEntity` , başarılı olursa bir liste veya değilse bir listesi içerir `errorMessage` . Bağlantılı varlıklar benzersiz olarak tanımlandıklarından, aynı varlığın oluşumları `LinkedEntity` nesne listesi olarak bir nesne altında gruplandırılır `LinkedEntityMatch` .


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Çıktı

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Kişisel olarak tanımlanabilir bilgi tanıma

`recognizePiiEntitiesExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `recognizePiiEntities()` işlevini çağırın. Döndürülen `PiiEntityCollection` nesne `PiiEntity` , başarılı olursa bir liste veya değilse bir listesi içerir `errorMessage` . Aynı zamanda, tüm tanımlanabilir varlıkların değiştirildiği metin giriş metninin bulunduğu Redaksiyonu yapılmış metni de içerecektir `*****` .

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Çıktı

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

> [!NOTE]
> Şu sürümde `3.0` :
> * NER kişisel bilgileri algılamak için ayrı yöntemler içerir. 
> * Varlık bağlama, NER 'den ayrı bir istek.

`recognizeEntitiesExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `recognizeEntities()` işlevini çağırın. Döndürülen `CategorizedEntityCollection` nesne `CategorizedEntity` , başarılı olursa bir liste veya değilse bir listesi içerir `errorMessage` .

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Çıktı

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>Varlık bağlama

`recognizeLinkedEntitiesExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `recognizeLinkedEntities()` işlevini çağırın. Döndürülen `LinkedEntityCollection` nesne `LinkedEntity` , başarılı olursa bir liste veya değilse bir listesi içerir `errorMessage` . Bağlantılı varlıklar benzersiz olarak tanımlandıklarından, aynı varlığın oluşumları `LinkedEntity` nesne listesi olarak bir nesne altında gruplandırılır `LinkedEntityMatch` .

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Çıktı

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Anahtar ifade ayıklama

`extractKeyPhrasesExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `extractKeyPhrases()` işlevini çağırın. Döndürülen `ExtractKeyPhraseResult` nesne, başarılı olursa anahtar tümceciklerin bir listesini içerir ya da `errorMessage` böyle değildir. Bu örnek, API 'nin sürüm 3,0 ve 3,1 ' de aynıdır.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Çıktı

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Çözümleme işlemi ile API 'YI zaman uyumsuz olarak kullanma

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

İşlevini çağıran, adlı yeni bir işlev oluşturun `analyzeBatchActionsExample()` `beginAnalyzeBatchActions()` . Sonuç, sonuçlar için yoklamak üzere uzun süren bir işlem olacaktır.

```java
static void analyzeBatchActionsExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        
        SyncPoller<AnalyzeBatchActionsOperationDetail, PagedIterable<AnalyzeBatchActionsResult>> syncPoller =
                client.beginAnalyzeBatchActions(documents,
                        new TextAnalyticsActions().setDisplayName("Analyze Batch Actions Quickstart")
                                .setRecognizeEntitiesOptions(new RecognizeEntitiesOptions()),
                        new AnalyzeBatchActionsOptions().setIncludeStatistics(false),
                        Context.NONE);

        // Task operation statistics
        while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
            final AnalyzeBatchActionsOperationDetail operationResult = syncPoller.poll().getValue();
            System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d, failed actions: %d, total actions: %d%n",
                    operationResult.getDisplayName(), operationResult.getActionsSucceeded(),
                    operationResult.getActionsInProgress(), operationResult.getActionsFailed(),
                    operationResult.getActionsInTotal());
        }

        syncPoller.waitForCompletion();

        Iterable<PagedResponse<AnalyzeBatchActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
        for (PagedResponse<AnalyzeBatchActionsResult> page : pagedResults) {
            System.out.printf("Response code: %d, Continuation Token: %s.%n", page.getStatusCode(), page.getContinuationToken());
            page.getElements().forEach(analyzeBatchActionsResult -> {
                System.out.println("Entities recognition action results:");
                IterableStream<RecognizeEntitiesActionResult> recognizeEntitiesActionResults =
                        analyzeBatchActionsResult.getRecognizeEntitiesActionResults();
                if (recognizeEntitiesActionResults != null) {
                    recognizeEntitiesActionResults.forEach(actionResult -> {
                        if (!actionResult.isError()) {
                            // Recognized entities for each of documents from a batch of documents
                            AtomicInteger counter = new AtomicInteger();
                            for (RecognizeEntitiesResult documentResult : actionResult.getResult()) {
                                System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                if (documentResult.isError()) {
                                    // Erroneous document
                                    System.out.printf("Cannot recognize entities. Error: %s%n",
                                            documentResult.getError().getMessage());
                                } else {
                                    // Valid document
                                    documentResult.getEntities().forEach(entity -> System.out.printf(
                                            "Recognized entity: %s, entity category: %s, entity subcategory: %s, confidence score: %f.%n",
                                            entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
                                }
                            }
                        } else {
                            TextAnalyticsError actionError = actionResult.getError();
                            // Erroneous action
                            System.out.printf("Cannot execute Entities Recognition action. Error: %s%n", actionError.getMessage());
                        }
                    });
                }
            });
        }
    }
```

Bu örneği uygulamanıza ekledikten sonra, `main()` yöntebir yönteme çağırın.

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>Çıktı

```console
Action display name: Analyze Batch Actions Quickstart, Successfully completed actions: 0, in-process actions: 1, failed actions: 0, total actions: 1
Response code: 200, Continuation Token: null.
Entities recognition action results:

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.970000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

PII 'yi algılamak, bağlı varlıkları tanımak ve anahtar tümceciği ayıklama için Çözümle işlemini de kullanabilirsiniz. GitHub 'daki [Çözümle örneği](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro) ' ne bakın.

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Bu özellik 3,0 sürümünde kullanılamaz.

---

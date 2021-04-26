---
title: 'Hızlı başlangıç: C# için Metin Analizi istemci kitaplığı | Microsoft Docs'
description: 'C için Metin Analizi istemci kitaplığı ile çalışmaya başlama #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 1fd102f0f94f1ce53bebfba94d4f4c1a1f9e3812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765151"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[v 3.1 başvuru belgeleri](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview)  |  [v 3.1 kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [v 3.1 paketi (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.5)  |  [v 3.1 örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

[v3 başvuru belgeleri](/dotnet/api/azure.ai.textanalytics)  |  [v3 kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics)  |  [V3 paketi (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics)  |  [v3 örnekleri](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Visual STUDIO IDE](https://visualstudio.microsoft.com/vs/)
* Azure aboneliğiniz olduktan sonra, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Metin Analizi kaynağı oluşturun metin analizi bir kaynak oluşturun.  Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Metin Analizi API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* Çözümle özelliğini kullanmak için standart fiyatlandırma katmanına sahip bir Metin Analizi kaynağına ihtiyacınız olacaktır.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-net-core-application"></a>Yeni bir .NET Core uygulaması oluşturma

Visual Studio IDE 'yi kullanarak yeni bir .NET Core konsol uygulaması oluşturun. Bu, tek bir C# kaynak dosyası içeren bir "Merhaba Dünya" projesi oluşturur: *program. cs*.

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

**Çözüm Gezgini** çözüme sağ tıklayıp **NuGet Paketlerini Yönet**' i seçerek istemci kitaplığını yüklemelisiniz. Açılan paket yöneticisinde, **bul** ve ara ' yı seçin `Azure.AI.TextAnalytics` . **Dahil etme prerelase** kutusunu işaretleyin, sürüm ' ü seçin `5.1.0-beta.5` ve ardından ' i seçin. [Paket Yöneticisi konsolunu](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)da kullanabilirsiniz.

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

**Çözüm Gezgini** çözüme sağ tıklayıp **NuGet Paketlerini Yönet**' i seçerek istemci kitaplığını yüklemelisiniz. Açılan paket yöneticisinde, **bul** ve ara ' yı seçin `Azure.AI.TextAnalytics` . Sürüm `5.0.0` ' ü ve ardından **öğesini seçin**. [Paket Yöneticisi konsolunu](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)da kullanabilirsiniz.


> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub 'da](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)bulabilirsiniz. 

---

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

*Program. cs* dosyasını açın ve aşağıdaki `using` yönergeleri ekleyin:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Uygulamanın `Program` sınıfında, kaynağınızın anahtarı ve uç noktası için değişkenler oluşturun.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Uygulamanın `Main` yöntemini değiştirin. Burada, daha sonra çağrılan yöntemleri tanımlayacaksınız.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

*Program. cs* dosyasını açın ve aşağıdaki `using` yönergeleri ekleyin:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Uygulamanın `Program` sınıfında, kaynağınızın anahtarı ve uç noktası için değişkenler oluşturun.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Uygulamanın `Main` yöntemini değiştirin. Burada, daha sonra çağrılan yöntemleri tanımlayacaksınız.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

---

## <a name="object-model"></a>Nesne modeli

Metin Analizi istemcisi, `TextAnalyticsClient` anahtarınızı kullanarak Azure 'da kimlik doğrulayan bir nesnedir ve metni tek dizeler veya toplu işlem olarak kabul etmek için işlevler sağlar. API 'ye eşzamanlı olarak veya zaman uyumsuz olarak metin gönderebilirsiniz. Yanıt nesnesi, göndereceğiniz her belge için analiz bilgilerini içerecektir. 

Hizmetin sürümünü kullanıyorsanız `3.x` , `TextAnalyticsClientOptions` istemciyi çeşitli varsayılan ayarlarla başlatmak için isteğe bağlı bir örnek kullanabilirsiniz (örneğin, varsayılan dil veya ülke/bölge ipucu). Ayrıca Azure Active Directory belirteci kullanarak kimlik doğrulaması yapabilirsiniz. 

## <a name="code-examples"></a>Kod örnekleri

* [Yaklaşım analizi](#sentiment-analysis)
* [Görüşün madenciliği](#opinion-mining)
* [Dil algılama](#language-detection)
* [Adlandırılmış Varlık Tanıma](#named-entity-recognition-ner)
* [Varlık bağlama](#entity-linking)
* [Anahtar tümceciği ayıklama](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

Daha önce gelen ana yönteminizin bitiş noktanız ve kimlik bilgilerinizle yeni bir istemci nesnesi oluşturduğundan emin olun.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Daha önce gelen ana yönteminizin bitiş noktanız ve kimlik bilgilerinizle yeni bir istemci nesnesi oluşturduğundan emin olun.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>Yaklaşım analizi

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`SentimentAnalysisExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `AnalyzeSentiment()` işlevini çağırın. Döndürülen `Response<DocumentSentiment>` nesne, tüm giriş belgesi için yaklaşım etiketini ve Puanını, başarılı olursa her tümce için de bir yaklaşım analizini içerir. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Çıktı

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Görüşün madenciliği

Daha önce oluşturduğunuz istemciyi alan adlı adlı yeni bir işlev oluşturun `SentimentAnalysisWithOpinionMiningExample()` ve bu işlevi `AnalyzeSentimentBatch()` `IncludeOpinionMining` pakette seçeneğini kullanarak çağırın `AnalyzeSentimentOptions` . Döndürülen `AnalyzeSentimentResultCollection` nesne, öğesinin temsil ettiği koleksiyonunu içerir `AnalyzeSentimentResult` `Response<DocumentSentiment>` . Ve arasındaki fark `SentimentAnalysis()` , `SentimentAnalysisWithOpinionMiningExample()` `SentenceOpinion` bir çözümlenmiş hedefi ve ilgili değerlendirmeyi gösteren her bir tümcede yer alacak. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Çıktı

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`SentimentAnalysisExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `AnalyzeSentiment()` işlevini çağırın. Döndürülen `Response<DocumentSentiment>` nesne, tüm giriş belgesi için yaklaşım etiketini ve Puanını, başarılı olursa her tümce için de bir yaklaşım analizini içerir. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Çıktı

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

---

## <a name="language-detection"></a>Dil algılama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)


`LanguageDetectionExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `DetectLanguage()` işlevini çağırın. Döndürülen `Response<DetectedLanguage>` nesne, adı ve ISO-6391 koduyla birlikte algılanan dili içerir. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

> [!Tip]
> Bazı durumlarda, girişi temel alarak dilleri ayırt etmek zor olabilir. `countryHint`Parametresini 2 harfli bir ülke/bölge kodu belirtmek için kullanabilirsiniz. Varsayılan olarak, API varsayılan Countryipucu olarak "US" kullanıyor, bu davranışı kaldırmak için bu değeri boş dize olarak ayarlayarak bu parametreyi sıfırlayabilirsiniz `countryHint = ""` . Farklı bir varsayılan ayarlamak için, özelliği ayarlayın `TextAnalyticsClientOptions.DefaultCountryHint` ve istemcinin başlatılması sırasında geçirin.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Çıktı

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)


`LanguageDetectionExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `DetectLanguage()` işlevini çağırın. Döndürülen `Response<DetectedLanguage>` nesne, adı ve ISO-6391 koduyla birlikte algılanan dili içerir. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

> [!Tip]
> Bazı durumlarda, girişi temel alarak dilleri ayırt etmek zor olabilir. `countryHint`Parametresini 2 harfli bir ülke/bölge kodu belirtmek için kullanabilirsiniz. Varsayılan olarak, API varsayılan Countryipucu olarak "US" kullanıyor, bu davranışı kaldırmak için bu değeri boş dize olarak ayarlayarak bu parametreyi sıfırlayabilirsiniz `countryHint = ""` . Farklı bir varsayılan ayarlamak için, özelliği ayarlayın `TextAnalyticsClientOptions.DefaultCountryHint` ve istemcinin başlatılması sırasında geçirin.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Çıktı

```console
Language:
        French, ISO-6391: fr
```


---

## <a name="named-entity-recognition-ner"></a>Adlandırılmış varlık tanıma (NER)

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)


`EntityRecognitionExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun, `RecognizeEntities()` işlevini çağırın ve sonuçlar arasında yineleme yapın. Döndürülen `Response<CategorizedEntityCollection>` nesne, algılanan varlıkların koleksiyonunu içerir `CategorizedEntity` . Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Çıktı

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Varlık bağlama

`EntityLinkingExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun, `RecognizeLinkedEntities()` işlevini çağırın ve sonuçlar arasında yineleme yapın. Döndürülen `Response<LinkedEntityCollection>` nesne, algılanan varlıkların koleksiyonunu içerir `LinkedEntity` . Bir hata oluşursa, bir oluşturur `RequestFailedException` . Bağlantılı varlıklar benzersiz olarak tanımlandıklarından, aynı varlığın oluşumları `LinkedEntity` nesne listesi olarak bir nesne altında gruplandırılır `LinkedEntityMatch` .

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Çıktı

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Kişisel olarak tanımlanabilir bilgi tanıma

`RecognizePIIExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun, `RecognizePiiEntities()` işlevini çağırın ve sonuçlar arasında yineleme yapın. Döndürülen `PiiEntityCollection` PII varlıklarının listesini temsil eder. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Çıktı

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)


> [!NOTE]
> Sürümünde yeni `3.0` :
> * Varlık bağlama artık varlık tanıma işleminden ayrılmıştır.


`EntityRecognitionExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun, `RecognizeEntities()` işlevini çağırın ve sonuçlar arasında yineleme yapın. Döndürülen `Response<IReadOnlyCollection<CategorizedEntity>>` nesne, algılanan varlıkların listesini içerir. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Çıktı

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Varlık bağlama

`EntityLinkingExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun, `RecognizeLinkedEntities()` işlevini çağırın ve sonuçlar arasında yineleme yapın. Döndürülen `Response<IReadOnlyCollection<LinkedEntity>>` varlıkların listesini temsil eder. Bir hata oluşursa, bir oluşturur `RequestFailedException` . Bağlantılı varlıklar benzersiz olarak tanımlandıklarından, aynı varlığın oluşumları `LinkedEntity` nesne listesi olarak bir nesne altında gruplandırılır `LinkedEntityMatch` .

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Çıktı

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

--- 


### <a name="key-phrase-extraction"></a>Anahtar ifade ayıklama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`KeyPhraseExtractionExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `ExtractKeyPhrases()` işlevini çağırın. Döndürülen `<Response<KeyPhraseCollection>` nesne, algılanan anahtar tümceciklerin listesini içerir. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Çıktı

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`KeyPhraseExtractionExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `ExtractKeyPhrases()` işlevini çağırın. Döndürülen `<Response<IReadOnlyCollection<string>>` nesne, algılanan anahtar tümceciklerin listesini içerir. Bir hata oluşursa, bir oluşturur `RequestFailedException` .

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Çıktı

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Çözümleme işlemi ile API 'YI zaman uyumsuz olarak kullanma

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

`AnalyzeOperationExample()`Daha önce oluşturduğunuz istemciyi alan adlı yeni bir işlev oluşturun ve `StartAnalyzeBatchActionsAsync()` işlevini çağırın. Döndürülen `AnalyzeBatchActionsOperation` nesne `Operation` Interface nesnesini içerecektir. Uzun süre çalışan bir Işlem olduğu için, `await` üzerinde `operation.WaitForCompletionAsync()` güncelleştirilecektir. Tamamlandıktan sonra `WaitForCompletionAsync()` koleksiyonun içinde güncelleştirilmeleri gerekir `operation.Value` . Bir hata oluşursa, bir oluşturur `RequestFailedException` .


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };


    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        RecognizeEntitiesOptions = new List<RecognizeEntitiesOptions>() { new RecognizeEntitiesOptions() },
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeBatchActionsOperation operation = await client.StartAnalyzeBatchActionsAsync(batchDocuments, actions);

    await operation.WaitForCompletionAsync();

    Console.WriteLine($"Status: {operation.Status}");
    Console.WriteLine($"Created On: {operation.CreatedOn}");
    Console.WriteLine($"Expires On: {operation.ExpiresOn}");
    Console.WriteLine($"Last modified: {operation.LastModified}");
    if (!string.IsNullOrEmpty(operation.DisplayName))
        Console.WriteLine($"Display name: {operation.DisplayName}");
    Console.WriteLine($"Total actions: {operation.TotalActions}");
    Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
    Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
    Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

    await foreach (AnalyzeBatchActionsResult documentsInPage in operation.Value)
    {
        RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesActionsResults.FirstOrDefault().Result;

        Console.WriteLine("Recognized Entities");

        foreach (RecognizeEntitiesResult result in entitiesResult)
        {
            Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

            foreach (CategorizedEntity entity in result.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
            }
            Console.WriteLine("");
        }
    }
}
```

Bu örneği uygulamanıza ekledikten sonra, `main()` kullanarak yönteminizi çağırın `await` .

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Çıktı

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

PII ve anahtar tümceciği ayıklamayı algılamak için Çözümle işlemini de kullanabilirsiniz. GitHub 'daki [Çözümle örneği](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) ' ne bakın.

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Bu özellik 3,0 sürümünde kullanılamaz.

---

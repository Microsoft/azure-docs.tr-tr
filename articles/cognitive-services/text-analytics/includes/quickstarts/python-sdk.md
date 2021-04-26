---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.openlocfilehash: d36666ea35a4ce3fc894e35dd8ca3860900ecef6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107880241"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[v 3.1 başvuru belgeleri](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [v 3.1 kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics)  |  [v 3.1 paketi (PIG)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v 3.1 örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

[v3 başvuru belgeleri](/python/api/azure-ai-textanalytics/azure.ai.textanalytics)  |  [v3 kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  [V3 paketi (Pıy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3 örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Azure aboneliğiniz olduktan sonra, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> </a> anahtarınızı ve uç noktanızı almak için Azure Portal bir Metin Analizi kaynağı oluşturun metin analizi bir kaynak oluşturun. Dağıtıldıktan sonra **Kaynağa Git ' e** tıklayın.
    * Uygulamanızı Metin Analizi API'si bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı daha sonra hızlı başlangıçta aşağıdaki koda yapıştırabilirsiniz.
    * `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.
* Çözümle özelliğini kullanmak için standart fiyatlandırma katmanına sahip bir Metin Analizi kaynağına ihtiyacınız olacaktır.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Python yükledikten sonra, ile istemci kitaplığını yükleyebilirsiniz:

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub 'da](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)bulabilirsiniz. 

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Tüm hızlı başlangıç kodu dosyasını aynı anda görüntülemek mi istiyorsunuz? Bu hızlı başlangıçta kod örneklerini içeren [GitHub 'da](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)bulabilirsiniz. 


---

### <a name="create-a-new-python-application"></a>Yeni bir Python uygulaması oluşturma

Yeni bir Python dosyası oluşturun ve kaynağınızın Azure uç noktası ve abonelik anahtarı için değişkenler oluşturun.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Nesne modeli

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

Metin Analizi istemcisi, Azure 'da `TextAnalyticsClient` kimlik doğrulayan bir nesnedir. İstemci, metin çözümlemek için çeşitli yöntemler sağlar. 

İşleme metni, bir `documents` dize listesi, bir dict benzeri gösterim listesi veya bir listesi olarak olan bir liste olarak API 'ye gönderilir `TextDocumentInput/DetectLanguageInput` . Bir `dict-like` nesnesi,, ve birleşimini `id` içerir `text` `language/country_hint` . `text`Özniteliği, kaynak olarak çözümlenecek metni depolar `country_hint` ve `id` herhangi bir değer olabilir. 

Yanıt nesnesi, her belge için analiz bilgilerini içeren bir listesidir. 

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Metin Analizi istemcisi, `TextAnalyticsClient` anahtarınızı kullanarak Azure 'da kimlik doğrulayan bir nesnedir. İstemci, toplu olarak metin çözümlemek için çeşitli yöntemler sağlar. 

Toplu işlem metni, API 'ye bir listesi olarak gönderildiğinde, `documents` `dictionary` `id` `text` `language` kullanılan yönteme bağlı olarak, ve özniteliklerinin bir bileşimini içeren nesneler olan. `text`Özniteliği, kaynak olarak çözümlenecek metni depolar `language` ve `id` herhangi bir değer olabilir. 

Yanıt nesnesi, her belge için çözümlenen bilgileri içeren bir listesidir. 

---

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için Metin Analizi istemci kitaplığı ile aşağıdaki görevlerin nasıl yapılacağını gösterir:

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yaklaşım Analizi](#sentiment-analysis)
* [Görüşün madenciliği](#opinion-mining)
* [Dil algılama](#language-detection)
* [Adlandırılmış varlık tanıma](#named-entity-recognition-ner) 
* [Kişisel olarak tanımlanabilir bilgi tanıma](#personally-identifiable-information-recognition) 
* [Varlık bağlama](#entity-linking)
* [Anahtar tümceciği ayıklama](#key-phrase-extraction)


# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yaklaşım Analizi](#sentiment-analysis)
* [Dil algılama](#language-detection)
* [Adlandırılmış varlık tanıma](#named-entity-recognition-ner) 
* [Varlık bağlama](#entity-linking)
* [Anahtar tümceciği ayıklama](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`TextAnalyticsClient` `key` Yukarıda oluşturduğunuz ve oluşturduğunuz nesneyi başlatmak için bir işlev oluşturun `endpoint` . Ardından yeni bir istemci oluşturun. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`TextAnalyticsClient` `key` Yukarıda oluşturduğunuz ve oluşturduğunuz nesneyi başlatmak için bir işlev oluşturun `endpoint` . Ardından yeni bir istemci oluşturun. `api_version=TextAnalyticsApiVersion.V3_0`Sürüm 3,0 ' i kullanmak için tanımlanması gerektiğini unutmayın.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Kullanarak istemci kitaplığının v 5.1.0 sürümünü yüklediyseniz `pip install azure-ai-textanalytics --pre` , metin Analizi API'si v 3.0 ' ı istemcinin `api_version` parametresiyle belirtebilirsiniz. Yalnızca `authenticate_client()` istemciniz v 5.1.0 veya üzeri ise aşağıdaki yöntemi kullanın.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>Yaklaşım analizi

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`sentiment_analysis_example()`İstemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `analyze_sentiment()` işlevi çağırır. Döndürülen yanıt nesnesi, tüm giriş belgesi için yaklaşım etiketini ve Puanını, her tümce için de yaklaşım analizini içerir.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Çıktı

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Görüşün madenciliği

Fikrinizi inceleme ile yaklaşım analizi yapmak için, `sentiment_analysis_with_opinion_mining_example()` istemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `analyze_sentiment()` işlevi seçenek bayrağıyla çağırır `show_opinion_mining=True` . Döndürülen yanıt nesnesi, her tümce için yaklaşım analiziyle birlikte tüm giriş belgesi ve açıklama düzeyi yaklaşım analizini değil yalnızca yaklaşım etiketini ve Puanını içermez.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Çıktı

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`sentiment_analysis_example()`İstemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `analyze_sentiment()` işlevi çağırır. Döndürülen yanıt nesnesi, tüm giriş belgesi için yaklaşım etiketini ve Puanını, her tümce için de yaklaşım analizini içerir.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Çıktı

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="language-detection"></a>Dil algılama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`language_detection_example()`İstemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `detect_language()` işlevi çağırır. Döndürülen yanıt nesnesi, başarılı olursa ' da algılanan dili içerir `primary_language` ve bunu `error` değildir.

> [!Tip]
> Bazı durumlarda, girişi temel alarak dilleri ayırt etmek zor olabilir. `country_hint`Parametresini 2 harfli bir ülke kodu belirtmek için kullanabilirsiniz. Varsayılan olarak, API varsayılan Countryipucu olarak "US" kullanıyor, bu davranışı kaldırmak için bu değeri boş dize olarak ayarlayarak bu parametreyi sıfırlayabilirsiniz `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Çıktı

```console
Language:  French
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`language_detection_example()`İstemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `detect_language()` işlevi çağırır. Döndürülen yanıt nesnesi, başarılı olursa ' da algılanan dili içerir `primary_language` ve bunu `error` değildir.

> [!Tip]
> Bazı durumlarda, girişi temel alarak dilleri ayırt etmek zor olabilir. `country_hint`Parametresini 2 harfli bir ülke kodu belirtmek için kullanabilirsiniz. Varsayılan olarak, API varsayılan Countryipucu olarak "US" kullanıyor, bu davranışı kaldırmak için bu değeri boş dize olarak ayarlayarak bu parametreyi sıfırlayabilirsiniz `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Çıktı

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>Adlandırılmış varlık tanıma (NER)

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

> [!NOTE]
> Şu sürümde `3.1` : 
> * Varlık bağlama, NER 'den ayrı bir istek.

`entity_recognition_example`İstemciyi bir bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `recognize_entities()` işlevi çağırır ve sonuçlar boyunca yinelenir. Döndürülen yanıt nesnesi, başarılı olursa ' da algılanan varlıkların listesini içerir `entity` ve bunu `error` değildir. Algılanan her varlık için kategorisini ve varsa Sub-Category yazdırın.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Çıktı

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Varlık Bağlama

`entity_linking_example()`İstemciyi bir bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `recognize_linked_entities()` işlevi çağırır ve sonuçlar boyunca yinelenir. Döndürülen yanıt nesnesi, başarılı olursa ' da algılanan varlıkların listesini içerir `entities` ve bunu `error` değildir. Bağlantılı varlıklar benzersiz olarak tanımlandıklarından, aynı varlığın oluşumları `entity` nesne listesi olarak bir nesne altında gruplandırılır `match` .

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Çıktı

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Kişisel olarak tanımlanabilir bilgi tanıma

`pii_recognition_example`İstemciyi bir bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `recognize_pii_entities()` işlevi çağırır ve sonuçlar boyunca yinelenir. Döndürülen yanıt nesnesi, başarılı olursa ' da algılanan varlıkların listesini içerir `entity` ve bunu `error` değildir. Algılanan her varlık için kategorisini ve varsa Sub-Category yazdırın.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Çıktı

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

> [!NOTE]
> Şu sürümde `3.0` : 
> * Varlık bağlama, NER 'den ayrı bir istek.

`entity_recognition_example`İstemciyi bir bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `recognize_entities()` işlevi çağırır ve sonuçlar boyunca yinelenir. Döndürülen yanıt nesnesi, başarılı olursa ' da algılanan varlıkların listesini içerir `entity` ve bunu `error` değildir. Algılanan her varlık için kategorisini ve varsa Sub-Category yazdırın.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Çıktı

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Varlık Bağlama

`entity_linking_example()`İstemciyi bir bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `recognize_linked_entities()` işlevi çağırır ve sonuçlar boyunca yinelenir. Döndürülen yanıt nesnesi, başarılı olursa ' da algılanan varlıkların listesini içerir `entities` ve bunu `error` değildir. Bağlantılı varlıklar benzersiz olarak tanımlandıklarından, aynı varlığın oluşumları `entity` nesne listesi olarak bir nesne altında gruplandırılır `match` .

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Çıktı

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

### <a name="key-phrase-extraction"></a>Anahtar ifade ayıklama

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

`key_phrase_extraction_example()`İstemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `extract_key_phrases()` işlevi çağırır. Sonuç, başarılı olursa ' de algılanan anahtar tümceciklerin listesini içerir `key_phrases` ve bunu `error` değildir. Algılanan tüm anahtar tümceleri yazdır.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Çıktı

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

`key_phrase_extraction_example()`İstemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `extract_key_phrases()` işlevi çağırır. Sonuç, başarılı olursa ' de algılanan anahtar tümceciklerin listesini içerir `key_phrases` ve bunu `error` değildir. Algılanan tüm anahtar tümceleri yazdır.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Çıktı

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-batch-analyze-operation"></a>Batch çözümleme işlemiyle API 'YI zaman uyumsuz olarak kullanma

# <a name="version-31-preview"></a>[Sürüm 3,1 Önizleme](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

`analyze_batch_example()`İstemciyi bağımsız değişken olarak alan adlı yeni bir işlev oluşturun, sonra `begin_analyze_batch_actions()` işlevi çağırır. Sonuç, sonuçlar için yoklamak üzere uzun süren bir işlem olacaktır.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_batch_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result) if not action_result.is_error]

        entities_recognition_task_result = action_results[0]
        print("Results of Entities Recognition action:")
        docs = [doc for doc in entities_recognition_task_result.document_results if not doc.is_error]

        for idx, doc in enumerate(docs):
            print("\nDocument text: {}".format(documents[idx]))
            for entity in doc.entities:
                print("Entity: {}".format(entity.text))
                print("...Category: {}".format(entity.category))
                print("...Confidence Score: {}".format(entity.confidence_score))
                print("...Offset: {}".format(entity.offset))
            print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Çıktı

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

PII algılaması ve anahtar tümceciği ayıklama gerçekleştirmek için Batch çözümleme işlemini de kullanabilirsiniz. GitHub 'da [Batch çözümleme örneğine](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_batch_actions.py) bakın.

# <a name="version-30"></a>[Sürüm 3,0](#tab/version-3)

Bu özellik 3,0 sürümünde kullanılamaz.

---

---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/02/2019
ms.author: aahi
ms.openlocfilehash: 847b2d0489dc04b4275465dbe957b72418bbf1a4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750249"
---
[Başvuru belgeleri](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [paketi (rubygems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [örnekleri](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
* [Ruby](https://www.ruby-lang.org/) 'nin geçerli sürümü

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-text-analytics-azure-resource"></a>Metin Analizi Azure kaynağı oluşturma 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Yeni bir Ruby uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin. Daha sonra kodunuz için `GemFile`adlı bir dosya ve Ruby dosyası oluşturun.

```console
mkdir myapp && cd myapp
```

`GemFile`, istemci kitaplığını bir bağımlılık olarak eklemek için aşağıdaki satırları ekleyin.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Ruby dosyanızda aşağıdaki paketleri içeri aktarın.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

`TEXT_ANALYTICS_ENDPOINT` ve `TEXT_ANALYTICS_SUBSCRIPTION_KEY`adlı kaynağınızın Azure uç noktası ve anahtarı için değişkenler oluşturun. Uygulamayı başlattıktan sonra ortam değişkenini oluşturduysanız, değişkene erişmek için onu çalıştıran düzenleyiciyi, IDE 'yi veya kabuğu kapatıp yeniden açmanız gerekir. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]


[!code-ruby[endpoint, key variables](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=vars)]

## <a name="object-model"></a>Nesne modeli 

Metin Analizi istemcisi anahtarınızı kullanarak Azure 'da kimlik doğrulaması yapar. İstemci, tek bir dize veya bir toplu iş olarak metin çözümlemek için çeşitli yöntemler sağlar. 

Metin, kullanılan yönteme bağlı olarak `id`, `text`ve `language` özniteliklerinin bir birleşimini içeren `dictionary` nesneler olan `documents`bir liste olarak API 'ye gönderilir. `text` özniteliği, kaynak `language`analiz edilecek metni depolar ve `id` herhangi bir değer olabilir. 

Yanıt nesnesi, her belge için analiz bilgilerini içeren bir listesidir. 

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, Python için Metin Analizi istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını gösterir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Yaklaşım Analizi](#sentiment-analysis)
* [Dil algılama](#language-detection)
* [Varlık tanıma](#entity-recognition)
* [Anahtar tümceciği ayıklama](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

`TextAnalyticsClient`adlı bir sınıf oluşturun. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Bu sınıfta, istemcinin kimliğini doğrulamak için `initialize` adlı bir işlev oluşturun. `TEXT_ANALYTICS_SUBSCRIPTION_KEY` ve `TEXT_ANALYTICS_ENDPOINT` ortam değişkenlerinizi kullanın. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Sınıfının dışında, istemcinin örneğini oluşturmak için `new()` işlevini kullanın.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Yaklaşım analizi

İstemci nesnesinde, daha sonra oluşturulacak giriş belgelerinin listesini alan `AnalyzeSentiment()` adlı bir işlev oluşturun. İstemcinin `sentiment()` işlevini çağırın ve sonucu alın. Ardından sonuçları yineleyin ve her belge KIMLIĞINI ve yaklaşım Puanını yazdırın. 0 ' a yakın bir puan negatif bir yaklaşım gösterir, 1 ' e yaklaşarak pozitif bir yaklaşım gösterilir.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

İstemci işlevinin dışında, daha önce oluşturulan `TextAnalyticsClient` nesnesini alan `SentimentAnalysisExample()` adlı yeni bir işlev oluşturun. Analiz etmek istediğiniz belgeleri içeren `MultiLanguageInput` nesnelerinin bir listesini oluşturun. Her nesne bir `id`, `Language` ve bir `text` özniteliği içerir. `text` özniteliği çözümlenecek metni depolar, `language` belgenin dilidir ve `id` herhangi bir değer olabilir. Ardından, istemcinin `AnalyzeSentiment()` işlevini çağırın.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

`SentimentAnalysisExample()` işlevini çağırın.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Çıktı

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Dil algılama

İstemci nesnesinde, daha sonra oluşturulacak giriş belgelerinin listesini alan `DetectLanguage()` adlı bir işlev oluşturun. İstemcinin `detect_language()` işlevini çağırın ve sonucu alın. Sonra sonuçlar arasında yineleme yapın ve her belge KIMLIĞINI ve algılanan dili yazdırın.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

İstemci işlevinin dışında, daha önce oluşturulan `TextAnalyticsClient` nesnesini alan `DetectLanguageExample()` adlı yeni bir işlev oluşturun. Analiz etmek istediğiniz belgeleri içeren `LanguageInput` nesnelerinin bir listesini oluşturun. Her nesne bir `id`ve bir `text` özniteliği içerir. `text` özniteliği çözümlenecek metni depolar ve `id` herhangi bir değer olabilir. Ardından, istemcinin `DetectLanguage()` işlevini çağırın.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

`DetectLanguageExample()` işlevini çağırın.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Çıktı

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Varlık tanıma

İstemci nesnesinde, daha sonra oluşturulacak giriş belgelerinin listesini alan `RecognizeEntities()` adlı bir işlev oluşturun. İstemcinin `entities()` işlevini çağırın ve sonucu alın. Sonra sonuçlar arasında yineleme yapın ve her belge KIMLIĞINI ve tanınan varlıkları yazdırın.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

İstemci işlevinin dışında, daha önce oluşturulan `TextAnalyticsClient` nesnesini alan `RecognizeEntitiesExample()` adlı yeni bir işlev oluşturun. Analiz etmek istediğiniz belgeleri içeren `MultiLanguageInput` nesnelerinin bir listesini oluşturun. Her nesne bir `id`, bir `language`ve bir `text` özniteliği içerir. `text` özniteliği çözümlenecek metni depolar, `language` metnin dilidir ve `id` herhangi bir değer olabilir. Ardından, istemcinin `RecognizeEntities()` işlevini çağırın.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

`RecognizeEntitiesExample()` işlevini çağırın.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Çıktı

```console
===== ENTITY RECOGNITION =====
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

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Anahtar ifade ayıklama

İstemci nesnesinde, daha sonra oluşturulacak giriş belgelerinin listesini alan `ExtractKeyPhrases()` adlı bir işlev oluşturun. İstemcinin `key_phrases()` işlevini çağırın ve sonucu alın. Ardından sonuçları yineleyin ve her belgenin KIMLIĞINI ve ayıklanan anahtar tümceciklerini yazdırın.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

İstemci işlevinin dışında, daha önce oluşturulan `TextAnalyticsClient` nesnesini alan `KeyPhraseExtractionExample()` adlı yeni bir işlev oluşturun. Analiz etmek istediğiniz belgeleri içeren `MultiLanguageInput` nesnelerinin bir listesini oluşturun. Her nesne bir `id`, bir `language`ve bir `text` özniteliği içerir. `text` özniteliği çözümlenecek metni depolar, `language` metnin dilidir ve `id` herhangi bir değer olabilir. Ardından, istemcinin `ExtractKeyPhrases()` işlevini çağırın.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


`KeyPhraseExtractionExample()` işlevini çağırın.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>Çıktı

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

---
title: Android 'de Java 'da Microsoft konuşma tanıma API 'SI ile çalışmaya başlama | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Konuşulan sesi metne dönüştüren Android uygulamaları geliştirmek için Microsoft konuşma API 'sini kullanın.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 77fee2ecee9cfabe3fad9c1df2c41c7803c3367e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966839"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Hızlı Başlangıç: Android 'de Java 'daki Bing Konuşma tanıma API 'sini kullanma

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Konuşma tanıma API 'SI ile, konuşulan sesi metne dönüştürmek için bulut tabanlı Bing Konuşma hizmetini kullanan Android uygulamaları geliştirebilirsiniz. API gerçek zamanlı akışı destekler, böylece uygulamanız aynı anda ve zaman uyumsuz olarak kısmi tanıma sonuçları alabilir ve bu da hizmete ses gönderiyor.

Bu makalede, Android cihazlar için Java 'da konuşmaya metin uygulamaları geliştirmek üzere Android için Konuşma istemci kitaplığı kullanmayı göstermek üzere örnek bir uygulama kullanılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="platform-requirements"></a>Platform gereksinimleri

Örnek, Java 'daki Windows için [Android Studio](https://developer.android.com/sdk/index.html) tarafından geliştirilmiştir.

### <a name="get-the-client-library-and-sample-application"></a>İstemci kitaplığı ve örnek uygulamayı edinme

Android için Konuşma istemci kitaplığı ve örnekleri, [Android Için konuşma istemcisi SDK 'sında](https://github.com/microsoft/cognitive-speech-stt-android)bulunur. Örnek/SpeechRecoExample dizini altında oluşturulabilir örneği bulabilirsiniz. Armeabi ve x86 klasörü altındaki SpeechSDK/libs içindeki kendi uygulamalarınızda kullanmanız gereken iki kitaplığı bulabilirsiniz. Libandroid_platform. so dosyasının boyutu 22 MB 'tır, ancak dağıtım zamanında 4 MB 'ye düşürülür.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Konuşma API 'sine abone olun ve ücretsiz deneme aboneliği anahtarı alın

Konuşma API 'SI bilişsel hizmetler 'in (daha önce Project Oxford) bir parçasıdır. Bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasından ücretsiz deneme aboneliği anahtarlarına sahip olabilirsiniz. Konuşma API 'sini seçtikten sonra anahtarı almak için **API anahtarı al** ' ı seçin. Birincil ve ikincil anahtar döndürür. Her iki anahtar de aynı kotaya bağlıdır, bu nedenle her iki anahtarı da kullanabilirsiniz.

*Tanımayı amaç ile birlikte*kullanmak istiyorsanız, [Language Understanding Intelligent Service (lusıs)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)için kaydolmanız gerekir.

> [!IMPORTANT]
>* Abonelik anahtarı alın. Konuşma istemci kitaplıklarını kullanabilmeniz için önce bir [abonelik anahtarınız](https://azure.microsoft.com/try/cognitive-services/)olması gerekir.
>
>* Abonelik anahtarınızı kullanın. Sağlanan Android örnek uygulamasıyla, örnek/SpeechRecoExample/res/Values/Strings. xml dosyasını abonelik anahtarlarınız ile güncelleştirin. Daha fazla bilgi için bkz. [oluşturma ve çalıştırma örnekleri](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Konuşma istemci kitaplığı kullanın

Uygulamanızdaki istemci kitaplığını kullanmak için [yönergeleri](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library)izleyin.

Android için istemci kitaplığı başvurusunu [Android Için konuşma istemcisi SDK 'sının](https://github.com/microsoft/cognitive-speech-stt-android)Docs klasöründe bulabilirsiniz.

## <a name="build-and-run-samples"></a>Örnek oluşturma ve çalıştırma

Örnek oluşturma ve çalıştırma hakkında bilgi edinmek için bu [Benioku sayfasına](https://github.com/microsoft/cognitive-speech-stt-android#the-sample)bakın.

## <a name="samples-explained"></a>Açıklanan örnekler

### <a name="create-recognition-clients"></a>Tanıma istemcileri oluşturma

Aşağıdaki örnekteki kod, Kullanıcı senaryolarına göre tanınma istemci sınıflarının nasıl oluşturulacağını gösterir:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

İstemci kitaplığı, konuşma tanımada tipik senaryolar için önceden uygulanmış tanıma istemci sınıfları sağlar:

* `DataRecognitionClient`: PCM verileriyle konuşma tanıma (örneğin, bir dosya veya ses kaynağından). Veriler arabelleklere ayrılır ve her bir arabellek konuşma hizmetine gönderilir. Arabelleklere herhangi bir değişiklik yapılmaz, böylece Kullanıcı isterseniz kendi sessizlik algılamayı uygulayabilir. Veriler WAV dosyalarından sağlanmışsa, dosyadan konuşma hizmetine doğru veri gönderebilirsiniz. Ham verileriniz varsa (örneğin, Bluetooth 'tan geliyorsa), önce konuşma hizmetine bir biçim üst bilgisi gönderin ve ardından veriler gelir.
* `MicrophoneRecognitionClient`: Mikrofondan gelen ses ile konuşma tanıma. Mikrofonun açık olduğundan ve mikrofondan alınan verilerin konuşma tanıma hizmetine gönderildiğinden emin olun. Mikrofon verilerine, tanıma hizmetine gönderilmeden önce yerleşik bir "sessizlik algılayıcısı" uygulanır.
* `DataRecognitionClientWithIntent`ve `MicrophoneRecognitionClientWithIntent`: Bu istemciler, tanıma metnine ek olarak, konuşmacının amacı hakkında yapılandırılmış bilgiler sağlar ve bu da, uygulamalarınıza göre başka işlemler için kullanılabilir. "Amaç" kullanmak için, bir modeli [lusıs](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)kullanarak eğitmeniz gerekir.

### <a name="recognition-language"></a>Tanıma dili

İstemcisini oluşturmak için `SpeechRecognitionServiceFactory` kullandığınızda bir dil seçmeniz gerekir. Konuşma hizmeti tarafından desteklenen dillerin tüm listesi için bkz. [desteklenen diller](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Ayrıca şunları ile `SpeechRecognitionServiceFactory`istemcisini oluştururken `SpeechRecognitionMode` belirtmeniz gerekir:

* `ShortPhrase`: En fazla 15 saniye uzunluğunda bir utterlik. Veriler hizmete gönderilirken, istemci birden çok kısmi sonuç ve birden çok n en iyi seçenek içeren bir nihai sonuç alır.
* `LongDictation`: En fazla iki dakikalık bir utterlik. Veriler hizmete gönderilirken, istemci, hizmetin cümle duraklamaları tanımladığı yere bağlı olarak birden çok kısmi sonuç ve birden çok son sonuç alır.

### <a name="attach-event-handlers"></a>Olay işleyicilerini Ekle

Oluşturduğunuz istemciye çeşitli olay işleyicileri ekleyebilirsiniz:

* **Kısmi sonuçlar olayları**: Bu olay, konuşma hizmeti, konuşmayı bitirmeden ( `MicrophoneRecognitionClient`kullanıyorsanız) veya veri göndermeyi ( `DataRecognitionClient`kullanıyorsanız) bir kez her tahmin edildiğinde çağrılır.
* **Hata olayları**: Hizmet bir hata algıladığında çağırılır.
* **Amaç olayları**: Son tanıma sonucu yapılandırılmış bir JSON amacına ayrıştırıldıktan sonra `ShortPhrase` , "withamaç" istemcilerinde (yalnızca modunda) çağırılır.
* **Sonuç olayları**:
  * `ShortPhrase` Modda bu olay çağrılır ve konuşmayı tamamladıktan sonra n en iyi sonuçları döndürür.
  * `LongDictation` Modunda, olay işleyicisi hizmetin cümle duraklamaları tanımladığı yere bağlı olarak birden çok kez çağrılır.
  * **Her n en iyi seçenek için**, tanınan metnin bir güvenirlik değeri ve birkaç farklı biçimi döndürülür. Daha fazla bilgi için bkz. [çıkış biçimi](../Concepts.md#output-format).

## <a name="related-topics"></a>İlgili konular

* [Android için istemci kitaplığı başvurusu](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [.NET ' te Windows C# Için Microsoft konuşma API 'si ile çalışmaya başlama](GetStartedCSharpDesktop.md)
* [İOS üzerinde amaç-C ' d a Microsoft konuşma API 'SI ile çalışmaya başlama](Get-Started-ObjectiveC-iOS.md)
* [JavaScript 'te Microsoft konuşma API 'SI ile çalışmaya başlama](GetStartedJSWebsockets.md)
* [REST aracılığıyla Microsoft konuşma API 'SI ile çalışmaya başlama](GetStartedREST.md)

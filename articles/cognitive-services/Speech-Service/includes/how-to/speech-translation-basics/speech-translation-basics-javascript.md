---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: b43c0da3303b4cdfd4941f9d76b663f8089a1417
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105120"
---
Konuşma hizmetinin temel özelliklerinden biri de insan konuşmanızı algılayabilir ve diğer dillere çevirebilir. Bu hızlı başlangıçta, uygulama ve ürünlarınızdaki konuşma SDK 'sını kullanarak yüksek kaliteli konuşma çevirisi gerçekleştirme hakkında bilgi edinebilirsiniz. Bu hızlı başlangıçta aşağıdakiler dahil konular ele alınmaktadır:

* Konuşmayı metne çevirme
* Konuşmayı birden çok hedef dile çevirme
* Doğrudan konuşma konuşmadan çeviri gerçekleştiriliyor

## <a name="skip-to-samples-on-github"></a>GitHub 'da örneklere atlayın

Örnek koda doğrudan atlamak istiyorsanız GitHub 'da [JavaScript hızlı başlangıç örneklerine](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir Azure hesabınız ve konuşma hizmeti aboneliğiniz olduğunu varsaymaktadır. Hesabınız ve aboneliğiniz yoksa [konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Herhangi bir şey yapabilmeniz <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">için önce JavaScript Için konuşma SDK 'sını </a>yüklemeniz gerekir. Platformunuza bağlı olarak, aşağıdaki yönergeleri kullanın:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web tarayıcısı </a>

Ayrıca, hedef ortama bağlı olarak, aşağıdakilerden birini kullanın:

# <a name="script"></a>[SCRIPT](#tab/script)

JavaScript *microsoft.cognitiveservices.speech.sdk.bundle.js* dosyası <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">için konuşma SDK 'sını</a> indirip ayıklayın ve HTML dosyanıza erişilebilen bir klasöre yerleştirin.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Bir Web tarayıcısını hedefliyorsanız ve etiketini kullanıyorsanız, `<script>` `sdk` ön ek gerekli değildir. `sdk`Ön ek, modülü adlandırmak için kullanılan bir diğer addır `require` .

# <a name="import"></a>[aktarmaya](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Hakkında daha fazla bilgi için `import` bkz. <a href="https://javascript.info/import-export" target="_blank">dışa ve içeri aktarma </a>.

# <a name="require"></a>[gerektirir](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Hakkında daha fazla bilgi için `require` bkz. <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">ne gerektiriyor? </a>.

---

## <a name="create-a-translation-configuration"></a>Çeviri yapılandırması oluşturma

Konuşma SDK 'sını kullanarak çeviri hizmetini çağırmak için bir oluşturmanız gerekir [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) . Bu sınıf, uygulamanız hakkında, anahtarınız ve ilgili bölge, uç nokta, ana bilgisayar veya yetkilendirme belirteci gibi bilgileri içerir.

> [!NOTE]
> Konuşma tanıma, konuşma birleştirme, çeviri veya amaç tanıma işlemlerini gerçekleştirmekten bağımsız olarak her zaman bir yapılandırma oluşturacaksınız.
Şunları başlatabilmeniz için birkaç yol vardır [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) :

* Abonelik ile: bir anahtarı ve ilişkili bölgeyi geçirin.
* Uç nokta ile: bir konuşma hizmeti uç noktasında geçirin. Anahtar veya yetkilendirme belirteci isteğe bağlıdır.
* Bir ana bilgisayar ile: bir konak adresini geçirin. Anahtar veya yetkilendirme belirteci isteğe bağlıdır.
* Yetkilendirme belirteci ile: bir yetkilendirme belirtecini ve ilişkili bölgeyi geçirin.

Bir [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) anahtar ve bölge kullanarak nasıl oluşturulduğuna göz atalım. [Konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free)bölümündeki adımları izleyerek bu kimlik bilgilerini alın.

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Çeviriciyi başlatma

Bir oluşturduktan sonra [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) , bir sonraki adım bir ' ı başlatmaktır [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) . Bir başlattığınızda [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) , bunu geçirmeniz gerekir `speechTranslationConfig` . Bu, çeviri hizmetinin isteğinizi doğrulamak için ihtiyaç duyduğu kimlik bilgilerini sağlar.

Cihazınızın varsayılan mikrofonuna göre sunulan konuşmayı çevirmezseniz, şöyle [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) görünmelidir:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Ses giriş cihazını belirtmek isterseniz, oluşturmanız ve ' ı [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) `audioConfig` başlatırken parametresini sağlamanız gerekir [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

> [!TIP]
> [Ses giriş cihazınız için CIHAZ kimliğini nasıl alabileceğinizi öğrenin](../../../how-to-select-audio-input-devices.md).
`AudioConfig`Nesneye aşağıdaki gibi başvurun:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Mikrofon kullanmak yerine bir ses dosyası sağlamak istiyorsanız, yine de sağlamanız gerekir `audioConfig` . Ancak, bu yalnızca **Node.js** hedeflenirken ve [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) ' yi çağırmak yerine bir oluşturduğunuzda, `fromDefaultMicrophoneInput` parametresini çağırarak `fromWavFileOutput` ve geçireceğiz `filename` .

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Konuşmayı çevirme

JavaScript için konuşma SDK 'Sı için [Translationtanıyıcı sınıfı](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) , konuşma çevirisi için kullanabileceğiniz birkaç yöntem sunar.

* Tek kararlı çeviri (Async)-blok olmayan (zaman uyumsuz) modda çeviri gerçekleştirir. Bu, tek bir utterance 'yi çevirecek. Tek bir utterüance 'in sonunda, sonda sessizlik dinlemesi veya en fazla 15 saniyelik ses işlenene kadar belirlenir.
* Sürekli çeviri (Async)-zaman uyumsuz olarak sürekli çeviri işlemini başlatır. Kullanıcı olaylara kaydolur ve çeşitli uygulama durumlarını işler. Zaman uyumsuz sürekli çeviriyi durdurmak için çağrısı yapın [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> [Konuşma tanıma modunu seçme](../../../get-started-speech-to-text.md)hakkında daha fazla bilgi edinin.
### <a name="specify-a-target-language"></a>Hedef dil belirtin

Çevirmek için hem bir kaynak dili hem de en az bir hedef dil belirtmeniz gerekir.
[Konuşma çevirisi tablosunda](../../../language-support.md#speech-translation)listelenen bir yerel ayarı kullanarak bir kaynak dili seçebilirsiniz. Aynı bağlantıda çevrilmiş dile yönelik seçeneklerinizi bulun. Hedef dillere yönelik seçenekleriniz, metin görüntülemek istediğinizde veya sentezlenmiş konuşmayı dinlemek istediğinizde farklılık gösterir. Ingilizce 'den Almanca 'ya çevirmek için, çeviri yapılandırma nesnesini değiştirin:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Tek atışı tanıma

Şu kullanılarak zaman uyumsuz tek kararlı çeviri örneği aşağıda verilmiştir [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Sonucu işlemek için bazı kodlar yazmanız gerekir. Bu örnek, [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) bir çevirisini Almanca için değerlendirir:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Kodunuz, çeviri işlendiği sırada belirtilen güncelleştirmeleri de işleyebilir.
Bu güncelleştirmeleri, çeviri ilerleme durumu hakkında görsel geri bildirim sağlamak için kullanabilirsiniz.
Çeviri işlemi sırasında belirtilen güncelleştirmeleri gösteren örnek kod için [Bu JavaScript Node.js örneğine](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) bakın. Aşağıdaki kod ayrıca, çeviri işlemi sırasında üretilen ayrıntıları görüntüler.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Sürekli çeviri

Sürekli çeviri, tek kararlı tanıma göre biraz daha karmaşıktır. `recognizing` `recognized` Tanıma sonuçlarını almak için,, ve olaylarına abone olmanızı gerektirir `canceled` . Çeviriyi durdurmak için çağrısı yapmanız gerekir [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) . İşte, bir ses giriş dosyasında sürekli çevirinin nasıl gerçekleştirilebileceğini gösteren bir örnek.

Girişi tanımlayarak ve şunu başlatarak başlayalım [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) :

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Kaynağından gönderilen olaylara abone edeceğiz [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): Ara çeviri sonuçları içeren olaylar için sinyal.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): Son çeviri sonuçlarını içeren olaylar için sinyal (başarılı bir çeviri denemesi olduğunu gösterir).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): Bir çeviri oturumunun sonunu belirten olaylar için sinyal (işlem).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): İptal edilen Çeviri sonuçlarını içeren olaylar için sinyal (sonuç veya doğrudan iptal isteği olarak iptal edilen bir çeviri girişimi veya ya da bir aktarım ya da protokol arızası).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Her şey ayarlandığında, çağırabiliriz [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Kaynak dili seçin

Konuşma çevirisi için ortak bir görev, giriş (veya kaynak) dilini belirtmektir. Giriş dilini Italyanca olarak nasıl değiştirebileceğinizi göz atalım. Kodunuzda, [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) daha sonra hemen altına aşağıdaki satırı ekleyin.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage)Özelliği, bir dil yerel ayar dizesi bekliyor. Desteklenen [yerel ayarlar/diller](../../../language-support.md)listesindeki **yerel ayar** sütununda herhangi bir değer sağlayabilirsiniz.

## <a name="choose-one-or-more-target-languages"></a>Bir veya daha fazla hedef dil seçin

Konuşma SDK 'Sı, paralel olarak birden çok hedef dile çeviri yapabilir. Kullanılabilir hedef diller, kaynak dil listesinden biraz farklıdır ve hedef dilleri bir yerel ayar yerine bir dil kodu kullanarak belirtirsiniz.
[Dil desteği sayfasındaki konuşma çevirisi tablosunda](../../../language-support.md#speech-translation)bulunan metin hedefleri için dil kodlarının listesini görüntüleyin. Ayrıca, bu dile çeviri hakkındaki ayrıntıları burada bulabilirsiniz.

Aşağıdaki kod, bir hedef dil olarak Almanca ekler:

```javascript
translationConfig.addTargetLanguage("de");
```

Birden çok hedef dil çevirisi mümkün olduğundan, sonucu incelerken kodunuzun hedef dili belirtmesi gerekir. Aşağıdaki kod, Almanca için çeviri sonuçlarını alır.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
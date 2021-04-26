---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: e030e6466570d3fc2abf809ca09d46c9f596b765
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105593"
---
Konuşma hizmetinin temel özelliklerinden biri de insan konuşmanızı algılayabilir ve diğer dillere çevirebilir. Bu hızlı başlangıçta, uygulama ve ürünlarınızdaki konuşma SDK 'sını kullanarak yüksek kaliteli konuşma çevirisi gerçekleştirme hakkında bilgi edinebilirsiniz. Bu hızlı başlangıçta aşağıdakiler dahil konular ele alınmaktadır:

* Konuşmayı metne çevirme
* Konuşmayı birden çok hedef dile çevirme
* Doğrudan konuşma konuşmadan çeviri gerçekleştiriliyor

## <a name="skip-to-samples-on-github"></a>GitHub 'da örneklere atlayın

Örnek koda doğrudan atlamak istiyorsanız GitHub 'daki [Java hızlı başlangıç örneklerine](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir Azure hesabınız ve konuşma hizmeti aboneliğiniz olduğunu varsaymaktadır. Hesabınız ve aboneliğiniz yoksa [konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Herhangi bir şey yapabilmeniz için önce konuşma SDK 'sını yüklemeniz gerekir. Platformunuza bağlı olarak, _konuşma SDK 'Sı hakkında_ konusunun <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">konuşma SDK 'sını al</a> bölümünde yer alan yönergeleri izleyin.

## <a name="import-dependencies"></a>Bağımlılıkları içeri aktar

Bu makaledeki örnekleri çalıştırmak için, `import` * en üst kısmına aşağıdaki deyimleri ekleyin *. Java* kod dosyası.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Hassas veriler ve ortam değişkenleri

Bu makaledeki örnek kaynak kodu, konuşma kaynağı abonelik anahtarı ve bölgesi gibi hassas verileri depolamak için ortam değişkenlerine bağımlıdır. Java kod dosyası `static final String` , ve gibi konak makineler ortam değişkenlerinden atanan iki değer içerir `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION` . Bu alanların her ikisi de sınıf kapsamıdır ve sınıfın yöntem gövdelerinde erişilebilir hale getirir. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [ortam değişkenleri ve uygulama yapılandırması](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
```

## <a name="create-a-speech-translation-configuration"></a>Konuşma çevirisi yapılandırması oluşturma

Konuşma SDK 'sını kullanarak konuşma hizmetini çağırmak için bir oluşturmanız gerekir [`SpeechTranslationConfig`][config] . Bu sınıf, uygulamanız hakkında, anahtarınız ve ilgili bölge, uç nokta, ana bilgisayar veya yetkilendirme belirteci gibi bilgileri içerir.

> [!TIP]
> Konuşma tanıma, konuşma birleştirme, çeviri veya amaç tanıma işlemlerini gerçekleştirmekten bağımsız olarak her zaman bir yapılandırma oluşturacaksınız.

Şunları başlatabilmeniz için birkaç yol vardır [`SpeechTranslationConfig`][config] :

* Abonelik ile: bir anahtarı ve ilişkili bölgeyi geçirin.
* Uç nokta ile: bir konuşma hizmeti uç noktasında geçirin. Anahtar veya yetkilendirme belirteci isteğe bağlıdır.
* Bir ana bilgisayar ile: bir konak adresini geçirin. Anahtar veya yetkilendirme belirteci isteğe bağlıdır.
* Yetkilendirme belirteci ile: bir yetkilendirme belirtecini ve ilişkili bölgeyi geçirin.

Bir [`SpeechTranslationConfig`][config] anahtar ve bölge kullanarak nasıl oluşturulduğuna göz atalım. [Konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free)bölümündeki adımları izleyerek bu kimlik bilgilerini alın.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Kaynak dilini değiştir

Konuşma çevirisi 'nin yaygın bir görevi, giriş (veya kaynak) dilini belirtmektir. Giriş dilini Italyanca olarak nasıl değiştirebileceğinizi göz atalım. Kodunuzda, [`SpeechTranslationConfig`][config] yöntemini çağırarak örnekle etkileşime geçin `setSpeechRecognitionLanguage` .

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

[`setSpeechRecognitionLanguage`][recognitionlang]İşlev, dil yerel ayar dizesi bekliyor. Desteklenen [yerel ayarlar/diller](../../../language-support.md)listesindeki **yerel ayar** sütununda herhangi bir değer sağlayabilirsiniz.

## <a name="add-translation-language"></a>Çeviri dili Ekle

Konuşma çevirisi 'nin başka bir ortak görevi, hedef çeviri dillerini belirtmektir, ancak en az bir tane gereklidir ancak katları desteklenir. Aşağıdaki kod parçacığı, hem Fransızca hem de Almanca ' yı çeviri dili hedefi olarak ayarlar.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Her çağrısıyla [`addTargetLanguage`][addlang] , yeni bir hedef çeviri dili belirtilir. Diğer bir deyişle, konuşma kaynak dilden tanındığında, her hedef çeviri, elde edilen çeviri işleminin bir parçası olarak kullanılabilir.

## <a name="initialize-a-translation-recognizer"></a>Çeviri tanıyıcısı başlatma

Bir oluşturduktan sonra [`SpeechTranslationConfig`][config] , bir sonraki adım bir ' ı başlatmaktır [`TranslationRecognizer`][recognizer] . Bir başlattığınızda [`TranslationRecognizer`][recognizer] , bunu geçirmeniz gerekir `translationConfig` . Yapılandırma nesnesi, konuşma hizmeti 'nin isteğinizi doğrulamak için ihtiyaç duyduğu kimlik bilgilerini sağlar.

Cihazınızın varsayılan mikrofonunu kullanarak konuşmayı tanıyor olmanız halinde şöyle [`TranslationRecognizer`][recognizer] görünmelidir:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Ses giriş cihazını belirtmek isterseniz, oluşturmanız ve ' ı [`AudioConfig`][audioconfig] `audioConfig` başlatırken parametresini sağlamanız gerekir [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Ses giriş cihazınız için CIHAZ kimliğini nasıl alabileceğinizi öğrenin](../../../how-to-select-audio-input-devices.md).

İlk `AudioConfig` olarak, nesnesine aşağıdaki gibi başvuracağız:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Mikrofon kullanmak yerine bir ses dosyası sağlamak istiyorsanız, yine de sağlamanız gerekir `audioConfig` . Ancak, [`AudioConfig`][audioconfig] öğesini çağırmak yerine bir oluşturduğunuzda, `fromDefaultMicrophoneInput` `fromWavFileInput` parametresini çağırır ve geçireceğiz `filename` .

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Konuşmayı çevirme

Konuşmayı çevirmek için, konuşma SDK 'Sı bir mikrofona veya ses dosyası girişine bağımlıdır. Konuşma tanıma, konuşma çevirisi öncesinde oluşur. Tüm nesneler başlatıldıktan sonra, Recognize-bir kez işlevini çağırın ve sonucu alın.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Konuşmayı metne dönüştürme hakkında daha fazla bilgi için bkz. [konuşma tanımanın temelleri](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Çevirileri sentezleştir

Başarılı bir konuşma tanıma ve çeviri işleminden sonra, sonuç bir Sözlükteki tüm çevirileri içerir. [`getTranslations`][translations]İşlevi, hedef çeviri dili olarak anahtar içeren bir sözlük döndürür ve değer çevrilmiş metindir. Tanınan konuşma çevrilebilir ve farklı bir dilde (konuşma konuşmaya) alınabilir.

### <a name="event-based-synthesis"></a>Olay tabanlı sensıs

`TranslationRecognizer`Nesne bir olay gösterir `synthesizing` . Olay birkaç kez harekete geçirilir ve çeviri tanıma sonucundan birleştirilmiş sesi almak için bir mekanizma sağlar. Birden çok dile arıyorsanız bkz. [el ile birleştirme](#manual-synthesis). Bir oluşturma ve olay için olay işleyicisi sağlama yoluyla sensıs sesini belirtin [`setVoiceName`][voicename] `synthesizing` , sesi alın. Aşağıdaki örnek, çevrilmiş sesi bir *. wav* dosyası olarak kaydeder.

> [!IMPORTANT]
> Olay tabanlı birleştirme yalnızca tek bir çeviri ile birlikte çalışarak birden çok hedef çeviri **dili eklemeyin.** Ayrıca, [`setVoiceName`][voicename] hedef çeviri diliyle aynı dil olmalıdır, örneğin, `"de"` ile eşleşebilmelidir `"de-DE-Hedda"` .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>El ile birleştirme

[`getTranslations`][translations]İşlevi, çeviri metninin sesini senmek için kullanılabilecek bir sözlük döndürür. Her bir çeviride yineleme yapın ve çeviriyi sentezleştirme. Bir örnek oluştururken `SpeechSynthesizer` `SpeechConfig` nesnenin [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] Özelliği istenen sesle ayarlanmış olması gerekir. Aşağıdaki örnek, beş dili dönüştürür ve her bir çeviri, karşılık gelen sinir dilinde bir ses dosyası ile birleştirilmiştir.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Konuşma birleştirme hakkında daha fazla bilgi için bkz. [konuşma birleştirme temelleri](../../../get-started-text-to-speech.md).

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename
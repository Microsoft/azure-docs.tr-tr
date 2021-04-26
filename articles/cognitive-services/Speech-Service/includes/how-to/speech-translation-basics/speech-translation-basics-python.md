---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bea61ac9a8c5700cd4f18cfb567b054de6b5613d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105396"
---
Konuşma hizmetinin temel özelliklerinden biri de insan konuşmanızı algılayabilir ve diğer dillere çevirebilir. Bu hızlı başlangıçta, uygulama ve ürünlarınızdaki konuşma SDK 'sını kullanarak yüksek kaliteli konuşma çevirisi gerçekleştirme hakkında bilgi edinebilirsiniz. Bu hızlı başlangıçta aşağıdakiler dahil konular ele alınmaktadır:

* Konuşmayı metne çevirme
* Konuşmayı birden çok hedef dile çevirme
* Doğrudan konuşma konuşmadan çeviri gerçekleştiriliyor

## <a name="skip-to-samples-on-github"></a>GitHub 'da örneklere atlayın

Örnek koda doğrudan atlamak istiyorsanız GitHub 'daki [Python hızlı başlangıç örneklerine](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir Azure hesabınız ve konuşma hizmeti aboneliğiniz olduğunu varsaymaktadır. Hesabınız ve aboneliğiniz yoksa [konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Herhangi bir şey yapabilmeniz için önce konuşma SDK 'sını yüklemeniz gerekir. Platformunuza bağlı olarak, _konuşma SDK 'Sı hakkında_ konusunun <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">konuşma SDK 'sını al</a> bölümünde yer alan yönergeleri izleyin.

## <a name="import-dependencies"></a>Bağımlılıkları içeri aktar

Bu makaledeki örnekleri çalıştırmak için `import` Python kod dosyasının en üstüne aşağıdaki deyimleri ekleyin.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Hassas veriler ve ortam değişkenleri

Bu makaledeki örnek kaynak kodu, konuşma kaynağı abonelik anahtarı ve bölgesi gibi hassas verileri depolamak için ortam değişkenlerine bağımlıdır. Python kod dosyası, ve gibi konak makineler ortam değişkenlerinden atanan iki değer içerir `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION` . Bu değişkenlerin her ikisi de genel kapsamdadır ve kod dosyasının işlev tanımı içinde erişilebilir hale getirir. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [ortam değişkenleri ve uygulama yapılandırması](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Kaynak dilini değiştir

Konuşma çevirisi 'nin yaygın bir görevi, giriş (veya kaynak) dilini belirtmektir. Giriş dilini Italyanca olarak nasıl değiştirebileceğinizi göz atalım. Kodunuzda, [`SpeechTranslationConfig`][config] özelliğine atayarak örnekle etkileşime geçin `speech_recognition_language` .

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

[`speech_recognition_language`][recognitionlang]Özelliği, bir dil yerel ayar dizesi bekliyor. Desteklenen [yerel ayarlar/diller](../../../language-support.md)listesindeki **yerel ayar** sütununda herhangi bir değer sağlayabilirsiniz.

## <a name="add-translation-language"></a>Çeviri dili Ekle

Konuşma çevirisi 'nin başka bir ortak görevi, hedef çeviri dillerini belirtmektir, ancak en az bir tane gereklidir ancak katları desteklenir. Aşağıdaki kod parçacığı, hem Fransızca hem de Almanca ' yı çeviri dili hedefi olarak ayarlar.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Her çağrısıyla [`add_target_language`][addlang] , yeni bir hedef çeviri dili belirtilir. Diğer bir deyişle, konuşma kaynak dilden tanındığında, her hedef çeviri, elde edilen çeviri işleminin bir parçası olarak kullanılabilir.

## <a name="initialize-a-translation-recognizer"></a>Çeviri tanıyıcısı başlatma

Bir oluşturduktan sonra [`SpeechTranslationConfig`][config] , bir sonraki adım bir ' ı başlatmaktır [`TranslationRecognizer`][recognizer] . Bir başlattığınızda [`TranslationRecognizer`][recognizer] , bunu geçirmeniz gerekir `translation_config` . Yapılandırma nesnesi, konuşma hizmeti 'nin isteğinizi doğrulamak için ihtiyaç duyduğu kimlik bilgilerini sağlar.

Cihazınızın varsayılan mikrofonunu kullanarak konuşmayı tanıyor olmanız halinde şöyle [`TranslationRecognizer`][recognizer] görünmelidir:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Ses giriş cihazını belirtmek isterseniz, oluşturmanız ve ' ı [`AudioConfig`][audioconfig] `audio_config` başlatırken parametresini sağlamanız gerekir [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Ses giriş cihazınız için CIHAZ kimliğini nasıl alabileceğinizi öğrenin](../../../how-to-select-audio-input-devices.md).

İlk `AudioConfig` olarak, nesnesine aşağıdaki gibi başvuracağız:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Mikrofon kullanmak yerine bir ses dosyası sağlamak istiyorsanız, yine de sağlamanız gerekir `audioConfig` . Ancak, [`AudioConfig`][audioconfig] ile çağırmak yerine bir oluşturduğunuzda, `use_default_microphone=True` ile çağrın `filename="path-to-file.wav"` ve `filename` parametresini sağlarsınız.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Konuşmayı çevirme

Konuşmayı çevirmek için, konuşma SDK 'Sı bir mikrofona veya ses dosyası girişine bağımlıdır. Konuşma tanıma, konuşma çevirisi öncesinde oluşur. Tüm nesneler başlatıldıktan sonra, Recognize-bir kez işlevini çağırın ve sonucu alın.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Konuşmayı metne dönüştürme hakkında daha fazla bilgi için bkz. [konuşma tanımanın temelleri](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Çevirileri sentezleştir

Başarılı bir konuşma tanıma ve çeviri işleminden sonra, sonuç bir Sözlükteki tüm çevirileri içerir. [`translations`][translations]Sözlük anahtarı hedef çeviri dilidir ve değer, çevrilmiş metindir. Tanınan konuşma çevrilebilir ve farklı bir dilde (konuşma konuşmaya) alınabilir.

### <a name="event-based-synthesis"></a>Olay tabanlı sensıs

`TranslationRecognizer`Nesne bir olay gösterir `Synthesizing` . Olay birkaç kez harekete geçirilir ve çeviri tanıma sonucundan birleştirilmiş sesi almak için bir mekanizma sağlar. Birden çok dile arıyorsanız bkz. [el ile birleştirme](#manual-synthesis). Bir oluşturma ve olay için olay işleyicisi sağlama yoluyla sensıs sesini belirtin [`voice_name`][voicename] `Synthesizing` , sesi alın. Aşağıdaki örnek, çevrilmiş sesi bir *. wav* dosyası olarak kaydeder.

> [!IMPORTANT]
> Olay tabanlı birleştirme yalnızca tek bir çeviri ile birlikte çalışarak birden çok hedef çeviri **dili eklemeyin.** Ayrıca, [`voice_name`][voicename] hedef çeviri diliyle aynı dil olmalıdır, örneğin, `"de"` ile eşleşebilmelidir `"de-DE-Hedda"` .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>El ile birleştirme

[`translations`][translations]Sözlük, çeviri metninin sesini sentezleştirmek için kullanılabilir. Her bir çeviride yineleme yapın ve çeviriyi sentezleştirme. Bir örnek oluştururken `SpeechSynthesizer` `SpeechConfig` nesnenin [`speech_synthesis_voice_name`][speechsynthesisvoicename] Özelliği istenen sesle ayarlanmış olması gerekir. Aşağıdaki örnek, beş dili dönüştürür ve her bir çeviri, karşılık gelen sinir dilinde bir ses dosyası ile birleştirilmiştir.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Konuşma birleştirme hakkında daha fazla bilgi için bkz. [konuşma birleştirme temelleri](../../../get-started-text-to-speech.md).

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name
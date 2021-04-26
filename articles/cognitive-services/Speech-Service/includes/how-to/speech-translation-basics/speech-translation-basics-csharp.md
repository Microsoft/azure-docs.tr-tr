---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 798db8e438a78fb818bd0af18f84c87c1f9ceb84
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105072"
---
Konuşma hizmetinin temel özelliklerinden biri de insan konuşmanızı algılayabilir ve diğer dillere çevirebilir. Bu hızlı başlangıçta, uygulama ve ürünlarınızdaki konuşma SDK 'sını kullanarak yüksek kaliteli konuşma çevirisi gerçekleştirme hakkında bilgi edinebilirsiniz. Bu hızlı başlangıçta aşağıdakiler dahil konular ele alınmaktadır:

* Konuşmayı metne çevirme
* Konuşmayı birden çok hedef dile çevirme
* Doğrudan konuşma konuşmadan çeviri gerçekleştiriliyor

## <a name="skip-to-samples-on-github"></a>GitHub 'da örneklere atlayın

Örnek koda doğrudan atlamak istiyorsanız GitHub 'daki [C# hızlı başlangıç örneklerine](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir Azure hesabınız ve konuşma hizmeti aboneliğiniz olduğunu varsaymaktadır. Hesabınız ve aboneliğiniz yoksa [konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Herhangi bir şey yapabilmeniz için önce konuşma SDK 'sını yüklemeniz gerekir. Platformunuza bağlı olarak, _konuşma SDK 'Sı hakkında_ konusunun <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">konuşma SDK 'sını al</a> bölümünde yer alan yönergeleri izleyin.

## <a name="import-dependencies"></a>Bağımlılıkları içeri aktar

Bu makaledeki örnekleri çalıştırmak için, `using` *program. cs* dosyasının en üstüne aşağıdaki deyimleri ekleyin.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Hassas veriler ve ortam değişkenleri

Bu makaledeki örnek kaynak kodu, konuşma kaynağı abonelik anahtarı ve bölgesi gibi hassas verileri depolamak için ortam değişkenlerine bağımlıdır. `Program`Sınıfı `static readonly string` , ve gibi konak makineler ortam değişkenlerinden atanan iki değer içerir `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION` . Bu alanların her ikisi de sınıf kapsamıdır ve sınıfın yöntem gövdelerinde erişilebilir hale getirir. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [ortam değişkenleri ve uygulama yapılandırması](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
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

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Kaynak dilini değiştir

Konuşma çevirisi 'nin yaygın bir görevi, giriş (veya kaynak) dilini belirtmektir. Giriş dilini Italyanca olarak nasıl değiştirebileceğinizi göz atalım. Kodunuzda, [`SpeechTranslationConfig`][config] özelliğine atayarak örnekle etkileşime geçin `SpeechRecognitionLanguage` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

[`SpeechRecognitionLanguage`][recognitionlang]Özelliği, bir dil yerel ayar dizesi bekliyor. Desteklenen [yerel ayarlar/diller](../../../language-support.md)listesindeki **yerel ayar** sütununda herhangi bir değer sağlayabilirsiniz.

## <a name="add-translation-language"></a>Çeviri dili Ekle

Konuşma çevirisi 'nin başka bir ortak görevi, hedef çeviri dillerini belirtmektir, ancak en az bir tane gereklidir ancak katları desteklenir. Aşağıdaki kod parçacığı, hem Fransızca hem de Almanca ' yı çeviri dili hedefi olarak ayarlar.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Her çağrısıyla [`AddTargetLanguage`][addlang] , yeni bir hedef çeviri dili belirtilir. Diğer bir deyişle, konuşma kaynak dilden tanındığında, her hedef çeviri, elde edilen çeviri işleminin bir parçası olarak kullanılabilir.

## <a name="initialize-a-translation-recognizer"></a>Çeviri tanıyıcısı başlatma

Bir oluşturduktan sonra [`SpeechTranslationConfig`][config] , bir sonraki adım bir ' ı başlatmaktır [`TranslationRecognizer`][recognizer] . Bir başlattığınızda [`TranslationRecognizer`][recognizer] , bunu geçirmeniz gerekir `translationConfig` . Yapılandırma nesnesi, konuşma hizmeti 'nin isteğinizi doğrulamak için ihtiyaç duyduğu kimlik bilgilerini sağlar.

Cihazınızın varsayılan mikrofonunu kullanarak konuşmayı tanıyor olmanız halinde şöyle [`TranslationRecognizer`][recognizer] görünmelidir:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Ses giriş cihazını belirtmek isterseniz, oluşturmanız ve ' ı [`AudioConfig`][audioconfig] `audioConfig` başlatırken parametresini sağlamanız gerekir [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Ses giriş cihazınız için CIHAZ kimliğini nasıl alabileceğinizi öğrenin](../../../how-to-select-audio-input-devices.md).

İlk `AudioConfig` olarak, nesnesine aşağıdaki gibi başvuracağız:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Mikrofon kullanmak yerine bir ses dosyası sağlamak istiyorsanız, yine de sağlamanız gerekir `audioConfig` . Ancak, [`AudioConfig`][audioconfig] öğesini çağırmak yerine bir oluşturduğunuzda, `FromDefaultMicrophoneInput` `FromWavFileInput` parametresini çağırır ve geçireceğiz `filename` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Konuşmayı çevirme

Konuşmayı çevirmek için, konuşma SDK 'Sı bir mikrofona veya ses dosyası girişine bağımlıdır. Konuşma tanıma, konuşma çevirisi öncesinde oluşur. Tüm nesneler başlatıldıktan sonra, Recognize-bir kez işlevini çağırın ve sonucu alın.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Konuşmayı metne dönüştürme hakkında daha fazla bilgi için bkz. [konuşma tanımanın temelleri](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Çevirileri sentezleştir

Başarılı bir konuşma tanıma ve çeviri işleminden sonra, sonuç bir Sözlükteki tüm çevirileri içerir. [`Translations`][translations]Sözlük anahtarı hedef çeviri dilidir ve değer, çevrilmiş metindir. Tanınan konuşma çevrilebilir ve farklı bir dilde (konuşma konuşmaya) alınabilir.

### <a name="event-based-synthesis"></a>Olay tabanlı sensıs

`TranslationRecognizer`Nesne bir olay gösterir `Synthesizing` . Olay birkaç kez harekete geçirilir ve çeviri tanıma sonucundan birleştirilmiş sesi almak için bir mekanizma sağlar. Birden çok dile arıyorsanız bkz. [el ile birleştirme](#manual-synthesis). Bir oluşturma ve olay için olay işleyicisi sağlama yoluyla sensıs sesini belirtin [`VoiceName`][voicename] `Synthesizing` , sesi alın. Aşağıdaki örnek, çevrilmiş sesi bir *. wav* dosyası olarak kaydeder.

> [!IMPORTANT]
> Olay tabanlı birleştirme yalnızca tek bir çeviri ile birlikte çalışarak birden çok hedef çeviri **dili eklemeyin.** Ayrıca, [`VoiceName`][voicename] hedef çeviri diliyle aynı dil olmalıdır, örneğin, `"de"` ile eşleşebilmelidir `"de-DE-Hedda"` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>El ile birleştirme

[`Translations`][translations]Sözlük, çeviri metninin sesini sentezleştirmek için kullanılabilir. Her bir çeviride yineleme yapın ve çeviriyi sentezleştirme. Bir örnek oluştururken `SpeechSynthesizer` `SpeechConfig` nesnenin [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] Özelliği istenen sesle ayarlanmış olması gerekir. Aşağıdaki örnek, beş dili dönüştürür ve her bir çeviri, karşılık gelen sinir dilinde bir ses dosyası ile birleştirilmiştir.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Konuşma birleştirme hakkında daha fazla bilgi için bkz. [konuşma birleştirme temelleri](../../../get-started-text-to-speech.md).

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename
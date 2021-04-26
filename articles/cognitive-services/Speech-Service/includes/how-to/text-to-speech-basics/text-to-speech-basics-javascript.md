---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2021
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 4d228388d951314b03ecd950052f76a20b6e4166
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108903"
---
Bu hızlı başlangıçta, konuşma SDK 'sını kullanarak metinden konuşmaya senşlerini kullanmaya yönelik yaygın tasarım düzenlerini öğrenirsiniz. Temel yapılandırma ve birleştirme işlemleri gerçekleştirerek başlar ve aşağıdakiler de dahil olmak üzere özel uygulama geliştirme için daha gelişmiş örneklere geçin:

* Bellek içi akışlar olarak yanıt alma
* Çıkış örneği hızını ve bit hızını özelleştirme
* SSML kullanarak senssıs istekleri gönderme (konuşma birleştirme biçimlendirme dili)
* Sinir seslerini kullanma

## <a name="skip-to-samples-on-github"></a>GitHub 'da örneklere atlayın

Örnek koda doğrudan atlamak istiyorsanız GitHub 'da [JavaScript hızlı başlangıç örneklerine](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) bakın.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede bir Azure hesabınız ve konuşma hizmeti kaynağınız olduğunu varsaymaktadır. Hesabınız ve kaynağınız yoksa [konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free).

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
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

Hakkında daha fazla bilgi için `import` bkz. <a href="https://javascript.info/import-export" target="_blank">dışa ve içeri aktarma </a>.

# <a name="require"></a>[gerektirir](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Hakkında daha fazla bilgi için `require` bkz. <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">ne gerektiriyor? </a>.

---


## <a name="create-a-speech-configuration"></a>Konuşma yapılandırması oluşturma

Konuşma SDK 'sını kullanarak konuşma hizmetini çağırmak için bir oluşturmanız gerekir [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) . Bu sınıf, anahtarınız ve ilgili bölge, uç nokta, ana bilgisayar veya yetkilendirme belirteciniz gibi, kaynağınız hakkındaki bilgileri içerir.

> [!NOTE]
> Konuşma tanıma, konuşma birleştirme, çeviri veya amaç tanıma işlemlerini gerçekleştirmekten bağımsız olarak her zaman bir yapılandırma oluşturacaksınız.

Şunları başlatabilmeniz için birkaç yol vardır [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) :

* Kaynak ile: bir anahtarı ve ilişkili bölgeyi geçirin.
* Uç nokta ile: bir konuşma hizmeti uç noktasında geçirin. Anahtar veya yetkilendirme belirteci isteğe bağlıdır.
* Bir ana bilgisayar ile: bir konak adresini geçirin. Anahtar veya yetkilendirme belirteci isteğe bağlıdır.
* Yetkilendirme belirteci ile: bir yetkilendirme belirtecini ve ilişkili bölgeyi geçirin.

Bu örnekte, bir [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) kaynak anahtarı ve bölgesi kullanarak bir oluşturabilirsiniz. [Konuşma hizmetini ücretsiz deneyin](../../../overview.md#try-the-speech-service-for-free)bölümündeki adımları izleyerek bu kimlik bilgilerini alın. Ayrıca, bu makalenin geri kalanı için kullanabileceğiniz, farklı özelleştirmeler için değiştirdiğiniz bazı temel ortak kod oluşturabilirsiniz.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Konuşmayı bir dosyaya sentezleştirme

Sonra, [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) metin okuma dönüştürmeleri yürüten ve hoparlörlerde, dosyalarda veya diğer çıkış akışlarına giden çıktıları yürüten bir nesne oluşturun. , [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer) [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) Önceki adımda oluşturulan nesneyi params olarak kabul eder ve [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) Çıkış sonuçlarının nasıl işleneceğini belirten bir nesnedir.

Başlamak için, `AudioConfig` `.wav` statik işlevi kullanarak çıktıyı bir dosyaya otomatik olarak yazmak üzere bir oluşturun `fromAudioFileOutput()` .

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Ardından, `SpeechSynthesizer` `speechConfig` nesne ve nesneyi params olarak geçirerek bir örnek oluşturun `audioConfig` . Daha sonra, konuşma birleştirmenin yürütülmesi ve bir dosyaya yazılması, `speakTextAsync()` bir metin dizesiyle çalışırken basittir. Sonucun doğru bir şekilde çalışması için sonuç geri çağırması, aslında çağırmak için gereken harika bir yerdir `synthesizer.close()` .

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            synthesizer.close();
            if (result) {
                // return result as stream
                return fs.createReadStream("path-to-file.wav");
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Programı çalıştırın ve belirttiğiniz konuma bir sentezlenmiş `.wav` dosya yazılır. Bu, en temel kullanımın iyi bir örneğidir, ancak sonraki bir deyişle, özel senaryolarla çalışmaya yönelik bellek içi akış olarak çıktıyı özelleştirmeye ve çıkış yanıtını işlemeye baktığınızın ardından.

## <a name="synthesize-to-speaker-output"></a>Konuşmacı çıktısına sentezleştirme

Bazı durumlarda, doğrudan sentezlenmiş konuşmayı doğrudan bir konuşmacının çıktısını almak isteyebilirsiniz. Bunu yapmak için `AudioConfig` static işlevini kullanarak örneğini oluşturun `fromDefaultSpeakerOutput()` . Bu, geçerli etkin çıkış cihazına çıkış verir.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                synthesizer.close();
                return result.audioData;
            }
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Bellek içi akış olarak sonuç alın

Konuşma uygulaması geliştirmede birçok senaryo için, büyük olasılıkla bir dosyaya doğrudan yazmak yerine, sonuçta elde edilen ses verilerinin bellek içi akış olarak olması gerekir. Bu, aşağıdakiler dahil olmak üzere özel davranış oluşturmanıza izin verir:

* Özel aşağı akış Hizmetleri için arama yapabilecek bir akış olarak ortaya çıkan bayt dizisinin soyut olması.
* Sonucu diğer API 'leri veya hizmetleriyle tümleştirin.
* Ses verilerini değiştirin, özel üstbilgiler yazın `.wav` vb.

Önceki örnekte bu değişikliği yapmak basittir. İlk `AudioConfig` olarak, daha fazla denetim için çıkış davranışını bu noktadan el ile yöneteceği için bloğu kaldırın. Sonra `undefined` `AudioConfig` Oluşturucu içinde öğesine geçirin `SpeechSynthesizer` .

> [!NOTE]
> `undefined` `AudioConfig` Yukarıdaki konuşmacı çıktısı örneğinde olduğu gibi değil, için geçirme, geçerli etkin çıkış cihazında varsayılan olarak sesi oynamaz.

Bu kez, sonucu bir [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult) değişkene kaydedersiniz. `SpeechSynthesisResult.audioData`Özelliği `ArrayBuffer` , varsayılan tarayıcı akış türü olan çıkış verilerini döndürür. Sunucu kodu için arrayBuffer 'ı bir arabellek akışına dönüştürün.

Aşağıdaki kod, istemci tarafı kodu için geçerlidir.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            synthesizer.close();
            return result.audioData;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Buradan, sonuçta elde edilen nesneyi kullanarak özel davranışları uygulayabilirsiniz `ArrayBuffer` . ArrayBuffer, bir tarayıcıda alacak ve bu biçimden oynatacak ortak bir türdür.

Sunucu tabanlı herhangi bir kod için, bir ArrayBuffer yerine verilerle birlikte çalışmanız gerekiyorsa, nesneyi bir akışa dönüştürmeniz gerekir.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            const { audioData } = result;

            synthesizer.close();

            // convert arrayBuffer to stream
            // return stream
            const bufferStream = new PassThrough();
            bufferStream.end(Buffer.from(audioData));
            return bufferStream;
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="customize-audio-format"></a>Ses biçimini Özelleştir

Aşağıdaki bölümde aşağıdakiler dahil olmak üzere ses çıkış özniteliklerinin nasıl özelleştirileceği gösterilmektedir:

* Ses dosyası türü
* Örnek hız
* Bit derinliği

Ses biçimini değiştirmek için `speechSynthesisOutputFormat` nesnesi üzerinde özelliğini kullanın `SpeechConfig` . Bu özellik `enum` [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) , çıkış biçimini seçmek için kullandığınız türünde bir tür bekliyor. Kullanılabilir [Ses biçimlerinin listesi](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat) için başvuru belgelerine bakın.

Gereksinimlerinize bağlı olarak farklı dosya türleri için çeşitli seçenekler vardır. Tanımına göre, gibi ham biçimlerin `Raw24Khz16BitMonoPcm` Ses üst bilgilerini içermediğini unutmayın. Ham biçimleri yalnızca aşağı akış uygulamanızın bir ham Bitstream kodunu çözemediğini veya bit derinliğine, örnek hızına, kanal sayısına ve vb. göre el ile üstbilgileri oluşturmayı planlıyorsanız kullanın.

Bu örnekte, `Riff24Khz16BitMonoPcm` nesnesi üzerinde ayarını yaparak Yüksek uygunluğa sahip bir biçim belirtirsiniz `speechSynthesisOutputFormat` `SpeechConfig` . Önceki bölümdeki örneğe benzer şekilde, ses `ArrayBuffer` verilerini alın ve onunla etkileşime geçin.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Programınızı yeniden çalıştırmak, `.wav` belirtilen yola bir dosya yazacak.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML 'yi kullanarak konuşma özelliklerini özelleştirme

Konuşma sen, biçimlendirme dili (SSML), isteklerinizi bir XML şemasından göndererek, metin okuma çıktılarından oluşan sıklık, Söyleniş, konuşma oranı, hacim ve daha fazlasını ayarlamanıza olanak sağlar. Bu bölümde, sesi değiştirme örneği gösterilmektedir, ancak daha ayrıntılı bir kılavuz için bkz. [SSML nasıl yapılır makalesi](../../../speech-synthesis-markup.md). 

Özelleştirme için SSML 'yi kullanmaya başlamak için, sese geçiş yapan basit bir değişiklik yaparsınız.
İlk olarak, bu örnekte, kök proje dizininizde SSML yapılandırması için yeni bir XML dosyası oluşturun `ssml.xml` . Kök öğesi her zaman `<speak>` ve metni bir öğe içinde sarmalama, `<voice>` param kullanarak sesi değiştirmenize olanak sağlar `name` . Desteklenen **sinir** sesin [tam listesine](../../../language-support.md#neural-voices) bakın. 

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Ardından, XML dosyanıza başvurmak için konuşma sensıs isteğini değiştirmeniz gerekir. İstek genellikle aynıdır, ancak işlevini kullanmak yerine `speakTextAsync()` kullanın `speakSsmlAsync()` . Bu işlev bir XML dizesi bekliyor, bu nedenle ilk olarak bir XML dosyası yüklemek için bir işlev oluşturun ve bunu bir dize olarak döndürün.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Hakkında daha fazla bilgi için `readFileSync` bkz. <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js dosya sistemi</a>. Buradan, sonuç nesnesi önceki örneklerle tamamen aynıdır.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

> [!NOTE]
> SSML kullanmadan sesi değiştirmek için, özelliğini `SpeechConfig` kullanarak `SpeechConfig.speechSynthesisVoiceName = "en-US-AriaNeural";`

## <a name="get-facial-pose-events"></a>Yüz poz olaylarını al

Konuşma, yüz ifadelerinin animasyonunu sağlamak için iyi bir yol olabilir.
Genellikle [visemes](../../../how-to-speech-synthesis-viseme.md) , belirli bir phoneme oluşturma sırasında LIP 'ler, Jaw ve dil sistemi gibi gözlemlenen konuşmayla ilgili önemli pozları temsil etmek için kullanılır.
Konuşma SDK 'sında viseme olayına abone olabilirsiniz.
Daha sonra, konuşma sesi oynatılırken bir karakter yüzünüzü hareketlendirmek için viseme olaylarını uygulayabilirsiniz.
[Viseme olaylarını nasıl alabileceğinizi](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)öğrenin.

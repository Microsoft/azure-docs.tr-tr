---
title: "Hızlı Başlangıç: Metin okuma, Node. js-konuşma hizmeti 'ni dönüştürme"
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Node. js ve metin okuma REST API kullanarak metin okumayı nasıl dönüştürebileceğinizi öğreneceksiniz. Bu kılavuzda yer örnek metni konuşma sentezi işaretleme dili (SSML'yi) olarak yapılandırılmıştır. Bu, ses ve konuşma yanıtın dili seçmenize olanak sağlar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 72be99ec666bb9e04ffca6e14ab4fcafa889ae68
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553941"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Hızlı Başlangıç: Node. js kullanarak metin konuşmaya dönüştürme

Bu hızlı başlangıçta, Node. js ve metin okuma REST API kullanarak metin okumayı nasıl dönüştürebileceğinizi öğreneceksiniz. Bu kılavuzdaki istek gövdesi olarak yapılandırılmış [konuşma sentezi işaretleme dili (SSML'yi)](speech-synthesis-markup.md), ses ve yanıtın dili seçmenize olanak tanıyan.

Bu hızlı başlangıç, bir konuşma Hizmetleri kaynağına sahip bir Azure bilişsel [Hizmetler hesabı](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) gerektirir. Bir hesabınız yoksa, abonelik anahtarı almak için [ücretsiz deneme sürümünü](get-started.md) kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç şunları gerektirir:

* [Node 8.12.x veya üzeri](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), veya en sevdiğiniz metin düzenleyiciyi
* Konuşma Hizmetleri için bir Azure abonelik anahtarı. [Ücretsiz olarak bir tane alın!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Proje oluşturma ve bağımlılıklar gerektirme

En sevdiğiniz IDE veya düzenleyiciyi kullanarak yeni bir Node. js projesi oluşturun. Ardından bu kod parçacığını projenizde `tts.js` adlı bir dosyaya kopyalayın.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Bu modülleri kullanmadıysanız, programınızı çalıştırmadan önce bunları yüklemeniz gerekir. Bu paketleri yüklemek için şunu çalıştırın: `npm install request request-promise xmlbuilder readline-sync`.

## <a name="get-an-access-token"></a>Bir erişim belirteci alma

Metin okuma REST API, kimlik doğrulaması için bir erişim belirteci gerektirir. Erişim belirteci almak için bir exchange gereklidir. Bu işlev, `issueToken` uç noktasını kullanarak bir erişim belirteci için konuşma Hizmetleri abonelik anahtarınızı değiştirir.

Bu örnek, konuşma Hizmetleri aboneliğinizin Batı ABD bölgesinde olduğunu varsayar. Farklı bir bölgeye kullanıyorsanız, değerini güncelleştirin `uri`. Tam bir listesi için bkz [bölgeleri](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Bu kodu projenize kopyalayın:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Kimlik doğrulaması hakkında daha fazla bilgi için bkz. [bir erişim belirteciyle kimlik doğrulama](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Sonraki bölümde, metin okuma API 'sini çağırmak ve sentezlenmiş konuşma yanıtını kaydetmek için işlevi oluşturacağız.

## <a name="make-a-request-and-save-the-response"></a>Bir istekte bulunmak ve yanıt Kaydet

Burada, metin okuma API 'sine yönelik isteği derleyeceksiniz ve konuşma yanıtını kaydetmelisiniz. Bu örnek, Batı ABD uç nokta kullanmakta olduğunuz varsayılır. Kaynağınız için farklı bir bölgede kayıtlı değilse, güncelleştirdiğinizden emin olun `uri`. Daha fazla bilgi için bkz. [konuşma Hizmetleri bölgeleri](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ardından, istek için gerekli üst bilgileri eklemeniz gerekir. Güncelleştirdiğinizden emin olun `User-Agent` kaynak (Azure Portalı'nda bulunur) ve küme adıyla `X-Microsoft-OutputFormat` tercih edilen ses çıkış için. Çıkış biçimleri tam bir listesi için bkz. [ses çıkarır](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Ardından, istek gövdesi konuşma sentezi işaretleme dili (SSML'yi) kullanarak oluşturun. Bu örnek, yapısını tanımlar ve kullandığı `text` daha önce oluşturduğunuz giriş.

>[!NOTE]
> Bu örnekte `JessaRUS` ses tipi. Ses/dillerde Microsoft tam bir listesi sağlanmaktadır için bkz: [dil desteği](language-support.md).
> Markanız için benzersiz, tanınan bir ses oluşturmak istiyorsanız bkz [özel ses tipi oluşturma](how-to-customize-voice-font.md).

Son olarak, hizmete istek yapacaksınız. İstek başarılı olursa ve 200 durum kodu döndürülürse, konuşma yanıtı olarak `TTSOutput.wav`yazılır.

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Hepsini bir araya getirin

Neredeyse bitti. Son adım zaman uyumsuz bir işlev oluşturmaktır. Bu işlev, bir ortam değişkeninden abonelik anahtarınızı okur, metin ister, belirteç alır, isteğin tamamlanmasını bekler, sonra metni konuşmaya dönüştürür ve sesi bir. WAV olarak kaydeder.

Ortam değişkenlerini tanımıyorsanız veya bir dize olarak abonelik anahtarınız ile test yapmayı tercih ediyorsanız, bir dize olarak abonelik anahtarınızla değiştirin `process.env.SPEECH_SERVICE_KEY` .

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

İşte bu kadar metin okuma örnek uygulamanızı çalıştırmak hazır olursunuz. Komut satırını (veya terminal oturumu), proje dizinine gidin ve çalıştırın:

```console
node tts.js
```

İstendiğinde, ne olursa olsun metin okuma dönüştürmek istediğiniz içinde yazın. Başarılı olursa, konuşma dosyanın proje klasöründe bulunur. Yürütün, sık kullanılan media player kullanarak.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Abonelik anahtarları gibi örnek uygulamanızın kaynak kodundan olan gizli bilgilerin kaldırdığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da Node. js örneklerini keşfet](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Okuma API başvurusu](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Özel ses tipi olarak oluşturma](how-to-customize-voice-font.md)
* [Özel ses oluşturma kayıt ses örnekleri](record-custom-voice-samples.md)

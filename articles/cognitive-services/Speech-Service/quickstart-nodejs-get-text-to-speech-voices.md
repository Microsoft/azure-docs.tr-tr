---
title: 'Hızlı Başlangıç: Metin okuma seslerini, Node. js-konuşma hizmetini listeleme'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Node. js kullanarak bir bölgenin/uç noktanın standart ve sinir sesinden tam listesini almayı öğreneceksiniz. Liste JSON olarak döndürülür ve ses kullanılabilirliği bölgeye göre değişir.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1044519110d8b0ae7b5a50860c8116d73b6b70bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559385"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Hızlı Başlangıç: Node. js kullanarak metin okuma sesin listesini alın

Bu hızlı başlangıçta, Node. js kullanarak bir bölgenin/uç noktanın standart ve sinir sesinden tam listesini almayı öğreneceksiniz. Liste JSON olarak döndürülür ve ses kullanılabilirliği bölgeye göre değişir. Desteklenen bölgelerin listesi için bkz. [bölgeler](regions.md).

Bu hızlı başlangıç, bir konuşma Hizmetleri kaynağına sahip bir Azure bilişsel [Hizmetler hesabı](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) gerektirir. Bir hesabınız yoksa, abonelik anahtarı almak için [ücretsiz deneme sürümünü](get-started.md) kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç şunları gerektirir:

* [Node 8.12.x veya üzeri](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), veya en sevdiğiniz metin düzenleyiciyi
* Konuşma Hizmetleri için bir Azure abonelik anahtarı. [Ücretsiz olarak bir tane alın!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Proje oluşturma ve bağımlılıklar gerektirme

En sevdiğiniz IDE veya düzenleyiciyi kullanarak yeni bir Node. js projesi oluşturun. Ardından bu kod parçacığını projenizde `get-voices.js` adlı bir dosyaya kopyalayın.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Bu modülleri kullanmadıysanız, programınızı çalıştırmadan önce bunları yüklemeniz gerekir. Bu paketleri yüklemek için şunu çalıştırın: `npm install request request-promise`.

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

Sonraki bölümde, seslerin listesini almak ve JSON çıkışını dosyaya kaydetmek için işlevi oluşturacağız.

## <a name="make-a-request-and-save-the-response"></a>Bir istekte bulunmak ve yanıt Kaydet

Burada, isteği derleyip döndürülen seslerin listesini kaydedeceklürsünüz. Bu örnek, Batı ABD uç nokta kullanmakta olduğunuz varsayılır. Kaynağınız için farklı bir bölgede kayıtlı değilse, güncelleştirdiğinizden emin olun `uri`. Daha fazla bilgi için bkz. [konuşma Hizmetleri bölgeleri](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Sonra, istek için gerekli üstbilgileri ekleyin. Son olarak, hizmete istek yapacaksınız. İstek başarılı olursa ve 200 durum kodu döndürülürse, yanıt dosyaya yazılır.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Hepsini bir araya getirin

Neredeyse bitti. Son adım zaman uyumsuz bir işlev oluşturmaktır. Bu işlev, bir ortam değişkeninden abonelik anahtarınızı okur, belirteç alır, isteğin tamamlanmasını bekler, ardından JSON yanıtını dosyaya yazar.

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
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

Bu, örnek uygulamanızı çalıştırmaya hazırsınız. Komut satırını (veya terminal oturumu), proje dizinine gidin ve çalıştırın:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Abonelik anahtarları gibi örnek uygulamanızın kaynak kodundan olan gizli bilgilerin kaldırdığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da Node. js örneklerini keşfet](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Okuma API başvurusu](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Özel ses tipi olarak oluşturma](how-to-customize-voice-font.md)
* [Özel ses oluşturma kayıt ses örnekleri](record-custom-voice-samples.md)

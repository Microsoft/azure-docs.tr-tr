---
title: 'Hızlı Başlangıç: Metin okuma seslerini, Python-konuşma hizmetini listeleme'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Python kullanarak bir bölgenin/uç noktanın standart ve sinir sesinden tam listesini almayı öğreneceksiniz. Liste JSON olarak döndürülür ve ses kullanılabilirliği bölgeye göre değişir.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: ac96c3ce3924b8b2fe834e2b350e95ce23c52e1f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559351"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Hızlı Başlangıç: Python kullanarak metin okuma sesin listesini alın

Bu hızlı başlangıçta, Python kullanarak bir bölgenin/uç noktanın standart ve sinir sesinden tam listesini almayı öğreneceksiniz. Liste JSON olarak döndürülür ve ses kullanılabilirliği bölgeye göre değişir. Desteklenen bölgelerin listesi için bkz. [bölgeler](regions.md).

Bu hızlı başlangıç, bir konuşma Hizmetleri kaynağına sahip bir Azure bilişsel [Hizmetler hesabı](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) gerektirir. Bir hesabınız yoksa, abonelik anahtarı almak için [ücretsiz deneme sürümünü](get-started.md) kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç şunları gerektirir:

* Python 2.7.x veya 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), veya en sevdiğiniz metin düzenleyiciyi
* Konuşma Hizmetleri için bir Azure abonelik anahtarı

## <a name="create-a-project-and-import-required-modules"></a>Bir proje oluşturun ve gerekli modülleri içeri aktarın

Favori IDE ortamınızda veya düzenleyicide yeni bir Python projesi oluşturun. Ardından bu kod parçacığını projenizde `get-voices.py` adlı bir dosyaya kopyalayın.

```python
import requests
```

> [!NOTE]
> Bu modülleri kullanmadıysanız, programınızı çalıştırmadan önce bunları yüklemeniz gerekir. Bu paketleri yüklemek için şunu çalıştırın: `pip install requests`.

İstekler, metin okuma hizmetine HTTP istekleri için kullanılır.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Abonelik anahtarı ve bir komut istemi için TTS oluşturma

Sonraki birkaç bölümde yetkilendirmeyi ele alırken, metin okuma API'si çağırmayı ve yanıt doğrulamak için yöntem oluşturacaksınız. Bir sınıf oluşturarak başlayalım. Biz bizim belirteç değişimi ve metin okuma API'si Çağırma yöntemlerini nerede giriyorum budur.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` Azure portalından, benzersiz anahtar.

## <a name="get-an-access-token"></a>Bir erişim belirteci alma

Bu uç nokta, kimlik doğrulaması için bir erişim belirteci gerektirir. Erişim belirteci almak için bir exchange gereklidir. Bu örnekte, `issueToken` uç noktasını kullanarak bir erişim belirteci için konuşma Hizmetleri abonelik anahtarınızı alışverişi yapılır.

Bu örnek, konuşma Hizmetleri aboneliğinizin Batı ABD bölgesinde olduğunu varsayar. Farklı bir bölgeye kullanıyorsanız, değerini güncelleştirin `fetch_token_url`. Tam bir listesi için bkz [bölgeleri](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Bu kodu kopyalayın `GetVoices` sınıfı:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Kimlik doğrulaması hakkında daha fazla bilgi için bkz. [bir erişim belirteciyle kimlik doğrulama](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Bir istekte bulunmak ve yanıt Kaydet

Burada, isteği derleyip döndürülen seslerin listesini kaydedeceklürsünüz. İlk olarak, ayarlanacak ihtiyacınız `base_url` ve `path`. Bu örnek, Batı ABD uç nokta kullanmakta olduğunuz varsayılır. Kaynağınız için farklı bir bölgede kayıtlı değilse, güncelleştirdiğinizden emin olun `base_url`. Daha fazla bilgi için bkz. [konuşma Hizmetleri bölgeleri](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Sonra, istek için gerekli üstbilgileri ekleyin. Son olarak, hizmete istek yapacaksınız. İstek başarılı olursa ve 200 durum kodu döndürülürse, yanıt dosyaya yazılır.

Bu kodu kopyalayın `GetVoices` sınıfı:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Hepsini bir araya getirin

Neredeyse bitti. Son adım, sınıfının örneği ve işlevlerinizin çağrı sağlamaktır.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

Bu, örneği çalıştırmaya hazırsınız. Komut satırını (veya terminal oturumu), proje dizinine gidin ve çalıştırın:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Abonelik anahtarları gibi örnek uygulamanızın kaynak kodundan olan gizli bilgilerin kaldırdığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da Python örneklerini keşfet](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Okuma API başvurusu](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Özel ses tipi olarak oluşturma](how-to-customize-voice-font.md)
* [Özel ses oluşturma kayıt ses örnekleri](record-custom-voice-samples.md)

---
title: "Hızlı başlangıç: metin okuma, Python-konuşma hizmeti 'ni dönüştürme"
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, Python ve metin okuma REST API kullanarak metin okumayı nasıl dönüştürebileceğinizi öğreneceksiniz. Bu kılavuza eklenen örnek metin, konuşma birleştirme biçimlendirme dili (SSML) olarak yapılandırılmıştır. Bu, konuşma yanıtının ses ve dilini seçmenizi sağlar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a66835d605b9005b8f94eb79a3c266f735f0a3b6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467201"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Hızlı başlangıç: Python kullanarak metin okumayı dönüştürme

Bu hızlı başlangıçta, Python ve metin okuma REST API kullanarak metin okumayı nasıl dönüştürebileceğinizi öğreneceksiniz. Bu kılavuzdaki istek gövdesi [konuşma sen, biçimlendirme dili (SSML)](speech-synthesis-markup.md)olarak yapılandırılmıştır ve bu da yanıtın ses ve dilini seçmenizi sağlar.

Bu hızlı başlangıç, bir konuşma Hizmetleri kaynağına sahip bir Azure bilişsel [Hizmetler hesabı](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) gerektirir. Bir hesabınız yoksa, abonelik anahtarı almak için [ücretsiz deneme sürümünü](get-started.md) kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç şunları gerektirir:

* Python 2.7.x veya 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)veya sık kullandığınız metin düzenleyiciniz
* Konuşma Hizmetleri için bir Azure abonelik anahtarı

## <a name="create-a-project-and-import-required-modules"></a>Bir proje oluşturun ve gerekli modülleri içeri aktarın

Favori IDE ortamınızda veya düzenleyicide yeni bir Python projesi oluşturun. Ardından bu kod parçacığını projenizde `tts.py` adlı bir dosyaya kopyalayın.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Bu modülleri kullanmadıysanız, programınızı çalıştırmadan önce bunları yüklemeniz gerekir. Bu paketleri yüklemek için şunu çalıştırın: `pip install requests`.

Bu modüller bir dosyaya konuşma yanıtını bir zaman damgasıyla yazmak, HTTP isteğini oluşturmak ve metin okuma API 'sini çağırmak için kullanılır.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Abonelik anahtarını ayarlama ve TTS için bir istem oluşturma

Sonraki birkaç bölümde, yetkilendirmeyi işlemek, metinden konuşmaya tanıma API 'sini çağırmak ve yanıtı doğrulamak için yöntemler oluşturacaksınız. Bu örneğin Python 2.7. x ve 3. x ile çalışabilmesine imkan tanıyan bazı kodlar ekleyerek başlayalım.

```python
try:
    input = raw_input
except NameError:
    pass
```

Daha sonra bir sınıf oluşturalım. Bu, belirteç değişimi için yöntemlerimizi koyacağımız ve metinden konuşmaya API 'sini çağıran yerdir.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key`, Azure portal benzersiz anahtarınızdan farklıdır. `tts` kullanıcıdan konuşmaya dönüştürülecek metni girmesini ister. Bu giriş bir dize sabit değeri olduğundan karakterlerin kaçışılması gerekmez. Son olarak, `timestr` dosyanızı adlandırmak için kullanacağımız geçerli saati alır.

## <a name="get-an-access-token"></a>Bir erişim belirteci alma

Metin okuma REST API kimlik doğrulaması için bir erişim belirteci gerektirir. Erişim belirteci almak için bir Exchange gereklidir. Bu örnek, `issueToken` uç noktasını kullanarak bir erişim belirteci için konuşma Hizmetleri abonelik anahtarınızı de değiş tokuş eder.

Bu örnek, konuşma Hizmetleri aboneliğinizin Batı ABD bölgesinde olduğunu varsayar. Farklı bir bölge kullanıyorsanız, `fetch_token_url`değerini güncelleştirin. Tam liste için bkz. [bölgeler](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Bu kodu `TextToSpeech` sınıfa kopyalayın:

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

## <a name="make-a-request-and-save-the-response"></a>İstek yapın ve yanıtı kaydedin

Burada, isteği derleyip konuşma yanıtını kaydedecekeceksiniz. İlk olarak, `base_url` ve `path`ayarlamanız gerekir. Bu örnek Batı ABD uç noktasını kullandığınızı varsayar. Kaynağınız farklı bir bölgeye kayıtlıysa, `base_url`güncelleştirdiğinizden emin olun. Daha fazla bilgi için bkz. [konuşma Hizmetleri bölgeleri](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Sonra, istek için gerekli üst bilgileri eklemeniz gerekir. `User-Agent` kaynak adı ile güncelleştirdiğinizden emin olun (Azure portal içinde bulunur) ve `X-Microsoft-OutputFormat` tercih ettiğiniz ses çıktısına ayarlayın. Çıkış biçimlerinin tam listesi için bkz. [Ses çıkışları](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Sonra, konuşma birleştirme biçimlendirme dili (SSML) kullanarak istek gövdesini oluşturun. Bu örnek yapıyı tanımlar ve daha önce oluşturduğunuz `tts` girişi kullanır.

>[!NOTE]
> Bu örnek `Guy24KRUS` ses yazı tipini kullanır. Microsoft tarafından sunulan seslerin/dillerin tüm listesi için bkz. [dil desteği](language-support.md).
> Markanız için benzersiz ve tanınabilir bir ses oluşturmaya ilgileniyorsanız, bkz. [özel ses yazı tipleri oluşturma](how-to-customize-voice-font.md).

Son olarak, hizmet için bir istek yaparsınız. İstek başarılı olursa ve 200 durum kodu döndürülürse, konuşma yanıtı bir zaman damgası dosyasına yazılır.

Bu kodu `TextToSpeech` sınıfa kopyalayın:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Hepsini bir araya getirin

Neredeyse bitti. Son adım, sınıfınızın örneğini oluşturmak ve işlevlerinizi çağırmak için kullanılır.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

Bu, metinden konuşmaya örnek uygulamanızı çalıştırmaya hazırsınız. Komut satırından (veya terminal oturumu), proje dizininize gidin ve şunu çalıştırın:

```console
python tts.py
```

İstendiğinde, metinden konuşmaya dönüştürmek istediğiniz her şeyi yazın. Başarılı olursa, konuşma dosyası proje klasörünüzde bulunur. En sevdiğiniz Medya yürütücünüzü kullanarak yürütün.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Örnek uygulamanızın kaynak kodundaki tüm gizli bilgileri abonelik anahtarları gibi kaldırdığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da Python örneklerini keşfet](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Ayrıca bkz.

* [Metin Okuma API başvurusu](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Metni konuşmaya dönüştürmek için Python ve konuşma SDK 'sını kullanma](quickstarts/speech-to-text-from-microphone.md)
* [Özel ses yazı tipleri oluşturma](how-to-customize-voice-font.md)
* [Özel bir ses oluşturmak için ses örneklerini kaydetme](record-custom-voice-samples.md)

---
title: 'Hızlı başlangıç: ses dosyasına konuşma Sentezleştirme, Python-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: d6f6519ea5df630a914243046e74c315b4bd7db9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503363"
---
## <a name="prerequisites"></a>Önkoşullar

* Konuşma Hizmetleri için bir Azure abonelik anahtarı. [Ücretsiz bir tane alın](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3,5 veya üzeri](https://www.python.org/downloads/).
* Python konuşma SDK 'Sı paketi, bu işletim sistemleri için kullanılabilir:
    * Windows: x64 ve x86.
    * Mac: macOS X sürüm 10,12 veya üzeri.
    * Linux: Ubuntu 16,04, Ubuntu 18,04, x64 üzerinde debir 9.
* Linux 'ta, gerekli paketleri yüklemek için şu komutları çalıştırın:

  * Ubuntu 'da:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * On yıl 9 ' da:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* Windows 'ta, platformunuz için [Visual Studio C++ 2019 için Microsoft Visual yeniden dağıtılabilir](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) gereklidir.

## <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Bu komut, konuşma SDK 'Sı için [Pypı](https://pypi.org/) 'den Python paketini yüklüyor:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Destek ve güncelleştirmeler

Konuşma SDK 'Sı Python paketine yönelik güncelleştirmeler PyPI aracılığıyla dağıtılır ve [sürüm notlarında](~/articles/cognitive-services/Speech-Service/releasenotes.md)duyurulmuştur.
Yeni bir sürüm varsa, bunu komut `pip install --upgrade azure-cognitiveservices-speech`ile güncelleştirebilirsiniz.
`azure.cognitiveservices.speech.__version__` değişkenini inceleyerek Şu anda yüklü olan sürümü denetleyin.

Bir sorununuz varsa veya bir özellik eksikse, bkz. [destek ve yardım seçenekleri](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Konuşma SDK 'sını kullanan bir Python uygulaması oluşturma

### <a name="run-the-sample"></a>Örneği çalıştırma

Bu hızlı başlangıçta [örnek kodu](#sample-code) bir kaynak dosyaya kopyalayabilir `quickstart.py` ve IDE 'niz içinde veya konsolunda çalıştırabilirsiniz:

```sh
python quickstart.py
```

Ya da bu hızlı başlangıç öğreticisini [konuşma SDK 'sı örnek deposundan](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) [Jupyter](https://jupyter.org) Not defteri olarak indirebilir ve Not defteri olarak çalıştırabilirsiniz.

### <a name="sample-code"></a>Örnek kod

````Python

import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and service region (e.g., "westus").
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Konuşma SDK 'sını Visual Studio Code ile yükleyip kullanma

1. Bilgisayarınıza [Python](https://www.python.org/downloads/), 3,5 veya üzeri bir 64 bit sürümü indirin ve yükleyin.
1. [Visual Studio Code](https://code.visualstudio.com/Download)indirin ve yükleyin.
1. Visual Studio Code açın ve Python uzantısını yükler. Menüden **dosya** > **tercihleri** > **uzantıları** ' nı seçin. **Python**için arama yapın.

   ![Python uzantısını yükler](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Projenin depolandığı bir klasör oluşturun. Bir örnek, Windows Gezgini 'ni kullanmaktır.
1. Visual Studio Code, **Dosya** simgesini seçin. Ardından oluşturduğunuz klasörü açın.

   ![Bir klasörü açın](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Yeni dosya simgesini seçerek `speechsdk.py`yeni bir Python kaynak dosyası oluşturun.

   ![Dosya oluşturma](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. [Python kodunu](#sample-code) kopyalayın, yapıştırın ve yeni oluşturulan dosyaya kaydedin.
1. Konuşma Hizmetleri abonelik bilgilerinizi ekleyin.
1. Seçilirse, pencerenin alt kısmındaki durum çubuğunun sol tarafında bir Python yorumlayıcı görüntülenir.
   Aksi takdirde, kullanılabilir Python yorumlayıcılarını bir listesini alın. Komut paletini açın (Ctrl + Shift + P) ve **Python: yorumlayıcı Seç**' i girin. Uygun olanı seçin.
1. Konuşma SDK 'Sı Python paketini Visual Studio Code içinden yükleyebilirsiniz. Seçtiğiniz Python yorumlayıcı için henüz yüklenmemişse bunu yapın.
   Konuşma SDK 'Sı paketini yüklemek için bir Terminal açın. Komut paletini tekrar açın (Ctrl + Shift + P) ve **Terminal: yeni tümleşik Terminal oluştur**' a girin.
   Açılan terminalde, komut `python -m pip install azure-cognitiveservices-speech` veya sisteminiz için uygun komutu girin.
1. Örnek kodu çalıştırmak için, düzenleyicinin içinde herhangi bir yere sağ tıklayın. **Terminalde Python dosyasını çalıştır '** ı seçin.
   Metniniz konuşmaya dönüştürülür ve belirtilen ses verilerinde kaydedilir.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Bu talimatları takip eden sorunlarınız varsa, daha kapsamlı [Visual Studio Code Python öğreticisine](https://code.visualstudio.com/docs/python/python-tutorial)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Özel bir ses oluşturun](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Özel ses örneklerini Kaydet](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

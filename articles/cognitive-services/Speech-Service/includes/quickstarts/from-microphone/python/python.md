---
title: 'Hızlı başlangıç: bir mikrofondan konuşmayı tanıma, Python-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Python için konuşma SDK 'sını kullanan bir konuşmaya metin konsol uygulaması oluşturmak için bu kılavuzu kullanın. İşiniz bittiğinde konuşmayı metne gerçek zamanlı dönüştürmek için bilgisayarınızın mikrofonunu kullanabilirsiniz.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 7fe11876a7ba20981f784bc41ce8094178591371
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503692"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Bir LUSıS uygulaması oluşturma ve bir uç nokta anahtarı edinme](../../../../quickstarts/create-luis.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md)

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

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

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
   İstendiğinde birkaç sözcükten konuşun. Yeniden yazılı metin kısa bir süre sonra görüntülenir.

   ![Örnek çalıştırma](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Bu talimatları takip eden sorunlarınız varsa, daha kapsamlı [Visual Studio Code Python öğreticisine](https://code.visualstudio.com/docs/python/python-tutorial)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

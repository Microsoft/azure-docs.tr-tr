---
title: 'Hızlı başlangıç: Sentezleştirme C++ konuşma, (Linux)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'sını kullanarak Linux 'ta C++ konuşmayı nasıl sentezleyeceğinizi öğrenin
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: ba0e04d4dadd9d3a4bea41aeb0a66b37f454f030
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503258"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=linux)

## <a name="add-sample-code"></a>Örnek kodu ekleyin

1. `helloworld.cpp` adlı bir C++ kaynak dosyası oluşturun ve aşağıdaki kodu dosyaya yapıştırın.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/text-to-speech/helloworld.cpp#code)]

1. Bu yeni dosyada, dize `YourSubscriptionKey`, konuşma Hizmetleri abonelik anahtarınızla değiştirin.

1. `YourServiceRegion` dizesini aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile (örneğin ücretsiz deneme aboneliğinde `westus`) değiştirin.

## <a name="build-the-app"></a>Uygulama oluşturma

> [!NOTE]
> Aşağıdaki komutları _tek bir komut satırı_ olarak girdiğinizden emin olun. Bunu yapmanın en kolay yolu, komutu her komutun yanındaki **Kopyala** düğmesini kullanarak kopyalayıp kabuk isteminize yapıştırmaktır.

* Uygulamayı derlemek için **x64**  (64 bit) bir sistemde aşağıdaki komutu çalıştırın.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Uygulamayı derlemek için **x86** (32 bit) bir sistemde aşağıdaki komutu çalıştırın.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* Bir **ARM64** (64 bit) sisteminde, uygulamayı derlemek için aşağıdaki komutu çalıştırın.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Uygulamayı çalıştırma

1. Yükleyicinin kitaplık yolunu Konuşma SDK'sı kitaplığına işaret edecek şekilde yapılandırın.

   * **x64** (64 bit) bir sistemde, aşağıdaki komutu girin.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86** (32 bit) bir sistemde, aşağıdaki komutu girin.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * Bir **ARM64** (64-bit) sisteminde, aşağıdaki komutu girin.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Uygulamayı çalıştırın.

   ```sh
   ./helloworld
   ```

1. Konsol penceresinde bir istem belirir ve bir metin yazmanızı ister. Birkaç kelime veya tümce yazın. Yazdığınız metin konuşma hizmetlerine iletilir ve konuşmacının oynadığı konuşmayı sentezleştirilmiştir.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Özel bir ses oluşturun](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Özel ses örneklerini Kaydet](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

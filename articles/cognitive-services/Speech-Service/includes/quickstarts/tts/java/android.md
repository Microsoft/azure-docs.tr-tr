---
title: 'Hızlı başlangıç: Sentezleştirme konuşma, Java (Android)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'sını kullanarak Android 'de konuşmayı nasıl sentezleyeceğinizi öğrenin
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 47120c6b6fee945938ac5348091be55795077f6c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503342"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=android)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=android)

## <a name="create-user-interface"></a>Kullanıcı arabirimi oluşturma

Uygulama için temel bir kullanıcı arabirimi oluşturacağız. Ana etkinliğiniz `activity_main.xml` için düzende değişiklik yapın. Başlangıçta, düzen uygulamanızın adıyla bir başlık çubuğu ve "Merhaba Dünya!" metnini içeren bir TextView içerir.

1. TextView öğesine tıklayın. Sağ üst köşedeki ID özniteliğini `outputMessage`değiştirin ve daha düşük ekrana sürükleyin. Metnini silin.

1. `activity_main.xml` penceresinin sol üst tarafındaki Paletten bir düğmeyi metnin üst kısmındaki boş alana sürükleyin.

1. Sağ taraftaki düğme özniteliklerde, `onClick` özniteliği değeri için `onSpeechButtonClicked` girin. Düğme olayını işlemek için bu adla bir yöntem yazacağız.  Sağ üst köşedeki ID özniteliğini `button` olarak değiştirin.

1. Bir düz metni düğmenin üzerindeki alana sürükleyin; ID özniteliğini `speakText`olarak değiştirin ve metin özniteliğini `Hi there!`olarak değiştirin.

1. Tasarımcının en üstündeki sihirli değnek simgesini kullanarak düzen kısıtlamalarını ortaya çıkarın.


    ![Sihirli değnek simgesinin ekran görüntüsü](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

UI 'nizin metin ve grafik gösterimi şu şekilde görünmelidir:

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Örnek kodu ekleyin

1. `MainActivity.java` kaynak dosyasını açın. Bu dosyanın içindeki kodun tamamını aşağıdakiyle değiştirin.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onSpeechButtonClicked` yöntemi daha önce de belirtildiği gibi düğme tıklama işleyicisidir. Bir düğme, konuşma senşlerini tetikler.

1. Aynı dosyada `YourSubscriptionKey` dizesini abonelik anahtarınız ile değiştirin.

1. Ayrıca `YourServiceRegion` dizesini de aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile (örneğin ücretsiz deneme aboneliğinde `westus`) değiştirin.

## <a name="build-and-run-the-app"></a>Uygulamayı derleme ve çalıştırma

1. Android cihazınızı geliştirme bilgisayarınıza bağlayın. Cihazda [geliştirme modunu ve USB hata ayıklamasını](https://developer.android.com/studio/debug/dev-options) etkinleştirdiğinizden emin olun. Alternatif olarak, bir [Android öykünücüsü](https://developer.android.com/studio/run/emulator)oluşturun.

1. Uygulamayı derlemek için Ctrl+F9 tuşlarına basın veya menü çubuğundan **Derle** > **Proje Yap**'ı seçin.

1. Uygulamayı başlatmak için Shift+F10 tuşlarına basın veya **Çalıştır** >  **'uygulama' çalıştır**'ı seçin.

1. Görüntülenen dağıtım hedefi penceresinde, Android cihazınızı veya öykünücüyü seçin.

   ![Dağıtım Hedefi Seç penceresinin ekran görüntüsü](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Konuşma birleştirme bölümüne başlamak için bir metin girin ve uygulamadaki düğmeye basın. Varsayılan konuşmacının birleştirilmiş sesini duyacaktır ve ekrandaki `speech synthesis succeeded` bilgileri görürsünüz.

![Android uygulamasının ekran görüntüsü](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Özel bir ses oluşturun](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Özel ses örneklerini Kaydet](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

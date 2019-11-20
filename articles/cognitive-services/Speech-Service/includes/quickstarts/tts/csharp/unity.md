---
title: 'Hızlı başlangıç: Sentezleştirme C# konuşma, (Unity)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Unity ile bir metin okuma uygulaması oluşturmak için bu kılavuzu kullanın. İşiniz bittiğinde, konuşmanızı gerçek zamanlı olarak cihazınızın hoparlörünü senkize ekleyebilirsiniz.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 281fbcf3f42160b18adcad3f06f6ef4a7b8de243
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502929"
---
> [!NOTE]
> Unity, Windows Masaüstü (x86 ve x64) veya Evrensel Windows Platformu (x86, x64, ARM/ARM64), Android (x86, ARM32/64) ve iOS (x64 simülatör, ARM32 ve ARM64) destekler.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=unity)

## <a name="add-a-ui"></a>Kullanıcı arabirimi ekleme

Sentiğimize metin girmek için bir giriş alanından oluşan, konuşma sentiğinin tetiklenmesi için bir düğme ve sonucu görüntüleyen bir metin alanı olan sahneye en az bir kullanıcı arabirimi ekleyeceğiz.

* [Hiyerarşi penceresinde](https://docs.unity3d.com/Manual/Hierarchy.html) (varsayılan olarak sol tarafta), Unity 'nin yeni projeyle oluşturulduğu örnek bir sahne gösterilmektedir.
* **Hiyerarşi** penceresinin en üstündeki **Oluştur** düğmesini seçin ve **Kullanıcı arabirimi** > **giriş alanı**' nı seçin.
* Bu seçenek, **hiyerarşi** penceresinde görebileceğiniz üç oyun nesnesini oluşturur: bir **tuval** nesnesi içinde Iç Içe geçmiş bir **giriş alanı** nesnesi ve bir **EventSystem** nesnesi.
* Sahnenin ve [sahne görünümündeki](https://docs.unity3d.com/Manual/UsingTheSceneView.html)giriş alanının iyi bir görünümünü görmek [Için sahne görünümünde gezinin](https://docs.unity3d.com/Manual/SceneViewNavigation.html) .
* Ayarlarını [Inspector penceresinde](https://docs.unity3d.com/Manual/UsingTheInspector.html) (varsayılan olarak, sağ tarafta) göstermek için **hiyerarşi** penceresinde **giriş alanı** nesnesini seçin.
* Giriş alanının tuvalin ortasında ortalanmasını sağlamak için **POS X** ve **POS Y** özelliklerini **0** olarak ayarlayın.
* **Hiyerarşi** penceresinin en üstündeki **Oluştur** düğmesini yeniden seçin. Düğme oluşturmak için **uı** > **düğmesini** seçin.
* Ayarlarını [Inspector penceresinde](https://docs.unity3d.com/Manual/UsingTheInspector.html) (varsayılan olarak, sağ tarafta) göstermek için **hiyerarşi** penceresinde **düğme** nesnesini seçin.
* **POS X** ve **POS Y** özelliklerini **0** ve **-48**olarak ayarlayın. Düğmenin ve giriş alanının çakışmadığından emin olmak için **Genişlik** ve **Yükseklik** özelliklerini **160** ve **30** olarak ayarlayın.
* **Hiyerarşi** penceresinin en üstündeki **Oluştur** düğmesini yeniden seçin. Bir metin alanı oluşturmak için **Kullanıcı arabirimi** > **metin** ' i seçin.
* Ayarlarını [Inspector penceresinde](https://docs.unity3d.com/Manual/UsingTheInspector.html) (varsayılan olarak, sağ tarafta) göstermek için **hiyerarşi** penceresinde **metin** nesnesini seçin.
* **POS X** ve **POS Y** özelliklerini **0** ve **80**olarak ayarlayın. Metin alanının ve giriş alanının çakışmadığından emin olmak için **Genişlik** ve **Yükseklik** özelliklerini **320** ve **80** olarak ayarlayın.
* **Hiyerarşi** penceresinin en üstündeki **Oluştur** düğmesini yeniden seçin. Ses **kaynağı** oluşturmak için ses > **ses kaynağı** ' nı seçin.

İşiniz bittiğinde, Kullanıcı arabirimi şu ekran görüntüsüne benzer şekilde görünmelidir:

[Unity düzenleyicisinde hızlı başlangıç Kullanıcı arabiriminin ekran görüntüsünü![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Örnek kod ekleme

1. [Proje penceresinde](https://docs.unity3d.com/Manual/ProjectView.html) (varsayılan olarak sol alt tarafta) **Oluştur** düğmesini seçin ve ardından  **C# komut dosyası**' nı seçin. Betiği `HelloWorld`olarak adlandırın.

1. Betiği çift tıklayarak düzenleyin.

   > [!NOTE]
   >  > **tercihlerini** **Düzenle** seçeneğini belirleyerek hangi kod düzenleyicisinin başlatılmış olduğunu yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Unity Kullanıcı el kitabı](https://docs.unity3d.com/Manual/Preferences.html).

1. Tüm kodu aşağıdaki kodla değiştirin:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` dize, konuşma Hizmetleri abonelik anahtarınızla bulun ve değiştirin.

1. `YourServiceRegion` dizesini bulun ve aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile değiştirin. Örneğin, ücretsiz deneme sürümünü kullanıyorsanız bölge `westus`.

1. Değişiklikleri betikte kaydedin.

1. Unity düzenleyicisine geri döndüğünüzde, betiği oyun nesnelerinizin birine bileşen olarak ekleyin.

   * Ayarla penceresinde ayarı açmak için **hiyerarşi** penceresinde **tuval** nesnesini [seçin (varsayılan](https://docs.unity3d.com/Manual/UsingTheInspector.html) olarak, sağ tarafta).
   * **Inspector** penceresindeki **Bileşen Ekle** düğmesini seçin. Daha önce oluşturduğumuz `HelloWorld` betiği arayın ve ekleyin.
   * HelloWorld bileşeni, `HelloWorld` sınıfının ortak özellikleriyle eşleşen dört başlatılmamış özellik, **çıkış metni**, **giriş alanı**, **konuşma düğmesi** ve **Ses kaynağıdır**.
     Bunları bağlamak için nesne seçiciyi (özelliğin sağ tarafındaki küçük daire simgesini) seçin. Daha önce oluşturduğunuz metin ve düğme nesnelerini seçin.

     > [!NOTE]
     > Giriş alanı ve düğme de iç içe metin nesnesine sahiptir. Yanlışlıkla metin çıktısı için seçim yaptığınızdan emin olun. Ya da bu karışıklığın önüne geçmek için **Inspector** penceresindeki **ad** alanını kullanan metin nesnelerini yeniden adlandırabilirsiniz.

## <a name="run-the-application-in-the-unity-editor"></a>Uygulamayı Unity düzenleyicisinde çalıştırma

* Menü çubuğunun altındaki Unity Düzenleyici araç çubuğunda **oynat** düğmesini seçin.
* Uygulama başlatıldıktan sonra, giriş alanına bir metin girin ve düğmeyi seçin. Metniniz, konuşmacının oynadığı konuşma hizmetine iletilir ve konuşmaya sentezleştirilmiştir.

  [Unity oyun penceresinde çalışan hızlı başlangıç ekranının ekran görüntüsünü![](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Hata ayıklama iletileri için [konsol penceresini](https://docs.unity3d.com/Manual/Console.html) denetleyin.

## <a name="additional-options-to-run-this-application"></a>Bu uygulamayı çalıştırmak için ek seçenekler

Bu uygulama, Windows tek başına uygulama veya UWP uygulaması olarak Android 'e de dağıtılabilir.
Bu ek hedeflerin yapılandırmasını açıklayan hızlı başlangıç/CSharp-Unity klasöründeki [örnek depoya](https://aka.ms/csspeech/samples) bakın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Özel bir ses oluşturun](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Özel ses örneklerini Kaydet](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

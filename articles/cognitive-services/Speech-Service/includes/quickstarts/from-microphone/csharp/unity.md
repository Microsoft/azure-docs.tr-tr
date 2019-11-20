---
title: 'Hızlı başlangıç: bir mikrofondan konuşmayı tanıma C# , (Unity)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: babc6b1c0b6207d404091db3117f95f6d71c53ac
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503503"
---
> [!NOTE]
> Unity için konuşma SDK 'Sı, Windows Masaüstü (x86 ve x64) veya Evrensel Windows Platformu (x86, x64, ARM/ARM64), Android (x86, ARM32/64) ve iOS (x64 simülatör, ARM32 ve ARM64) destekler

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=unity) Bunu zaten yaptıysanız harika. Şimdi devam edelim.

## <a name="create-a-unity-project"></a>Unity projesi oluşturma

1. Unity 'yi açın. Unity 'yi ilk kez kullanıyorsanız, **Unity Hub** *<version number>* penceresi görüntülenir. (Ayrıca, bu pencereye ulaşmak için Unity hub 'ını doğrudan açabilirsiniz.)

   [![Unity hub penceresi](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. **Yeni**'yi seçin. **Unity ile yeni proje oluştur** *<version number>* penceresi görüntülenir.

   [Unity hub 'da yeni bir proje oluşturmak ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. **Proje adı**' nda, **CSharp-Unity**girin.
1. **Şablonlar**' da, **3B** zaten seçili değilse, bunu seçin.
1. **Konum**' da, projenin kaydedileceği klasörü seçin veya oluşturun.
1. **Oluştur**'u seçin.

Bir süre sonra Unity Düzenleyicisi penceresi görüntülenir.



## <a name="add-ui"></a>UI Ekle

Şimdi sahemizi en az bir kullanıcı arabirimi ekleyelim. Bu Kullanıcı arabirimi, konuşma tanımayı tetikleme ve sonucu görüntüleyen bir metin alanı içeren bir düğmeden oluşur. [ **Hiyerarşi** penceresinde](https://docs.unity3d.com/Manual/Hierarchy.html), Unity 'nin yeni projeyle oluşturulan örnek bir sahne gösterilmektedir.

1. **Hiyerarşi** penceresinin üst kısmında > **Kullanıcı arabirimi** > düğmesi **Oluştur** ' u seçin.

   Bu eylem **hiyerarşi** penceresinde görebileceğiniz üç oyun nesnesi oluşturur: **düğme** nesnesi, düğmeyi içeren bir **tuval** nesnesi ve bir **EventSystem** nesnesi.

   [![Unity düzenleyici ortamı](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [ ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) Sahnenin ve [ **sahne** görünümündeki](https://docs.unity3d.com/Manual/UsingTheSceneView.html)düğmenin iyi bir görünümünü görmek için sahne görünümünde gezinin.

1. [ **Inspector** penceresinde](https://docs.unity3d.com/Manual/UsingTheInspector.html) (varsayılan olarak, sağ tarafta), **POS X** ve **POS Y** özelliklerini **0**olarak ayarlayın, bu nedenle düğme tuvalin ortasında ortalanır.

1. **Hiyerarşi** penceresinde, **metin** nesnesi **oluşturmak Için** > **Kullanıcı arabirimi** > **Oluştur** ' u seçin.

1. **Inspector** penceresinde, **POS X** ve **pos Y** özelliklerini **0** ve **120**olarak ayarlayıp **Genişlik** ve **Yükseklik** özelliklerini **240** ve **120**olarak ayarlayın. Bu değerler metin alanının ve düğmenin çakışmadığından emin olmanızı sağlamaktır.

İşiniz bittiğinde **sahne** görünümü şu ekran görüntüsüne benzer görünmelidir:

[Unity düzenleyicisinde ![sahne görünümü](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Örnek kod ekleme

Unity projesi için örnek betik kodu eklemek için aşağıdaki adımları izleyin:

1. Yeni C# bir betik eklemek için [Proje penceresinde](https://docs.unity3d.com/Manual/ProjectView.html) >  **C# betiği** **Oluştur** ' u seçin.

   [Unity düzenleyicisinde proje penceresini ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Betiği `HelloWorld`olarak adlandırın.

1. Yeni oluşturulan betiği düzenlemek için `HelloWorld` çift tıklayın.

   > [!NOTE]
   > Kod düzenleyicisini düzenleme için Unity tarafından kullanılacak şekilde yapılandırmak için > **tercihlerini** **Düzenle** ' yi seçin ve ardından **dış araçlar** tercihlerine gidin. Daha fazla bilgi için bkz. [Unity Kullanıcı el kitabı](https://docs.unity3d.com/Manual/Preferences.html).

1. Mevcut betiği şu kodla değiştirin:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` dize, konuşma Hizmetleri abonelik anahtarınızla bulun ve değiştirin.

1. Dize `YourServiceRegion` bulun ve aboneliğiniz ile ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile değiştirin. Örneğin, ücretsiz denemeyi kullanıyorsanız bölge `westus` olur.

1. Değişiklikleri betikte kaydedin.

Şimdi Unity düzenleyicisine dönün ve betiği oyun nesnelerinizin birine bileşen olarak ekleyin:

1. **Hiyerarşi** penceresinde **tuval** nesnesini seçin.

1. **Inspector** penceresinde, **Bileşen Ekle** düğmesini seçin.

   [Unity düzenleyicisinde ![Inspector penceresi](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Aşağı açılan listede, yukarıda oluşturduğumuz `HelloWorld` betiği arayın ve ekleyin. **Inspector** penceresinde, iki başlatılmamış özellik, **çıkış metni** ve **Start ku düğmesi**listelenerek bir **Merhaba Dünya (betik)** bölümü görüntülenir. Bu Unity bileşen özellikleri `HelloWorld` sınıfının ortak özellikleriyle eşleşir.

1. **Start ku Button** özelliğinin nesne seçicisini (özelliğin sağ tarafındaki küçük daire simgesini) seçin ve daha önce oluşturduğunuz **düğme** nesnesini seçin.

1. **Çıkış metni** özelliğinin nesne seçicisini seçin ve daha önce oluşturduğunuz **metin** nesnesini seçin.

   > [!NOTE]
   > Düğme Ayrıca iç içe metin nesnesine sahiptir. Metni yanlışlıkla metin çıktısı için seçtiğinizden emin olun (veya bir karışıklığı önlemek için **Inspector** penceresindeki **ad** alanını kullanarak metin nesnelerinden birini yeniden adlandırın).

## <a name="run-the-application-in-the-unity-editor"></a>Uygulamayı Unity düzenleyicisinde çalıştırma

Artık, uygulamayı Unity Düzenleyicisi içinde çalıştırmaya hazırsınız.

1. Unity Düzenleyici araç çubuğunda (menü çubuğunun altında) **oynat** düğmesini (sağ taraftaki bir üçgen) seçin.

1. [ **Oyun** görünümü](https://docs.unity3d.com/Manual/GameView.html)' ne gidin ve **metin** nesnesinin **konuşmayı tanımak için düğme ' yi**görüntülemesini bekleyin. (Uygulamanın başlatıldığı veya yanıt vermeye hazırlanmadığı durumlarda **Yeni metin** görüntüler.)

1. Düğmesini seçin ve bilgisayarınızın mikrofonuna bir Ingilizce tümcecik veya cümle konuşun. Konuşma, konuşma hizmetlerine iletilir ve **oyun** görünümünde görüntülenen metne gönderilir.

   [Unity düzenleyicisinde oyun görünümü ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Hata ayıklama iletileri için [ **konsol** penceresini](https://docs.unity3d.com/Manual/Console.html) denetleyin. **Konsol** penceresi görünmüyorsa, menü çubuğuna gidin ve bunu göstermek için **pencere** > **genel** > **konsolu** ' nu seçin.

1. Konuşmayı tanımayı tamamladığınızda, uygulamayı durdurmak için Unity Düzenleyici araç çubuğundaki **oynat** düğmesini seçin.

## <a name="additional-options-to-run-this-application"></a>Bu uygulamayı çalıştırmak için ek seçenekler

Bu uygulama, bir Android uygulaması, Windows tek başına uygulaması veya UWP uygulaması olarak da dağıtılabilir.
Daha fazla bilgi için bkz. [örnek depomız](https://aka.ms/csspeech/samples). `quickstart/csharp-unity` klasörü, bu ek hedeflerin yapılandırmasını açıklar.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

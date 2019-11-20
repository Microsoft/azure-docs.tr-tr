---
title: 'Hızlı başlangıç: Sentezleştirme C# konuşma, (UWP)-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Bu makalede bilişsel hizmetler konuşma C# SDK 'sını kullanarak bir evrensel WINDOWS platformu (UWP) uygulaması oluşturacaksınız. Konuşmayı gerçek zamanlı olarak cihazınızın hoparlörünü sentezleştir. Uygulama, konuşma SDK 'Sı NuGet paketiyle oluşturulmuştur ve 2019 Microsoft Visual Studio.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 3a88f4fc14286a60feb9d72676827b0cce91eb25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503132"
---
> [!NOTE]
> Evrensel Windows Platformu; PC, Xbox, Surface Hub ve diğer cihazlar dahil olmak üzere Windows 10 destekleyen tüm cihazlarda çalışan uygulamalar geliştirmenize olanak tanır.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Örnek kodu ekleyin

Şimdi uygulamanın kullanıcı arabirimini tanımlayan XAML kodunu ekleyin ve arka plan C# kod uygulamasını ekleyin.

1. **Çözüm Gezgini**' de `MainPage.xaml`açın.

1. Tasarımcı XAML görünümünde, **kılavuz** ETIKETINE aşağıdaki xaml kod parçacığını ekleyin (`<Grid>` ve `</Grid>`arasında):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. **Çözüm Gezgini**, arka plan kod kaynak dosyası `MainPage.xaml.cs`açın. (`MainPage.xaml`altında gruplandırılır.)

1. İçindeki tüm kodu aşağıdaki kod parçacığıyla değiştirin:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Kaynak dosyanın `Speak_ButtonClicked` işleyicisinde, `YourSubscriptionKey`dizesini bulun ve abonelik anahtarınızla değiştirin.

1. `Speak_ButtonClicked` işleyicisinde, `YourServiceRegion`dizesini bulun ve aboneliğinizle ilişkili [bölge](~/articles/cognitive-services/Speech-Service/regions.md) ile değiştirin. (Örneğin, ücretsiz deneme aboneliği için `westus` kullanın.)

1. Değişikliklerinizi kaydetmek için menü çubuğundan **dosya** > **Tümünü Kaydet** ' i seçin.

## <a name="build-and-run-the-application"></a>Uygulamayı derleme ve çalıştırma

Artık uygulamanızı derlemek ve test etmek için hazırsınız.

1. Menü çubuğundan, uygulamayı derlemek için **build** > **Build Solution** öğesini seçin. Kodun artık hatasız derlenmesi gerekir.

1. Uygulamayı başlatmak için hata **ayıklamayı başlatın** > **Hata Ayıkla** ' yı seçin (veya **F5**tuşuna basın). **HelloWorld** penceresi görüntülenir.

   ![Örnek UWP konuşma sen, Hızlı Başlangıç C# uygulaması](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Metin kutusuna bir metin girin ve **konuş**' a tıklayın. Metniniz konuşma hizmetlerine iletilir ve konuşmacının oynadığı konuşmayı sentezleştirilmiştir.

    ![Konuşma birleştirme Kullanıcı arabirimi](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Özel bir ses oluşturun](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Özel ses örneklerini Kaydet](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

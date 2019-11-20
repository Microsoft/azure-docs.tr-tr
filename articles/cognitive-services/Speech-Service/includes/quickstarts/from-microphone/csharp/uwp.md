---
title: 'Hızlı başlangıç: bir mikrofondan konuşmayı tanıma C# , (UWP)-konuşma hizmeti'
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
ms.openlocfilehash: 0d1da9a9ef32aed1975595bb15909b9531ab2400
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503559"
---
## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

> [!div class="checklist"]
> * [Azure konuşma kaynağı oluşturma](../../../../get-started.md)
> * [Geliştirme ortamınızı kurma](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Boş bir örnek proje oluşturun](../../../../quickstarts/create-project.md?tabs=uwp)

Bunu zaten yaptıysanız harika. Şimdi devam edelim.

## <a name="open-your-project-in-visual-studio"></a>Projenizi Visual Studio 'da açın

İlk adım, projenizin Visual Studio 'da açık olduğundan emin olmak.

## <a name="start-with-some-boilerplate-code"></a>Bazı demirbaş kodla başlayın

Projemiz için bir çatı olarak çalışacak bir kod ekleyelim.

1. **Çözüm Gezgini**' de `MainPage.xaml`açın.

2. Tasarımcı XAML görünümünde, **kılavuz** ETIKETINE aşağıdaki xaml kod parçacığını ekleyin (`<Grid>` ve `</Grid>`arasında):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. **Çözüm Gezgini**, arka plan kod kaynak dosyası `MainPage.xaml.cs`açın. (`MainPage.xaml`altında gruplandırılır.)

4. Kodu aşağıdaki temel kodla değiştirin:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Konuşma yapılandırması oluşturma

Bir `SpeechRecognizer` nesnesi başlatabilmeniz için önce abonelik anahtarınızı ve abonelik bölgenizi kullanan bir yapılandırma oluşturmanız gerekir. `RecognizeSpeechAsync()` yöntemine bu kodu ekleyin.

> [!NOTE]
> Bu örnek, `SpeechConfig`oluşturmak için `FromSubscription()` yöntemini kullanır. Kullanılabilir yöntemlerin tam listesi için bkz. [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer başlatma

Şimdi bir `SpeechRecognizer`oluşturalım. Yönetilmeyen kaynakların doğru şekilde yayınlanmasıyla emin olmak için bir using ifadesinin içinde bu nesne oluşturulur. Bu kodu, konuşma yapılandırmanızın hemen altına `RecognizeSpeechAsync()` yöntemine ekleyin.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Bir tümceciği tanıma

`SpeechRecognizer` nesnesinden `RecognizeOnceAsync()` yöntemini çağıracağız. Bu yöntem, konuşma hizmetinin tanıma için tek bir tümcecik gönderdiğini ve bu ifadenin konuşmayı tanımayı durdur olarak belirlenmesinin ardından olduğunu bilmesini sağlar.

Using ifadesinin içinde şu kodu ekleyin: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Tanıma sonuçlarını (veya hatalarını) görüntüleme

Tanınma sonucu konuşma hizmeti tarafından döndürüldüğünde, onunla ilgili bir şey yapmak isteyeceksiniz. Bu uygulamayı basit tutacağız ve sonucu durum paneline yazdıracağız.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Uygulamayı derleme ve çalıştırma

Artık uygulamanızı derlemek ve test etmek için hazırsınız.

1. Menü çubuğundan, uygulamayı derlemek için **build** > **Build Solution** öğesini seçin. Kodun artık hatasız derlenmesi gerekir.

1. Uygulamayı başlatmak için hata **ayıklamayı başlatın** > **Hata Ayıkla** ' yı seçin (veya **F5**tuşuna basın). **HelloWorld** penceresi görüntülenir.

   ![Hızlı başlangıçta örnek UWP konuşma tanıma C# uygulaması](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. **Mikrofonu etkinleştir**' i seçin ve erişim izni Isteği açıldığında **Evet**' i seçin.

   ![Mikrofon erişimi izin isteği](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **Mikrofon girdisi ile konuşma tanıma**’yı seçin ve cihazınızın mikrofonuna İngilizce bir deyim ya da cümle söyleyin. Konuşma, konuşma hizmetlerine iletilir ve pencerede görüntülenen metne gönderilir.

   ![Konuşma tanıma kullanıcı arabirimi](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [footer](./footer.md)]

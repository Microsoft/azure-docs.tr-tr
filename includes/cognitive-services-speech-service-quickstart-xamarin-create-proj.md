---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675620"
---
Xamarin ile platformlar arası mobil uygulama .NET geliştirmesi için Visual Studio projesi oluşturmak üzere şunları yapmanız gerekir:
- Visual Studio geliştirme seçeneklerini ayarlayın.
- Projeyi oluşturun ve hedef mimariyi seçin. 
- Konuşma SDK 'sını yükler.

### <a name="set-up-visual-studio-development-options"></a>Visual Studio geliştirme seçeneklerini ayarlama

Başlamak için, .NET ile platformlar arası mobil geliştirme için Visual Studio 'da doğru şekilde ayarlandığından emin olun:

1. Visual Studio 2019 ' i açın.

1. Visual Studio menü çubuğunda **Araçlar** ' ı seçin > Araçlar **ve Özellikler** ' i seçerek Visual Studio yükleyicisi açın ve **değiştirme** iletişim kutusunu görüntüleyin.

   ![İş yükleri sekmesi, değiştirme iletişim kutusu, Visual Studio Yükleyicisi](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. **Iş yükleri** sekmesinde, **Windows**altında .NET iş yüküne **sahip mobil geliştirmeyi** bulun. Bu iş yükünün yanındaki onay kutusu zaten seçiliyse, **değiştirme** iletişim kutusunu kapatın ve 5. adıma gidin.

1. **.Net Ile mobil geliştirme** onay kutusunu seçin ve **Değiştir**' i seçin. Kullanmaya **başlamadan önce** iletişim kutusunda, .NET iş yüküne sahip mobil geliştirmeyi yüklemek için **devam** ' ı seçin. Yeni özelliğin yüklenmesi biraz zaman alabilir.

1. Visual Studio Yükleyicisi kapatın.

### <a name="create-the-project"></a>Proje oluşturma

1. **Yeni proje oluştur** penceresini göstermek Için Visual Studio menü çubuğunda **dosya** > **Yeni** > **projesi** ' ni seçin.

   ![Yeni proje oluşturma-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. **Mobil uygulama (Xamarin. Forms)** bulun ve seçin.

1. **Yeni projeyi Yapılandır** ekranınızı göstermek için **İleri ' yi** seçin.

   ![Yeni projenizi yapılandırma-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. **Proje adı**alanına *HelloWorld*yazın.

1. **Konum**' a gidin ve projenizi kaydetmek için klasörü seçin veya oluşturun.

1. **Yeni mobil uygulama Xamarin formları proje** penceresine gitmek için **Oluştur** ' u seçin.

   ![Yeni Evrensel Windows Platformu Projesi iletişim kutusu-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. **Boş** şablonu seçin.

1. **Platformda**, **Android**, **iOS**ve **Windows (UWP)** kutularını seçin.

1. **Tamam**’ı seçin. Visual Studio IDE 'ye geri döndürüyorsunuz, yeni proje oluşturulup **Çözüm Gezgini** bölmesinde görünür.

   ![HelloWorld projesi-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Şimdi hedef platform mimarinizi ve başlangıç projenizi seçin. Visual Studio araç çubuğunda **çözüm platformları** açılan kutusunu bulun. (Bunu görmüyorsanız, **çözüm platformlarını**içeren araç çubuğunu görüntülemek Için > **araç çubuklarını** **Standart** > **göster** ' i seçin.) 64 bit Windows çalıştırıyorsanız, açılan kutuda **x64** ' ü seçin. 64 bit Windows 'un 32 bit uygulamaları da çalıştırabildiğinden, isterseniz **x86** ' yı seçebilirsiniz. **Başlangıç projeleri** açılan kutusunda HelloWorld ' i ayarlayın **. UWP (Evrensel Windows)** .

### <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

[Konuşma SDK 'Sı NuGet paketini](https://aka.ms/csspeech/nuget)yükledikten sonra projenizdeki konuşma SDK 'sına başvurun.

1. **Çözüm Gezgini**, çözümünüze sağ tıklayın. **NuGet çözüm** penceresine gitmek için **çözüm Için NuGet Paketlerini Yönet** ' i seçin.

1. **Gözat**’ı seçin.

   ![Çözüm için Paketleri Yönet iletişim kutusunun ekran görüntüsü](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. **Paket kaynağı**' nda NuGet.org ' yi seçin.

1. **Arama** kutusuna *Microsoft. Biliveservices. Speech*yazın. Ardından, arama sonuçlarında göründükten sonra bu paketi seçin.

   ![Çözüm için Paketleri Yönet iletişim kutusunun ekran görüntüsü](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > `Microsoft.CognitiveServices.Speech` NuGet içindeki iOS kitaplığında bitcode 'u etkin değil. Uygulamanız için bit kod kitaplığı etkinleştirilmiş olması gerekiyorsa, özel olarak iOS projesi için `Microsoft.CognitiveServices.Speech.Xamarin.iOS` NuGet kullanın.

1. Arama sonuçlarının yanındaki paket durumu bölmesinde tüm projeler: **HelloWorld**, HelloWorld ' i seçin **. Android**, **HelloWorld. iOS**ve **HelloWorld. UWP**.

1. **Yükle**’yi seçin.

1. **Değişiklikleri Önizle** Iletişim kutusunda **Tamam**' ı seçin.

1. **Lisans kabulü** iletişim kutusunda, lisansı görüntüleyin ve **kabul ediyorum**' u seçin. Tüm projelere konuşma SDK 'Sı paketi başvurusunu yükler. Yükleme başarıyla tamamlandıktan sonra HelloWorld. iOS için aşağıdaki uyarıyı görebilirsiniz. Bu bilinen bir sorundur ve uygulama işlevlerinden etkilenmemelidir.

   > "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a" başvurusu çözümlenemedi. Kodunuz için bu başvuru gerekliyse, derleme hataları alabilirsiniz.

---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: erhopf
ms.openlocfilehash: 942636a6ff6dcfe74a04a66d67a4490224b4538e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391812"
---
Masaüstü geliştirme için C++ bir Visual Studio projesi oluşturmak üzere Visual Studio geliştirme seçeneklerini ayarlamanız, projeyi oluşturmanız, hedef mimariyi seçmeniz ve konuşma SDK 'sını yüklemeniz gerekir. 

### <a name="set-up-visual-studio-development-options"></a>Visual Studio geliştirme seçeneklerini ayarlama

Başlamak için, masaüstü geliştirme için C++ Visual Studio 'da doğru şekilde ayarlandığından emin olun:

1. **Başlangıç** penceresini göstermek Için Visual Studio 2019 ' i açın.

   ![Başlangıç penceresi-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png) 

1. Visual Studio IDE 'ye gitmek için **kod olmadan devam et** ' i seçin.

1. Visual Studio menü çubuğunda **araçlar** >  ' i seçerek Visual Studio yükleyicisi**açın ve** **değiştirme** iletişim kutusunu görüntüleyin.

   ![İş yükleri sekmesi, değiştirme iletişim kutusu, Visual Studio Yükleyicisi](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. **İş yükleri** sekmesinde, **Windows**altında iş yüküyle **Masaüstü C++ geliştirmeyi** bulun. Bu iş yükünün yanındaki onay kutusu zaten seçili değilse, bunu seçin.

1. **Ayrı bileşenler** sekmesinde, **NuGet Paket Yöneticisi** onay kutusunu bulun. Onay kutusu zaten seçili değilse, bunu seçin.

1. Köşede **Kapat** veya **Değiştir**etiketli düğmeyi seçin. (Düğme adı, yükleme için herhangi bir özellik seçtiğinizden bağımsız olarak değişir.) **Değiştir**' i seçerseniz yükleme başlar ve bu işlem biraz zaman alabilir.

1. Visual Studio Yükleyicisi kapatın.

### <a name="create-the-project-and-select-the-target-architecture"></a>Projeyi oluşturma ve hedef mimarisini seçme

Sonra, projenizi oluşturun:

1. Visual Studio menü çubuğunda **dosya** > **Yeni** > **Proje** ' yi seçerek **Yeni proje oluştur** penceresini görüntüleyin.

   ![Yeni proje oluşturma, C++ -Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. **Konsol uygulaması**bulun ve seçin. Bu proje türünün ( C++ C# veya Visual Basic aksine) sürümünü seçtiğinizden emin olun.

1. **Yeni projeyi Yapılandır** ekranınızı göstermek için **İleri ' yi** seçin.

   ![Yeni projenizi yapılandırın, C++ -Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. **Proje adı**' nda `helloworld` girin.

1. **Konum**' a gidin ve projenizi kaydetmek için klasörü seçin veya oluşturun.

Şimdi hedef platform mimarinizi seçin. Visual Studio araç çubuğunda, **çözüm platformları** açılan kutusunu bulun. (Bunu görmüyorsanız, **çözüm platformlarını**içeren araç çubuğunu görüntülemek için  > **araç çubuklarını** **görüntüle** > **standardı** ' nı seçin.) 64 bit Windows çalıştırıyorsanız, açılan kutuda **x64** ' ü seçin. 64 bit Windows ayrıca 32 bit uygulamalar çalıştırabilir, bu sayede isterseniz **x86** seçeneğini belirleyebilirsiniz.

### <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Son olarak, [konuşma SDK 'Sı NuGet paketini](https://aka.ms/csspeech/nuget)yükleyip projenizde konuşma SDK 'sına başvurun:

1. **Çözüm Gezgini**, çözümünüze sağ tıklayın ve **çözüm Için NuGet Paketlerini Yönet** ' i seçerek **NuGet-çözüm** penceresine gidin.

1. **Gözat**’ı seçin.

   ![NuGet-çözüm sekmesi, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. **Paket kaynağı**' nda **NuGet.org**' yi seçin.

1. **Arama** kutusuna `Microsoft.CognitiveServices.Speech` girin ve sonra arama sonuçlarında göründükten sonra bu paketi seçin.

   ![Microsoft. Biliveservices. Speech C++ paketi yüklemesi-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. Arama sonuçlarının yanındaki paket durumu bölmesinde **HelloWorld** projenizi seçin.

1. **Yükle**’yi seçin.

1. **Değişiklikleri Önizle** Iletişim kutusunda **Tamam**' ı seçin.

1. **Lisans kabulü** iletişim kutusunda, lisansı görüntüleyin ve **kabul ediyorum**' u seçin. Paket yüklemesi başlar ve yükleme tamamlandığında, **Çıkış** bölmesinde şu metne benzer bir ileti görüntülenir: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.7.0' to helloworld`. 

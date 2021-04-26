---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 7386cfaaf410614c6eeee19669f8f769ad691c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99214338"
---
Evrensel Windows Platformu (UWP) geliştirme için Visual Studio projesi oluşturmak üzere Visual Studio geliştirme seçeneklerini ayarlamanız, projeyi oluşturmanız, hedef mimariyi seçmeniz, ses yakalamayı ayarlamanız ve konuşma SDK 'sını yüklemeniz gerekir.

### <a name="set-up-visual-studio-development-options"></a>Visual Studio geliştirme seçeneklerini ayarlama

Başlamak için, UWP geliştirmesi için Visual Studio 'da doğru şekilde ayarlandığından emin olun:

1. **Başlangıç** penceresini göstermek Için Visual Studio 2019 ' i açın.

   !["Kod olmadan devam et" eylemi vurgulanmış şekilde "Başlat" penceresini gösteren ekran görüntüsü.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Visual Studio IDE 'ye gitmek için **kod olmadan devam et** ' i seçin.

1. Visual Studio menü çubuğunda **Araçlar**' ı  >  açmak için Araçlar **ve Özellikler al** ' ı seçin Visual Studio yükleyicisi açın ve **değiştirme** iletişim kutusunu görüntüleyin.

   !["Evrensel Windows Platformu geliştirme" vurgulanmış "değiştirme" iletişim kutusunun "Iş yükleri" sekmesini gösteren ekran görüntüsü.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. **Iş yükleri** sekmesinde, **Windows** altında **Evrensel Windows platformu geliştirme** iş yükünü bulun. Bu iş yükünün yanındaki onay kutusu zaten seçiliyse, **değiştirme** iletişim kutusunu kapatın ve 6. adıma gidin.

1. **Evrensel Windows platformu geliştirme** onay kutusunu seçin, **Değiştir**' i seçin ve ardından **Başlarken** iletişim kutusunda, UWP geliştirme iş yükünü yüklemek için **devam** ' ı seçin. Yeni özelliğin yüklenmesi biraz zaman alabilir.

1. Visual Studio Yükleyicisi kapatın.

### <a name="create-the-project-and-select-the-target-architecture"></a>Projeyi oluşturma ve hedef mimarisini seçme

Sonra, projenizi oluşturun:

1.   >    >  **Yeni proje oluştur** penceresini göstermek için Visual Studio menü çubuğunda dosya yeni **Proje** ' yi seçin.

   !["Boş uygulama (Evrensel Windows)" seçiliyken ve "Ileri" düğmesinin vurgulandığı "yeni proje oluştur" penceresini gösteren ekran görüntüsü.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Boş uygulama bulun ve seçin **(Evrensel Windows)**. Bu proje türünün C# sürümünü seçtiğinizden emin olun (Visual Basic aksine).

1. **Yeni projeyi Yapılandır** ekranınızı göstermek için **İleri ' yi** seçin.

   !["Proje adı" ve "konum" alanları ve "Oluştur" düğmesinin vurgulandığı "yeni projenizi yapılandırma" ekranını gösteren ekran görüntüsü.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. **Proje adı** alanına girin `helloworld` .

1. **Konum**' a gidin ve projenizi kaydetmek için klasörü seçin veya oluşturun.

1. **Yeni Evrensel Windows platformu projesi** penceresine gitmek için **Oluştur** ' u seçin.

   !["Yeni Evrensel Windows Platformu Projesi" iletişim kutusunu gösteren ekran görüntüsü.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. **En düşük sürüm** (ikinci açılan kutu) altında, **Windows 10 Fall Creators Update (10,0) öğesini seçin. Yapı 16299)**, konuşma SDK 'sı için en düşük gereksinimdir.

1. **Hedef sürümde** (ilk açılan kutu), **En düşük sürümdeki** değerden veya daha sonraki bir değere sahip bir değer seçin.

1. **Tamam**’ı seçin. Visual Studio IDE 'ye geri döndürüyorsunuz, yeni proje oluşturulup **Çözüm Gezgini** bölmesinde görünür.

   ![HelloWorld projesi-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Şimdi hedef platform mimarinizi seçin. Visual Studio araç çubuğunda, **çözüm platformları** açılan kutusunu bulun. (Görmüyorsanız, **Görünüm**  >  ' ü seçin. **Araç çubukları**  >  **Çözüm platformlarını** içeren araç çubuğunu göstermek için **Standart** .) 64 bit Windows çalıştırıyorsanız, açılan kutuda **x64** ' ü seçin. 64 bit Windows ayrıca 32 bit uygulamalar çalıştırabilir, bu sayede isterseniz **x86** seçeneğini belirleyebilirsiniz.

> [!NOTE]
> Konuşma SDK 'Sı, tüm Intel uyumlu işlemcileri destekler, ancak **yalnızca** ARM işlemcilerin x64 sürümleridir.

### <a name="set-up-audio-capture"></a>Ses yakalamayı ayarlama

Projenin ses girişi yakalamaya izin ver:

1. **Çözüm Gezgini**, paket uygulama bildirimini açmak için **Package. appxmanifest** öğesine çift tıklayın.

1. **Özellikler** sekmesini seçin.

   ![Yetenekler sekmesi, paket uygulama bildirimi-Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. **Mikrofon** yeteneğinin kutusunu seçin.

1. Değişikliklerinizi kaydetmek için menü çubuğundan **Dosya**  >  **Kaydet. appxmanifest** ' i seçin.

### <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Son olarak, [konuşma SDK 'Sı NuGet paketini](https://aka.ms/csspeech/nuget)yükleyip projenizde konuşma SDK 'sına başvurun:

1. **Çözüm Gezgini**, çözümünüze sağ tıklayın ve **çözüm Için NuGet Paketlerini Yönet** ' i seçerek **NuGet-çözüm** penceresine gidin.

1. **Gözat**'ı seçin.

   !["Tarayıcı için paketleri Yönet" iletişim kutusunun "tarama" sekmesi, "arama" kutusu ve "paket kaynağı" vurgulanmış şekilde gösterildiği ekran görüntüsü.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. **Paket kaynağı**' nda **NuGet.org**' yi seçin.

1. **Arama** kutusuna girin `Microsoft.CognitiveServices.Speech` ve ardından arama sonuçlarında göründükten sonra bu paketi seçin.

   !["Microsoft. Biliveservices. Speech" seçeneğinin seçili olduğunu ve proje ve "Install" düğmesinin vurgulandığını gösteren ekran görüntüsü.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. Arama sonuçlarının yanındaki paket durumu bölmesinde **HelloWorld** projenizi seçin.

1. **Yükle**'yi seçin.

1. **Değişiklikleri Önizle** Iletişim kutusunda **Tamam**' ı seçin.

1. **Lisans kabulü** iletişim kutusunda, lisansı görüntüleyin ve **kabul ediyorum**' u seçin. Paket yüklemesi başlar ve yükleme tamamlandığında **Çıkış** bölmesinde şu metne benzer bir ileti görüntülenir: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld` .

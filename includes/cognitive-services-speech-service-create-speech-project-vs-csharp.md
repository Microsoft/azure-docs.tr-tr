---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327039"
---
Windows geliştirme için bir Visual Studio projesi oluşturmak için, projeyi oluşturmanız, .NET masaüstü geliştirme için Visual Studio 'yu ayarlamanız, konuşma SDK 'sını yüklemeniz ve hedef mimariyi seçmeniz gerekir.

### <a name="create-the-project-and-add-the-workload"></a>Projeyi oluşturma ve iş yükünü ekleme

Başlamak için Visual Studio 'da projeyi oluşturun ve Visual Studio 'Nun .NET masaüstü geliştirme için ayarlandığından emin olun:

1. Visual Studio 2019 ' i açın.

1. Başlangıç penceresinde **Yeni proje oluştur**' u seçin. 

1. **Yeni proje oluştur** penceresinde **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın** penceresinde, **Proje adı**alanına *HelloWorld* girin, **konum**bölümünde dizin yolunu seçin veya oluşturun ve ardından **Oluştur**' u seçin.

1. Visual Studio menü çubuğunda **araçlar** >  ' i seçin ve Visual Studio yükleyicisi açan ve **değiştirme** iletişim kutusunu görüntüleyen**araçları ve özellikleri alın**.

1. **.Net masaüstü geliştirme** iş yükünün kullanılabilir olup olmadığını denetleyin. İş yükü yüklenmemişse, yanındaki onay kutusunu işaretleyin ve ardından yüklemeyi başlatmak için **Değiştir** ' i seçin. İndirmesi ve yüklenmesi birkaç dakika sürebilir.

   **.Net masaüstü geliştirme** 'nın yanındaki onay kutusu zaten seçiliyse, iletişim kutusundan çıkmak için **Kapat** ' ı seçin.

   ![.NET masaüstü geliştirmesini etkinleştirme](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual Studio Yükleyicisi kapatın.

### <a name="install-the-speech-sdk"></a>Konuşma SDK 'sını yükler

Bir sonraki adım, [konuşma SDK 'Sı NuGet paketini](https://aka.ms/csspeech/nuget)yüklerken kodda başvurabilirsiniz.

1. Çözüm Gezgini, **HelloWorld** projesine sağ tıklayın ve ardından NuGet Paket Yöneticisi ' ni görüntülemek Için **NuGet Paketlerini Yönet** ' i seçin.

   ![NuGet Paket Yöneticisi](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Sağ üst köşede, **paket kaynağı** açılan kutusunu bulun ve **NuGet.org** ' nin seçili olduğundan emin olun.

1. Sol üst köşede, **Araştır**' ı seçin.

1. Arama kutusuna *Microsoft. Biliveservices. Speech* yazın ve **ENTER**' u seçin.

1. Arama sonuçlarından, **Microsoft. Biliveservices. Speech** paketini seçin ve ardından en son kararlı sürümü yüklemek için **yüklenir** ' i seçin.

   ![Microsoft. Biliveservices. Speech NuGet paketini yükler](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Yüklemeyi başlatmak için tüm anlaşmaları ve lisansları kabul edin.

   Paket yüklendikten sonra, **Paket Yöneticisi konsol** penceresinde bir onay görüntülenir.

### <a name="choose-the-target-architecture"></a>Hedef mimariyi seçin

Şimdi, konsol uygulamasını derlemek ve çalıştırmak için bilgisayarınızın mimarisiyle eşleşen bir platform yapılandırması oluşturun.

1. Menü çubuğundan **derleme** > **Configuration Manager**' yi seçin. **Configuration Manager** iletişim kutusu görüntülenir.

   ![Configuration Manager iletişim kutusu](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **Etkin çözüm platformu** açılan kutusunda **Yeni**' yi seçin. **Yeni çözüm platformu** iletişim kutusu görüntülenir.

1. **Yazın veya yeni platform** açılan kutusunu seçin:
   - 64 bit Windows çalıştırıyorsanız **x64**' ü seçin.
   - 32 bit Windows çalıştırıyorsanız, **x86**' yı seçin.

1. **Tamam** ' ı ve ardından **Kapat**' ı seçin.

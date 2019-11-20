---
title: Hızlı başlangıç-Azure uzamsal bağlayıcılarla Unity HoloLens uygulaması oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, uzamsal bağlayıcıları kullanarak Unity ile bir HoloLens uygulaması oluşturmayı öğreneceksiniz.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e8196c9561465930fc9723c9b8f8a0d02668ef5
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114088"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Hızlı başlangıç: Azure uzamsal bağlayıcıları kullanan bir Unity HoloLens uygulaması oluşturma

Bu hızlı başlangıçta, [Azure uzamsal bağlayıcıları](../overview.md)kullanan bir Unity HoloLens uygulaması oluşturacaksınız. Uzamsal bağlantılar, zaman içinde cihazlarda kendi konumlarını kalıcı hale getiremeyen nesneler ile karışık gerçeklik deneyimleri oluşturmanıza olanak tanıyan platformlar arası bir geliştirici hizmetidir. İşiniz bittiğinde, bir uzamsal bağlayıcıyı kaydedip geri çekebilmeniz için Unity ile oluşturulmuş bir HoloLens uygulamanız olacaktır.

Şunları öğrenirsiniz:

- Uzamsal bir bağlayıcı hesabı oluşturun.
- Unity derleme ayarlarını hazırlayın.
- Uzamsal Tutturucuların hesap tanımlayıcısını ve hesap anahtarını yapılandırın.
- HoloLens Visual Studio projesini dışarı aktarın.
- Uygulamayı dağıtın ve bir HoloLens cihazında çalıştırın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

- <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,1</a> veya üzeri ve <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ya da sonraki sürümlerin yüklendiği bir Windows bilgisayar olması gerekir. Visual Studio yüklemenizin **Evrensel Windows platformu geliştirme** iş yükü ve **Windows 10 SDK (10.0.18362.0 veya daha yeni)** bileşenini içermesi gerekir. Ayrıca <a href="https://git-scm.com/download/win" target="_blank">Windows Için git</a>'i de yüklemelisiniz.
- [Geliştirici modunun](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) etkinleştirildiği bir HoloLens cihazına ihtiyacınız vardır. [Windows 10 ekim 2018 güncelleştirmesi](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (Ayrıca RS5 olarak da bilinir) cihazda yüklü olmalıdır. HoloLens 'te en son sürüme güncelleştirmek için **Ayarlar** uygulamasını açın, **güncelleştirme & Güvenliği**' ne gidin ve ardından **Güncelleştirmeleri denetle**' yi seçin.
- Uygulamanızda, **Spatialperception** özelliğini etkinleştirmeniz gerekir. Bu ayar, > **oynatıcı** ayarları > **yayınlama** ayarları > **özellikleri**' nde **yapı ayarları** ' nda bulunur.
- Uygulamanızda, **Windows Mixed REALITY SDK**Ile **desteklenen sanal gerçeklik** 'i etkinleştirmeniz gerekir. Bu ayar, > **XR ayarları** > **oynatıcı ayarları** ' nın **yapı ayarları** ' nda bulunur.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Unity örnek projesini indir ve aç

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

**Dosya** > **derleme ayarları**' nı seçerek **derleme ayarlarını** açın.

**Platform** bölümünde **Evrensel Windows platformu**' yi seçin. **Hedef cihazı** **HoloLens**olarak değiştirin.

Platformu **Evrensel Windows platformu**olarak değiştirmek için **platformu** Değiştir ' i seçin. Unity, eksik olmaları durumunda UWP destek bileşenleri yüklemenizi isteyebilir.

![Unity derleme ayarları penceresi](./media/get-started-unity-hololens/unity-build-settings.png)

**Derleme ayarları** penceresini kapatın.

## <a name="configure-the-account-identifier-and-key"></a>Hesap tanımlayıcıyı ve anahtarı yapılandırın

**Proje** bölmesinde `Assets/AzureSpatialAnchors.Examples/Scenes` ' a gidin ve `AzureSpatialAnchorsBasicDemo.unity` sahne dosyasını açın.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

**Dosya** > **Kaydet**' i seçerek sahneyi kaydedin.

## <a name="export-the-hololens-visual-studio-project"></a>HoloLens Visual Studio projesini dışarı aktarma

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**Oluştur**' u seçin. İletişim kutusunda, HoloLens Visual Studio projesinin dışarı aktarılacağı klasörü seçin.

Dışarı aktarma işlemi tamamlandığında, dışarı aktarılmış HoloLens projesini içeren bir klasör görüntülenir.

## <a name="deploy-the-hololens-application"></a>HoloLens uygulamasını dağıtma

Klasörde, Visual Studio 'da projeyi açmak için **Merhaba ar U3D. sln** öğesine çift tıklayın.

**Çözüm yapılandırmasını** **yayın**olarak değiştirin, **çözüm platformunu** **x86**olarak değiştirin ve dağıtım hedefi seçeneklerinden **cihaz** ' ı seçin.

HoloLens 2 kullanıyorsanız, **x86**yerine **çözüm platformu**olarak **ARM64** kullanın.

   ![Visual Studio yapılandırması](./media/get-started-unity-hololens/visual-studio-configuration.png)

HoloLens cihazını açın, oturum açın ve USB kablosu kullanarak cihazı BILGISAYARA bağlayın.

Uygulamanızı dağıtmak ve hata ayıklamayı başlatmak için hata **ayıklamayı başlat** > **Hata Ayıkla** ' yı seçin.

Bir bağlayıcıyı yerleştirmek ve geri çağırmak için uygulamadaki yönergeleri izleyin.

Visual Studio 'da, **hata ayıklamayı Durdur** veya SHIFT + F5 ' i seçerek uygulamayı durdurun.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Öğretici: cihazlarda uzamsal bağlayıcıları paylaşma](../tutorials/tutorial-share-anchors-across-devices.md)

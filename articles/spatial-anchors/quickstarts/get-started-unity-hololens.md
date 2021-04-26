---
title: 'Hızlı başlangıç: Unity ile bir HoloLens uygulaması oluşturma'
description: Bu hızlı başlangıçta, uzamsal bağlayıcıları kullanarak Unity ile bir HoloLens uygulaması oluşturmayı öğreneceksiniz.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c0cce7717dda73a381ec977c3bf520bf8db5bbb3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105876"
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

- <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> ve <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> veya sonraki bir sürümü yüklü bir Windows bilgisayara ihtiyacınız vardır. **Unity 2020 LTS** 'YI asa SDK 'sı sürüm 2,9 veya üzeri ( [Unity XR eklenti çerçevesini](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)kullanan) ya da asa SDK 'sı 2,8 veya daha önceki bir sürümüyle **Unity 2019 LTS** kullanın. Visual Studio yüklemenizin **Evrensel Windows platformu geliştirme** iş yükü ve **Windows 10 SDK (10.0.18362.0 veya daha yeni)** bileşenini içermesi gerekir. Ayrıca, Windows ve <a href="https://git-lfs.github.com/">GIT LFS</a> <a href="https://git-scm.com/download/win" target="_blank">için git</a> 'i de yüklemelisiniz.
- [Geliştirici modunun](/windows/mixed-reality/using-visual-studio) etkinleştirildiği bir HoloLens cihazına ihtiyacınız vardır. Cihazda [Windows 10 mayıs 2020 güncelleştirmesi](/windows/mixed-reality/whats-new/release-notes-may-2020) yüklü olmalıdır. HoloLens 'te en son sürüme güncelleştirmek için **Ayarlar** uygulamasını açın, **güncelleştirme & güvenliği**' ne gidin ve ardından **Güncelleştirmeleri denetle**' yi seçin.
- Uygulamanızda, **Spatialperception** özelliğini etkinleştirmeniz gerekir. Bu ayar, **derleme ayarları**  >  **oynatıcı ayarları**  >  **Yayımlama ayarları**  >  **özellikleri**' nde bulunur.
- Uygulamanızda, **Windows Mixed REALITY SDK** Ile **desteklenen sanal gerçeklik** 'i etkinleştirmeniz gerekir. Bu ayar, **derleme ayarları**  >  **oynatıcı ayarları**  >  **XR ayarları**' nda bulunur.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Unity örnek projesini indir ve aç

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

HoloLens platformu için gereken ASA SDK paketlerini indirmek ve içeri aktarmak için [buradaki](../how-tos/setup-unity-project.md#download-asa-packages) yönergeleri izleyin.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

**Dosya** derleme ayarları ' nı seçerek **derleme ayarlarını** açın  >  .

**Platform** bölümünde **Evrensel Windows platformu**' yi seçin. **Hedef cihazı** **HoloLens** olarak değiştirin.

Platformu **Evrensel Windows platformu** olarak değiştirmek için **platformu** Değiştir ' i seçin. Unity, eksik olmaları durumunda UWP destek bileşenleri yüklemenizi isteyebilir.

![Unity derleme ayarları penceresi](./media/get-started-unity-hololens/unity-build-settings.png)

**Derleme ayarları** penceresini kapatın.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>HoloLens Visual Studio projesini dışarı aktarma

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**Oluştur**' u seçin. İletişim kutusunda, HoloLens Visual Studio projesinin dışarı aktarılacağı klasörü seçin.

Dışarı aktarma işlemi tamamlandığında, dışarı aktarılmış HoloLens projesini içeren bir klasör görüntülenir.

## <a name="deploy-the-hololens-application"></a>HoloLens uygulamasını dağıtma

Klasörde, Visual Studio 'da projeyi açmak için **Merhaba ar U3D. sln** öğesine çift tıklayın.

**Çözüm yapılandırmasını** **yayın** olarak değiştirin, **çözüm platformunu** **x86** olarak değiştirin ve dağıtım hedefi seçeneklerinden **cihaz** ' ı seçin.

HoloLens 2 kullanıyorsanız, **x86** yerine **çözüm platformu** olarak **ARM64** kullanın.

   ![Visual Studio yapılandırması](./media/get-started-unity-hololens/visual-studio-configuration.png)

HoloLens cihazını açın, oturum açın ve USB kablosu kullanarak cihazı BILGISAYARA bağlayın.

  >  Uygulamanızı dağıtmak ve hata ayıklamayı başlatmak için hata **ayıklamayı Başlat** ' ı seçin.

Uygulamada, okları kullanarak **Basicdemo** ' i seçin ve git ' e basın **!** gösteriyi çalıştırmak için düğme. Bir bağlayıcıyı yerleştirmek ve geri çağırmak için yönergeleri izleyin.

![Ekran görüntüsü 1 ](./media/get-started-unity-hololens/screenshot-1.jpg)
 ![ ekran görüntüsü 2 ](./media/get-started-unity-hololens/screenshot-2.jpg)
 ![ ekran görüntüsü 3 ](./media/get-started-unity-hololens/screenshot-3.jpg)
 ![ ekran görüntüsü 4](./media/get-started-unity-hololens/screenshot-4.jpg)

Visual Studio 'da, **hata ayıklamayı Durdur** veya SHIFT + F5 ' i seçerek uygulamayı durdurun.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Öğretici: cihazlarda uzamsal bağlayıcıları paylaşma](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Nasıl yapılır: Unity projesinde Azure uzamsal bağlayıcılarını yapılandırma](../how-tos/setup-unity-project.md)
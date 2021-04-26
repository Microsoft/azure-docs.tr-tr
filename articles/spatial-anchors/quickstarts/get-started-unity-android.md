---
title: 'Hızlı başlangıç: Unity Android uygulaması oluşturma'
description: Bu hızlı başlangıçta, uzamsal bağlayıcıları kullanarak Unity ile bir Android uygulaması oluşturmayı öğreneceksiniz.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4f4bfdb5d09da6a9967b53ca56bb2491976592a1
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105893"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Hızlı başlangıç: Azure uzamsal bağlayıcılarla Unity Android uygulaması oluşturma

Bu hızlı başlangıçta, [Azure uzamsal bağlayıcılarını](../overview.md)kullanarak Unity Android uygulamasının nasıl oluşturulacağı ele alınmaktadır. Azure uzamsal bağlantıları, zaman içinde cihazlarda konumlarını sürekli tutan nesneleri kullanarak karma gerçeklik deneyimleri oluşturmanıza olanak sağlayan bir platformlar arası geliştirici hizmetidir. İşiniz bittiğinde, bir uzamsal bağlayıcıyı kaydedip geri çekebilmeniz için Unity ile oluşturulmuş bir ARCore Android uygulamanız olacaktır.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Uzamsal bağlayıcı hesabı oluşturma
> * Unity derleme ayarlarını hazırlama
> * Uzamsal Tutturucuların hesap tanımlayıcısını ve hesap anahtarını yapılandırın
> * Android Studio projeyi dışarı aktarma
> * Android cihazında dağıtma ve çalıştırma

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakileri yaptığınızdan emin olun:

- **Android SDK & NDK araçları** ve **OpenJDK** modülleriyle **Android derleme desteği** de dahil olmak üzere <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a>içeren bir Windows veya MacOS makinesi. **Unity 2020 LTS** 'YI asa SDK 'sı sürüm 2,9 veya üzeri ( [Unity XR eklenti çerçevesini](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)kullanan) ya da asa SDK 'sı 2,8 veya daha önceki bir sürümüyle **Unity 2019 LTS** kullanın.
  - Windows üzerinde çalışıyorsa, Windows ve <a href="https://git-lfs.github.com/">GIT LFS</a> <a href="https://git-scm.com/download/win" target="_blank">için git</a> de gerekir.
  - MacOS üzerinde çalışıyorsa, HomeBrew aracılığıyla Git ' i alın. Terminalin tek bir satırına şu komutu girin: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Ardından, ve öğesini çalıştırın `brew install git` `brew install git-lfs` .
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">Geliştirici etkin</a> ve <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">korumalı</a> bir Android cihaz.
  - Bilgisayarınızın Android cihazından iletişim kurması için ek cihaz sürücüleri gerekebilir. Daha fazla bilgi ve yönergeler için [buraya](https://developer.android.com/studio/run/device.html)bakın.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Unity örnek projesini indir ve aç

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Android platformu için gereken ASA SDK paketlerini indirmek ve içeri aktarmak için [buradaki](../how-tos/setup-unity-project.md#download-asa-packages) yönergeleri izleyin.

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Android Studio projeyi dışarı aktarma

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**Cihazı Çalıştır** bölümünde seçin ve ardından **Oluştur ve Çalıştır**' ı seçin. `.apk`İçin herhangi bir ad seçebileceğiniz bir dosyayı kaydetmeniz istenecektir.

Uygulamada, okları kullanarak **Basicdemo** ' i seçin ve git ' e basın **!** gösteriyi çalıştırmak için düğme. Bir bağlayıcıyı yerleştirmek ve geri çağırmak için yönergeleri izleyin.

![Ekran görüntüsü 1 ](./media/get-started-unity-android/screenshot-1.jpg)
 ![ ekran görüntüsü 2 ](./media/get-started-unity-android/screenshot-2.jpg)
 ![ ekran görüntüsü 3](./media/get-started-unity-android/screenshot-3.jpg)

Bir bağlayıcıyı yerleştirmek ve geri çağırmak için uygulamadaki yönergeleri izleyin.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="rendering-issues"></a>İşleme sorunları

Uygulamayı çalıştırırken, kamerayı arka plan olarak görmüyorsanız (örneğin, boş, mavi veya başka bir doku görürseniz), muhtemelen varlıkları Unity 'de yeniden içeri aktarmanız gerekir. Uygulamayı durdurun. Unity 'deki üst menüden **varlıklar-> tümünü yeniden al**' ı seçin. Sonra, uygulamayı yeniden çalıştırın.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Öğretici: cihazlarda uzamsal bağlayıcıları paylaşma](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Nasıl yapılır: Unity projesinde Azure uzamsal bağlayıcılarını yapılandırma](../how-tos/setup-unity-project.md)

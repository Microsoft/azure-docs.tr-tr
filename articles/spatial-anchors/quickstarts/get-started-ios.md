---
title: Hızlı başlangıç-Azure uzamsal bağlayıcılarla iOS uygulaması oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, uzamsal bağlayıcıları kullanarak bir iOS uygulaması oluşturmayı öğreneceksiniz.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: a1d2fa083c0083423767484b20e296a3080e4ebe
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092109"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Hızlı başlangıç: Azure uzamsal bağlayıcılarla, Swift veya amaç-C ' d a iOS uygulaması oluşturma

Bu hızlı başlangıçta, Swift veya amaç-C ' d e [Azure uzamsal bağlayıcılarını](../overview.md) kullanarak bir iOS uygulamasının nasıl oluşturulacağı ele alınmaktadır. Azure uzamsal bağlantıları, zaman içinde cihazlarda konumlarını sürekli tutan nesneleri kullanarak karma gerçeklik deneyimleri oluşturmanıza olanak sağlayan bir platformlar arası geliştirici hizmetidir. İşiniz bittiğinde, bir uzamsal bağlayıcıyı kaydedip geri çekebilmeniz için bir ARKit iOS uygulamasına sahip olacaksınız.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Uzamsal bağlayıcı hesabı oluşturma
> * Uzamsal Tutturucuların hesap tanımlayıcısını ve hesap anahtarını yapılandırın
> * İOS cihazında dağıtma ve çalıştırma

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakileri yaptığınızdan emin olun:

- En son <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> ve <a href="https://cocoapods.org" target="_blank">Cocoapods</a> sürümü yüklü olan MacOS makinesi özellikli bir geliştirici.
- Git, HomeBrew aracılığıyla yüklendi. Terminalin tek bir satırına şu komutu girin: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ardından `brew install git`çalıştırın.
- Bir geliştirici, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit uyumlu</a> bir iOS cihazını etkinleştirdi.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Örnek projeyi açın

Aşağıdaki eylemleri gerçekleştirmek için terminali kullanın.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Cocoapods kullanarak gerekli Pod 'yi yükler:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

`iOS/Swift/` sayfasına gidin.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

`iOS/Objective-C/` sayfasına gidin.

```bash
cd ./iOS/Objective-C/
```

---

Proje için CocoaPods yüklemek üzere `pod install --repo-update` çalıştırın.

Şimdi `.xcworkspace` Xcode 'da açın.

> [!NOTE]
> MacOS Catalina 'e yükselttikten sonra CocoaPod sorunları [yaşıyorsanız, sorun giderme adımlarına bakın](#cocoapods-issues-on-macos-catalina-1015) (10,15).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Hesap tanımlayıcıyı ve anahtarı yapılandırın

Bir sonraki adım, uygulamayı hesap tanımlayıcıyı ve hesap anahtarınızı kullanacak şekilde yapılandırmaktır. [Uzamsal Tutturucuların kaynağını ayarlarken](#create-a-spatial-anchors-resource)bunları bir metin düzenleyicisine kopyaladınız.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Açık `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

`spatialAnchorsAccountKey` alanını bulun ve `Set me` hesap anahtarıyla değiştirin.

`spatialAnchorsAccountId` alanını bulun ve `Set me` hesap tanımlayıcısı ile değiştirin.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Açık `iOS/Objective-C/SampleObjC/BaseViewController.m`.

`SpatialAnchorsAccountKey` alanını bulun ve `Set me` hesap anahtarıyla değiştirin.

`SpatialAnchorsAccountId` alanını bulun ve `Set me` hesap tanımlayıcısı ile değiştirin.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Uygulamayı iOS cihazınıza dağıtma

İOS cihazını Mac 'e bağlayın ve **etkin düzeni** iOS cihazınıza ayarlayın.

![Cihazı seçin](./media/get-started-ios/select-device.png)

**Oluştur ' u seçin ve ardından geçerli düzeni çalıştırın**.

![Dağıt ve Çalıştır](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> `library not found for -lPods-SampleObjC` bir hata görürseniz, büyük olasılıkla `.xcworkspace`yerine `.xcodeproj` dosyasını açtınız. `.xcworkspace` açın ve yeniden deneyin.

Xcode 'da **Durdur**' a basarak uygulamayı durdurun.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>MacOS Catalina CocoaPods sorunlar (10,15)

Yakın zamanda macOS Catalina (10,15) ' e güncelleştirdiyseniz ve önceden CocoaPods yüklüyse, CocoaPods bozuk bir durumda olabilir ve dizin ve `.xcworkspace` proje dosyalarınızı düzgün şekilde yapılandıramadı. Bu sorunu çözmek için aşağıdaki komutları çalıştırarak CocoaPods öğesini yeniden yüklemeniz gerekir:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Öğretici: cihazlarda uzamsal bağlayıcıları paylaşma](../tutorials/tutorial-share-anchors-across-devices.md)

---
title: Hızlı başlangıç-Azure uzamsal bağlayıcılarla Android uygulaması oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, uzamsal bağlayıcıları kullanarak bir Android uygulaması oluşturmayı öğreneceksiniz.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 248b66a928d373678d3058be488e226ee51bbcae
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092138"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Hızlı başlangıç: Azure uzamsal bağlayıcılarla bir Android uygulaması oluşturma

Bu hızlı başlangıçta, Java veya C++/Ndk'teki [Azure uzamsal bağlayıcılarını](../overview.md) kullanarak bir Android uygulamasının nasıl oluşturulacağı ele alınmaktadır. Azure uzamsal bağlantıları, zaman içinde cihazlarda konumlarını sürekli tutan nesneleri kullanarak karma gerçeklik deneyimleri oluşturmanıza olanak sağlayan bir platformlar arası geliştirici hizmetidir. İşiniz bittiğinde, bir uzamsal bağlayıcıyı kaydedip geri çekebilmeniz için bir ARCore Android uygulamanız olacaktır.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Uzamsal bağlayıcı hesabı oluşturma
> * Uzamsal Tutturucuların hesap tanımlayıcısını ve hesap anahtarını yapılandırın
> * Android cihazında dağıtma ve çalıştırma

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakileri yaptığınızdan emin olun:

- <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>Içeren bir Windows veya MacOS makinesi.
  - Windows üzerinde çalışıyorsa, <a href="https://git-scm.com/download/win" target="_blank">Windows Için git</a>de gerekecektir.
  - MacOS üzerinde çalışıyorsa, HomeBrew aracılığıyla Git ' i alın. Terminalin tek bir satırına şu komutu girin: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Ardından `brew install git`çalıştırın.
  - NDK örneğini oluşturmak için, Android Studio ' de NDK ve CMake 3,6 veya daha büyük SDK Tools yüklemeniz gerekir.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">Geliştirici etkin</a> ve <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">korumalı</a> bir Android cihaz.
  - Bilgisayarınızın Android cihazından iletişim kurması için ek cihaz sürücüleri gerekebilir. Ek bilgi ve yönergeler için [buraya](https://developer.android.com/studio/run/device.html) bakın.
- Uygulamanızın ARCore **1.11.0**'ı hedeflemesi gerekir.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Örnek projeyi açın

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

`arcore_c_api.h` [buradan](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) indirin ve `Android\NDK\libraries\include`yerleştirin.

Yeni kopyalanan deponun içinden aşağıdaki komutu çalıştırarak alt modülleri başlatın:

```console
git submodule update --init --recursive
```

---

Android Studio’yu açın.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

**Mevcut bir Android Studio projesini aç** ' ı seçin ve `Android/Java/`bulunan projeyi seçin.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

**Mevcut bir Android Studio projesini aç** ' ı seçin ve `Android/NDK/`bulunan projeyi seçin.

---

## <a name="configure-account-identifier-and-key"></a>Hesap tanımlayıcıyı ve anahtarı yapılandırın

Bir sonraki adım, uygulamayı hesap tanımlayıcıyı ve hesap anahtarınızı kullanacak şekilde yapılandırmaktır. [Uzamsal Tutturucuların kaynağını ayarlarken](#create-a-spatial-anchors-resource)bunları bir metin düzenleyicisine kopyaladınız.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Açık `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

`SpatialAnchorsAccountKey` alanını bulun ve `Set me` hesap anahtarıyla değiştirin.

`SpatialAnchorsAccountId` alanını bulun ve `Set me` hesap tanımlayıcısı ile değiştirin.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Açık `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

`SpatialAnchorsAccountKey` alanını bulun ve `Set me` hesap anahtarıyla değiştirin.

`SpatialAnchorsAccountId` alanını bulun ve `Set me` hesap tanımlayıcısı ile değiştirin.

---

## <a name="deploy-the-app-to-your-android-device"></a>Uygulamayı Android cihazınıza dağıtma

Android cihazda güç açın, oturum açın ve USB kablosu kullanarak BILGISAYARA bağlayın.

Android Studio araç çubuğundan **Çalıştır** ' ı seçin.

![Android Studio Dağıt ve Çalıştır](./media/get-started-android/android-studio-deploy-run.png)

**Dağıtım hedefi seç** Iletişim kutusunda Android cihazını seçin ve uygulamayı Android cihazında çalıştırmak için **Tamam** ' ı seçin.

Bir bağlayıcıyı yerleştirmek ve geri çağırmak için uygulamadaki yönergeleri izleyin.

Android Studio araç çubuğundan **Durdur** ' i seçerek uygulamayı durdurun.

![Android Studio durdur](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Öğretici: cihazlarda uzamsal bağlayıcıları paylaşma](../tutorials/tutorial-share-anchors-across-devices.md)

---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882294"
---
## <a name="androidtabandroid"></a>[Android](#tab/Android)

Java Android örneği cihazlar arasında paylaşmayı destekler.
Dosya `SharedActivity.java` Android Studio ' deki örnekler klasöründen açın. Önceki adımda edindiğiniz URL 'yi (ASP.NET Web uygulaması Azure dağıtımından) `SharedActivity.java` dosyasında `SharingAnchorsServiceUrl` değer olarak girin. URL 'deki `index.html` `api/anchors`ile değiştirin. Şöyle görünmelidir: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="iostabios"></a>[iOS](#tab/iOS)

Amaç-C iOS örneği, cihazlar arasında paylaşmayı destekler.
Samples klasöründeki dosya `SharedDemoViewController.m` açın. Önceki adımda edindiğiniz URL 'yi (ASP.NET Web uygulaması Azure dağıtımından) `SharedActivity.java` dosyasında `SharingAnchorsServiceUrl` değer olarak girin. URL 'deki `index.html` `api/anchors`ile değiştirin. Şöyle görünmelidir: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarintabxamarin"></a>[Xamarin](#tab/Xamarin)

Hem Xamarin Android hem de iOS örnekleri cihazlar arasında paylaşmayı destekler.
Samples klasöründeki dosya `AccountDetails.cs` açın. Önceki adımda edindiğiniz URL 'yi (ASP.NET Web uygulaması Azure dağıtımından) `SharedActivity.java` dosyasında `AnchorSharingServiceUrl` değer olarak girin. URL 'deki `index.html` `api/anchors`ile değiştirin. Şöyle görünmelidir: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unitytabunity"></a>['Yi](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android cihazı ayarlama

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>İOS cihazı ayarlama

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Hesap tanımlayıcıyı ve anahtarı yapılandırın

**Proje** bölmesinde `Assets/AzureSpatialAnchorsPlugin/Examples` ' a gidin ve `AzureSpatialAnchorsLocalSharedDemo.unity` sahne dosyasını açın.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**Proje** bölmesinde `Assets\AzureSpatialAnchors.Examples\Resources`' a gidin. `SpatialAnchorSamplesConfig` öğesini seçin. Ardından, **Inspector** bölmesinde, `index.html` `api/anchors`ile değiştirerek `Base Sharing Url`değeri olarak `Sharing Anchors Service url` (ASP.NET Web App Azure dağıtımından) girin. Şöyle görünmelidir: `https://<app_name>.azurewebsites.net/api/anchors`.

**Dosya** > **Kaydet**' i seçerek sahneyi kaydedin.

## <a name="deploy-to-your-device"></a>Cihazınıza dağıtma

### <a name="deploy-to-android-device"></a>Android cihazına dağıtma

Android cihazınızda oturum açın ve USB kablosu kullanarak bilgisayarınıza bağlayın.

**Dosya** > **derleme ayarları**' nı seçerek **derleme ayarlarını** açın.

**Yapı**' ın altında, tüm sahnelerin yanında onay işareti olduğundan emin olun.

**Dışarı aktarma projesinin** onay işareti olmadığından emin olun. **Oluştur ve Çalıştır ' ı**seçin. `.apk` dosyanızı kaydetmeniz istenecektir. Bunun için herhangi bir ad seçebilirsiniz.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>İOS cihazına dağıtma

**Dosya** > **derleme ayarları**' nı seçerek **derleme ayarlarını** açın.

**Yapı**' ın altında, tüm sahnelerin yanında onay işareti olduğundan emin olun.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Xcode 'da **Durdur**' u seçerek uygulamayı durdurun.

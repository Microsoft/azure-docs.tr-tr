---
title: 'Hızlı Başlangıç: Tam ekran okuyucuyu (Swift) başlatan bir iOS uygulaması oluşturma'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, sıfırdan bir iOS uygulaması oluşturacaksınız ve tam ekran okuyucu işlevselliği eklersiniz.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899524"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Hızlı Başlangıç: Tam ekran okuyucuyu (Swift) başlatan bir iOS uygulaması oluşturma

[Tam ekran okuyucu](https://www.onenote.com/learningtools) , okuma kavramasını geliştirmek için kendini kanıtlamış teknikler uygulayan, ve dahil tasarlanmış bir araçtır.

Bu hızlı başlangıçta, sıfırdan bir iOS uygulaması oluşturacaksınız ve modern Okuyucu SDK 'sını kullanarak tam ekran okuyucuyu tümleştirirsiniz. Bu hızlı başlangıç için tam bir çalışma örneğine [buradan](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift)ulaşabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Azure Active Directory (Azure AD) kimlik doğrulaması için yapılandırılmış bir tam ekran okuyucu kaynağı. Kurulumunu yapmak için [Bu yönergeleri](./azure-active-directory-authentication.md) izleyin. Örnek proje özellikleri yapılandırılırken burada oluşturulan bazı değerler gerekir. Daha sonra başvurmak üzere oturumunuzun çıkışını bir metin dosyasına kaydedin.

## <a name="create-an-xcode-project"></a>Xcode projesi oluşturma

Xcode 'da yeni bir proje oluşturun.

![Yeni Proje](./media/ios/xcode-create-project.png)

**Tek görünüm uygulaması**' nı seçin.

![Yeni tek görünüm uygulaması](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>SDK CocoaPod edinme
Tam ekran okuyucu SDK 'sını kullanmanın en kolay yolu CocoaPods aracılığıyla yapılır. Cocoapods aracılığıyla yüklemek için:
1. [Install Cocoapods](http://guides.cocoapods.org/using/getting-started.html) -Cocoapods yüklemek için Başlarken Kılavuzunu izleyin.
2. Xcode projenizin kök dizininde çalıştırarak `pod init` bir pod dosyası oluşturun.
3.  CocoaPod öğesini ekleyerek `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`Pod dosyanıza ekleyin. Aşağıdaki gibi görünmelidir ve hedefin adı, hızlı başlangıç-Swift ' ı değiştiriyor:
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. Terminalde, Xcode projenizin dizininde, modern Okuyucu SDK Pod 'u yüklemek için komutunu `pod install` çalıştırın.
5. SDK `import immersive_reader_sdk` 'ya başvurması gereken tüm dosyalara ekleyin.
6. `.xcworkspace` Dosya`.xcodeproj` değil, dosyayı açarak projeyi açmanız emin olun.

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD kimlik doğrulaması belirteci alma

Bu bölüm için yukarıdaki Azure AD kimlik doğrulama yapılandırması önkoşul adımından bazı değerlere ihtiyacınız vardır. Bu oturumu kaydettiğiniz metin dosyasına geri bakın.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

ViewController. Swift dosyasını içeren ana proje klasöründe, sabitler. Swift adlı bir Swift sınıfı dosyası oluşturun. Sınıfı aşağıdaki kodla değiştirin ve uygun olduğunda değerlerinize ekleyin. Bu dosyayı yalnızca makinenizde bulunan yerel bir dosya olarak tutun ve bu dosyayı kaynak denetimine kaydetmediğinden emin olun. bu dosya, genel hale getirilmemelidir. Uygulamanızda gizli dizileri tutmanız önerilir. Bunun yerine, belirteci almak için bir arka uç hizmeti kullanmanızı öneririz; burada gizli dizi, uygulamanın dışında ve cihazın dışında tutulabilir. Yetkisiz kullanıcıların, bir dizi kimlik doğrulama (örneğin, [OAuth](https://oauth.net/2/)) arkasında güvenli hale getirilmesi gerekir, bu da yetkisiz kullanıcıların, tam ekran okuyucusu hizmetinize ve faturalandırmaya yönelik belirteçleri kullanmasını önler; Bu iş, bu hızlı başlangıç kapsamının ötesinde.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Uygulamayı görsel taslak olmadan çalışacak şekilde ayarlama

AppDelegate. Swift dosyasını açın ve dosyayı aşağıdaki kodla değiştirin.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>Başlatma görünümü denetleyicisini oluşturma ve örnek içerik ekleme

ViewController. Swift 'ı LaunchViewController. Swift olarak yeniden adlandırıp dosyayı aşağıdaki kodla değiştirin.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Uygulamayı derleme ve çalıştırma

Bir simülatör veya cihaz hedefi seçerek Xcode 'da arşiv şemasını ayarlayın.
![Arşiv şeması](./media/ios/xcode-archive-scheme.png)<br/>
![Hedef seçin](./media/ios/xcode-select-target.png)

Xcode 'da, CTRL + R tuşlarına basın veya projeyi çalıştırmak için Yürüt düğmesine tıklayın ve uygulamanın belirtilen simülatör veya cihazda başlatılması gerekir.

Uygulamanızda şunları görmeniz gerekir:

![Örnek uygulama](./media/ios/sample-app-ipad.png)

"Modern okuyucu" düğmesine tıkladığınızda, modern okuyucu uygulamasının uygulama içeriğiyle birlikte başlatılan olduğunu görürsünüz.

![Tam Ekran Okuyucu](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Modern okuyucu IOS SDK 'sını](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) ve [tam ekran okuyucu iOS SDK başvurusunu](./ios-reference.md) keşfet

---
title: "Hızlı başlangıç: Microsoft 'a iOS veya macOS uygulamasına oturum açma ekleme | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir iOS veya macOS uygulamasının kullanıcılara nasıl oturum açmasını, Microsoft Identity platformundan bir erişim belirteci almasını ve Microsoft Graph API 'sini çağırabileceğinizi öğrenin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: d2c5af6aeccfbae0851513ff575bde3c39e3ca5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103796"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Hızlı başlangıç: Kullanıcı oturum açma ve iOS veya macOS uygulamasından Microsoft Graph API 'sini çağırma

Bu hızlı başlangıçta, yerel bir iOS veya macOS uygulamasının kullanıcılara nasıl oturum açıp Microsoft Graph API 'sini çağırmak için bir erişim belirteci edindiğini gösteren bir kod örneği indirip çalıştırırsınız.

Hızlı başlangıç, hem iOS hem de macOS uygulamaları için geçerlidir. Bazı adımlar yalnızca iOS uygulamaları için gereklidir ve bu şekilde belirtilecektir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* XCode 10 +
* iOS 10 +
* macOS 10.12 +

## <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı

![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı kaydetme ve indirme
> Hızlı başlangıç uygulamanızı başlatmak için kullanabileceğiniz iki seçenek vardır:
> * [Hızlı] [1. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırma ve ardından kod örneğinizi indirme](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [El ile] [2. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>1. seçenek: uygulamanızı kaydedin ve otomatik olarak yapılandırın ve ardından kod örneğini indirin
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
> Uygulamanızı kaydetmek için
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs" target="_blank">Azure portal uygulama kayıtları</a> hızlı başlangıç deneyimine gidin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
> 1. Yönergeleri izleyerek yeni uygulamanızı yalnızca tek tıklamayla indirin ve otomatik olarak yapılandırın.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma
>
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze el ile eklemek için şu adımları izleyin:
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.    
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. Uygulamanız için bir **ad** girin. Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
> 1. **Kaydet**’i seçin.
> 1. **Yönet**' in altında **kimlik doğrulama**  >  **platformu Ekle**  >  **iOS**' u seçin.
> 1. Uygulamanız için **paket kimliğini** girin. Paket tanıtıcısı, uygulamanızı benzersiz bir şekilde tanımlayan benzersiz bir dizedir (örneğin,) `com.<yourname>.identitysample.MSALMacOS` . Kullandığınız değeri bir yere getirin. İOS yapılandırmasının macOS uygulamaları için de geçerli olduğunu unutmayın.
> 1. Bu hızlı başlangıçta yapılandırma ve **msal yapılandırma** ayrıntılarını Kaydet ' **i seçin.**
> 1. **Bitti** seçeneğini belirleyin.

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>1. Adım: Uygulamanızı yapılandırma
> Bu hızlı başlangıçta çalışması için kod örneği için, auth broker ile uyumlu bir **yeniden yönlendirme URI 'si** ekleyin.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişikliği benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-ios/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış
>
> #### <a name="step-2-download-the-sample-project"></a>2. Adım: örnek projeyi Indirme
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [İOS için kod örneğini indirin]()
>
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [MacOS için kod örneğini indirin]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>2. Adım: örnek projeyi Indirme
>
> - [İOS için kod örneğini indirin](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [MacOS için kod örneğini indirin](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>3. Adım: bağımlılıkları yüklemeyi

Bir Terminal penceresinde indirilen kod örneğini içeren klasöre gidin ve `pod install` en son msal kitaplığını yüklemek için çalıştırın.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>4. Adım: uygulamanız yapılandırıldı ve çalıştırılmaya hazırlanıyor
> Projenizi uygulamanızın özelliklerinin değerleriyle yapılandırdık ve çalıştırılmaya hazırlanıyor.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>4. Adım: projenizi yapılandırma
> Yukarıdaki 1. seçeneği belirlediyseniz, bu adımları atlayabilirsiniz.
> 1. Zip dosyasını ayıklayın ve projeyi XCode’da açın.
> 1. **ViewController. Swift** 'u düzenleyin ve ' Let Kclitıd ' ile başlayan satırı aşağıdaki kod parçacığı ile değiştirin. `kClientID`Uygulamanızı bu hızlı başlangıçta daha önce portala kaydettiğinizde kaydettiğiniz ClientID değeriyle güncelleştirmeyi unutmayın:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. [Azure AD Ulusal bulutları](/graph/deployments#app-registration-and-token-service-root-endpoints)için bir uygulama oluşturuyorsanız, ' Let kGraphEndpoint ' ve ' Let kAuthority ' ile başlayan satırı doğru uç noktalarla değiştirin. Genel erişim için varsayılan değerleri kullanın:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Diğer uç noktalar [burada](/graph/deployments#app-registration-and-token-service-root-endpoints)belgelenmiştir. Örneğin, Azure AD Almanya ile hızlı başlangıcı çalıştırmak için aşağıdakileri kullanın:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Proje ayarlarını açın. **Kimlik** bölümünde, portala girdiğiniz **paket kimliğini** girin.
> 1. **Info. plist** ' e sağ tıklayıp kaynak **kodu olarak aç**' ı seçin  >  .
> 1. Dict kök düğümünün altında, öğesini `Enter_the_bundle_Id_Here` portalda kullandığınız ***paket kimliğiyle*** değiştirin.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Uygulamayı oluşturun & çalıştırın!

## <a name="more-information"></a>Daha Fazla Bilgi

Bu hızlı başlangıç hakkında daha fazla bilgi almak için şu bölümleri okuyun.

### <a name="get-msal"></a>MSAL al

MSAL ([msal. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)), kullanıcıların oturum açmasını ve Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri istemesini sağlamak için kullanılan kitaplıktır. Aşağıdaki işlemi uygulayarak uygulamanıza MSAL ekleyebilirsiniz:

```
$ vi Podfile

```
Aşağıdakini Bu Pod dosyasına ekleyin (projenizin hedefi ile):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

CocoaPods yükleme komutunu çalıştır:

```pod install```

### <a name="initialize-msal"></a>MSAL Başlat

Şu kodu ekleyerek MSAL başvurusunu ekleyebilirsiniz:

```swift
import MSAL
```

Sonra şu kodu kullanarak MSAL'yi başlatın:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Konum: | Description |
> |---------|---------|
> | `clientId` | *portal.azure.com*’da kaydedilen uygulamanın Uygulama Kimliği |
> | `authority` | Microsoft Identity platformu. Çoğu durumda bu durum `https://login.microsoftonline.com/common` |
> | `redirectUri` | Uygulamanın yeniden yönlendirme URI 'SI. Varsayılan değeri veya özel yeniden yönlendirme URI 'nizi kullanmak için ' Nil ' geçirebilirsiniz. |

### <a name="for-ios-only-additional-app-requirements"></a>Yalnızca iOS için, ek uygulama gereksinimleri

Uygulamanızda de şunlar olmalıdır `AppDelegate` . Bu, kimlik doğrulaması yaparken MSAL SDK 'sının auth Broker uygulamasından belirteç yanıtı işlemesini sağlar.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> İOS 13 ' de yerine benimsediğinizde, `UISceneDelegate` `UIApplicationDelegate` Bu kodu `scene:openURLContexts:` geri aramaya yerleştirin ( [Apple belgelerine](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)bakın).
> Daha eski iOS ile uyumluluk için hem UISceneDelegate hem de Uıapplicationdelegate 'i destekleriyseniz, MSAL geri çağrısının her iki yere de yerleştirilmesi gerekir.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Son olarak, uygulamanızın `LSApplicationQueriesSchemes` ***Info. plist*** dosyasında ile birlikte bir girişi olması gerekir `CFBundleURLTypes` . Örnek, bu dahil edilmiştir.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Kullanıcı & istek belirteçlerinde oturum açma

Belirteç almak için MSAL’in iki yöntemi vardır: `acquireToken` ve `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: bir belirteci etkileşimli olarak alın

Bazı durumlar, kullanıcıların Microsoft Identity platformu ile etkileşime girmesini gerektirir. Bu durumlarda, son kullanıcının hesabını seçmesini, kimlik bilgilerini girmesi veya uygulamanızın izinlerine izin vermesini gerekli olabilir. Örneğin,

* Kullanıcılar uygulamada ilk kez oturum açtığında
* Kullanıcı parolasını sıfırlarsa, kimlik bilgilerini girmeleri gerekir
* Uygulamanız bir kaynağa ilk kez erişim isteğinde bulunduğunda
* MFA veya diğer koşullu erişim ilkeleri gerektiğinde

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Konum:| Description |
> |---------|---------|
> | `scopes` | İstenen kapsamları içerir (yani `[ "user.read" ]` Microsoft Graph veya `[ "<Application ID URL>/scope" ]` özel Web API 'leri için ( `api://<Application ID>/access_as_user` ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: erişim belirtecini sessizce al

Uygulamalar, kullanıcıların her belirteç istediklerinde oturum açmasını gerektirmemelidir. Kullanıcı zaten oturum açmışsa, bu yöntem uygulamaların belirteçleri sessizce istemesine izin verir.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Konum: | Description |
> |---------|---------|
> | `scopes` | İstenen kapsamları içerir (yani `[ "user.read" ]` Microsoft Graph veya `[ "<Application ID URL>/scope" ]` özel Web API 'leri için ( `api://<Application ID>/access_as_user` ) |
> | `account` | Belirtecin istendiği hesap. Bu hızlı başlangıç, tek bir hesap uygulaması hakkında. Çok sunuculu bir uygulama oluşturmak istiyorsanız, kullanarak belirteç istekleri için hangi hesabın kullanılacağını belirlemek `accountsFromDeviceForParameters:completionBlock:` ve doğru geçirme yapmak için Logic tanımlamanız gerekir `accountIdentifier` |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Identity platformundan erişim belirteci alan ve Microsoft Graph API 'sini çağırmak için onu kullanan bir iOS veya macOS uygulaması oluşturduğunuz adım adım öğreticiye geçin.

> [!div class="nextstepaction"]
> [Öğretici: Kullanıcı oturum açma ve iOS veya macOS uygulamasından Microsoft Graph çağırma](tutorial-v2-ios.md)

---
title: iOS cihazlar için paylaşılan cihaz modu
titleSuffix: Microsoft identity platform | Azure
description: Saflarda çalışanlarının bir iOS cihazını paylaşmasına izin vermek için paylaşılan cihaz modunu etkinleştirmeyi öğrenin
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 3353d87291492563acbd3a85bbae266c711377f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611812"
---
# <a name="shared-device-mode-for-ios-devices"></a>iOS cihazlar için paylaşılan cihaz modu

>[!IMPORTANT]
> Bu özellik [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Retail Associates, uçuş ekibi üyeleri ve alan hizmeti çalışanları gibi Frontline çalışanları genellikle işlerini gerçekleştirmek için paylaşılan bir mobil cihaz kullanır. Bu paylaşılan cihazlar, kullanıcılarınızın parolalarını veya PIN 'lerini paylaşılan cihazdaki müşteri ve iş verilerine erişmek üzere kasıtlı veya olmayan bir şekilde paylaşıyorsa güvenlik riskleri sunabilir.

Paylaşılan cihaz modu, iOS 13 veya daha yüksek bir cihazı, çalışanlar tarafından daha kolay ve güvenli bir şekilde paylaşılabilecek şekilde yapılandırmanıza olanak tanır. Çalışanlar oturum açabilir ve müşteri bilgilerine hızlıca erişebilir. Bunlar, vardiyası veya göreviyle bitdiklerinde, cihazın oturumunu kapatıp bir sonraki çalışan tarafından kullanıma hazır olabilir.

Paylaşılan cihaz modu, Ayrıca, cihazın Microsoft kimliği ile desteklenen yönetimini de sağlar.

Bu özellik, cihazdaki kullanıcıları yönetmek ve [Apple cihazları Için Microsoft ENTERPRISE SSO eklentisini](apple-sso-plugin.md)dağıtmak üzere [Microsoft Authenticator uygulamasını](../user-help/user-help-auth-app-overview.md) kullanır.

## <a name="create-a-shared-device-mode-app"></a>Paylaşılan cihaz modu uygulaması oluşturma

Bir paylaşılan cihaz modu uygulaması oluşturmak için, geliştiriciler ve bulut cihaz yöneticileri birlikte çalışır:

1. **Uygulama geliştiricileri** tek hesap uygulaması yazar (çoklu hesap uygulamaları paylaşılan cihaz modunda desteklenmez) ve paylaşılan cihaz oturumu kapatma gibi işlemleri işlemek için kod yazın.

1. **Cihaz yöneticileri** , kuruluşlarındaki cihazları yönetmek için Microsoft Intune gibi bir mobil cihaz YÖNETIMI (MDM) sağlayıcısı kullanarak cihazı paylaştırmaya hazırlar. MDM, Microsoft Authenticator uygulamayı cihazlara gönderir ve cihaza bir profil güncelleştirmesi aracılığıyla her bir cihaz için "paylaşılan mod" öğesini açar. Bu paylaşılan mod ayarı, cihazdaki desteklenen uygulamaların davranışının değiştiği şeydir. MDM sağlayıcısından bu yapılandırma, cihazın paylaşılan cihaz modunu ayarlar ve paylaşılan cihaz modu için gerekli olan [Apple cihazları Için Microsoft ENTERPRISE SSO eklentisini](apple-sso-plugin.md) etkinleştirilir.

1. [**Yalnızca genel önizleme sırasında gereklidir**] [Bulut Cihaz Yöneticisi](../roles/permissions-reference.md#cloud-device-administrator) rolüne sahip bir kullanıcının [Microsoft Authenticator uygulamasını](../user-help/user-help-auth-app-overview.md) başlatması ve cihazını kuruluşa katılması gerekir.

    Azure Portal kurumsal rollerinizin üyeliğini yapılandırmak için: **Azure Active Directory**  >  **Roller ve yöneticiler**  >  **bulut Cihaz Yöneticisi**

Aşağıdaki bölümler, paylaşılan cihaz modunu destekleyecek şekilde uygulamanızı güncelleştirmenize yardımcı olur.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Intune 'U kullanarak paylaşılan cihaz modunu & SSO uzantısını etkinleştirme

> [!NOTE]
> Aşağıdaki adım yalnızca genel önizleme sırasında gereklidir.

Cihazınızın paylaşılan cihaz modunu destekleyecek şekilde yapılandırılması gerekir. İOS 13 + yüklü olmalıdır ve MDM kaydı yapılmalıdır. MDM yapılandırmasının Ayrıca [Apple cihazları Için Microsoft ENTERPRISE SSO eklentisini](apple-sso-plugin.md)etkinleştirmesi gerekir. SSO uzantıları hakkında daha fazla bilgi için [Apple videosunu](https://developer.apple.com/videos/play/tech-talks/301/)inceleyin.

1. Intune yapılandırma portalında, cihaza aşağıdaki yapılandırmaya sahip [Apple cihazları Için Microsoft ENTERPRISE SSO eklentisini](apple-sso-plugin.md) etkinleştirmesini söyleyin:

    - **Tür**: Redirect
    - **UZANTı kimliği**: com. Microsoft. azuyeniden kimlik doğrulayıcısının. ssoextension
    - **Takım Kimliği**: (Bu alan iOS için gerekli değildir)
    - **URL 'ler**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **Yapılandırılacak ek veriler**:
      - Anahtar: sharedDeviceMode
      - Tür: Boolean
      - Değer: true

    Intune ile yapılandırma hakkında daha fazla bilgi için bkz. [Intune yapılandırma belgeleri](/intune/configuration/ios-device-features-settings).

1. Ardından, MDM 'nizi bir MDM profili aracılığıyla Microsoft Authenticator uygulamasını cihazınıza göndermek üzere yapılandırın.

    Paylaşılan cihaz modunu açmak için aşağıdaki yapılandırma seçeneklerini ayarlayın:

    - Yapılandırma 1:
      - Anahtar: sharedDeviceMode
      - Tür: Boolean
      - Değer: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>İOS uygulamanızı paylaşılan cihaz modunu destekleyecek şekilde değiştirme

Kullanıcılarınız, verilerinin başka bir kullanıcıya sızmasını sağlamak için size bağlıdır. Aşağıdaki bölümlerde, uygulamanızda bir değişikliğin gerçekleştiğini ve işlenmesi gerektiğini göstermek için faydalı sinyaller sağlanmaktadır.

Uygulamanızın her kullanıldığı her seferinde kullanıcının durumunu kontrol etmekten ve ardından önceki kullanıcının verilerini temizleyerek sorumlu olursunuz. Bu, çoklu görev sırasında arka planda yeniden yüklenmiş olup olmadığını içerir.

Kullanıcı değişikliği üzerinde, hem önceki Kullanıcı verilerinin temizlendiğinden hem de uygulamanızda görüntülenen önbelleğe alınmış verilerin kaldırıldığından emin olmalısınız. Uygulamanızı, paylaşılan cihaz modunu destekleyecek şekilde güncelleştirdikten sonra, sizin ve şirketiniz bir güvenlik incelemesi işlemi yapmanızı önemle tavsiye ederiz.

### <a name="detect-shared-device-mode"></a>Paylaşılan cihaz modunu Algıla

Paylaşılan cihaz modunun algılanmasının nedeni, uygulamanız için önemlidir. Uygulama paylaşılan bir cihazda kullanıldığında, birçok uygulamanın kullanıcı deneyiminde (UX) bir değişikliği olması gerekir. Örneğin, uygulamanız zaten bir hesabı olduğundan, en düşük bir çalışan için uygun olmayan bir "kaydolma" özelliğine sahip olabilir. Ayrıca, paylaşılan cihaz modundaysa uygulamanızın verileri işlemeye ek güvenlik eklemek isteyebilirsiniz.

`getDeviceInformationWithParameters:completionBlock:` `MSALPublicClientApplication` Uygulamasının paylaşılan cihaz modundaki bir cihazda çalışıp çalışmadığını öğrenmek için içindeki API 'yi kullanın.

Aşağıdaki kod parçacıkları, API kullanma örneklerini gösterir `getDeviceInformationWithParameters:completionBlock:` .

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Oturum açan kullanıcıyı al ve cihazda bir kullanıcının değişip değişmediğini belirleme

Paylaşılan cihaz modunu desteklemeye yönelik başka bir önemli bölüm, cihazdaki kullanıcının durumunu belirler ve bir Kullanıcı değiştiyse ya da cihazda hiç Kullanıcı yoksa, uygulama verilerini temizliyordadır. Verilerin başka bir kullanıcıya sızdırılmamasını sağlamaya sorumlusunuz.

`getCurrentAccountWithParameters:completionBlock:`API 'yi, cihazda Şu anda oturum açmış olan hesabı sorgulamak için kullanabilirsiniz.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Bir kullanıcı için genel olarak oturum açma

Bir cihaz paylaşılan bir cihaz olarak yapılandırıldığında, uygulamanız `acquireTokenWithParameters:completionBlock:` hesapta oturum açmak için API 'yi çağırabilir. Hesap, ilk uygulama oturum açtıktan sonra cihazdaki tüm uygun uygulamalar için genel olarak kullanılabilir olacaktır.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Kullanıcı oturumunu küresel olarak kapatma

Aşağıdaki kod, oturum açmış olan hesabı kaldırır ve önbelleğe alınmış belirteçleri yalnızca uygulamadan değil, ayrıca paylaşılan cihaz modundaki cihazdan temizler. Ancak, uygulamanızdan *verileri* temizlemez. Uygulamanızdaki verileri temizlemeniz ve uygulamanızın kullanıcıya görüntülüyor olabileceği önbelleğe alınmış tüm verileri temizlemeniz gerekir.

#### <a name="clear-browser-state"></a>Tarayıcı durumunu temizle

> [!NOTE]
> Aşağıdaki adım yalnızca genel önizleme sırasında gereklidir.

Bu genel önizleme sürümünde, [Apple cihazları Için Microsoft ENTERPRISE SSO eklentisi](apple-sso-plugin.md) yalnızca uygulamalar için durumu temizler. Safari tarayıcısında durumu temizlemez. Kullanıcı durumunun izlemelerinin gerisinde kalmasını sağlamak için tarayıcı oturumunu el ile temizlemeniz önerilir. `signoutFromBrowser`Herhangi bir tanımlama bilgisini temizlemek için aşağıda gösterilen isteğe bağlı özelliği kullanabilirsiniz. Bu, tarayıcının cihazda kısa bir süre başlatılmasına neden olur.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Sonraki adımlar

Paylaşılan cihaz modunu çalışırken görmek için GitHub 'daki aşağıdaki kod örneği, paylaşılan cihaz modundaki bir iOS cihazında saflarda çalışan uygulaması çalıştırmaya ilişkin bir örnek içerir:

[MSAL iOS Swift Microsoft Graph API örneği](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)

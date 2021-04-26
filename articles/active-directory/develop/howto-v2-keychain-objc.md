---
title: Anahtarlık yapılandırma
titleSuffix: Microsoft identity platform
description: Anahtarlığınıza, uygulamanızın belirteçleri anahtarlıkta önbelleğe abilmesi için Anahtarlık yapılandırmayı öğrenin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 06e197a6e445c7dc1179be696318905f2132ee36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477745"
---
# <a name="configure-keychain"></a>Anahtarlık yapılandırma

[İOS ve macOS Için Microsoft kimlik doğrulama kitaplığı](msal-overview.md) (msal) bir kullanıcıya kaydolursa veya bir belirteci yenilediğinde, anahtarlıkta belirteçleri önbelleğe almaya çalışır. Anahtarlıkta önbelleğe alma belirteçleri, MSAL 'ın aynı Apple geliştiricisi tarafından dağıtılan birden çok uygulama arasında sessiz çoklu oturum açma (SSO) sağlamasına izin verir. Anahtar Zinciri erişim grupları işlevselliği aracılığıyla SSO elde edilir. Daha fazla bilgi için bkz. Apple 'ın [Anahtarlık öğeleri belgeleri](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc).

Bu makalede, MSAL 'in önbelleğe alınmış belirteçleri iOS ve macOS anahtarlığa yazabilmesi için uygulama yetkilendirmelerini yapılandırma konuları ele alınmaktadır.

## <a name="default-keychain-access-group"></a>Varsayılan Anahtarlık erişim grubu

### <a name="ios"></a>iOS

İOS üzerinde MSAL, `com.microsoft.adalcache` Varsayılan olarak erişim grubunu kullanır. Bu, hem MSAL hem de Azure AD kimlik doğrulama kitaplığı (ADAL) SDK 'Ları tarafından kullanılan paylaşılan erişim grubudur ve aynı yayımcıdan gelen birden çok uygulama arasında en iyi çoklu oturum açma (SSO) deneyimini sağlar.

İOS 'ta, `com.microsoft.adalcache` Anahtarlık grubunu, **proje ayarları**  >  **özellikleri**  >  **Anahtarlık paylaşımı** altındaki Xcode 'da uygulamanızın yetkilendirenlerine ekleyin

### <a name="macos"></a>Mac OS

MacOS on MSAL, `com.microsoft.identity.universalstorage` Varsayılan olarak erişim grubunu kullanır.

MacOS anahtar zinciri sınırlamaları nedeniyle MSAL, `access group` macos 10,14 ve önceki sürümlerde Anahtarlık erişim grubu özniteliğine (bkz. [Ksecattraccessgroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) doğrudan çevrilmez. Ancak, aynı Apple geliştiricisi tarafından dağıtılan birden çok uygulamanın sessiz SSO 'ya sahip olduğundan emin olarak SSO perspektifinden aynı şekilde davranır.

MacOS 10,15 ve sonraki sürümlerinde (macOS Catalina), MSAL, iOS 'a benzer şekilde sessiz SSO elde etmek için Anahtarlık erişim grubu özniteliğini kullanır.

## <a name="custom-keychain-access-group"></a>Özel Anahtarlık erişim grubu

Farklı bir Anahtarlık erişim grubu kullanmak istiyorsanız, oluşturma işleminden önce oluşturma sırasında özel grubunuzu `MSALPublicClientApplicationConfig` `MSALPublicClientApplication` Şu şekilde geçirebilirsiniz:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Anahtarlık paylaşımını devre dışı bırak

Birden çok uygulama arasında SSO durumu paylaşmak istemiyorsanız veya herhangi bir Anahtarlık erişim grubunu kullanarak, uygulama paketi KIMLIĞINI keychainGroup olarak geçirerek anahtar zinciri paylaşımını devre dışı bırakın:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Tanıtıcı-34018 hatası (öğe anahtarlığa ayarlanamadı)

Hata-34018 normalde Anahtarlık doğru şekilde yapılandırılmadığı anlamına gelir. MSAL ' de yapılandırılmış Anahtarlık erişim grubunun yetkilendirmeler ' de yapılandırılmış bir ile eşleştiğinden emin olun.

## <a name="ensure-your-application-is-properly-signed"></a>Uygulamanızın düzgün şekilde imzalandığından emin olun

MacOS 'ta uygulamalar, geliştirici tarafından imzalanmadan çalıştırılabilir. MSAL işlevlerinin büyük bir kısmının çalışmaya devam etmesi, Anahtarlık erişimi aracılığıyla SSO 'nun, uygulamanın imzalanmasını gerektirir. Birden çok Anahtarlık istemler yaşıyorsanız, uygulamanızın imzasının geçerli olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Apple 'ın [, anahtar zinciri öğelerine bir uygulamalar koleksiyonu Ile paylaşım erişimi](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) üzerinde Anahtarlık erişim grupları hakkında daha fazla bilgi edinin.

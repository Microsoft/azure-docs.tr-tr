---
title: Xamarin iOS konuları (.NET için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Xamarin iOS kullanırken belirli hususlar hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc4eb0084e0606251d3fbd80d08723701c6a260
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175625"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>MSAL.NET ile Xamarin iOS 'a özgü hususlar
Xamarin iOS üzerinde MSAL.NET kullanırken dikkate almanız gereken birkaç önemli noktalar vardır.

- [İOS 12 ve kimlik doğrulama ile ilgili bilinen sorunlar](#known-issues-with-ios-12-and-authentication)
- [`AppDelegate``OpenUrl` işlevini geçersiz kılın ve uygulayın](#implement-openurl)
- [Anahtarlık gruplarını etkinleştir](#enable-keychain-access)
- [Belirteç önbelleği paylaşımını etkinleştir](#enable-token-cache-sharing-across-ios-applications)
- [Anahtarlık erişimini etkinleştir](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>İOS 12 ve kimlik doğrulama ile ilgili bilinen sorunlar
Microsoft, iOS12 ve bazı kimlik doğrulama türleri arasında uyumsuzluk hakkında bilgi sağlamak için bir [güvenlik önerisi](https://github.com/aspnet/AspNetCore/issues/4647) yayımlamıştır. Uyumsuzluk sosyal, Wsbeslenir ve OıDC oturumlarını keser. Bu Danışma Ayrıca, geliştiricilerin iOS12 ile uyumlu hale gelmesi için ASP.NET tarafından eklenen geçerli güvenlik kısıtlamalarını kaldırmak için neler yapabileceğini yönergeler sağlar.  

Xamarin iOS üzerinde MSAL.NET uygulamaları geliştirirken, iOS 12 ' den web sitelerinde oturum açmaya çalışırken sonsuz bir döngü görebilirsiniz (Bu [adal sorununa](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)benzer. 

Ayrıca, bu [WebKit sorunu](https://bugs.webkit.org/show_bug.cgi?id=188165)bölümünde açıklandığı gibi IOS 12 Safari ile ASP.NET Core OIDC kimlik doğrulamasında bir kesme de görebilirsiniz.

## <a name="implement-openurl"></a>OpenUrl 'Yi Uygula

İlk olarak, `FormsApplicationDelegate` türetilmiş sınıfın `OpenUrl` yöntemini geçersiz kılmanız ve `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`çağırmanız gerekir.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Ayrıca, bir URL şeması tanımlamanız, uygulamanızın başka bir uygulamayı çağırması için izin almanız, yeniden yönlendirme URL 'SI için belirli bir form uygulamanız ve bu yeniden yönlendirme URL 'sini [Azure Portal](https://portal.azure.com) kaydetmeniz gerekir

### <a name="enable-keychain-access"></a>Anahtarlık erişimini etkinleştir

Anahtarlık erişimini etkinleştirmek için uygulamanızın bir Anahtarlık erişim grubu olması gerekir.
Aşağıda gösterildiği gibi, uygulamanızı oluştururken `WithIosKeychainSecurityGroup()` API 'sini kullanarak Anahtarlık erişim grubunuzu ayarlayabilirsiniz:

Çoklu oturum açmayı etkinleştirmek için, `PublicClientApplication.iOSKeychainSecurityGroup` özelliğini tüm uygulamalarda aynı değere ayarlamanız gerekir.

MSAL v3. x kullanılarak buna bir örnek şöyle olabilir:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Yetkilendirmeler. plist aşağıdaki XML parçası gibi görünecek şekilde güncellenmelidir:

Bu değişiklik, `Entitlements.plist` dosyasında anahtar zinciri erişiminin *etkinleştirilmesinin yanı sıra* aşağıdaki erişim grubunu ya da kendinizinkini kullanmaktır:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

MSAL v4. x kullanılarak buna bir örnek şöyle olabilir:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

`WithIosKeychainSecurityGroup()` API 'si kullanılırken, uygulamanızı Xcode kullanarak oluşturduğunuzda, MSAL otomatik olarak uygulamanın "ekip KIMLIĞI" (Appıdentifierprefix) sonuna güvenlik grubunuzu ekler. [Daha fazla bilgi Için bkz. iOS yetkilendirmeleri belgeleri](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). Bu nedenle, yetkilendirmeler. plist dosyasındaki Anahtarlık erişim grubundan önce $ (Appıdentifierprefix) dahil etmek için yetkilendirmeleri güncelleştirmeniz gerekir.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>İOS uygulamaları arasında belirteç önbelleği paylaşımını etkinleştir

MSAL 2. x öğesinden, birden çok uygulama arasında belirteç önbelleğini kalıcı hale getirmeniz için kullanılacak bir Anahtarlık erişim grubu belirtebilirsiniz. Bu ayar, [adal.net](https://aka.ms/adal-net), msal.net Xamarin. iOS uygulamaları ve ile [geliştirilen Yerel iOS uygulamaları dahil olmak üzere aynı Anahtarlık erişim grubuna sahip çeşitli uygulamalar arasında belirteç önbelleğini paylaşmanıza olanak sağlar ADAL. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) veya [msal. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Belirteç önbelleğinin paylaşılması, aynı Anahtarlık erişim grubunu kullanan tüm uygulamalar arasında çoklu oturum açma olanağı sağlar.

Bu önbellek paylaşımını etkinleştirmek için, anahtar zinciri erişim grubunu Yukarıdaki örnekte gösterildiği gibi aynı önbelleği paylaşan tüm uygulamalarda aynı değere ayarlamak için ' WithIosKeychainSecurityGroup () ' metodunu kullanın.

Daha önce, `WithIosKeychainSecurityGroup()` API 'yi her kullandığınızda MSAL 'nin $ (Appıdentifierprefix) eklemiş olması bahsedildi. Bunun nedeni, yalnızca aynı yayımcı tarafından yapılan uygulamaların Anahtarlık erişimini paylaşabildiğinden emin olmak için Appıdentifierprefix veya "takım KIMLIĞI" kullanılır.

> [!NOTE]
> **`KeychainSecurityGroup` özelliği kullanım dışıdır.**
> 
> Daha önce, MSAL 2. x öğesinden, `KeychainSecurityGroup` özelliği kullanılırken geliştiricilerin Teamıd önekini eklemesi zorlandı.
>
>  MSAL 2.7. x öğesinden yeni `iOSKeychainSecurityGroup` özelliği kullanılırken, MSAL çalışma zamanı sırasında Teamıd önekini çözmeyecektir. Bu özellik kullanılırken, değer Teamıd önekini içermemelidir.
>  Önceki `KeychainSecurityGroup` özelliği artık kullanılmıyor olduğundan, Teamıd sağlamanızı gerektirmeyen yeni `iOSKeychainSecurityGroup` özelliğini kullanın.

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator kullan

Uygulamanız, etkinleştirmek için Microsoft Authenticator (bir aracı) kullanabilir:

- Çoklu oturum açma (SSO). Kullanıcılarınızın her uygulamada oturum açması gerekmez.
- Cihaz kimliği. Cihazda, çalışma alanına katılmış olduğunda oluşturulan cihaz sertifikasına erişerek. Kiracı yöneticileri, cihazlarla ilgili koşullu erişimi etkinleştirmeye olanak varsa uygulamanız size hazırlanacaktır.
- Uygulama tanımlama doğrulaması. Bir uygulama aracıyı çağırdığında, yeniden yönlendirme URL 'sini geçirir ve aracı tarafından doğrulanır.

Aracısının nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Microsoft Authenticator kullanma veya Microsoft Intune şirket portalı, Xamarin iOS ve Android uygulamalarında](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Xamarin iOS 'e özgü özellikleri gösteren örnek

Aşağıdaki örnek readme.md dosyasının IOS 'a [özgü önemli noktalar](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) paragrafında daha fazla ayrıntı verilmiştir:

Örnek | Platform | Açıklama
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Azure AD V 2.0 uç noktası aracılığıyla MSA ve Azure AD kimlik doğrulaması için MSAL kullanmayı ve elde edilen belirteçle Microsoft Graph erişmeyi gösteren basit bir Xamarin Forms uygulaması.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

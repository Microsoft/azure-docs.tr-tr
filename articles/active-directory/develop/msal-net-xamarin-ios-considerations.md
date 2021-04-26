---
title: Xamarin iOS konuları (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Xamarin iOS kullanma hakkında konular hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064496"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>MSAL.NET ile Xamarin iOS kullanma konuları

Xamarin iOS üzerinde .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullandığınızda şunları yapmalısınız:

- İşlevini geçersiz kılın ve `OpenUrl` ' de uygulayın `AppDelegate` .
- Anahtarlık gruplarını etkinleştirin.
- Belirteç önbelleği paylaşımını etkinleştirin.
- Anahtarlık erişimini etkinleştirin.
- İOS 12 ve iOS 13 ve kimlik doğrulama ile ilgili bilinen sorunları anlayın.

## <a name="implement-openurl"></a>OpenUrl 'Yi Uygula

`OpenUrl` `FormsApplicationDelegate` Türetilmiş sınıfın yöntemini ve çağrısını geçersiz kılın `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Aşağıda bir örnek verilmiştir:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Ayrıca, aşağıdaki görevleri gerçekleştirin:

* Yeniden yönlendirme URI şeması tanımlayın.
* Uygulamanızın başka bir uygulamayı çağırması için izin gerektir.
* Yeniden yönlendirme URI 'SI için belirli bir form olmalıdır.
* Azure portal [bir yeniden yönlendirme URI 'Si kaydedin](quickstart-register-app.md#add-a-redirect-uri) .

### <a name="enable-keychain-access"></a>Anahtarlık erişimini etkinleştir

Anahtarlık erişimini etkinleştirmek için, uygulamanızın bir Anahtarlık erişim grubuna sahip olduğundan emin olun. API 'yi kullanarak uygulamanızı oluştururken Anahtarlık erişim grubunu ayarlayabilirsiniz `WithIosKeychainSecurityGroup()` .

Önbellek ve çoklu oturum açma (SSO) avantajlarından yararlanmak için, Anahtarlık erişim grubunu tüm uygulamalarınızda aynı değere ayarlayın.

Kurulumun bu örneği MSAL 4. x kullanır:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Ayrıca, dosyasında Anahtarlık erişimini etkinleştirin `Entitlements.plist` . Aşağıdaki erişim grubunu ya da kendi erişim grubunuzu kullanın.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

API 'yi kullandığınızda `WithIosKeychainSecurityGroup()` , msal otomatik olarak güvenlik grubunuzu uygulamanın *Takım Kimliği* () sonuna ekler `AppIdentifierPrefix` . MSAL, uygulamanızı Xcode 'da oluştururken aynı şekilde güvenlik grubunuzu ekler. Bu nedenle, dosyadaki yetkilendirmelerin `Entitlements.plist` `$(AppIdentifierPrefix)` Anahtarlık erişim grubundan önce eklenmesi gerekir.

Daha fazla bilgi için bkz. [iOS yetkilendirmeleri belgeleri](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps).

### <a name="enable-token-cache-sharing-across-ios-applications"></a>İOS uygulamaları arasında belirteç önbelleği paylaşımını etkinleştir

MSAL 2. x ' den başlayarak, belirteç önbelleğini birden çok uygulama arasında kalıcı hale getirmek için bir Anahtarlık erişim grubu belirtebilirsiniz. Bu ayar, belirteç önbelleğini aynı Anahtarlık erişim grubuna sahip çeşitli uygulamalar arasında paylaşmanızı sağlar. Belirteç önbelleğini [adal.net](https://aka.ms/adal-net) uygulamaları, msal.net Xamarin. iOS uygulamaları ve [adal. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) veya [msal. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc)içinde geliştirilen Yerel iOS uygulamaları arasında paylaşabilirsiniz.

Belirteç önbelleğini paylaşarak, aynı Anahtarlık erişim grubunu kullanan tüm uygulamalar arasında çoklu oturum açma 'ya (SSO) izin verebilirsiniz.

Bu önbellek paylaşımını etkinleştirmek için yöntemini kullanarak, `WithIosKeychainSecurityGroup()` Anahtarlık erişim grubunu aynı önbelleği paylaşan tüm uygulamalarda aynı değere ayarlayın. Bu makaledeki ilk kod örneği, yönteminin nasıl kullanılacağını gösterir.

Bu makalenin önceki kısımlarında, `$(AppIdentifierPrefix)` API 'yi her KULLANDıĞıNıZDA msal eklediği hakkında daha fazla öğrendiniz `WithIosKeychainSecurityGroup()` . Takım KIMLIĞI, `AppIdentifierPrefix` yalnızca aynı yayımcı tarafından yapılan uygulamaların Anahtarlık erişimini paylaşabilmesini sağladığından, msal bu öğeyi ekler.

> [!NOTE]
> `KeychainSecurityGroup`Özelliği kullanım dışıdır. `iOSKeychainSecurityGroup`Bunun yerine özelliğini kullanın. `TeamId`Kullandığınızda ön ek gerekli değildir `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator kullan

Uygulamanız, etkinleştirmek için bir aracı olarak Microsoft Authenticator kullanabilir:

- **SSO**: SSO 'yu etkinleştirdiğinizde, kullanıcılarınızın her uygulamada oturum açması gerekmez.
- **Cihaz kimliği**: cihaz sertifikasına erişerek kimlik doğrulamak için cihaz kimliğini kullanın. Bu sertifika, çalışma alanına katıldığında cihazda oluşturulur. Kiracı yöneticileri cihazlarla ilgili koşullu erişimi etkinleştirmeleri durumunda uygulamanız hazırlanacaktır.
- **Uygulama tanımlama doğrulaması**: bir uygulama aracıyı çağırdığında, yeniden yönlendirme URL 'sini geçirir. Aracı yeniden yönlendirme URL 'sini doğrular.

Bir aracısının nasıl etkinleştirileceği hakkında ayrıntılar için bkz. [Use Microsoft Authenticator veya Microsoft Intune şirket portalı Xamarin iOS ve Android uygulamalarında](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>İOS 12 ve kimlik doğrulama ile ilgili bilinen sorunlar

Microsoft, iOS 12 ve bazı kimlik doğrulama türleri arasında uyumsuzluk hakkında bir [Güvenlik Danışmanlığı](https://github.com/aspnet/AspNetCore/issues/4647) yayımlamıştır. Uyumsuzluk sosyal, Wsbeslenir ve OıDC oturum açma işlemlerini keser. Güvenlik Danışmanlığı, ASP.NET güvenlik kısıtlamalarını uygulamalarınızdan kaldırarak iOS 12 ile uyumlu hale getirmek için nasıl kaldırılacağını anlamanıza yardımcı olur.

Xamarin iOS üzerinde MSAL.NET uygulamaları geliştirirken, iOS 12 ' den web sitelerinde oturum açmaya çalıştığınızda sonsuz bir döngüye karşılaşabilirsiniz. Bu tür davranışlar, [iOS 12 #1329 Web sitesinde oturum açmaya çalışırken GitHub: sonsuz döngüsünde](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)bu adal sorununa benzer.

İOS 12 Safari ile ASP.NET Core OıDC kimlik doğrulamasında bir kesme de görebilirsiniz. Daha fazla bilgi için bu [WebKit sorununa](https://bugs.webkit.org/show_bug.cgi?id=188165)bakın.

## <a name="known-issues-with-ios-13-and-authentication"></a>İOS 13 ve kimlik doğrulama ile ilgili bilinen sorunlar

Uygulamanız koşullu erişim veya sertifika kimlik doğrulama desteği gerektiriyorsa, uygulamanızın Microsoft Authenticator Broker uygulamasıyla iletişim kurmasını sağlayın. MSAL daha sonra uygulamanız ve Microsoft Authenticator arasındaki istekleri ve yanıtları işlemekten sorumludur.

İOS 13 ' te, Apple, bir dış uygulamadan özel URL şemaları aracılığıyla yanıt alırken uygulamanın kaynak uygulamayı okuma yeteneğini kaldırarak bir son API değişikliği yaptı.

[Uıapplicationopenurloptionssourceapplicationkey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) durumları için Apple belgeleri:

> *İstek ekibinize ait başka bir uygulamadan kaynaklandıysa, Uıit bu anahtarın değerini bu uygulamanın KIMLIĞINE ayarlar. Kaynak uygulamanın takım tanımlayıcısı geçerli uygulamanın takım tanımlayıcısından farklıysa, anahtarın değeri Nil olur.*

Bu değişiklik, `UIApplication.SharedApplication.OpenUrl` msal ile Microsoft Authenticator uygulaması arasındaki iletişimi doğrulamak IÇIN msal.

Ayrıca, iOS 13 ' te, kullanırken bir sunum denetleyicisi sağlamak için geliştirici gereklidir `ASWebAuthenticationSession` .

Xcode 11 ile derleme yapıyorsanız ve iOS Broker ya da kullanıyorsanız, uygulamanız etkilenir `ASWebAuthenticationSession` .

Bu gibi durumlarda, başarılı kimlik doğrulamasını etkinleştirmek için [msal.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) kullanın.

### <a name="additional-requirements"></a>Ek gereksinimler

- En son MSAL kitaplıklarını kullanırken cihazda **6.3.19 + sürümünün yüklü Microsoft Authenticator** olduğundan emin olun.
- MSAL.NET 4.4.0 + ' e güncelleştirirken, `LSApplicationQueriesSchemes` *Info. plist* dosyasında dosyanızı güncelleştirin ve şunu ekleyin `msauthv3` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    `msauthv3`İOS 13 ' ü destekleyen cihazda en son Microsoft Authenticator uygulamasının varlığını algılamak Için *Info. plist* ' ye ekleme gerekir.

## <a name="report-an-issue"></a>Bir sorun bildirin

Sorularınız varsa veya MSAL.NET içinde bulduğunuz bir sorunu raporlamak istiyorsanız, GitHub 'da [Azuread/Microsoft-Authentication-Library-for-DotNet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) deposunda bir sorun açın.

## <a name="next-steps"></a>Sonraki adımlar

Xamarin iOS özellikleri hakkında daha fazla bilgi için, aşağıdaki örneğin README.md dosyasının [iOS 'a özgü dikkat edilecek noktalar](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) paragrafına bakın:

Örnek | Platform | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Evrensel Windows Platformu (UWP) | Azure AD 2,0 uç noktası aracılığıyla Microsoft kişisel hesaplarının ve Azure AD kimlik doğrulaması için MSAL kullanmayı gösteren basit bir Xamarin Forms uygulaması. Uygulama ayrıca Microsoft Graph erişmek için elde edilen belirtecin nasıl kullanılacağını gösterir.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->

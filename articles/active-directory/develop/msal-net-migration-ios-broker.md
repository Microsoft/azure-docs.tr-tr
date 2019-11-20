---
title: ADAL.NET 'den MSAL.NET 'ye Microsoft Authenticator kullanan Xamarin iOS uygulamalarını geçirin
titleSuffix: Microsoft identity platform
description: .NET için Azure AD kimlik doğrulama kitaplığı 'ndan Microsoft Authenticator kullanan Xamarin iOS uygulamalarını .NET için Microsoft kimlik doğrulama kitaplığı 'na (MSAL.NET) geçirmeyi öğrenin.
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c64f9f371424eddc7295b6ec40bda1ebdaaafd5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175580"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>ADAL.NET 'den MSAL.NET 'ye Microsoft Authenticator kullanan iOS uygulamalarını geçirme

.NET (ADAL.NET) ve iOS Aracısı için Azure Active Directory kimlik doğrulama kitaplığını kullanıyorsunuz. Şimdi .NET için [Microsoft kimlik doğrulama kitaplığı](msal-overview.md) 'na (msal.net) geçiş yapma zamanı, bu sürüm 4,3 ' den iOS üzerindeki aracıyı destekler. 

Nereden başlamanız gerekir? Bu makale, Xamarin iOS uygulamanızı ADAL 'dan MSAL 'e geçirmenize yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, iOS broker ile tümleştirilmiş bir Xamarin iOS uygulamasına zaten sahip olduğunuz varsayılır. Bunu yapmazsanız, doğrudan MSAL.NET 'e taşıyın ve aracı uygulamasını orada başlatın. MSAL.NET içinde iOS Broker 'ı yeni bir uygulamayla çağırma hakkında daha fazla bilgi için [Bu belgelere](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)bakın.

## <a name="background"></a>Arka plan

### <a name="what-are-brokers"></a>Aracılar nelerdir?

Aracılar, Android ve iOS 'ta Microsoft tarafından sunulan uygulamalardır. (İOS ve Android 'de [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) uygulamasına ve android üzerinde Intune Şirket Portalı uygulamasına bakın.) 

Bunlar şunları etkinleştirir:

- Çoklu oturum açma.
- Bazı [koşullu erişim ilkeleri](../conditional-access/overview.md)için gereken cihaz kimliği. Daha fazla bilgi için bkz. [cihaz yönetimi](../conditional-access/conditions.md#device-platforms).
- Uygulama tanımlama doğrulaması, bazı kurumsal senaryolarda de gereklidir. Daha fazla bilgi için bkz. [Intune mobil uygulama yönetimi (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>ADAL 'ten MSAL 'e geçiş

### <a name="step-1-enable-the-broker"></a>1\. Adım: aracıyı etkinleştirme

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
ADAL.NET ' de, bir kimlik doğrulama bağlamı temelinde Aracı desteği etkinleştirilmiştir. Varsayılan olarak devre dışıdır. Şunu ayarlamanız gerekiyordu 

Aracıyı çağırmak için `PlatformParameters` oluşturucuda `useBroker` bayrağını True olarak işaretle:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Ayrıca, platforma özgü kodda, bu örnekte iOS için sayfa işleyicide `useBroker` ayarlayın. 
true olarak işaretle:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Ardından, belirteci al çağrısına parametreleri ekleyin:
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
MSAL.NET ' de, aracı desteği PublicClientApplication temelinde etkinleştirilir. Varsayılan olarak devre dışıdır. Etkinleştirmek için şunu kullanın 

Aracıyı çağırmak için `WithBroker()` parametresi (varsayılan olarak true olarak ayarlanır):

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Belirteç al çağrısında:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>2\. Adım: UIViewController () ayarlama
ADAL.NET ' de, `PlatformParameters`kapsamında bir UIViewController 'a geçtiniz. (Adım 1 ' deki örneğe bakın.) MSAL.NET ' de, geliştiricilere daha fazla esneklik sağlamak için bir nesne penceresi kullanılır, ancak normal iOS kullanımı için gerekli değildir. Aracıyı kullanmak için, aracıdan yanıt göndermek ve almak üzere nesne penceresini ayarlayın. 
<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
Bir UIViewController öğesine geçirildi 

iOS 'a özgü platformda `PlatformParameters`.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
MSAL.NET ' de, iOS için nesne penceresini ayarlamak için iki şey yapmanız gerekir:

1. `AppDelegate.cs`, `App.RootViewController` yeni bir `UIViewController()`olarak ayarlayın. Bu atama, aracı çağrısına bir UIViewController olduğundan emin olmanızı sağlar. Doğru ayarlanmamışsa şu hatayı alabilirsiniz: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Acquiretokenınteractive çağrısında, `.WithParentActivityOrWindow(App.RootViewController)`kullanın ve kullanacağınız nesne penceresi başvurusunu geçirin.

**Örneğin:**

`App.cs` içinde:
```CSharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs` içinde:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Belirteç al çağrısında:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3\. Adım: geri aramayı işlemek için AppDelegate 'i güncelleştirme
Hem ADAL hem de MSAL aracı çağırır ve içindeki aracı, `AppDelegate` sınıfının `OpenUrl` yöntemi aracılığıyla uygulamanıza geri çağrı çağırır. Daha fazla bilgi için [Bu belgelere](msal-net-use-brokers-with-xamarin-apps.md#step-2-update-appdelegate-to-handle-the-callback)bakın.

Burada ADAL.NET ve MSAL.NET arasında bir değişiklik yoktur.

### <a name="step-4-register-a-url-scheme"></a>4\. Adım: URL düzenini kaydetme
ADAL.NET ve MSAL.NET, aracıyı çağırmak ve aracı yanıtını uygulamaya geri döndürmek için URL 'Leri kullanın. URL şemasını uygulamanızın `Info.plist` dosyasına kaydedin:

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
URL şeması, uygulamanız için benzersizdir.
</td><td>
Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için 

`CFBundleURLSchemes` adı şunu içermelidir 

`msauth.`

ön ek olarak, ardından `CFBundleURLName`

Örneğin, `$"msauth.(BundleId")`

```CSharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
> Bu URL şeması, Aracıdan yanıtı aldığında uygulamayı benzersiz şekilde tanımlamak için kullanılan yeniden yönlendirme URI 'sinin bir parçası haline gelir.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5\. Adım: aracı tanımlayıcısını Lsapplicationqueriesdüzenlerinin bölümüne ekleme

ADAL.NET ve MSAL.NET, aracının cihaza yüklenip yüklenmediğini denetlemek için `-canOpenURL:` kullanır. İOS Aracısı için doğru tanımlayıcıyı Info. plist dosyasının Lsapplicationqueriesdüzenlerinin bölümüne aşağıdaki şekilde ekleyin:

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
Kullanımlar 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Kullanımlar 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>6\. Adım: yeniden yönlendirme URI 'nizi portala kaydetme

ADAL.NET ve MSAL.NET, aracıyı hedefliyorsa yeniden yönlendirme URI 'sine ek bir gereksinim ekler. Yeniden yönlendirme URI 'sini portaldaki uygulamanıza kaydedin.
<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Örnek: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Örnek:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Yeniden yönlendirme URI 'sini portalda kaydetme hakkında daha fazla bilgi için bkz. [Xamarin. iOS uygulamalarında aracıdan yararlanma](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Sonraki adımlar

[Msal.net Ile Xamarin iOS 'a özgü hususlar](msal-net-xamarin-ios-considerations.md)hakkında bilgi edinin. 

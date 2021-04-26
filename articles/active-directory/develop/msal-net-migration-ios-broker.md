---
title: Aracıları kullanarak Xamarin uygulamalarını MSAL.NET’e geçirme
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator kullanan Xamarin iOS uygulamalarını ADAL.NET 'ten MSAL.NET 'e geçirmeyi öğrenin.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89177440"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>ADAL.NET 'den MSAL.NET 'ye Microsoft Authenticator kullanan iOS uygulamalarını geçirme

.NET (ADAL.NET) ve iOS Aracısı için Azure Active Directory kimlik doğrulama kitaplığını kullanıyorsunuz. Şimdi .NET için [Microsoft kimlik doğrulama kitaplığı](msal-overview.md) 'na (msal.net) geçiş yapma zamanı, bu sürüm 4,3 ' den iOS üzerindeki aracıyı destekler.

Nereden başlamanız gerekir? Bu makale, Xamarin iOS uygulamanızı ADAL 'dan MSAL 'e geçirmenize yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, iOS broker ile tümleştirilmiş bir Xamarin iOS uygulamasına zaten sahip olduğunuz varsayılır. Bunu yapmazsanız, doğrudan MSAL.NET 'e taşıyın ve aracı uygulamasını orada başlatın. MSAL.NET içinde iOS Broker 'ı yeni bir uygulamayla çağırma hakkında daha fazla bilgi için [Bu belgelere](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)bakın.

## <a name="background"></a>Arka Plan

### <a name="what-are-brokers"></a>Aracılar nelerdir?

Aracılar, Android ve iOS 'ta Microsoft tarafından sunulan uygulamalardır. (İOS ve Android 'de [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) uygulamasına ve android üzerinde Intune Şirket Portalı uygulamasına bakın.)

Bunlar şunları etkinleştirir:

- Çoklu oturum açma.
- Bazı [koşullu erişim ilkeleri](../conditional-access/overview.md)için gereken cihaz kimliği. Daha fazla bilgi için bkz. [cihaz yönetimi](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Uygulama tanımlama doğrulaması, bazı kurumsal senaryolarda de gereklidir. Daha fazla bilgi için bkz. [Intune mobil uygulama yönetimi (MAM)](/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>ADAL 'ten MSAL 'e geçiş

### <a name="step-1-enable-the-broker"></a>1. Adım: aracıyı etkinleştirme

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
ADAL.NET ' de, bir kimlik doğrulama bağlamı temelinde Aracı desteği etkinleştirilmiştir. Varsayılan olarak devre dışıdır. Şunu ayarlamanız gerekiyordu

`useBroker``PlatformParameters`aracıyı çağırmak için oluşturucuda true olarak bayrak ekleyin:

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
Ayrıca, platforma özgü kodda, bu örnekte, iOS için sayfa İşleyicide, `useBroker`
true olarak işaretle:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

Ardından, belirteci al çağrısına parametreleri ekleyin:
```csharp
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

`WithBroker()` Aracıyı çağırmak için parametresi (varsayılan olarak true olarak ayarlanır):

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Belirteç al çağrısında:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>2. Adım: UIViewController () ayarlama
ADAL.NET ' de, bir UIViewController içine bir parçası olarak geçtiniz `PlatformParameters` . (Adım 1 ' deki örneğe bakın.) MSAL.NET ' de, geliştiricilere daha fazla esneklik sağlamak için bir nesne penceresi kullanılır, ancak normal iOS kullanımı için gerekli değildir. Aracıyı kullanmak için, aracıdan yanıt göndermek ve almak üzere nesne penceresini ayarlayın.
<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
Bir UIViewController öğesine geçirildi

`PlatformParameters` iOS 'a özgü platformda.

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
MSAL.NET ' de, iOS için nesne penceresini ayarlamak için iki şey yapmanız gerekir:

1. İçinde `AppDelegate.cs` , `App.RootViewController` yeni olarak ayarlayın `UIViewController()` .
Bu atama, aracı çağrısına bir UIViewController olduğundan emin olmanızı sağlar. Doğru ayarlanmamışsa şu hatayı alabilirsiniz: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Acquiretokenınteractive çağrısında, kullanın `.WithParentActivityOrWindow(App.RootViewController)` ve kullanacağınız nesne penceresi başvurusunu geçirin.

**Örnek:**

`App.cs` içinde:
```csharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs` içinde:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Belirteç al çağrısında:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3. Adım: geri aramayı işlemek için AppDelegate 'i güncelleştirme
Hem ADAL hem de MSAL aracı çağırır ve içindeki aracı, sınıfının yöntemi aracılığıyla uygulamanıza geri çağrı çağırır `OpenUrl` `AppDelegate` . Daha fazla bilgi için [Bu belgelere](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)bakın.

Burada ADAL.NET ve MSAL.NET arasında bir değişiklik yoktur.

### <a name="step-4-register-a-url-scheme"></a>4. Adım: URL düzenini kaydetme
ADAL.NET ve MSAL.NET, aracıyı çağırmak ve aracı yanıtını uygulamaya geri döndürmek için URL 'Leri kullanın. Aşağıdaki şekilde, URL şemasını `Info.plist` uygulamanız için dosyaya kaydedin:

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
URL şeması, uygulamanız için benzersizdir.
</td><td>
Bu

`CFBundleURLSchemes` ad içermeli

`msauth.`

ön ek olarak, `CFBundleURLName`

Örnek: `$"msauth.(BundleId")`

```csharp
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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>5. Adım: aracı tanımlayıcısını Lsapplicationqueriesdüzenlerinin bölümüne ekleme

ADAL.NET ve MSAL.NET her ikisi de `-canOpenURL:` aracının cihaza yüklenip yüklenmediğini denetlemek için kullanılır. İOS Aracısı için doğru tanımlayıcıyı Info. plist dosyasının Lsapplicationqueriesdüzenlerinin bölümüne aşağıdaki şekilde ekleyin:

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
Kullanımlar

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Kullanımlar

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>6. Adım: yeniden yönlendirme URI 'nizi Azure portal kaydetme

ADAL.NET ve MSAL.NET, aracıyı hedefliyorsa yeniden yönlendirme URI 'sine ek bir gereksinim ekler. Yeniden yönlendirme URI 'sini Azure portal uygulamanıza kaydedin.
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

Yeniden yönlendirme URI 'sini Azure portal kaydetme hakkında daha fazla bilgi için, bkz. [7. Adım: uygulama kaydınız için yeniden yönlendirme URI 'Si ekleme](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration).

### <a name="step-7-set-the-entitlementsplist"></a>**7. Adım: yetkilendirmeler. plist ' i ayarlama**

*Yetkilendirmeler. plist* dosyasında Anahtarlık erişimini etkinleştirin:

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

Anahtarlık erişimini etkinleştirme hakkında daha fazla bilgi için bkz. [Anahtarlık erişimini etkinleştirme](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

## <a name="next-steps"></a>Sonraki adımlar

[Msal.net Ile Xamarin iOS 'a özgü hususlar](msal-net-xamarin-ios-considerations.md)hakkında bilgi edinin.
---
title: MSAL.NET 'e aracılar kullanarak Xamarin Android uygulamalarını geçirme
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator veya Intune Şirket Portalı kullanan Xamarin Android uygulamalarını ADAL.NET ' den MSAL.NET ' ye geçirmeyi öğrenin.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 01af0e620ecb100839f7b1101e5ff9fcfc874eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206678"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>ADAL.NET 'den MSAL.NET 'e aracı kullanan Android uygulamalarını geçirme

Şu anda .NET için Azure Active Directory kimlik doğrulama kitaplığı 'nı (ADAL.NET) ve bir [kimlik doğrulama Aracısı](msal-android-single-sign-on.md)'nı kullanarak bir Xamarin Android uygulamanız varsa, [.net Için Microsoft kimlik doğrulama kitaplığı ](msal-overview.md) 'na geçiş süresi (msal.net).

## <a name="prerequisites"></a>Önkoşullar

* Bir aracı ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) veya [Intune şirket portalı](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) Ile tümleştirilmiş bir Xamarin Android uygulaması ve MSAL.NET 'e geçirmeniz gereken adal.net.

## <a name="step-1-enable-the-broker"></a>1. Adım: aracıyı etkinleştirme

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
ADAL.NET ' de, aracı desteği kimlik doğrulama bağlamı temelinde etkinleştirilir.

Aracıyı çağırmak için `useBroker` oluşturucuda bir olarak *true değerini* ayarlamanız gerekiyordu `PlatformParameters` :

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

Android için platforma özgü sayfa Oluşturucu kodunda, `useBroker` bayrağını doğru olarak ayarlarsınız:

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
MSAL.NET ' de, aracı desteği PublicClientApplication temelinde etkinleştirilir.

`WithBroker()`Aracıyı çağırmak için parametresini (varsayılan olarak true olarak ayarlanır) kullanın:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

Ardından, AcquireToken çağrısında:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>2. Adım: etkinlik ayarlama

ADAL.NET ' de, [1. Adım: aracıyı etkinleştirme](#step-1-enable-the-broker)bölümünde gösterildiği gibi, bir etkinlikte (genellikle MainActivity), PlatformParameters bir parçası olarak geçtiniz.

MSAL.NET ayrıca bir etkinlik kullanır, ancak aracı olmayan normal Android kullanımı için gerekli değildir. Aracıyı kullanmak için, aktiviteyi aracıdan yanıt gönderecek ve alacak şekilde ayarlayın.

<table>
<tr><td>Geçerli ADAL kodu:</td><td>MSAL karşılığı:</td></tr>
<tr><td>
Etkinlik, Android 'e özgü platformda bulunan PlatformParameters 'a geçirilir.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

MSAL.NET ' de, Android için etkinliği ayarlamak için iki şey yapın:

1. İçinde, `MainActivity.cs` `App.RootViewController` `MainActivity` Aracı çağrısıyla bir etkinlik olduğundan emin olmak için öğesini olarak ayarlayın.

    Doğru ayarlanmamışsa şu hatayı alabilirsiniz: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. Acquiretokenınteractıve çağrısında `.WithParentActivityOrWindow(App.RootViewController)` kullanın ve kullanacağınız etkinliğin başvurusunu geçirin. Bu örnek MainActivity ' i kullanacaktır.

**Örnek:**

*App. cs*'de:

```CSharp
   public static object RootViewController { get; set; }
```

*MainActivity. cs* dosyasında:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

AcquireToken çağrısında:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Sonraki adımlar

Xamarin ile MSAL.NET kullanırken Android 'e özgü hususlar hakkında daha fazla bilgi için bkz. [msal.net Ile Xamarin Android Için yapılandırma gereksinimleri ve sorun giderme ipuçları](msal-net-xamarin-android-considerations.md).
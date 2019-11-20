---
title: İstemci uygulamalarını başlatma (.NET için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak genel istemci ve gizli istemci uygulamalarını başlatma hakkında bilgi edinin.
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
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8cf7b7004097ef5a4d915d8fdff60cc9606c5be
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927073"
---
# <a name="initialize-client-applications-using-msalnet"></a>MSAL.NET kullanarak istemci uygulamalarını başlatma
Bu makalede, .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) kullanılarak ortak istemci ve gizli istemci uygulamalarının başlatılması açıklanmaktadır.  İstemci uygulama türleri ve uygulama yapılandırma seçenekleri hakkında daha fazla bilgi edinmek için [genel bakış](msal-client-applications.md)makalesini okuyun.

MSAL.NET 3. x ile, bir uygulamayı başlatmak için önerilen yol, uygulama oluşturucularını kullanmaktır: `PublicClientApplicationBuilder` ve `ConfidentialClientApplicationBuilder`. Bu kişiler, uygulamayı koddan veya bir yapılandırma dosyasından yapılandırmak için güçlü bir mekanizma sunar ve hatta her iki yaklaşımı de karıştırarak sağlar.

## <a name="prerequisites"></a>Önkoşullar
Uygulamayı başlatmadan önce, uygulamanızın Microsoft Identity platformu ile tümleştirilebilmesi için öncelikle [kaydetmeniz](quickstart-register-app.md) gerekir.  Kayıttan sonra, aşağıdaki bilgiler (Azure portal bulunabilir) gerekebilir:

- İstemci KIMLIĞI (bir GUID 'YI temsil eden dize)
- Kimlik sağlayıcısı URL 'SI (örnek olarak adlandırılır) ve uygulamanız için oturum açma hedef kitlesi. Bu iki parametre, her topluca yetkili olarak bilinir.
- Yalnızca kuruluşunuz için bir iş kolu uygulaması yazıyorsanız (tek kiracılı uygulama olarak da adlandırılır) kiracı KIMLIĞI.
- Gizli bir istemci uygulaması ise, uygulama gizli anahtarı (istemci gizli dizisi) veya sertifika (X509Certificate2 türünde).
- Web uygulamaları için ve bazen genel istemci uygulamaları için (uygulamanızın bir aracı kullanması gerektiğinde), kimlik sağlayıcısının güvenlik belirteçleriyle uygulamanızı geri yükleyeceğim yeniden yönlendirilebilir.

## <a name="ways-to-initialize-applications"></a>Uygulamaları başlatma yolları
İstemci uygulamaları oluşturmak için birçok farklı yol vardır.

### <a name="initializing-a-public-client-application-from-code"></a>Koddan ortak bir istemci uygulaması başlatma

Aşağıdaki kod, iş ve okul hesaplarıyla veya kendi kişisel Microsoft hesaplarıyla Microsoft Azure genel bulutta oturum açan kullanıcıları ortak bir istemci uygulaması başlatır.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Koddan gizli bir istemci uygulaması başlatma

Aynı şekilde, aşağıdaki kod, Microsoft Azure genel buluttaki kullanıcılardan, iş ve okul hesaplarıyla ya da kendi kişisel Microsoft hesaplarıyla bir gizli uygulama (`https://myapp.azurewebsites.net`bulunan bir Web uygulaması) oluşturur. Uygulama, bir istemci gizli anahtarını paylaşarak kimlik sağlayıcısı ile tanımlanır:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Bildiğiniz gibi, bir istemci gizli anahtarı kullanmak yerine, Azure AD bir sertifika ile paylaşmak isteyebilirsiniz. Kod daha sonra şunlar olur:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Yapılandırma seçeneklerinden ortak bir istemci uygulaması başlatma

Aşağıdaki kod, program aracılığıyla veya bir yapılandırma dosyasından okunan bir yapılandırma nesnesinden ortak bir istemci uygulaması başlatır:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Yapılandırma seçeneklerinden bir gizli istemci uygulaması başlatma

Aynı tür bir model gizli istemci uygulamaları için geçerlidir. `.WithXXX` değiştiriciler (burada bir sertifika) kullanarak da başka parametreler ekleyebilirsiniz.

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Oluşturucu değiştiricileri

Uygulama oluşturucularını kullanan kod parçacıkları içinde, bir dizi `.With` yöntemi değiştirici olarak uygulanabilir (örneğin, `.WithCertificate` ve `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Ortak ve gizli istemci uygulamalarında ortak olan değiştiriciler

Ortak bir istemcide veya gizli istemci uygulama tasarımcısında ayarlayabileceğiniz değiştiriciler şunlardır:

|İcisi | Açıklama|
|--------- | --------- |
|`.WithAuthority()` 7 geçersiz kılmalar | Azure bulutu, hedef kitlesi, kiracı (kiracı KIMLIĞI veya etki alanı adı) veya doğrudan yetkili URI sağlama olasılığa sahip olan uygulama varsayılan yetkilisini bir Azure AD yetkilisine ayarlar.|
|`.WithAdfsAuthority(string)` | Uygulamanın varsayılan yetkilisini bir ADFS yetkilisi olacak şekilde ayarlar.|
|`.WithB2CAuthority(string)` | Uygulama varsayılan yetkilisini bir Azure AD B2C yetkilisi olacak şekilde ayarlar.|
|`.WithClientId(string)` | İstemci KIMLIĞINI geçersiz kılar.|
|`.WithComponent(string)` | MSAL.NET kullanarak kitaplığın adını ayarlar (telemetri nedenleri için). |
|`.WithDebugLoggingCallback()` | Çağrılırsa, uygulama hata ayıklama izlemelerini etkinleştirmek `Debug.Write` çağırır. Daha fazla bilgi için [günlüğe](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) bakın.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Tüm kimlik doğrulama isteğinde gönderilecek olan uygulama düzeyi ek sorgu parametrelerini ayarlayın. Bu, her bir belirteç alma yöntemi düzeyinde (aynı `.WithExtraQueryParameters pattern`) geçersiz kılınabilir.|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Bir HTTP proxy 'si için yapılandırma gibi gelişmiş senaryolar sağlar veya MSAL belirli bir HttpClient (örneğin ASP.NET Core Web Apps/API 'Lerinde) kullanmasına zorlanır.|
|`.WithLogging()` | Çağrılırsa, uygulama hata ayıklama izlemelerinde bir geri çağırma çağırır. Daha fazla bilgi için [günlüğe](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) bakın.|
|`.WithRedirectUri(string redirectUri)` | Varsayılan yeniden yönlendirme URI 'sini geçersiz kılar. Ortak istemci uygulamaları söz konusu olduğunda, bu, aracı ile ilgili senaryolar için yararlı olacaktır.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Telemetri göndermek için kullanılan temsilciyi ayarlar.|
|`.WithTenantId(string tenantId)` | Kiracı KIMLIĞINI veya kiracı açıklamasını geçersiz kılar.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin. iOS uygulamalarına özgü değiştiriciler

Xamarin. iOS üzerinde ortak bir istemci uygulama Oluşturucusu üzerinde ayarlayabileceğiniz değiştiriciler şunlardır:

|İcisi | Açıklama|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Yalnızca Xamarin. iOS**: iOS anahtar zinciri güvenlik grubunu ayarlar (önbellek kalıcılığı için).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Gizli istemci uygulamalarına özgü değiştiriciler

Gizli bir istemci uygulama Oluşturucusu üzerinde ayarlayabileceğiniz değiştiriciler şunlardır:

|İcisi | Açıklama|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Azure AD ile uygulamayı tanımlayan sertifikayı ayarlar.|
|`.WithClientSecret(string clientSecret)` | Uygulamayı Azure AD ile tanımlayan istemci gizli anahtarını (uygulama parolası) ayarlar.|

Bu değiştiriciler birbirini dışlıyor. Her ikisini de sağlarsanız, MSAL anlamlı bir özel durum oluşturur.

### <a name="example-of-usage-of-modifiers"></a>Değiştiriciler kullanım örneği

Uygulamanızın yalnızca kuruluşunuz için olan iş kolu uygulaması olduğunu varsayalım.  Daha sonra şunu yazabilirsiniz:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

İlginç hale geldiği durumlarda, Ulusal bulutlar için programlama artık basitleştirilmiştir. Uygulamanızın ulusal bir bulutta çok kiracılı bir uygulama olmasını istiyorsanız, örneğin şunu yazabilirsiniz:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS için bir geçersiz kılma da vardır (ADFS 2019 Şu anda desteklenmiyor):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Son olarak, Azure AD B2C geliştiriciyseniz, kiracınızı şu şekilde belirtebilirsiniz:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

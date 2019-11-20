---
title: Java için ADAL MSAL geçiş kılavuzu | Mavisi
titleSuffix: Microsoft identity platform
description: Azure Active Directory kimlik doğrulaması kitaplığı (ADAL) Java uygulamanızı Microsoft kimlik doğrulama kitaplığı 'na (MSAL) geçirmeyi öğrenin.
services: active-directory
author: sangonzal
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/4/2019
ms.author: sagonzal
ms.reviewer: navyari.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 158cbe4f2598722abe3b2ed3a0dd179a21dc1e1c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145263"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Java için ADAL MSAL geçiş kılavuzu

Bu makalede, Microsoft kimlik doğrulama kitaplığı 'nı (ADAL) Azure Active Directory kullanan bir uygulamayı Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanmak üzere geçirmek için gereken değişiklikler vurgulanmaktadır.

Hem Java için Microsoft kimlik doğrulama kitaplığı (MSAL4J) hem de Java için Azure AD kimlik doğrulama kitaplığı (ADAL4J), Azure AD varlıklarının kimliğini doğrulamak için ve Azure AD 'de belirteçleri istemek için kullanılır. Bu aşamada, çoğu geliştirici Azure AD kimlik doğrulama kitaplığı (ADAL) kullanarak belirteçleri isteyerek Azure AD kimliklerinin (iş ve okul hesapları) kimliğini doğrulamak için Azure AD 'de geliştiriciler platformu (v 1.0) ile çalıştık.

MSAL aşağıdaki avantajları sunar:

- Daha yeni Microsoft Identity platform uç noktasını kullandığından, Azure AD Iş ve tüketici (B2C) aracılığıyla Azure AD kimlikleri, Microsoft hesapları ve sosyal ve yerel hesaplar gibi daha geniş bir dizi Microsoft kimliği için kimlik doğrulaması yapabilirsiniz.
- Kullanıcılarınız en iyi çoklu oturum açma deneyimini alacak.
- Uygulamanız artımlı onayı etkinleştirebilir ve Koşullu erişimin desteklenmesi daha kolay olabilir.

Java için MSAL (MSAL4J), Microsoft Identity platformu ile kullanmanızı önerdiğimiz kimlik doğrulama kitaplığıdır. ADAL4J üzerinde yeni özellik uygulanmayacak. Öne çıkan tüm çabalar MSAL iyileştirmeye odaklanılmıştır.

## <a name="differences"></a>Fark

Geliştiriciler için Azure AD (v 1.0) uç noktası (ve ADAL4J) ile çalışıyorsanız, [Microsoft Identity platform (v 2.0) uç noktası hakkında ne kadar farklı olduğunu](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)okumak isteyebilirsiniz.

## <a name="scopes-not-resources"></a>Kapsam kaynakları değil

ADAL4J, kaynaklar için belirteçleri alır, MSAL4J kapsamlar için belirteçleri edinme. Bir dizi MSAL4J sınıfı bir kapsamlar parametresi gerektirir. Bu parametre, istenen izinleri ve istenen kaynakları bildiren dizelerin bir listesidir. Örnek kapsamları görmek için [Microsoft Graph kapsamlarına](https://docs.microsoft.com/graph/permissions-reference) bakın.

## <a name="core-classes"></a>Çekirdek sınıflar

ADAL4J ' de `AuthenticationContext` sınıfı, bir yetkili aracılığıyla güvenlik belirteci hizmeti (STS) veya yetkilendirme sunucusu bağlantısını temsil eder. Ancak, MSAL4J istemci uygulamaları etrafında tasarlanmıştır. İki ayrı sınıf sağlar: `PublicClientApplication` ve `ConfidentialClientApplication` istemci uygulamalarını temsil eder.  İkinci `ConfidentialClientApplication`, bir arka plan uygulaması için uygulama tanımlayıcısı gibi bir gizli anahtarı güvenli bir şekilde korumak için tasarlanan bir uygulamayı temsil eder.

Aşağıdaki tabloda, ADAL4J işlevlerinin yeni MSAL4J işlevlerine nasıl eşlendiğini gösterilmektedir:

| ADAL4J yöntemi| MSAL4J yöntemi|
|------|-------|
|acquireToken (dize kaynağı, ClientCredential kimlik bilgileri, AuthenticationCallback geri çağırma) | acquireToken (ClientCredentialParameters)|
|acquireToken (dize kaynağı, ClientAssertion onaylama, AuthenticationCallback geri çağırma)|acquireToken (ClientCredentialParameters)|
|acquireToken (dize kaynağı, AsymmetricKeyCredential kimlik bilgileri, AuthenticationCallback geri çağırma)|acquireToken (ClientCredentialParameters)|
|acquireToken (dize kaynağı, dize ClientID, dize Kullanıcı adı, dize parolası, AuthenticationCallback geri çağırma)| acquireToken (UsernamePasswordParameters)|
|acquireToken (dize kaynağı, dize ClientID, dize Kullanıcı adı, dize parolası = null, AuthenticationCallback geri çağırma)|acquireToken (IntegratedWindowsAuthenticationParameters)|
|acquireToken (dize kaynağı, Kullanıcı onaylama Kullanıcı onaylama, ClientCredential kimlik bilgileri, AuthenticationCallback geri çağırma)| acquireToken (OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode () | acquireToken (AuthorizationCodeParameters) |
| acquireDeviceCode () ve acquireTokenByDeviceCode ()| acquireToken (DeviceCodeParameters)|
|acquireTokenByRefreshToken ()| Acquiretokensessizce (SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IUser yerine IAccount

ADAL4J tarafından yönetilen kullanıcılar. Bir kullanıcı tek bir insan veya yazılım aracısını temsil ediyorsa, Microsoft Identity sisteminde bir veya daha fazla hesabı olabilir. Örneğin, bir kullanıcının birkaç Azure AD, Azure AD B2C veya Microsoft Kişisel hesabı olabilir.

MSAL4J, `IAccount` arabirimi aracılığıyla hesap kavramını tanımlar. Bu, ADAL4J ' den önemli bir değişiklik olmakla kalmaz, ancak aynı kullanıcının çeşitli hesaplara sahip olabileceğinden ve belki de farklı Azure AD dizinlerinde bile olduğu gerçeğini yakaladığı için bu iyi bir yoldur. MSAL4J, Konuk senaryolarında ana hesap bilgileri sağlandığı için daha iyi bilgiler sağlar.

## <a name="cache-persistence"></a>Önbellek kalıcılığı

ADAL4J belirteç önbelleği için desteğe sahip değildi.
MSAL4J, mümkün olduğunda süresi geçen belirteçleri otomatik olarak yenileyerek ve mümkün olduğunda kullanıcının kimlik bilgilerini sağlaması için gereksiz istemler yaparak belirteç yaşam sürelerinin yönetilmesini kolaylaştırmak için bir [belirteç önbelleği](msal-acquire-cache-tokens.md) ekler.

## <a name="common-authority"></a>Ortak yetkili

V 1.0 'da, `https://login.microsoftonline.com/common` yetkilisini kullanıyorsanız, kullanıcılar herhangi bir Azure Active Directory (AAD) hesabıyla oturum açabilirler (herhangi bir kuruluş için).

V 2.0 'da `https://login.microsoftonline.com/common` yetkilisini kullanıyorsanız, kullanıcılar herhangi bir AAD organizasyonu ile veya hatta bir Microsoft Kişisel hesabı (MSA) ile oturum açabilirler. MSAL4J ' de, herhangi bir AAD hesabıyla oturum açmayı kısıtlamak istiyorsanız, `https://login.microsoftonline.com/organizations` yetkilisini (ADAL4J ile aynı davranış) kullanmanız gerekir. Bir yetkili belirtmek için, `PublicClientApplication` sınıfınızı oluştururken [Publicclientapplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) yönteminde `authority` parametresini ayarlayın.

## <a name="v10-and-v20-tokens"></a>v 1.0 ve v 2.0 belirteçleri

V 1.0 uç noktası (ADAL tarafından kullanılan) yalnızca v 1.0 belirteçlerini yayar.

V 2.0 uç noktası (MSAL tarafından kullanılan), v 1.0 ve v 2.0 belirteçlerini yayabilir. Web API 'sinin uygulama bildiriminin bir özelliği, geliştiricilerin hangi belirteç sürümünün kabul edildiğini seçmesini sağlar. [Uygulama bildirimi](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) başvuru belgelerindeki `accessTokenAcceptedVersion` bakın.

V 1.0 ve v 2.0 belirteçleri hakkında daha fazla bilgi için bkz. [Azure Active Directory erişim belirteçleri](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>ADAL to MSAL geçişi

ADAL4J ' de, yenileme belirteçleri gösterilir ve izin verilen geliştiricilerin onları önbelleğe alma izni vardır. Daha sonra, Kullanıcı artık bağlı olmadığında Kullanıcı adına panoları yenileyen uzun süre çalışan hizmetleri uygulama gibi çözümleri etkinleştirmek için `AcquireTokenByRefreshToken()` kullanır.

MSAL4J, güvenlik nedenleriyle yenileme belirteçleri sunmaz. Bunun yerine, MSAL belirteçleri sizin için yenilemeyi işler.

MSAL4J, ADAL4j ile elde ettiğiniz yenileme belirteçlerini ClientApplication: [Acquiretoken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)içine geçirmenize olanak tanıyan bir API içerir. Bu yöntemde, daha önce kullanılan yenileme belirtecini istediğiniz kapsamlar (kaynaklar) ile birlikte sağlayabilirsiniz. Yenileme belirteci yeni bir tane için değiştirilir ve uygulamanız tarafından kullanılmak üzere önbelleğe alınır.

Aşağıdaki kod parçacığı, bir gizli istemci uygulamasındaki bazı geçiş kodlarını gösterir:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult`, yeni yenileme belirteciniz önbellekte depolanırken bir erişim belirteci ve KIMLIK belirteci döndürür. Uygulama artık bir IAccount da içerecektir:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Şu anda önbellekte olan belirteçleri kullanmak için şunu çağırın:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```

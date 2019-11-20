---
title: MSAL.NET 'e geçiş
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ve .NET için Azure AD kimlik doğrulama kitaplığı (ADAL.NET) ve MSAL.NET 'e geçiş arasındaki farklar hakkında bilgi edinin.
services: active-directory
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
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5059acea753b4b8b7db80bfc79b4bb9718e0484e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175566"
---
# <a name="migrating-applications-to-msalnet"></a>Uygulamaları MSAL.NET 'a geçirme

Hem .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) hem de .NET için Azure AD kimlik doğrulama kitaplığı (ADAL.NET), Azure AD varlıklarının kimliğini doğrulamak ve Azure AD 'nin belirteçleri istemek için kullanılır. Bu aşamada, çoğu geliştirici Azure AD kimlik doğrulama kitaplığı (ADAL) kullanarak belirteçleri isteyerek Azure AD kimliklerinin (iş ve okul hesapları) kimliğini doğrulamak için Azure AD 'de geliştiriciler platformu (v 1.0) ile çalıştık. MSAL kullanma:

- Microsoft Identity platform uç noktasını kullandığından, daha geniş bir Microsoft kimliği kümesinin (Azure AD kimlikleri ve Microsoft hesapları ve sosyal ve yerel hesaplar Azure AD B2C aracılığıyla) kimlik doğrulaması yapabilirsiniz.
- Kullanıcılarınız en iyi çoklu oturum açma deneyimini alacak.
- Uygulamanız artımlı onayı etkinleştirebilir ve Koşullu erişimin desteklenmesi daha kolay olabilir
- yeniliğin avantajlarından yararlanabilirsiniz.

**Msal.net artık Microsoft Identity platformu ile kullanmak için önerilen kimlik doğrulama kitaplığıdır**. ADAL.NET üzerinde yeni özellik uygulanmayacak. Çabalar MSAL geliştirmeye odaklanılmıştır.

Bu makalede, .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ve .NET için Azure AD kimlik doğrulama kitaplığı (ADAL.NET) arasındaki farklar açıklanmakta ve MSAL 'e geçiş yapmanıza yardımcı olur.  

## <a name="differences-between-adal-and-msal-apps"></a>ADAL ve MSAL uygulamaları arasındaki farklılıklar

Çoğu durumda, Microsoft kimlik doğrulama kitaplıklarının en son nesli olan MSAL.NET ve Microsoft Identity platform uç noktasını kullanmak istersiniz. MSAL.NET kullanarak, Azure AD (iş ve okul hesapları), Microsoft (kişisel) hesapları (MSA) veya Azure AD B2C ile uygulamanıza oturum açan kullanıcılar için belirteçler elde edersiniz. 

Geliştiriciler için Azure AD (v 1.0) uç noktası (ve ADAL.NET) hakkında zaten bilgi sahibiyseniz, [Microsoft Identity platform (v 2.0) uç noktası hakkında ne kadar farklı olduğunu](active-directory-v2-compare.md)okumak isteyebilirsiniz.

Ancak, uygulamanızın daha önceki [Active Directory Federasyon Hizmetleri (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services)sürümleriyle oturum açması gerekiyorsa adal.NET kullanmanız gerekir. Daha fazla bilgi için bkz. [ADFS desteği](https://aka.ms/msal-net-adfs-support).

Aşağıdaki resimde, ADAL.NET ve MSAL.NET ![yan yana kod arasındaki farklılıklar özetlenmektedir](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet paketleri ve ad alanları

ADAL.NET, [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet paketinden kullanılır. kullanılacak ad alanı `Microsoft.IdentityModel.Clients.ActiveDirectory`.

MSAL.NET kullanmak için [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet paketini eklemeniz ve `Microsoft.Identity.Client` ad alanını kullanmanız gerekir

### <a name="scopes-not-resources"></a>Kapsam kaynakları değil

ADAL.NET, *kaynaklar*için belirteçleri alır, ancak msal.net *kapsamlar*için belirteçleri elde edin. Bir dizi MSAL.NET AcquireToken geçersiz kılma kapsamları (`IEnumerable<string> scopes`) adlı bir parametre gerektirir. Bu parametre, istenen izinleri ve istenen kaynakları bildiren dizelerin basit bir listesidir. İyi bilinen kapsamlar [Microsoft Graph kapsamlardır](/graph/permissions-reference).

V 1.0 kaynaklarına erişmek için MSAL.NET de mümkündür. Bkz. [bir v 1.0 uygulaması Için kapsamlar](#scopes-for-a-web-api-accepting-v10-tokens)içindeki ayrıntılar. 

### <a name="core-classes"></a>Çekirdek sınıflar

- ADAL.NET, bir yetkili aracılığıyla güvenlik belirteci hizmeti (STS) veya yetkilendirme sunucusu bağlantınızın temsili olarak [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) kullanır. Aksine, MSAL.NET [istemci uygulamaları](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications)etrafında tasarlanmıştır. İki ayrı sınıf sağlar: `PublicClientApplication` ve `ConfidentialClientApplication`

- Belirteçleri alma: ADAL.NET ve MSAL.NET aynı kimlik doğrulama çağrılarına sahiptir (ADAL.NET için`AcquireTokenAsync` ve `AcquireTokenSilentAsync`, ancak farklı parametrelerle `AcquireTokenInteractive` ve `AcquireTokenSilent`). Tek fark, MSAL.NET ' de, her bir AcquireTokenXX çağrısında uygulamanızın `ClientID` artık geçmeme çalışmadır. Aslında, `ClientID` (`IPublicClientApplication` veya `IConfidentialClientApplication`) oluşturulurken yalnızca bir kez ayarlanır.

### <a name="iaccount-not-iuser"></a>IAccount IUser değil

ADAL.NET tarafından yönetilen kullanıcılar. Ancak, bir Kullanıcı insan veya yazılım aracısıdır, ancak Microsoft Identity System (çeşitli Azure AD hesapları, Azure AD B2C, Microsoft kişisel hesapları) içindeki bir veya daha fazla hesaptan sorumlu olabilir/bu hesaplardan sorumlu olabilir. 

MSAL.NET 2. x artık hesap kavramını (IAccount arabirimi aracılığıyla) tanımlıyor. Bu son değişiklik, doğru semantiğini sağlar: aynı kullanıcının farklı Azure AD dizinlerinde birçok hesabı olabilir. Ayrıca MSAL.NET, ana hesap bilgileri sağlandığı için konuk senaryolarında daha iyi bilgiler sağlar.

IUser ve IAccount arasındaki farklar hakkında daha fazla bilgi için bkz. [msal.NET 2. x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Özel Durumlar

#### <a name="interaction-required-exceptions"></a>Etkileşim gerekli özel durumları

MSAL.NET daha açık özel durumlara sahiptir. Örneğin, ADAL 'da sessiz kimlik doğrulaması başarısız olduğunda, yordam özel durumu yakalamalı ve `user_interaction_required` hata kodunu arayacaktır:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

ADAL.NET ile [belirteç almak için önerilen düzende](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) ayrıntılara bakın

MSAL.NET kullanarak, [Acquiretokensilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token)' de açıklandığı gibi `MsalUiRequiredException` yakalayın.

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Talep sınama özel durumlarını işleme

ADAL.NET ' de, talep sınama özel durumları aşağıdaki şekilde işlenir:

- `AdalClaimChallengeException`, bir kaynağın kullanıcıdan daha fazla talep gerektirmesi durumunda (örneğin iki etmenle kimlik doğrulaması için), hizmet tarafından oluşturulan bir özel durumdur (`AdalServiceException`türetiliyor). `Claims` üyesi, talepler içeren bazı JSON parçaları içeriyor, bu da bekleniyor.
- Hala ADAL.NET içinde, bu özel durumu alan genel istemci uygulamasının, bir talep parametresine sahip `AcquireTokenInteractive` geçersiz kılmayı çağırması gerekir. Bu `AcquireTokenInteractive` geçersiz kılma, gerekli olmadığı için önbellekte de yararlanmaya çalışmaz. Bu nedenle, önbellekteki belirtecin doğru talepler yoktur (Aksi takdirde bir `AdalClaimChallengeException` oluşturulmaz). Bu nedenle, önbelleğe bakmamız gerekmez. `ClaimChallengeException`, bu Web API 'sini çağıran bir ortak istemci uygulamasında `AcquireTokenInteractive` çağrılması gerektiğinden, bir WebAPI, OBO 'da alınılabileceğini unutmayın.
- örnekler de dahil olmak üzere Ayrıntılar için bkz. [Adalclaimbir Geexception](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception) işleme

MSAL.NET ' de, talep sınama özel durumları aşağıdaki şekilde işlenir:

- `Claims` `MsalServiceException`ortaya çıkmış.
- `AcquireTokenInteractive` oluşturucuya uygulanabilecek bir `.WithClaim(claims)` yöntemi vardır. 

### <a name="supported-grants"></a>Desteklenen izin

Tüm izin verilmez, MSAL.NET ve v 2.0 uç noktasında henüz desteklenmez. Aşağıda, ADAL.NET ve MSAL ' i karşılaştıran bir Özet verilmiştir. NET 'in desteklenen izni.

#### <a name="public-client-applications"></a>Ortak istemci uygulamaları

Masaüstü ve mobil uygulamalar için ADAL.NET ve MSAL.NET içinde desteklenen izin verir

Semantiği | ADAL.NET | MSAL.NET
----- |----- | -----
Etkileşimli | [Etkileşimli kimlik doğrulaması](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [MSAL.NET içinde belirteçleri etkileşimli olarak alma](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Tümleşik Windows kimlik doğrulaması | [Windows üzerinde tümleşik kimlik doğrulaması (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Tümleşik Windows kimlik doğrulaması](msal-authentication-flows.md#integrated-windows-authentication)
Kullanıcı adı/parola | [Kullanıcı adı ve parola ile belirteçler alınıyor](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Kullanıcı adı parola kimlik doğrulaması](msal-authentication-flows.md#usernamepassword)
Cihaz kod akışı | [Web tarayıcıları olmayan cihazlar için cihaz profili](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Cihaz kod akışı](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Gizli istemci uygulamaları

Web uygulamaları, Web API 'Leri ve Daemon uygulamaları için ADAL.NET ve MSAL.NET içinde desteklenen izin verir:

Uygulama türü | Semantiği | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web uygulaması, Web API 'SI, Daemon | İstemci kimlik bilgileri | [ADAL.NET 'deki istemci kimlik bilgileri akışları](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Msal.net 'Deki istemci kimlik bilgileri akışları](msal-authentication-flows.md#client-credentials))
Web API’si | Adına | [ADAL.NET ile Kullanıcı adına çağrı hizmeti](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [MSAL.NET içindeki adına](msal-authentication-flows.md#on-behalf-of)
Web Uygulaması | Kimlik doğrulama kodu | [ADAL.NET ile Web Apps 'teki yetkilendirme kodlarıyla belirteç alma](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [MSAL.NET ile Web Apps 'teki yetkilendirme kodlarıyla belirteç alma](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Önbellek kalıcılığı

ADAL.NET, `BeforeAccess`ve `BeforeWrite` yöntemlerini kullanarak güvenli bir depolama (.NET Framework ve .NET Core) olmayan platformlarda istenen Kalıcılık işlevlerini uygulamak için `TokenCache` sınıfını genişletmenizi sağlar. Ayrıntılar için bkz. [adal.net Içinde belirteç önbelleği serileştirme](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET, belirteç önbelleğini, genişletme özelliğini kaldırarak kapalı bir sınıf haline getirir. Bu nedenle, belirteç önbelleği kalıcılığı uygulamanız, korumalı belirteç önbelleği ile etkileşim kuran bir yardımcı sınıf biçiminde olmalıdır. Bu etkileşim, MSAL.NET sürümündeki [belirteç önbelleği serileştirme](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)bölümünde açıklanmaktadır.

## <a name="signification-of-the-common-authority"></a>Ortak yetkiyi belirtir

V 1.0 'da, https://login.microsoftonline.com/common yetkilisini kullanıyorsanız, kullanıcıların herhangi bir AAD hesabıyla oturum açmalarına izin verirsiniz (herhangi bir kuruluş için). Bkz. [adal.net 'de yetkili doğrulaması](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

V 2.0 'da https://login.microsoftonline.com/common yetkilisini kullanıyorsanız, kullanıcıların herhangi bir AAD organizasyonu veya bir Microsoft Kişisel hesabı (MSA) ile oturum açmalarına izin verirsiniz. MSAL.NET ' de, oturum açmayı herhangi bir AAD hesabıyla kısıtlamak isterseniz (ADAL.NET ile aynı davranış), https://login.microsoftonline.com/organizations kullanmanız gerekir. Ayrıntılar için bkz. [genel istemci uygulamasındaki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)`authority` parametresi.

## <a name="v10-and-v20-tokens"></a>v 1.0 ve v 2.0 belirteçleri

Belirteçlerin iki sürümü vardır:
- v 1.0 belirteçleri
- v 2.0 belirteçleri 

V 1.0 uç noktası (ADAL tarafından kullanılan) yalnızca v 1.0 belirteçlerini yayar.

Ancak, v 2.0 uç noktası (MSAL tarafından kullanılan), Web API 'sinin kabul ettiği belirtecin sürümünü yayar. Web API 'sinin uygulama bildiriminin bir özelliği, geliştiricilerin hangi belirteç sürümünün kabul edildiğini seçmesini sağlar. [Uygulama bildirimi](reference-app-manifest.md) başvuru belgelerindeki `accessTokenAcceptedVersion` bakın.

V 1.0 ve v 2.0 belirteçleri hakkında daha fazla bilgi için bkz. [Azure Active Directory erişim belirteçleri](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>V 1.0 belirteçlerini kabul eden bir Web API 'SI için kapsamlar

OAuth2 izinleri, bir v 1.0 Web API (kaynak) uygulamasının istemci uygulamaları için sunduğu izin kapsamlardır. Bu izin kapsamları, izin sırasında istemci uygulamalarına verilebilir. [Azure Active Directory uygulama bildiriminde](active-directory-application-manifest.md)oauth2Permissions hakkında bölümüne bakın.

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Bir v 1.0 uygulamasının belirli OAuth2 izinlerine erişim istemek için kapsamlar

Bir v 1.0 uygulamasının belirli kapsamları için belirteçler almak istiyorsanız (örneğin, AAD https://graph.windows.net) grafiğinde, bu kaynak için istenen bir OAuth2 iznine sahip istenen bir kaynak tanımlayıcısını birleştirerek `scopes` oluşturmanız gerekir.

Örneğin, Kullanıcı adına, uygulama KIMLIĞI URI 'sinin `ResourceId`bir v 1.0 Web API 'sine erişmek için şunu kullanmak isteyeceksiniz:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

AAD Graph API 'sini kullanarak MSAL.NET Azure Active Directory okumak ve yazmak istiyorsanız (https://graph.windows.net/) , aşağıdaki kod parçacığında olduğu gibi kapsamların bir listesini oluşturursunuz:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Uyarı: bir v 1.0 Web API 'sine karşılık gelen kapsamda bir veya iki eğik çizgi olmalıdır

Azure Resource Manager API 'sine karşılık gelen kapsamı yazmak istiyorsanız (https://management.core.windows.net/) , aşağıdaki kapsamı istemeniz gerekir (iki eğik çizgi) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Bunun nedeni, Kaynak Yöneticisi API 'sinin hedef kitle talebinde (`aud`) bir eğik çizgi beklediği ve sonra API adını kapsamdan ayıran bir eğik çizgi vardır.

Azure AD tarafından kullanılan mantık şunlardır:
- Bir v 1.0 erişim belirtecine sahip ADAL (v 1.0) uç noktası (tek olası), AUD = kaynak
- MSAL (v 2.0 uç noktası) için, v 2.0 belirteçleri, AUD = kaynağı kabul eden bir kaynak için erişim belirteci soran bir. AppID
- MSAL (v 2.0 uç noktası) için, bir v 1.0 erişim belirtecini kabul eden bir kaynak için erişim belirteci isteyen (Yukarıdaki durum), Azure AD, son eğik çizgiden önce her şeyi alarak ve bunu kaynak tanımlayıcısı olarak kullanarak istenen kapsamdaki hedef kitleyi ayrıştırır. Bu nedenle, https:\//database.windows.net "https://database.windows.net/ " kitlesini beklediğinde, https:\/ /database.windows.net//.default kapsamını istemeniz gerekir. Ayrıca bkz. sorun #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): kaynak URL 'sinin sondaki eğik çizgi atlandığından SQL kimlik doğrulama hatasına neden oldu #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Bir v 1.0 uygulamasının tüm izinlerine erişim istemek için kapsamlar

Örneğin, bir v 1.0 uygulamasının tüm statik kapsamları için bir belirteç elde etmek istiyorsanız, bunun kullanılması gerekir

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>İstemci kimlik bilgisi akışı/Daemon uygulaması durumunda istek yapılacak kapsamlar

İstemci kimlik bilgileri akışı durumunda geçirilecek kapsam de `/.default`olacaktır. Bu kapsam, Azure AD 'ye bildirir: "yöneticinin uygulama kaydında sahip olduğu tüm uygulama düzeyi izinleri.

## <a name="adal-to-msal-migration"></a>ADAL to MSAL geçişi

ADAL.NET v2. X, yenileme belirteçleri kullanıma sunulmuştur ve ADAL 2. x tarafından sunulan `AcquireTokenByRefreshToken` yöntemlerini kullanarak bu belirteçlerin kullanımı etrafında çözümler geliştirmenize olanak tanır. Bu çözümlerden bazıları gibi senaryolarda kullanılmıştır:
* Kullanıcıların adına panoları yenileme dahil olmak üzere, uzun süre çalışan hizmetler, kullanıcılar artık bağlı değildir. 
* İstemcinin Web hizmetine RT 'yi (önbelleğe alma işlemi tamamlandı istemci tarafı, şifreli tanımlama bilgisi ve sunucu tarafı değil) almasını sağlayan WebFarm senaryoları

MSAL.NET, güvenlik nedenleriyle yenileme belirteçleri sunmaz: MSAL, sizin için belirteçleri yenilemeyi işler. 

Neyse ki, MSAL.NET artık önceki yenileme belirteçlerinizi (ADAL ile elde edilen) `IConfidentialClientApplication`içine geçirmenize izin veren bir API 'ye sahiptir:

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Bu yöntemde, daha önce kullanılan yenileme belirtecini istediğiniz kapsamlar (kaynaklar) ile birlikte sağlayabilirsiniz. Yenileme belirteci yeni bir tane için değiştirilir ve uygulamanızda önbelleğe kaydedilir.  

Bu yöntem, tipik olmayan senaryolar için düşünülbildiği için, `IConfidentialClientApplication` önce `IByRefreshToken`' a bırakmadan, bu yönteme kolayca erişilemez.

Bu kod parçacığı, bir gizli istemci uygulamasındaki bazı geçiş kodlarını gösterir. `GetCachedRefreshTokenForSignedInUser`, bazı depolamada depolanan yenileme belirtecini, uygulamanın ADAL 2. x ' i kullanmasını sağlamak için kullanılan önceki bir sürümüyle elde edin. `GetTokenCacheForSignedInUser`, oturum açmış kullanıcı için bir önbelleği seri hale getirir (gizli istemci uygulamalarının Kullanıcı başına bir önbelleği olması gerekir).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Yeni yenileme belirteciniz önbellekte depolandığından, AuthenticationResult içinde bir erişim belirteci ve KIMLIK belirteci görürsünüz.

Ayrıca, kullanılabilir yenileme belirteciniz olan çeşitli tümleştirme senaryolarında bu yöntemi kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Microsoft Identity platform uç noktasındaki kapsamlar, izinler ve onay](v2-permissions-and-consent.md) içindeki kapsamlar hakkında daha fazla bilgi edinebilirsiniz

---
title: MSAL.NET 'e geçiş
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ve .NET için Azure AD kimlik doğrulama kitaplığı (ADAL.NET) ve MSAL.NET 'e geçiş arasındaki farklar hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 0e7dc3540dc54e0563a5ea416510bddb9a41fb65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861706"
---
# <a name="migrating-applications-to-msalnet"></a>Uygulamaları MSAL.NET 'a geçirme

Hem .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) hem de .NET için Azure AD kimlik doğrulama kitaplığı (ADAL.NET), Azure AD varlıklarının kimliğini doğrulamak ve Azure AD 'de belirteçleri istemek için kullanılır. Bu aşamada, çoğu geliştirici Azure AD kimlik doğrulama kitaplığı (ADAL) kullanarak belirteçleri isteyerek Azure AD kimliklerinin (iş ve okul hesapları) kimliğini doğrulamak için Azure AD 'de geliştiriciler platformu (v 1.0) ile çalıştık. MSAL kullanma:

- Microsoft Identity platformunu kullandığından, daha geniş bir Microsoft kimliği kümesinin (Azure AD kimlikleri ve Microsoft hesapları ve sosyal ve yerel hesaplar Azure AD B2C aracılığıyla) kimlik doğrulaması yapabilirsiniz.
- Kullanıcılarınız en iyi çoklu oturum açma deneyimini alacak.
- Uygulamanız artımlı onayı etkinleştirebilir ve Koşullu erişimin desteklenmesi daha kolay olabilir
- yeniliğin avantajlarından yararlanabilirsiniz.

**Msal.NET veya Microsoft. Identity. Web artık Microsoft Identity platformu ile kullanmak için önerilen kimlik doğrulama kitaplıklarıdır**. ADAL.NET üzerinde yeni özellik uygulanmayacak. Çabalar MSAL geliştirmeye odaklanılmıştır.

Bu makalede, .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ve .NET için Azure AD kimlik doğrulama kitaplığı (ADAL.NET) arasındaki farklar açıklanmakta ve MSAL 'e geçiş yapmanıza yardımcı olur.

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>MSAL.NET 'e veya Microsoft. Identity. Web 'e geçirmeniz gerekir

MSAL.NET vs ADAL.NET ayrıntılarına tıklamadan önce, MSAL.NET veya [Microsoft. Identity. Web](microsoft-identity-web.md) gibi daha yüksek düzeyde bir soyutlama kullanmak istediğinizi denetlemek isteyebilirsiniz.

Aşağıdaki karar ağacı hakkında daha fazla bilgi için, okuma [yalnızca msal.net mi? ne de daha yüksek düzeyde bir soyutlama mi kullanmalıyım?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Is-MSAL.NET-right-for-me%3F)

:::image type="content" source="media/msal-net-migration/decision-diagram.png" alt-text="ADAL.NET 'den geçiş yaparken MSAL.NET ve Microsoft. Identity. Web 'i veya her ikisini de kullanmanız gerekiyorsa nasıl seçleyeceğinizi açıklayan blok diyagramı":::

## <a name="differences-between-adal-and-msal-apps"></a>ADAL ve MSAL uygulamaları arasındaki farklılıklar

Çoğu durumda, Microsoft kimlik doğrulama kitaplıklarının en son nesli olan MSAL.NET ve Microsoft Identity platformunu kullanmak istersiniz. MSAL.NET kullanarak, Azure AD (iş ve okul hesapları), Microsoft (kişisel) hesapları (MSA) veya Azure AD B2C ile uygulamanıza oturum açan kullanıcılar için belirteçler elde edersiniz.

Azure AD for Developers (v 1.0) uç noktası (ve ADAL.NET) hakkında zaten bilgi sahibiyseniz, [Microsoft Identity platformu hakkında ne kadar farklı olduğunu](../azuread-dev/azure-ad-endpoint-comparison.md)okumak isteyebilirsiniz.

Ancak, uygulamanızın daha önceki [Active Directory Federasyon Hizmetleri (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services)sürümleriyle oturum açması gerekiyorsa adal.NET kullanmanız gerekir. Daha fazla bilgi için bkz. [ADFS desteği](https://aka.ms/msal-net-adfs-support).

Aşağıdaki resimde, bir genel istemci uygulamasının yan yana kodu için ADAL.NET ve MSAL.NET arasındaki farklılıklar özetlenmektedir ![](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet paketleri ve ad alanları

ADAL.NET, [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet paketinden kullanılır. kullanılacak ad alanı `Microsoft.IdentityModel.Clients.ActiveDirectory` .

MSAL.NET kullanmak için [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet paketini eklemeniz ve `Microsoft.Identity.Client` ad alanını kullanmanız gerekir

### <a name="scopes-not-resources"></a>Kapsam kaynakları değil

ADAL.NET, *kaynaklar* için belirteçleri alır, ancak msal.net *kapsamlar* için belirteçleri elde edin. Bir dizi MSAL.NET AcquireToken geçersiz kılma kapsamları () adlı bir parametre gerektirir `IEnumerable<string> scopes` . Bu parametre, istenen izinleri ve istenen kaynakları bildiren dizelerin basit bir listesidir. İyi bilinen kapsamlar [Microsoft Graph kapsamlardır](/graph/permissions-reference).

V 1.0 kaynaklarına erişmek için MSAL.NET de mümkündür. Bkz. [bir v 1.0 uygulaması Için kapsamlar](#scopes-for-a-web-api-accepting-v10-tokens)içindeki ayrıntılar.

### <a name="core-classes"></a>Çekirdek sınıflar

- ADAL.NET, bir yetkili aracılığıyla güvenlik belirteci hizmeti (STS) veya yetkilendirme sunucusu bağlantınızın temsili olarak [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) kullanır. Aksine, MSAL.NET [istemci uygulamaları](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications)etrafında tasarlanmıştır. İki ayrı sınıf sağlar: `PublicClientApplication` ve `ConfidentialClientApplication`

- Belirteçleri alma: ADAL.NET ve MSAL.NET aynı kimlik doğrulama çağrılarına sahiptir ( `AcquireTokenAsync` ve `AcquireTokenSilentAsync` adal.net, ve `AcquireTokenInteractive` msal.NET için `AcquireTokenSilent` ), ancak farklı parametrelerle gereklidir. Tek fark, MSAL.NET ' de, `ClientID` her AcquireTokenXX çağrısında uygulamanızın içinde geçiş yapmak zorunda kalmayın. Aslında, `ClientID` (veya) derlerken yalnızca bir kez ayarlanır `IPublicClientApplication` `IConfidentialClientApplication` .

### <a name="iaccount-not-iuser"></a>IAccount IUser değil

ADAL.NET tarafından yönetilen kullanıcılar. Ancak, bir Kullanıcı insan veya yazılım aracısıdır, ancak Microsoft Identity System (çeşitli Azure AD hesapları, Azure AD B2C, Microsoft kişisel hesapları) içindeki bir veya daha fazla hesaptan sorumlu olabilir/bu hesaplardan sorumlu olabilir.

MSAL.NET 2. x artık hesap kavramını (IAccount arabirimi aracılığıyla) tanımlıyor. Bu son değişiklik, doğru semantiğini sağlar: aynı kullanıcının farklı Azure AD dizinlerinde birçok hesabı olabilir. Ayrıca MSAL.NET, ana hesap bilgileri sağlandığı için konuk senaryolarında daha iyi bilgiler sağlar.

IUser ve IAccount arasındaki farklar hakkında daha fazla bilgi için bkz. [msal.NET 2. x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Özel durumlar

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

MSAL.NET kullanarak, `MsalUiRequiredException` [Acquiretokensilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token)bölümünde açıklandığı gibi yakalarsanız.

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Talep sınama özel durumlarını işleme

ADAL.NET ' de, talep sınama özel durumları aşağıdaki şekilde işlenir:

- `AdalClaimChallengeException` , `AdalServiceException` bir kaynağın kullanıcıdan daha fazla talep gerektirmesi durumunda (örneğin iki etmenle kimlik doğrulaması için), hizmet tarafından oluşturulan bir özel durumdur (türetiliyor). `Claims`Üye, talepler içeren bazı JSON parçaları içeriyor, bu da bekleniyor.
- Hala ADAL.NET sürümünde, bu özel durumu alan ortak istemci uygulamasının, `AcquireTokenInteractive` bir talep parametresine sahip geçersiz kılmayı çağırması gerekir. Bu geçersiz kılma, `AcquireTokenInteractive` gerekli olmadığı için önbellekte vurmasına bile gerek yoktur. Bu nedenle, önbellekteki belirtecin doğru talepler yoktur (Aksi halde, bir şekilde oluşturulmaz `AdalClaimChallengeException` ). Bu nedenle, önbelleğe bakmamız gerekmez. `ClaimChallengeException` `AcquireTokenInteractive` Bu Web API 'sini çağıran bir ortak istemci uygulamasında çağrılması gerektiğinde, bir WebAPI, OBO 'da alınılabileceğini unutmayın.
- örnekler de dahil olmak üzere Ayrıntılar için bkz. [Adalclaimbir Geexception](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception) işleme

MSAL.NET ' de, talep sınama özel durumları aşağıdaki şekilde işlenir:

- , `Claims` İçinde ortaya çıkmış `MsalServiceException` .
- `.WithClaim(claims)`Oluşturucuya uygulanabilecek bir yöntem vardır `AcquireTokenInteractive` .

### <a name="supported-grants"></a>Desteklenen izin

Tüm izin verilmez, MSAL.NET ve v 2.0 uç noktasında henüz desteklenmez. Aşağıda, ADAL.NET ve MSAL ' i karşılaştıran bir Özet verilmiştir. NET 'in desteklenen izni.

#### <a name="public-client-applications"></a>Ortak istemci uygulamaları

Masaüstü ve mobil uygulamalar için ADAL.NET ve MSAL.NET içinde desteklenen izin verir

İzin verme | ADAL.NET | MSAL.NET
----- |----- | -----
Etkileşimli | [Etkileşimli kimlik doğrulaması](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [MSAL.NET içinde belirteçleri etkileşimli olarak alma](scenario-desktop-acquire-token.md?tabs=dotnet#acquire-a-token-interactively)
Tümleşik Windows Kimlik Doğrulaması | [Windows üzerinde tümleşik kimlik doğrulaması (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Tümleşik Windows Kimlik Doğrulaması](scenario-desktop-acquire-token.md?tabs=dotnet#integrated-windows-authentication)
Kullanıcı adı/parola | [Kullanıcı adı ve parola ile belirteçler alınıyor](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Kullanıcı adı parola kimlik doğrulaması](scenario-desktop-acquire-token.md?tabs=dotnet#username-and-password)
Cihaz kod akışı | [Web tarayıcıları olmayan cihazlar için cihaz profili](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Cihaz kod akışı](scenario-desktop-acquire-token.md?tabs=dotnet#command-line-tool-without-a-web-browser)

#### <a name="confidential-client-applications"></a>Gizli istemci uygulamaları

Web uygulamaları, Web API 'Leri ve Daemon uygulamaları için ADAL.NET, MSAL.NET ve Microsoft. Identity. Web 'de desteklenen izin verir:

Uygulama türü | İzin verme | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web uygulaması, Web API 'SI, Daemon | İstemci kimlik bilgileri | [ADAL.NET 'deki istemci kimlik bilgileri akışları](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [MSAL.NET 'deki istemci kimlik bilgileri akışları](scenario-daemon-acquire-token.md?tabs=dotnet#acquiretokenforclient-api)
Web API | Adına | [ADAL.NET ile Kullanıcı adına çağrı hizmeti](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [MSAL.NET içindeki adına](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)
Web uygulaması | Kimlik doğrulama kodu | [ADAL.NET ile Web Apps 'teki yetkilendirme kodlarıyla belirteç alma](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [MSAL.NET ile Web Apps 'teki yetkilendirme kodlarıyla belirteç alma](scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

### <a name="cache-persistence"></a>Önbellek kalıcılığı

ADAL.NET `TokenCache` , ve yöntemlerini kullanarak, güvenli depolama (.NET Framework ve .NET Core) olmayan platformlarda istenen Kalıcılık işlevlerini uygulamak için sınıfını genişletmenizi sağlar `BeforeAccess` `BeforeWrite` . Ayrıntılar için bkz. [adal.net Içinde belirteç önbelleği serileştirme](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET, belirteç önbelleğini, genişletme özelliğini kaldırarak kapalı bir sınıf haline getirir. Bu nedenle, belirteç önbelleği kalıcılığı uygulamanız, korumalı belirteç önbelleği ile etkileşim kuran bir yardımcı sınıf biçiminde olmalıdır. Bu etkileşim, MSAL.NET sürümündeki [belirteç önbelleği serileştirme](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)bölümünde açıklanmaktadır.

## <a name="signification-of-the-common-authority"></a>Ortak yetkiyi belirtir

V 1.0 sürümünde, `https://login.microsoftonline.com/common` yetkilisini kullanıyorsanız, kullanıcıların herhangi BIR AAD hesabıyla oturum açmalarına izin verirsiniz (herhangi bir kuruluş için). Bkz. [adal.net 'de yetkili doğrulaması](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

`https://login.microsoftonline.com/common`Yetkilisini v 2.0 'da kullanıyorsanız, kullanıcıların herhangi BIR AAD organizasyonu veya Microsoft Kişisel hesabı (MSA) ile oturum açmalarına izin verirsiniz. MSAL.NET ' de, oturum açmayı herhangi bir AAD hesabıyla kısıtlamak isterseniz (ADAL.NET ile aynı davranış), kullanın `https://login.microsoftonline.com/organizations` . Ayrıntılar için bkz `authority` . [genel istemci uygulamasındaki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)parametresi.

## <a name="v10-and-v20-tokens"></a>v 1.0 ve v 2.0 belirteçleri

Belirteçlerin iki sürümü vardır:
- v 1.0 belirteçleri
- v 2.0 belirteçleri

V 1.0 uç noktası (ADAL tarafından kullanılan) yalnızca v 1.0 belirteçlerini yayar.

Ancak, v 2.0 uç noktası (MSAL tarafından kullanılan), Web API 'sinin kabul ettiği belirtecin sürümünü yayar. Web API 'sinin uygulama bildiriminin bir özelliği, geliştiricilerin hangi belirteç sürümünün kabul edildiğini seçmesini sağlar. Bkz `accessTokenAcceptedVersion` . [uygulama bildirimi](reference-app-manifest.md) başvuru belgeleri.

V 1.0 ve v 2.0 belirteçleri hakkında daha fazla bilgi için bkz. [Azure Active Directory erişim belirteçleri](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>V 1.0 belirteçlerini kabul eden bir Web API 'SI için kapsamlar

OAuth2 izinleri, bir v 1.0 Web API (kaynak) uygulamasının istemci uygulamaları için sunduğu izin kapsamlardır. Bu izin kapsamları, izin sırasında istemci uygulamalarına verilebilir. [Azure Active Directory uygulama bildiriminde](./reference-app-manifest.md)oauth2Permissions hakkında bölümüne bakın.

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Bir v 1.0 uygulamasının belirli OAuth2 izinlerine erişim istemek için kapsamlar

V 1.0 belirteçlerini kabul eden bir uygulama için belirteçler almak istiyorsanız Microsoft Graph (diğer bir deyişle, https://graph.microsoft.com) `scopes` Bu kaynak için Istenen bir OAuth2 izniyle istenen kaynak tanımlayıcısını birleştirerek oluşturmanız gerekir.

Örneğin, Kullanıcı adına, uygulama KIMLIĞI URI 'SI olan bir v 1.0 Web API 'sine erişmek için `ResourceId` şunu kullanmak isteyeceksiniz:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Microsoft Graph API 'sini kullanarak MSAL.NET Azure Active Directory okumak ve yazmak istiyorsanız ( https://graph.microsoft.com/) Aşağıdaki kod parçacığında olduğu gibi kapsamların bir listesini oluşturursunuz:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Uyarı: bir v 1.0 Web API 'sine karşılık gelen kapsamda bir veya iki eğik çizgi olmalıdır

Azure Resource Manager API 'sine karşılık gelen kapsamı yazmak isterseniz ( https://management.core.windows.net/) aşağıdaki kapsamı isteyin (iki eğik çizgi).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Bunun nedeni, Kaynak Yöneticisi API 'sinin hedef kitle talebinde () bir eğik çizgi beklediği `aud` ve sonra API adını kapsamdan ayıran bir eğik çizgi vardır.

Azure AD tarafından kullanılan mantık şunlardır:
- Bir v 1.0 erişim belirtecine sahip ADAL (v 1.0) uç noktası (tek olası), AUD = kaynak
- MSAL (v 2.0 uç noktası) için, v 2.0 belirteçleri, AUD = kaynağı kabul eden bir kaynak için erişim belirteci soran bir. AppID
- MSAL (v 2.0 uç noktası) için, bir v 1.0 erişim belirtecini kabul eden bir kaynak için erişim belirteci isteyen (Yukarıdaki durum), Azure AD, son eğik çizgiden önce her şeyi alarak ve bunu kaynak tanımlayıcısı olarak kullanarak istenen kapsamdaki hedef kitleyi ayrıştırır. Bu nedenle, https: \/ /Database.Windows.net "" kitlesini beklediğinde https://database.windows.net/ , https:/Database.Windows.net//.Default kapsamını istemeniz gerekir \/ . Ayrıca bkz. sorun #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): kaynak URL 'sinin sondaki eğik çizgi atlandığından SQL kimlik doğrulama hatasına neden oldu #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Bir v 1.0 uygulamasının tüm izinlerine erişim istemek için kapsamlar

Örneğin, bir v 1.0 uygulamasının tüm statik kapsamları için bir belirteç elde etmek istiyorsanız, bunun kullanılması gerekir

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>İstemci kimlik bilgisi akışı/Daemon uygulaması durumunda istek yapılacak kapsamlar

İstemci kimlik bilgileri akışı durumunda geçirilecek kapsam de olur `/.default` . Bu kapsam, Azure AD 'ye bildirir: "yöneticinin uygulama kaydında sahip olduğu tüm uygulama düzeyi izinleri.

## <a name="adal-to-msal-migration"></a>ADAL to MSAL geçişi

ADAL.NET v2. X, yenileme belirteçleri kullanıma sunulmuştur ve `AcquireTokenByRefreshToken` adal 2. x tarafından sunulan yöntemleri kullanarak bu belirteçlerin kullanımı etrafında çözümler geliştirmenize olanak tanır.
Bu çözümlerden bazıları gibi senaryolarda kullanılmıştır:
* Kullanıcıların adına panoları yenileme dahil olmak üzere, uzun süre çalışan hizmetler, kullanıcılar artık bağlı değildir.
* İstemcinin Web hizmetine RT 'yi (önbelleğe alma işlemi tamamlandı istemci tarafı, şifreli tanımlama bilgisi ve sunucu tarafı değil) almasını sağlayan WebFarm senaryoları

MSAL.NET, güvenlik nedenleriyle yenileme belirteçleri sunmaz: MSAL, sizin için belirteçleri yenilemeyi işler.

Neyse ki, MSAL.NET artık önceki yenileme belirteçlerinizi (ADAL ile elde edilen) içine geçirmenize izin veren bir API 'ye sahiptir `IConfidentialClientApplication` :

```csharp
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

Bu yöntem, tipik olmayan senaryolar için düşünülbildiği için, bu, `IConfidentialClientApplication` ilk olarak kendisine atama yapılmadan ile erişilebilir değildir `IByRefreshToken` .

Bu kod parçacığı, bir gizli istemci uygulamasındaki bazı geçiş kodlarını gösterir. `GetCachedRefreshTokenForSignedInUser` bir depolama alanında depolanan yenileme belirtecini, ADAL 2. x 'ten yararlanmak için kullanılan uygulamanın önceki bir sürümüyle elde edin. `GetTokenCacheForSignedInUser` oturum açan kullanıcı için bir önbelleği kaldırır (gizli istemci uygulamalarının Kullanıcı başına bir önbelleği olması gerekir).

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

[Microsoft Identity platformunda kapsamlar, izinler ve onay](v2-permissions-and-consent.md) içindeki kapsamlar hakkında daha fazla bilgi edinebilirsiniz

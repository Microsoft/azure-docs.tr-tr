---
title: Azure AD B2C ve MSAL.NET
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı ile Azure AD B2C kullanırken dikkat edilecek noktalar (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: cdbba1618815dbc8b52195169b7de2bcec2986d8
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255588"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Kullanıcıları sosyal kimliklerle oturum açmak için MSAL.NET kullanma

[Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md)kullanarak sosyal kimliklerle kullanıcıları oturum açmak için msal.net kullanabilirsiniz. Azure AD B2C, ilke kavramı etrafında oluşturulmuştur. MSAL.NET ' de, bir ilke belirtildiğinde bir yetkili sağlamaya çevrilir.

- Ortak istemci uygulamasını başlattığınızda, ilkeyi yetkilinin bir parçası olarak belirtin.
- Bir ilke uygulamak istediğinizde, parametresini kabul eden bir geçersiz kılma çağırın `AcquireTokenInteractive` `authority` .

Bu makale, MSAL.NET 3. x için geçerlidir. MSAL.NET 2. x için bkz. GitHub 'da MSAL.NET wiki içindeki [msal 2. x içinde Azure AD B2C özellikleri](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) .

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C kiracı ve ilke için yetki

Azure AD B2C için yetki biçimi: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` -Azure AD B2C kiracının ve ana bilgisayarın adı. Örneğin, *contosob2c.b2clogin.com*.
- `tenant` -Azure AD B2C kiracının etki alanı adı veya dizin (kiracı) KIMLIĞI. Örneğin, sırasıyla *contosob2c.onmicrosoft.com* veya GUID.
- `policyName` -Uygulanacak Kullanıcı akışı veya özel ilke adı. Örneğin, *b2c_1_susi* gibi bir kaydolma/oturum açma ilkesi.

Azure AD B2C yetkilileri hakkında daha fazla bilgi için bkz. [yeniden yönlendirme URL 'lerini b2clogin.com 'A ayarlama](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Uygulamanın örneğini oluşturma

`WithB2CAuthority()`Uygulama nesnesini oluştururken çağırarak yetkiyi sağlayın:

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>İlke uygulamak için belirteç alma

Ortak bir istemci uygulamasındaki Azure AD B2C korumalı bir API için belirteç alma, geçersiz kılmaları bir yetkiliyle kullanmanızı gerektirir:

```csharp
AuthenticationResult authResult = null;
IEnumerable<IAccount> accounts = await application.GetAccountsAsync(policy);
IAccount account = accounts.FirstOrDefault();
try
{
    authResult = await application.AcquireTokenSilent(scopes, account)
                      .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    authResult = await application.AcquireTokenInteractive(scopes)
                        .WithAccount(account)
                        .WithParentActivityOrWindow(ParentActivityOrWindow)
                        .ExecuteAsync();
}  
```

Önceki kod parçacığında:

- `policy` , Azure AD B2C Kullanıcı akışınız veya özel ilkenizin adını (örneğin,) içeren bir dizedir `PolicySignUpSignIn` .
- `ParentActivityOrWindow` Android (etkinlik) için gereklidir ve iOS 'ta Microsoft Windows ve UIViewController üzerinde Windows gibi üst Kullanıcı arabirimini destekleyen diğer platformlar için isteğe bağlıdır. UI iletişim kutusu hakkında daha fazla bilgi için bkz. [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) on msal wiki.

Bir Kullanıcı akışı veya özel ilke uygulama (örneğin, kullanıcının profilini düzenlemesine veya parolalarını sıfırlamasına izin vermek) Şu anda çağırarak yapılır `AcquireTokenInteractive` . Bu iki ilke için döndürülen belirteç/kimlik doğrulama sonucunu kullanamazsınız.

## <a name="profile-edit-policies"></a>Profil düzenleme ilkeleri

Kullanıcılarınızın bir sosyal kimlikle oturum açmasını ve sonra profilini düzenlemesini sağlamak için Azure AD B2C profili düzenleme ilkesini uygulayın.

`AcquireTokenInteractive`Bu ilkeyi, bu ilke için olan yetkiliyle çağırarak yapın. Kullanıcı zaten oturum açmış ve etkin bir tanımlama bilgisi oturumuna sahip olduğundan, `Prompt.NoPrompt` Hesap seçimi iletişim kutusunun görüntülenmesini engellemek için kullanın.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await application.GetAccountsAsync(PolicyEditProfile);
    IAccount account = accounts.FirstOrDefault();
    try
    {
        var authResult = await application.AcquireTokenInteractive(scopes)
                            .WithPrompt(Prompt.NoPrompt),
                            .WithAccount(account)
                            .WithB2CAuthority(AuthorityEditProfile)
                            .ExecuteAsync();
     }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Kaynak sahibi parola kimlik bilgileri (ROPC)

ROPC akışı hakkında daha fazla bilgi için bkz. [kaynak sahibi parola kimlik bilgileri verme Ile oturum açma](v2-oauth-ropc.md).

Bir kullanıcının uygulamanızdaki parolasını sorması güvenli olmadığından ROPC akışı **önerilmez** . Bu sorun hakkında daha fazla bilgi için bkz. [parola büyüyen soruna neden olan çözüm nedir?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Bir ROPC akışında Kullanıcı adı/parola kullanarak birkaç şeyi fesınız:

- Modern kimlik 'in temel anları: paylaşılan gizlilik yakalanabileceğinden parola, daha fazla veya yeniden çalınabilir. Tanım olarak, ROPC, passwordless akışlarıyla uyumsuzdur.
- MFA yapması gereken kullanıcılar oturum açamaz (hiçbir etkileşim yoktur).
- Kullanıcılar çoklu oturum açma (SSO) kullanamayacak.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C 'de ROPC akışını yapılandırma

Azure AD B2C kiracınızda yeni bir Kullanıcı akışı oluşturun ve ropc **kullanarak** Kullanıcı akışı için ropc 'yi etkinleştirin. Daha fazla bilgi için bkz. [kaynak sahibi parola kimlik bilgileri akışını yapılandırma](../../active-directory-b2c/add-ropc-policy.md).

`IPublicClientApplication` yöntemi içerir `AcquireTokenByUsernamePassword` :

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Bu `AcquireTokenByUsernamePassword` Yöntem aşağıdaki parametreleri alır:

- Erişim belirtecinin alınabileceği *kapsamlar* .
- Bir *Kullanıcı adı*.
- Kullanıcı için SecureString *parolası* .

### <a name="limitations-of-the-ropc-flow"></a>ROPC Flow 'un sınırlamaları

ROPC akışı yalnızca, kullanıcılarınızın e-posta adresi veya Kullanıcı adı kullanarak Azure AD B2C kaydoldığı **yerel hesaplar için kullanılır**. Bu akış, Azure AD B2C (Facebook, Google, vb.) tarafından desteklenen bir dış kimlik sağlayıcısına federasyona eklemek için çalışmaz.

## <a name="google-auth-and-embedded-webview"></a>Google Auth ve Embedded WebView

Bir kimlik sağlayıcısı olarak Google kullanıyorsanız, Google ['ın katıştırılmış Web görünümlerinden kimlik doğrulamasına](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)izin vermediği için sistem tarayıcısını kullanmanızı öneririz. Şu anda, `login.microsoftonline.com` Google ile güvenilen bir yetkiliniz ve katıştırılmış WebView ile çalışacağız. Ancak, `b2clogin.com` Google ile güvenilen bir yetkili değildir, bu nedenle kullanıcılar kimlik doğrulaması yapamaz.

Değişiklik olursa bu [soruna](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) yönelik bir güncelleştirme sağlayacağız.

## <a name="token-caching-in-msalnet"></a>MSAL.NET içinde belirteç önbelleğe alma

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C ile ilgili bilinen sorun

MSAL.NET, [belirteç önbelleğini](/dotnet/api/microsoft.identity.client.tokencache)destekler. Belirteç önbelleğe alma anahtarı, kimlik sağlayıcısı (IDP) tarafından döndürülen talepleri temel alır.

Şu anda MSAL.NET bir belirteç önbellek anahtarı oluşturmak için iki talebe ihtiyaç duyuyor:

- `tid` (Azure AD kiracı KIMLIĞI)
- `preferred_username`

Tüm sosyal kimlik sağlayıcıları (Facebook, Google ve diğerleri), Azure AD B2C döndüren belirteçlerde döndürülmediğinden, bu taleplerin her ikisi de Azure AD B2C senaryolarında eksik olabilir.

Bu tür bir senaryonun belirtisi, `Missing from the token response` `preferred_username` Azure AD B2C tarafından verilen belirteçlerde talep değerine eriştiğinizde msal.net döndürmektedir. MSAL, `Missing from the token response` `preferred_username` Kitaplıklar arasında önbellek çapraz uyumluluğunu sürdürmek için değerini kullanır.

### <a name="workarounds"></a>Geçici Çözümler

#### <a name="mitigation-for-missing-tenant-id"></a>Eksik kiracı KIMLIĞI için risk azaltma

Önerilen geçici çözüm, daha önce açıklanan [ilkeye göre önbelleğe alma](#acquire-a-token-to-apply-a-policy) özelliğini kullanmaktır.

Alternatif olarak, `tid` Azure AD B2C içinde [özel ilkeler](../../active-directory-b2c/user-flow-overview.md) kullanıyorsanız talebi kullanabilirsiniz. Özel ilkeler, [talep dönüşümünü](../../active-directory-b2c/claims-transformation-technical-profile.md)kullanarak uygulamanıza ek talepler döndürebilir.

#### <a name="mitigation-for-missing-from-the-token-response"></a>"Belirteç yanıtında yok" için azaltma

Bir seçenek, `name` yerine talebi kullanmaktır `preferred_username` . `name`Talebi Azure AD B2C tarafından VERILEN kimlik belirteçlerine dahil etmek için, Kullanıcı akışınızı yapılandırırken **görünen ad** ' ı seçin.

Kullanıcı akışlarınız tarafından hangi taleplerin döndürüleceğini belirtme hakkında daha fazla bilgi için bkz. [öğretici: Kullanıcı akışlarını oluşturma Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure AD B2C uygulamaları için MSAL.NET ile etkileşimli olarak belirteçleri alma hakkında daha fazla ayrıntı aşağıdaki örnekte verilmiştir.

| Örnek | Platform | Description|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Azure AD B2C aracılığıyla kullanıcıların kimliğini doğrulamak ve ardından döndürülen belirteçlerle bir Web API 'sine erişmek için MSAL.NET kullanan bir Xamarin Forms uygulaması.|

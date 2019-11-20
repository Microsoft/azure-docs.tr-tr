---
title: Azure AD B2C (.NET için Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Azure AD B2C kullanırken belirli hususlar hakkında bilgi edinin.
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
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0996c5635223800a981497256654b7e418bf4163
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175607"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Kullanıcıları sosyal kimliklerle oturum açmak için MSAL.NET kullanma

[Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c)kullanarak sosyal kimliklerle kullanıcıları oturum açmak için msal.net kullanabilirsiniz. Azure AD B2C, ilke kavramı etrafında oluşturulmuştur. MSAL.NET ' de, bir ilke belirtildiğinde bir yetkili sağlamaya çevrilir.

- Ortak istemci uygulamasını örneklediğinizde, yetkinin ilkesini belirtmeniz gerekir.
- Bir ilke uygulamak istediğinizde, bir `authority` parametresi içeren `AcquireTokenInteractive` bir geçersiz kılma çağrısı yapmanız gerekir.

Bu sayfa MSAL 3. x içindir. MSAL 2. x ile ilgileniyorsanız, lütfen [msal 2. x içinde Azure AD B2C özellikleri](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)bölümüne bakın.

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C kiracı ve ilke için yetki

Kullanım yetkilisi şu konumda `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`:

- `azureADB2CHostname`, Azure AD B2C kiracının ve ana bilgisayarın adıdır (örneğin, `{your-tenant-name}.b2clogin.com`),
- `tenant`, Azure AD B2C kiracının tam adıdır (örneğin, `{your-tenant-name}.onmicrosoft.com`) veya kiracının GUID 'SI, 
- uygulanacak ilke veya Kullanıcı akışının adını `policyName` (örneğin, kaydolma/oturum açma için "b2c_1_susi").

Azure AD B2C yetkilileri hakkında daha fazla bilgi için bu [belgelere](/azure/active-directory-b2c/b2clogin)bakın.

## <a name="instantiating-the-application"></a>Uygulamanın örneğini oluşturma

Uygulamayı oluştururken, yetkili sağlamanız gerekir.

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

Ortak bir istemci uygulamasında Azure AD B2C korunan bir API için belirteç alma, geçersiz kılmaları bir yetkiliyle kullanmanızı gerektirir:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

Yeni değer:

- önceki dizelerden biri olan `policy` (örneğin `PolicySignUpSignIn`).
- Android (etkinlik) için `ParentActivityOrWindow` ve iOS 'daki Windows ve UIViewController gibi üst Kullanıcı arabirimini destekleyen diğer platformlar için isteğe bağlıdır. [Kullanıcı arabirimi iletişim kutusunda](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)daha fazla bilgi görüntüleyin.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`, belirli bir ilke için bir hesap bulan bir yöntemdir. Örnek:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

İlke veya Kullanıcı akışı uygulama (örneğin, son kullanıcının profilini düzenlemesine veya parolalarını sıfırlamasına izin vermek) Şu anda `AcquireTokenInteractive`çağırarak yapılır. Bu iki ilke söz konusu olduğunda, döndürülen belirteç/kimlik doğrulama sonucunu kullanmayın.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile ve ResetPassword ilkelerinin özel durumu

Son kullanıcılarınızın sosyal kimlik ile oturum açıp profilini düzenleyebileceği bir deneyim sağlamak istediğinizde Azure AD B2C profili düzenleme ilkesini uygulamak istiyorsunuz. Bunu yapmanın yolu, bu ilke için belirli bir yetkiliyle `AcquireTokenInteractive` çağırarak bir Istem, hesap seçimi iletişim kutusunun görüntülenmesini engellemek için `Prompt.NoPrompt` olarak ayarlanır (Kullanıcı zaten oturum açmış ve etkin bir tanımlama bilgisi oturumuna sahip olduğu için).

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Azure AD B2C ile kaynak sahibi parola kimlik bilgileri (ROPC)
ROPC akışı hakkında daha fazla bilgi için lütfen bu [belgelere](v2-oauth-ropc.md)bakın.

Bu akış, bir kullanıcıdan parolasını sormasının güvenli olmadığı için **önerilmez** . Bu sorun hakkında daha fazla bilgi için [Bu makaleye](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)bakın. 

Kullanıcı adı/parola kullanarak bir dizi şey sağlarsınız:
- Modern kimlik 'in temel anları: parola, daha fazla, yeniden yürütülür. Bu, ele geçirilebilecek bir paylaşılan gizlilik kavramımız için. Bu, passwordless ile uyumsuzdur.
- MFA yapması gereken kullanıcılar oturum açamaz (hiçbir etkileşim yoktur).
- Kullanıcılar çoklu oturum açma yapamaz.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C 'de ROPC akışını yapılandırma
Azure AD B2C kiracınızda yeni bir Kullanıcı akışı oluşturun ve **ROPC kullanarak oturum aç**' ı seçin. Bu, kiracınız için ROPC ilkesini etkinleştirir. Daha fazla ayrıntı için bkz. [kaynak sahibi parola kimlik bilgileri akışını yapılandırma](/azure/active-directory-b2c/configure-ropc) .

`IPublicClientApplication` bir yöntem içerir:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Bu yöntem parametre olarak alır:
- İçin erişim belirteci istemek için *kapsamlar* .
- Bir *Kullanıcı adı*.
- Kullanıcı için SecureString *parolası* .

ROPC ilkesini içeren yetkiyi kullanmayı unutmayın.

### <a name="limitations-of-the-ropc-flow"></a>ROPC Flow 'un sınırlamaları
 - ROPC akışı **yalnızca yerel hesaplar için** (bir e-posta veya Kullanıcı adı kullanarak Azure AD B2C kaydettiğinizde) kullanılabilir. Bu akış, Azure AD B2C (Facebook, Google, vb.) tarafından desteklenen kimlik sağlayıcılarının herhangi birine federasyona eklemek için çalışmaz.

## <a name="google-auth-and-embedded-webview"></a>Google Auth ve Embedded WebView

Bir kimlik sağlayıcısı olarak Google kullanan Azure AD B2C geliştiricisiyseniz, Google ['ın katıştırılmış Web görünümlerinden kimlik doğrulamasına](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)izin vermediği için sistem tarayıcısını kullanın. Şu anda, `login.microsoftonline.com` Google ile güvenilen bir yetkiliniz. Bu yetkilinin kullanılması katıştırılmış WebView ile çalışacaktır. Ancak `b2clogin.com` kullanmak Google ile güvenilir bir yetkili değildir, bu nedenle kullanıcılar kimlik doğrulaması yapamaz.

Değişiklik olursa bu [soruna](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) yönelik bir güncelleştirme sağlayacağız.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>MSAL.Net içinde Azure AD B2C ile önbelleğe alma 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C ile ilgili bilinen sorun

MSAL.Net, [belirteç önbelleğini](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)destekler. Belirteç önbelleğe alma anahtarı, kimlik sağlayıcısı tarafından döndürülen talepleri temel alır. Şu anda MSAL.Net bir belirteç önbellek anahtarı oluşturmak için iki talebe ihtiyaç duyuyor:  
- Azure AD kiracı KIMLIĞI olan `tid` ve 
- `preferred_username` 

Çoğu Azure AD B2C senaryosunda bu talepler eksiktir. 

Müşteri etkisi, Kullanıcı adı alanını görüntülemeye çalışırken, değer olarak "belirteç yanıtında yok" değerini alıyor musunuz? Bu durumda, sosyal hesaplar ve dış kimlik sağlayıcıları (IDPs) ile ilgili sınırlamalar nedeniyle Azure AD B2C, preferred_username için ıdtoken 'da bir değer döndürmemelidir. Kullanıcı bir yerel hesap, Facebook, Google, GitHub vb. ile oturum açacağından, preferred_username için Azure AD, kullanıcının kim olduğunu bildiğinden, Azure AD B2C için bir değer döndürür. bu Azure AD B2C, preferred_username için kullanılacak tutarlı bir değer değildir. MSAL 'in, ADAL ile önbellek uyumluluğunu kullanıma almasını sağlamak için, ıdtoken preferred_username için hiçbir şey döndürdüğünde Azure AD B2C hesaplarıyla ilgilenirken bizim bizim sonunda "belirteç yanıtında eksik" seçeneğini kullanmaya karar verdiniz. MSAL, kitaplıklar arasında önbellek uyumluluğunu sürdürmek üzere preferred_username için bir değer döndürmelidir.

### <a name="workarounds"></a>Çözümlerin

#### <a name="mitigation-for-the-missing-tenant-id"></a>Eksik kiracı KIMLIĞI için risk azaltma

Önerilen geçici çözüm, [ilkeye göre önbelleğe almayı](#acquire-a-token-to-apply-a-policy) kullanmaktır

Alternatif olarak, [B2C özel ilkelerini](https://aka.ms/ief)kullanıyorsanız, uygulamaya ek talepler döndürme özelliği sağladığından `tid` talebini kullanabilirsiniz. [Talep dönüştürmesi](/azure/active-directory-b2c/claims-transformation-technical-profile) hakkında daha fazla bilgi edinmek için

#### <a name="mitigation-for-missing-from-the-token-response"></a>"Belirteç yanıtında yok" için azaltma
Bir seçenek, "ad" talebini tercih edilen Kullanıcı adı olarak kullanmaktır. İşlem bu [B2C belgesi](../../active-directory-b2c/active-directory-b2c-reference-policies.md) -> "bölümünde belirtilmiştir" dönüş talebi sütununda, başarılı bir profil düzenlemesi deneyiminden sonra uygulamanıza geri gönderilen yetkilendirme belirteçlerinde döndürülmesini istediğiniz talepleri seçin. Örneğin, görünen ad, posta kodu ' nu seçin.

## <a name="next-steps"></a>Sonraki adımlar 

Azure AD B2C uygulamaları için MSAL.NET ile etkileşimli olarak belirteçleri alma hakkında daha fazla ayrıntı aşağıdaki örnekte verilmiştir.

| Örnek | Platform | Açıklama|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Azure AD B2C aracılığıyla kullanıcıların kimliğini doğrulamak ve sonuç belirteçleriyle bir Web API 'sine erişmek için MSAL.NET kullanmayı gösteren basit bir Xamarin Forms uygulaması.|

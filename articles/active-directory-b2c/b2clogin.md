---
title: Uygulamaları ve API 'Leri b2clogin.com 'e geçirme
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C için yeniden yönlendirme URL 'Lerinde b2clogin.com kullanma hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20df5fc3a4d7c392be62df2b7778854d1e2e1cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109071"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C için yeniden yönlendirme URL 'Lerini b2clogin.com olarak ayarlayın

Azure Active Directory B2C (Azure AD B2C) uygulamanızda kaydolma ve oturum açma için bir kimlik sağlayıcısı ayarladığınızda, bir yeniden yönlendirme URL 'SI belirtmeniz gerekir. Artık Azure AD B2C, uygulamalarınızda ve API 'Lerinde Kullanıcı kimliğini doğrulamaya yönelik *login.microsoftonline.com* başvurmamalıdır. Bunun yerine, tüm yeni uygulamalar için *b2clogin.com* kullanın ve mevcut uygulamaları *login.microsoftonline.com* ' den *b2clogin.com*' ye geçirin.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Login.microsoftonline.com kullanımdan kaldırma

**Ekim 2020 güncelleştirmesi:** İlk olarak bildirilen ve 04 Aralık 2020 tarihini karşılayamayacak kiracılar için bir yetkisiz kullanım süresi uzatıyoruz. Login.microsoftonline.com 'ın kullanımdan kaldırılması artık **14 ocak 2021** ' den önce gerçekleşmeyecektir.

**Arka plan**: 04 Aralık 2019 ' de, başlangıçta Login.microsoftonline.com desteğinin zamanlanan kullanımdan kaldırılmasıyla Azure AD B2C 04 Aralık 2020 tarihinde [duyurduk](https://azure.microsoft.com/updates/b2c-deprecate-msol/) . Bu, var olan kiracılar için bir (1) yıl b2clogin.com 'e geçiş için verilmiştir. 04 Aralık 2019 ' den sonra oluşturulan yeni kiracılar, login.microsoftonline.com 'dan gelen istekleri kabul etmez. Tüm işlevler, b2clogin.com uç noktasında aynı kalır.

Login.microsoftonline.com 'nin kullanımdan kaldırılması Azure Active Directory kiracıları etkilemez. Bu değişiklikten yalnızca Azure Active Directory B2C kiracılar etkilenir.

## <a name="what-endpoints-does-this-apply-to"></a>Bunun için hangi uç noktalar geçerlidir?
B2clogin.com 'e geçiş yalnızca kullanıcıların kimliğini doğrulamak için Azure AD B2C ilkeleri (Kullanıcı akışları veya özel ilkeler) kullanan kimlik doğrulama uç noktaları için geçerlidir. Bu uç noktalar `<policy-name>` , Azure AD B2C kullanması gereken ilkeyi belirten bir parametreye sahiptir. [Azure AD B2C ilkeleri hakkında daha fazla bilgi edinin](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Bu uç noktalar şöyle görünebilir:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Alternatif olarak, `<policy-name>` bir sorgu parametresi olarak geçirilebilir:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> ' İlke ' parametresini kullanan uç noktaların yanı sıra [kimlik sağlayıcısı yeniden yönlendirme URL 'leri](#change-identity-provider-redirect-urls)de güncelleştirilmeleri gerekir.

Bazı Azure AD B2C müşteriler, Azure AD kurumsal kiracılarının OAuth 2,0 istemci kimlik bilgileri verme akışı gibi paylaşılan yeteneklerini kullanır. Bu özelliklere, *bir ilke parametresi Içermeyen* Azure AD 'nin Login.microsoftonline.com uç noktaları kullanılarak erişilir. __Bu uç noktalar etkilenmez__.

## <a name="benefits-of-b2clogincom"></a>B2clogin.com avantajları

Yeniden yönlendirme URL 'SI olarak *b2clogin.com* kullandığınızda:

* Microsoft Hizmetleri tarafından kullanılan tanımlama bilgisi üstbilgisinde tüketilen alan azalır.
* Yeniden yönlendirme URL 'Lerinin artık Microsoft 'a bir başvuru eklemesi gerekmez.
* JavaScript istemci tarafı kodu özelleştirilmiş sayfalarda desteklenir (Şu anda [önizlemede](javascript-and-page-layout.md)). Güvenlik kısıtlamaları nedeniyle, *login.microsoftonline.com* kullanıyorsanız, JavaScript kodu ve HTML form öğeleri özel sayfalardan kaldırılır.

## <a name="overview-of-required-changes"></a>Gerekli değişikliklere genel bakış

Uygulamalarınızı *b2clogin.com*'e geçirmek için yapmanız gerekebilecek birkaç değişiklik vardır:

* Kimlik sağlayıcınızın uygulamalarındaki yeniden yönlendirme URL 'sini *b2clogin.com* başvurusuna değiştirin.
* Azure AD B2C uygulamalarınızı, Kullanıcı akışında ve belirteç uç noktası başvurularında *b2clogin.com* kullanacak şekilde güncelleştirin. Bu, Microsoft kimlik doğrulama kitaplığı (MSAL) gibi bir kimlik doğrulama kitaplığı kullanlarınızın güncelleştirilmesini içerebilir.
* [Kullanıcı arabirimi özelleştirmesi](customize-ui-with-html.md)için CORS ayarlarında tanımladığınız tüm **izin verilen kaynakları** güncelleştirin.

Eski bir uç nokta şöyle görünebilir:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

Buna karşılık gelen güncelleştirilmiş bir uç nokta şöyle görünür:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Kimlik sağlayıcısı yeniden yönlendirme URL 'Lerini Değiştir

Bir uygulama oluşturduğunuz her bir kimlik sağlayıcısının Web sitesinde, login.microsoftonline.com yerine yeniden yönlendirmek için tüm güvenilen URL 'Leri değiştirin `your-tenant-name.b2clogin.com` . 

B2clogin.com yeniden yönlendirme URL 'Leri için kullanabileceğiniz iki biçim vardır. Birincisi, kiracı etki alanı adınızın yerine kiracı KIMLIĞINI (GUID) kullanarak URL 'de herhangi bir yerde "Microsoft" görünmeme avantajını sağlar:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

İkinci seçenek, kiracı etki alanı adınızı biçiminde kullanır `your-tenant-name.onmicrosoft.com` . Örnek:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Her iki biçim için:

* `{your-tenant-name}`Azure AD B2C kiracınızın adıyla değiştirin.
* `/te`URL 'de varsa kaldırın.

## <a name="update-your-applications-and-apis"></a>Uygulamalarınızı ve API 'lerinizi güncelleştirme

Azure AD B2C özellikli uygulamalardaki ve API 'lerinizde bulunan kod, `login.microsoftonline.com` birkaç yerde öğesine başvurabilir. Örneğin, kodunuzun Kullanıcı akışlarına ve belirteç uç noktalarına başvuruları olabilir. Bunun yerine aşağıdaki başvuruyu güncelleştirin `your-tenant-name.b2clogin.com` :

* Yetkilendirme uç noktası
* Belirteç uç noktası
* Belirteç veren

Örneğin, contoso kaydolma/oturum açma ilkesi için yetkili uç noktası şu şekilde olacaktır:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

OWIN tabanlı Web uygulamalarını b2clogin.com 'e geçirme hakkında bilgi için bkz. [OWIN tabanlı Web API 'sini b2clogin.com 'ye geçirme](multiple-token-endpoints.md).

Azure AD B2C tarafından korunan Azure API Management API ['leri geçirmek için](secure-api-management.md#migrate-to-b2clogincom) , [Azure API Management API 'Sinin Azure AD B2C Ile güvenli hale getirme](secure-api-management.md)bölümüne bakın.

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET ValidateAuthority özelliği

[Msal.net][msal-dotnet] v2 veya daha önceki bir sürümünü kullanıyorsanız, b2clogin.com 'e yeniden yönlendirmeye izin vermek Için **validateauthority** özelliğini `false` istemci örneği oluşturma ' ya ayarlayın. Bu değerin olarak ayarlanması `false` msal.net v3 ve üzeri için gerekli değildir.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>JavaScript için MSAL validateAuthority özelliği

JavaScript v 1.2.2 veya daha önceki bir sürümü [Için msal][msal-js] kullanıyorsanız, **validateauthority** özelliğini olarak ayarlayın `false` .

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

`validateAuthority: true`MSAL.js 1.3.0 + (varsayılan) ' de ayarlarsanız, ile geçerli bir belirteç veren de belirtmeniz gerekir `knownAuthorities` :

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Sonraki adımlar

OWIN tabanlı Web uygulamalarını b2clogin.com 'e geçirme hakkında bilgi için bkz. [OWIN tabanlı Web API 'sini b2clogin.com 'ye geçirme](multiple-token-endpoints.md).

Azure AD B2C tarafından korunan Azure API Management API ['leri geçirmek için](secure-api-management.md#migrate-to-b2clogincom) , [Azure API Management API 'Sinin Azure AD B2C Ile güvenli hale getirme](secure-api-management.md)bölümüne bakın.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md

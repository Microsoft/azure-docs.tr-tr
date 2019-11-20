---
title: Yeniden yönlendirme URL 'Lerini b2clogin.com-Azure Active Directory B2C olarak ayarlama
description: Azure Active Directory B2C için yeniden yönlendirme URL 'Lerinde b2clogin.com kullanma hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533750"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C için yeniden yönlendirme URL 'Lerini b2clogin.com olarak ayarlayın

Azure Active Directory B2C (Azure AD B2C) uygulamanızda kaydolma ve oturum açma için bir kimlik sağlayıcısı ayarladığınızda, bir yeniden yönlendirme URL 'SI belirtmeniz gerekir. Artık uygulamalarınızda ve API 'lerinize *login.microsoftonline.com* başvurmamalıdır. Bunun yerine, tüm yeni uygulamalar için *b2clogin.com* kullanın ve mevcut uygulamaları *login.microsoftonline.com* ' den *b2clogin.com*' ye geçirin.

## <a name="benefits-of-b2clogincom"></a>B2clogin.com avantajları

Yeniden yönlendirme URL 'SI olarak *b2clogin.com* kullandığınızda:

* Microsoft Hizmetleri tarafından kullanılan tanımlama bilgisi üstbilgisinde tüketilen alan azalır.
* Yeniden yönlendirme URL 'Lerinin artık Microsoft 'a bir başvuru eklemesi gerekmez.
* JavaScript istemci tarafı kodu özelleştirilmiş sayfalarda desteklenir (Şu anda [önizlemede](user-flow-javascript-overview.md)). Güvenlik kısıtlamaları nedeniyle, *login.microsoftonline.com*kullanıyorsanız, JavaScript kodu ve HTML form öğeleri özel sayfalardan kaldırılır.

## <a name="overview-of-required-changes"></a>Gerekli değişikliklere genel bakış

Uygulamalarınızı *b2clogin.com*'e geçirmek için yapmanız gerekebilecek birkaç değişiklik vardır:

* Kimlik sağlayıcınızın uygulamalarındaki yeniden yönlendirme URL 'sini *b2clogin.com*başvurusuna değiştirin.
* Azure AD B2C uygulamalarınızı, Kullanıcı akışında ve belirteç uç noktası başvurularında *b2clogin.com* kullanacak şekilde güncelleştirin.
* [Kullanıcı arabirimi özelleştirmesi](active-directory-b2c-ui-customization-custom-dynamic.md)için CORS ayarlarında tanımladığınız tüm **izin verilen kaynakları** güncelleştirin.

## <a name="change-identity-provider-redirect-urls"></a>Kimlik sağlayıcısı yeniden yönlendirme URL 'Lerini Değiştir

Bir uygulama oluşturduğunuz her bir kimlik sağlayıcısının Web sitesinde, `your-tenant-name.b2clogin.com` *login.microsoftonline.com*yerine yeniden yönlendirmek için tüm güvenilen URL 'leri değiştirin.

B2clogin.com yeniden yönlendirme URL 'Leri için kullanabileceğiniz iki biçim vardır. Birincisi, kiracı etki alanı adınızın yerine kiracı KIMLIĞINI (GUID) kullanarak URL 'de herhangi bir yerde "Microsoft" görünmeme avantajını sağlar:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

İkinci seçenek, kiracı etki alanı adınızı biçiminde `your-tenant-name.onmicrosoft.com`kullanır. Örneğin:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Her iki biçim için:

* Değiştirin `{your-tenant-name}` Azure AD B2C kiracınızın adı.
* URL `/te` 'de varsa kaldırın.

## <a name="update-your-applications-and-apis"></a>Uygulamalarınızı ve API 'lerinizi güncelleştirme

Azure AD B2C özellikli uygulamalardaki ve API 'lerinizde bulunan kod, birkaç yerde öğesine `login.microsoftonline.com` başvurabilir. Örneğin, kodunuzun Kullanıcı akışlarına ve belirteç uç noktalarına başvuruları olabilir. Bunun yerine aşağıdaki başvuruyu `your-tenant-name.b2clogin.com`güncelleştirin:

* Yetkilendirme uç noktası
* Belirteç uç noktası
* Belirteci veren

Örneğin, contoso kaydolma/oturum açma ilkesi için yetkili uç noktası şu şekilde olacaktır:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority özelliği

[Msal.net][msal-dotnet] v2 veya daha önceki bir sürümünü kullanıyorsanız, *b2clogin.com*'e yeniden yönlendirmeye izin vermek `false` için **validateauthority** özelliğini istemci örneği oluşturma ' ya ayarlayın. Bu ayar MSAL.NET v3 ve üzeri için gerekli değildir.

```CSharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

[JavaScript Için msal][msal-js]kullanıyorsanız:

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
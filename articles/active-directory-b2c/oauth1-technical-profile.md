---
title: Özel ilkede OAuth1 teknik profili tanımlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkede bir OAuth 1,0 Teknik profili tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a2cbca8c02dcbfc0e59d31040d1fca7a790cd35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96936667"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde bir OAuth1 teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), [OAuth 1,0 protokol](https://tools.ietf.org/html/rfc5849) kimlik sağlayıcısı için destek sağlar. Bu makalede, bu standartlaştırılmış protokolü destekleyen bir talep sağlayıcısıyla etkileşim kurmaya yönelik teknik bir profilin ayrıntıları açıklanmaktadır. OAuth1 teknik profiliyle, Twitter gibi bir OAuth1 tabanlı kimlik sağlayıcısıyla federasyona bağlayabilirsiniz. Kimlik sağlayıcısı ile birleştirme, kullanıcıların var olan sosyal veya kurumsal kimliklerinde oturum açmasına olanak tanır.

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `OAuth1` . Örneğin, **Twitter-OAUTH1** Technical profile için protokol `OAuth1` .

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Giriş talepleri

**Inputclaim** ve **ınputclaimstransformations** öğeleri boş veya yok.

## <a name="output-claims"></a>Çıkış talepleri

**Outputclaim** öğesi, OAuth1 Identity provider tarafından döndürülen taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını kimlik sağlayıcısında tanımlanan adla eşlemeniz gerekebilir. Ayrıca, **DefaultValue** özniteliğini ayarladığınız sürece kimlik sağlayıcısı tarafından döndürülmeyen talepleri de ekleyebilirsiniz.

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

Aşağıdaki örnekte, Twitter kimlik sağlayıcısı tarafından döndürülen talepler gösterilmektedir:

- **Issueruserıd** talebine eşlenen **user_id** talebi.
- **DisplayName** talebine eşlenen **screen_name** talebi.
- Ad eşleştirmesi olmayan **e-posta** talebi.

Teknik profil, kimlik sağlayıcısı tarafından döndürülmeyen talepleri de döndürür:

- Kimlik sağlayıcısının adını içeren **IdentityProvider** talebi.
- Varsayılan değeri olan **Authenticationsource** talebi `socialIdpAuthentication` .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_id | Yes | Kimlik sağlayıcısının uygulama tanımlayıcısı. |
| Adı | No | Kimlik sağlayıcısının adı. |
| request_token_endpoint | Yes | RFC 5849 başına istek belirteci uç noktasının URL 'SI. |
| authorization_endpoint | Yes | RFC 5849 başına yetkilendirme uç noktasının URL 'SI. |
| access_token_endpoint | Yes | RFC 5849 başına belirteç uç noktasının URL 'SI. |
| ClaimsEndpoint | No | Kullanıcı bilgisi uç noktasının URL 'SI. |
| ClaimsResponseFormat | No | Talep yanıtı biçimi.|

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

**Cryptographickeys** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_secret | Yes | Kimlik sağlayıcısı uygulamasının istemci gizli anahtarı.   |

## <a name="redirect-uri"></a>Yeniden Yönlendirme URI'si

Kimlik sağlayıcınızın yeniden yönlendirme URI 'sini yapılandırdığınızda, girin `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/{policy-id}/oauth1/authresp` . `{tenant-name}`, Kiracınızın adıyla (örneğin, contosob2c) ve `{policy-id}` ilkenizin tanımlayıcısıyla (örneğin, b2c_1a_policy) değiştirdiğinizden emin olun. Yeniden yönlendirme URI 'sinin tamamen küçük harfle olması gerekir. Kimlik sağlayıcısı oturum açma bilgilerini kullanan tüm ilkeler için yeniden yönlendirme URL 'SI ekleyin.

Örnekler:

- [Özel ilkeler kullanarak Twitter 'ı OAuth1 Identity sağlayıcısı olarak ekleme](identity-provider-twitter.md)

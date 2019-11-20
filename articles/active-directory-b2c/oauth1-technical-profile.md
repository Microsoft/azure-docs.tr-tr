---
title: Azure Active Directory B2C bir özel ilkede bir OAuth1 Technical profile tanımlayın | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkede OAuth1 Technical profile tanımlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 97fa5757f8b77e29545f6d6f6b885334c7b526f1
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064000"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde bir OAuth1 teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), [OAuth 1,0 protokol](https://tools.ietf.org/html/rfc5849) kimlik sağlayıcısı için destek sağlar. Bu makalede, bu standartlaştırılmış protokolü destekleyen bir talep sağlayıcısıyla etkileşim kurmaya yönelik teknik bir profilin ayrıntıları açıklanmaktadır. OAuth1 teknik profiliyle, Twitter gibi bir OAuth1 tabanlı kimlik sağlayıcısıyla federasyona bağlayabilirsiniz. Kimlik sağlayıcısı ile birleştirme, kullanıcıların var olan sosyal veya kurumsal kimliklerinde oturum açmasına olanak tanır.

## <a name="protocol"></a>Protocol

**Protokol** öğesinin `OAuth1` **Name** özniteliğinin olarak ayarlanması gerekir. Örneğin, **Twitter-OAUTH1** Technical profile `OAuth1`için protokol.

```XML
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
- Varsayılan değeri `socialIdpAuthentication`olan **authenticationsource** talebi.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Meta Veriler

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_id | Evet | Kimlik sağlayıcısının uygulama tanımlayıcısı. |
| Adı | Hayır | Kimlik sağlayıcısının adı. |
| request_token_endpoint | Evet | RFC 5849 başına istek belirteci uç noktasının URL 'SI. |
| authorization_endpoint | Evet | RFC 5849 başına yetkilendirme uç noktasının URL 'SI. |
| access_token_endpoint | Evet | RFC 5849 başına belirteç uç noktasının URL 'SI. |
| ClaimsEndpoint | Hayır | Kullanıcı bilgisi uç noktasının URL 'SI. |
| ClaimsResponseFormat | Hayır | Talep yanıtı biçimi.|

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

**Cryptographickeys** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_secret | Evet | Kimlik sağlayıcısı uygulamasının istemci gizli anahtarı.   |

## <a name="redirect-uri"></a>Yönlendirme URI'si

Kimlik sağlayıcınızın yeniden yönlendirme URL 'sini yapılandırdığınızda, girin `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. **Kiracınızı** kiracı adınızla (örneğin, contosob2c.onmicrosoft.com) ve **PolicyId** ile ilkenizin tanıtıcısına (örneğin, b2c_1a_policy) değiştirdiğinizden emin olun. Yeniden yönlendirme URI 'sinin tamamen küçük harfle olması gerekir. Kimlik sağlayıcısı oturum açma bilgilerini kullanan tüm ilkeler için yeniden yönlendirme URL 'SI ekleyin.

**Login.microsoftonline.com** yerine **b2clogin.com** etki alanını kullanıyorsanız Login.microsoftonline.com yerine b2clogin.com kullandığınızdan emin olun.

Örnekler:

- [Özel ilkeler kullanarak Twitter 'ı OAuth1 Identity sağlayıcısı olarak ekleme](active-directory-b2c-custom-setup-twitter-idp.md)














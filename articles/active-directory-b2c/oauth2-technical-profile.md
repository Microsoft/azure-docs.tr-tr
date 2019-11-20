---
title: Azure Active Directory B2C bir özel ilkede bir OAuth2 Technical profile tanımlayın | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkede OAuth2 Technical profile tanımlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 63500c057b5c9f497e59589286a852a4394059ec
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063981"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde bir OAuth2 teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), OAuth2 protokol kimlik sağlayıcısı için destek sağlar. OAuth2, yetkilendirme ve temsilci kimlik doğrulaması için birincil protokoldür. Daha fazla bilgi için bkz. [RFC 2,0 6749 yetkilendirme çerçevesi](https://tools.ietf.org/html/rfc6749). OAuth2 teknik profiliyle, Facebook gibi bir OAuth2 tabanlı kimlik sağlayıcısıyla federasyona bağlayabilirsiniz. Bir kimlik sağlayıcısı ile federasyona eklemek, kullanıcıların mevcut sosyal veya kurumsal kimliklerinde oturum açmasına olanak tanır.

## <a name="protocol"></a>Protocol

**Protokol** öğesinin `OAuth2` **Name** özniteliğinin olarak ayarlanması gerekir. Örneğin, **Facebook-OAUTH** Technical profile `OAuth2`için protokol:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Giriş talepleri

**Inputclaim** ve **ınputclaimstransformations** öğeleri gerekli değildir. Ancak kimlik sağlayıcınıza ek parametreler göndermek isteyebilirsiniz. Aşağıdaki örnek, değerini `contoso.com` yetkilendirme isteğine **domain_hint** sorgu dizesi parametresini ekler.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Çıkış talepleri

**Outputclaim** öğesi, OAuth2 Identity provider tarafından döndürülen taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını kimlik sağlayıcısında tanımlanan adla eşlemeniz gerekebilir. `DefaultValue` Özniteliği ayarladığınız sürece, kimlik sağlayıcısı tarafından döndürülmeyen talepleri de ekleyebilirsiniz.

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

Aşağıdaki örnekte, Facebook kimlik sağlayıcısı tarafından döndürülen talepler gösterilmektedir:

- **First_name** **talebi, bu talep ile** eşleştirilir.
- **Last_name** talebi, **Soyadı** talebine eşlenir.
- Ad eşleme olmadan **DisplayName** talebi.
- Ad eşleştirmesi olmayan **e-posta** talebi.

Teknik profil, kimlik sağlayıcısı tarafından döndürülmeyen talepleri de döndürür:

- Kimlik sağlayıcısının adını içeren **IdentityProvider** talebi.
- Varsayılan bir **Socialidpauthentication**değeri olan **authenticationsource** talebi.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Meta Veriler

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_id | Evet | Kimlik sağlayıcısının uygulama tanımlayıcısı. |
| Idtokenaudience | Hayır | İd_token kitlesi. Belirtilmişse Azure AD B2C, belirtecin kimlik sağlayıcısı tarafından döndürülen bir talep içinde olup olmadığını denetler ve belirtilen değere eşittir. |
| authorization_endpoint | Evet | RFC 6749 başına yetkilendirme uç noktasının URL 'SI. |
| AccessTokenEndpoint | Evet | RFC 6749 başına belirteç uç noktasının URL 'SI. |
| ClaimsEndpoint | Evet | RFC 6749 başına Kullanıcı bilgileri uç noktasının URL 'SI. |
| Accesstokenresponsebiçimi | Hayır | Erişim belirteci uç noktası çağrısının biçimi. Örneğin, Facebook bir HTTP GET yöntemi gerektirir, ancak erişim belirteci yanıtı JSON biçimindedir. |
| AdditionalRequestQueryParameters | Hayır | Ek istek sorgu parametreleri. Örneğin, kimlik sağlayıcınıza ek parametreler göndermek isteyebilirsiniz. Virgül sınırlayıcısı kullanarak birden çok parametre ekleyebilirsiniz. |
| ClaimsEndpointAccessTokenName | Hayır | Erişim belirteci sorgu dizesi parametresinin adı. Bazı kimlik sağlayıcılarının talep uç noktaları HTTP isteği al 'ı destekler. Bu durumda, taşıyıcı belirteci Yetkilendirme üstbilgisi yerine bir sorgu dizesi parametresi kullanılarak gönderilir. |
| ClaimsEndpointFormatName | Hayır | Biçim sorgu dizesi parametresinin adı. Örneğin, bu adı bu LinkedIn talep uç noktasında `format` `https://api.linkedin.com/v1/people/~?format=json`olduğu gibi ayarlayabilirsiniz. |
| ClaimsEndpointFormat | Hayır | Biçim sorgusu dize parametresinin değeri. Örneğin, değeri bu LinkedIn talep uç noktasında `json` `https://api.linkedin.com/v1/people/~?format=json`olarak ayarlayabilirsiniz. |
| Adı | Hayır | Kimlik sağlayıcısının adı. |
| response_mode | Hayır | Kimlik sağlayıcısının sonucu Azure AD B2C geri göndermek için kullandığı yöntem. Olası değerler: `query`, `form_post` (varsayılan) veya `fragment`. |
| scope | Hayır | OAuth2 Identity Provider belirtimine göre tanımlanan isteğin kapsamı. `openid` ,`profile`Ve gibi`email`. |
| HttpBinding | Hayır | Erişim belirtecine ve talep belirteci uç noktalarına beklenen HTTP bağlaması. Olası değerler: `GET` veya `POST`.  |
| ResponseErrorCodeParamName | Hayır | HTTP 200 (Tamam) üzerinden döndürülen hata iletisini içeren parametrenin adı. |
| Extraparamsınaccesstokenendpointresponse | Hayır | Bazı kimlik sağlayıcıları tarafından **Accesstokenendpoint** yanıtı içinde döndürülebilecek ek parametreleri içerir. Örneğin, **accesstokenendpoint** yanıtı `openid`, bir **claimsendpoint** istek sorgu dizesinde access_token 'in yanı sıra zorunlu bir parametre olan gibi ek bir parametre içerir. Birden çok parametre adının kaçılması ve virgül ', ' sınırlayıcısı ile ayrılması gerekir. |
| ExtraParamsInClaimsEndpointRequest | Hayır | Bazı kimlik sağlayıcıları tarafından **Claimsendpoint** isteğine döndürülebilecek ek parametreleri içerir. Birden çok parametre adının kaçılması ve virgül ', ' sınırlayıcısı ile ayrılması gerekir. |

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

**Cryptographickeys** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_secret | Evet | Kimlik sağlayıcısı uygulamasının istemci gizli anahtarı. Şifreleme anahtarı yalnızca **response_types** meta verileri olarak `code`ayarlandıysa gereklidir. Bu durumda Azure AD B2C, bir erişim belirtecinin yetkilendirme kodunu Exchange için başka bir çağrı yapar. Meta veriler olarak `id_token`ayarlandıysa, şifreleme anahtarını atlayabilirsiniz. |

## <a name="redirect-uri"></a>Yönlendirme URI'si

Kimlik sağlayıcınızın yeniden yönlendirme URL 'sini yapılandırdığınızda, girin `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. **Kiracısı** kiracınızın adıyla (örneğin, contosob2c.onmicrosoft.com) ve **PolicyId** ile ilkenizin tanıtıcısına (örneğin, b2c_1a_policy) değiştirdiğinizden emin olun. Yeniden yönlendirme URI 'sinin tamamen küçük harfle olması gerekir.

**Login.microsoftonline.com** yerine **b2clogin.com** etki alanını kullanıyorsanız Login.microsoftonline.com yerine b2clogin.com kullandığınızdan emin olun.

Örnekler:

- [Özel ilkeleri kullanarak Google + OAuth2 Identity Provider olarak ekleme](active-directory-b2c-custom-setup-goog-idp.md)














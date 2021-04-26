---
title: Özel ilkede bir OpenID Connect Teknik profili tanımlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkede OpenID Connect Teknik profili tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fea42cb89dce717431c188deeb2ce83f9413f560
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283890"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde OpenID Connect Teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokol kimlik sağlayıcısı için destek sağlar. OpenID Connect 1,0, OAuth 2,0 üzerinde bir kimlik katmanını tanımlar ve modern kimlik doğrulama protokollerinde bulunan resimlerin durumunu temsil eder. OpenID Connect Teknik profiliyle birlikte, OpenID Connect tabanlı bir kimlik sağlayıcısıyla (örneğin, Azure AD) federasyona bağlayabilirsiniz. Bir kimlik sağlayıcısı ile federasyona eklemek, kullanıcıların mevcut sosyal veya kurumsal kimliklerinde oturum açmasına olanak tanır.

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `OpenIdConnect` . Örneğin, **MSA-OıDC** Technical profile için protokol `OpenIdConnect` :

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Giriş talepleri

**Inputclaim** ve **ınputclaimstransformations** öğeleri gerekli değildir. Ancak kimlik sağlayıcınıza ek parametreler göndermek isteyebilirsiniz. Aşağıdaki örnek, değerini yetkilendirme isteğine **domain_hint** sorgu dizesi parametresini ekler `contoso.com` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Çıkış talepleri

**Outputclaim** öğesi, OpenID Connect kimlik sağlayıcısı tarafından döndürülen taleplerin bir listesini içerir. İlkenizde tanımlanan talebin adını kimlik sağlayıcısında tanımlanan adla eşlemeniz gerekebilir. Özniteliği ayarladığınız sürece, kimlik sağlayıcısı tarafından döndürülmeyen talepleri de ekleyebilirsiniz `DefaultValue` .

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

Aşağıdaki örnekte, Microsoft hesabı kimlik sağlayıcısı tarafından döndürülen talepler gösterilmektedir:

- **Issueruserıd** talebine eşlenen **Sub** talebi.
- **DisplayName** talebine eşlenen **ad** talebi.
- Ad eşleştirmesi olmayan **e-posta** .

Teknik profil, kimlik sağlayıcısı tarafından döndürülmeyen talepleri de döndürür:

- Kimlik sağlayıcısının adını içeren **IdentityProvider** talebi.
- Varsayılan bir **Socialidpauthentication** değeri olan **authenticationsource** talebi.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_id | Yes | Kimlik sağlayıcısının uygulama tanımlayıcısı. |
| Idtokenaudience | No | İd_token kitlesi. Belirtilmişse, `aud` kimlik sağlayıcısı tarafından döndürülen bir belirteçte talebin ıdtokenaudience meta verilerinde belirtilen değere eşit olup olmadığını denetler Azure AD B2C.  |
| VERIYI | Yes | OpenID ile bilinen yapılandırma uç noktası olarak da bilinen bir OpenID Connect kimlik sağlayıcısı yapılandırma belgesine işaret eden bir URL. URL, `{tenant}` kiracı adıyla değiştirilmiş olan ifadesini içerebilir.  |
| authorization_endpoint | No | Bir OpenID Connect kimlik sağlayıcısı yapılandırma yetkilendirmesi uç noktasına işaret eden bir URL. Authorization_endpoint meta veri değeri, `authorization_endpoint` OpenID iyi bilinen yapılandırma uç noktasında belirtilen değere göre önceliklidir. URL, `{tenant}` kiracı adıyla değiştirilmiş olan ifadesini içerebilir. |
| end_session_endpoint | No | Bitiş oturumu uç noktasının URL 'SI. Authorization_endpoint meta veri değeri, `end_session_endpoint` OpenID iyi bilinen yapılandırma uç noktasında belirtilen değere göre önceliklidir. |
| yayınlayan | No | Bir OpenID Connect kimlik sağlayıcısının benzersiz tanımlayıcısı. Verenin meta verilerinin değeri, `issuer` OpenID iyi bilinen yapılandırma uç noktasında belirtilen değere göre önceliklidir.  Belirtilmişse, `iss` kimlik sağlayıcısı tarafından döndürülen bir belirteçte talebin, verenin meta verilerinde belirtilen değere eşit olup olmadığını denetler Azure AD B2C. |
| Adı | No | Kimlik sağlayıcısının adı.  |
| response_types | No | OpenID Connect Core 1,0 belirtimine göre yanıt türü. Olası değerler: `id_token` , `code` , veya `token` . |
| response_mode | No | Kimlik sağlayıcısının sonucu Azure AD B2C geri göndermek için kullandığı yöntem. Olası değerler: `query` , `form_post` (varsayılan) veya `fragment` . |
| scope | No | OpenID Connect Core 1,0 belirtimine göre tanımlanan isteğin kapsamı. `openid`, Ve gibi `profile` `email` . |
| HttpBinding | No | Erişim belirtecine ve talep belirteci uç noktalarına beklenen HTTP bağlaması. Olası değerler: `GET` veya `POST` .  |
| Validtokenıssueröneklerini | No | Azure Active Directory gibi çok kiracılı bir kimlik sağlayıcısı kullanırken kiracıların her birinde oturum açmak için kullanılabilen bir anahtar. |
| Usepolicınredirecturi | No | Yeniden yönlendirme URI 'SI oluşturulurken bir ilke kullanılıp kullanılmayacağını belirtir. Uygulamanızı kimlik sağlayıcısında yapılandırdığınızda, yeniden yönlendirme URI 'sini belirtmeniz gerekir. Yeniden yönlendirme URI 'SI Azure AD B2C, ' a işaret eder `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  Belirtirseniz `true` , kullandığınız her ilke için bir yeniden yönlendirme URI 'si eklemeniz gerekir. Örneğin: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | No | Http durum kodu 5xx aralığalıyorsa, bir dış hizmete yönelik isteğin hata olarak işaretlenip işaretlenmeyeceğini gösterir. Varsayılan değer: `false`. |
| Discovermetadatabyıtokenıssuer | No | OıDC meta verilerinin JWT belirtecindeki veren kullanılarak bulunup bulunmadığını gösterir. |
| IncludeClaimResolvingInClaimsHandling  | No | Giriş ve çıkış talepleri için, [talep çözümlemenin](claim-resolver-overview.md) teknik profile dahil edilip edilmeyeceğini belirtir. Olası değerler: `true` , veya `false` (varsayılan). Teknik profilde bir talep çözümleyici kullanmak istiyorsanız, bunu olarak ayarlayın `true` . |
|token_endpoint_auth_method| No | Azure AD B2C, kimlik doğrulama üst bilgisini belirteç uç noktasına nasıl göndereceğini belirtir. Olası değerler: `client_secret_post` (varsayılan) ve `client_secret_basic` (Genel Önizleme), `private_key_jwt` (Genel Önizleme). Daha fazla bilgi için bkz. [OpenID Connect istemci kimlik doğrulaması bölümü](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
|token_signing_algorithm| No | Olarak ayarlandığında kullanılacak imzalama algoritmasını belirtir `token_endpoint_auth_method` `private_key_jwt` . Olası değerler: `RS256` (varsayılan) veya `RS512` .|
| SingleLogoutEnabled | No | Teknik profilde oturum açma sırasında, federal kimlik sağlayıcılarının oturumunu açmaya çalışıp çalışmadığını belirtir. Daha fazla bilgi için bkz. [oturum oturumunu Azure AD B2C](./session-behavior.md#sign-out).  Olası değerler: `true` (varsayılan) veya `false` . |
|Readbodyclaimsonıdpredirect| No| `true`Kimlik sağlayıcısı yeniden yönlendirbir yanıt gövdesinden talepleri okumak için olarak ayarlayın. Bu meta veriler, taleplerin yanıt yükünde döndürdüğü [Apple ID](identity-provider-apple-id.md)ile birlikte kullanılır.|

```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">false</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>Kullanıcı arabirimi öğeleri
 
Hata durumunda görüntülenecek hata iletisini yapılandırmak için aşağıdaki ayarlar kullanılabilir. Meta verilerin OpenID Connect Teknik profilinde yapılandırılması gerekir. Hata iletileri [yerelleştirilebilecek](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Dizinde, girilen kullanıcı adına sahip bir hesap bulunmazsa kullanıcıya görüntülenecek ileti. |
| Usermessageifınvalidpassword | No | Parola yanlışsa kullanıcıya görüntülenecek ileti. |
| UserMessageIfOldPasswordUsed| No |  Eski bir parola kullanılıyorsa kullanıcıya görüntülenecek ileti.|

## <a name="cryptographic-keys"></a>Şifreleme anahtarları

**Cryptographickeys** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| client_secret | Yes | Kimlik sağlayıcısı uygulamasının istemci gizli anahtarı. Bu şifreleme anahtarı yalnızca **response_types** meta verileri olarak ayarlanmışsa `code` ve **token_endpoint_auth_method** veya olarak ayarlandıysa gereklidir `client_secret_post` `client_secret_basic` . Bu durumda Azure AD B2C, bir erişim belirtecinin yetkilendirme kodunu Exchange için başka bir çağrı yapar. Meta veriler olarak ayarlandıysa, `id_token` şifreleme anahtarını atlayabilirsiniz.  |
| assertion_signing_key | Yes | İstemci onayını imzalamak için kullanılacak RSA özel anahtarı. Bu şifreleme anahtarı yalnızca **token_endpoint_auth_method** meta verileri olarak ayarlanmışsa gereklidir `private_key_jwt` . |

## <a name="redirect-uri"></a>Yeniden yönlendirme URI 'Si

Kimlik sağlayıcınızın yeniden yönlendirme URI 'sini yapılandırdığınızda, girin `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . Öğesini `{your-tenant-name}` kiracınızın adıyla değiştirdiğinizden emin olun. Yeniden yönlendirme URI 'sinin tamamen küçük harfle olması gerekir.

Örnekler:

- [Özel ilkeler kullanarak bir kimlik sağlayıcısı olarak Microsoft hesabı (MSA) ekleme](identity-provider-microsoft-account.md)
- [Azure AD hesaplarını kullanarak oturum açın](identity-provider-azure-ad-single-tenant.md)
- [Kullanıcıların özel ilkeleri kullanarak çok kiracılı bir Azure AD kimlik sağlayıcısında oturum açmalarına izin ver](identity-provider-azure-ad-multi-tenant.md)

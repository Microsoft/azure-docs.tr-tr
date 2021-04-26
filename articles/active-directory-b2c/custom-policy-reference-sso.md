---
title: Özel ilkeler kullanarak çoklu oturum açma oturumu yönetimi
titleSuffix: Azure AD B2C
description: Azure AD B2C özel ilkeleri kullanarak SSO oturumlarını yönetmeyi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f3fb07eaf7f63d15232f4c94eeee45f43c81616
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075145"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'da çoklu oturum açma oturumu yönetimi

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Çoklu oturum açma (SSO) oturum](session-behavior.md) yönetimi, özel ilkelerdeki diğer teknik profille aynı semantiğini kullanır. Bir düzenleme adımı yürütüldüğünde, adımla ilişkili teknik profil bir başvuru için sorgulanır `UseTechnicalProfileForSessionManagement` . Varsa, başvurulan SSO oturum sağlayıcısı, kullanıcının bir oturum katılımcısı olup olmadığını görmek için denetlenir. Öyleyse, oturum yeniden doldurmak için SSO oturum sağlayıcısı kullanılır. Benzer şekilde, bir düzenleme adımının yürütülmesi tamamlandığında, bir SSO oturum sağlayıcısı belirtilmişse, oturum bilgilerini oturumdaki depolamak için sağlayıcı kullanılır.

Azure AD B2C, kullanılabilecek birkaç SSO oturum sağlayıcısı tanımladı:

|Oturum sağlayıcısı  |Kapsam  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Yok       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C iç oturum Yöneticisi.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Azure AD B2C ile OAuth1, OAuth2 veya OpenID Connect kimlik sağlayıcısı arasında.        | 
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Bir OAuth2 veya OpenID Connect bağlı olan taraf uygulaması ve Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Azure AD B2C ve SAML kimlik sağlayıcısı arasında. Ve bir SAML hizmet sağlayıcısı (bağlı olan taraf uygulaması) ve Azure AD B2C.  |        




SSO yönetim sınıfları, `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` bir teknik profilin öğesi kullanılarak belirtilir.

## <a name="input-claims"></a>Giriş talepleri

`InputClaims`Öğe boş veya yok.

## <a name="persisted-claims"></a>Kalıcı talepler

Uygulamanın geri döndürülmesi veya sonraki adımlarda ön koşullar tarafından kullanılması gereken talepler, oturum üzerinde depolanması veya dizindeki Kullanıcı profilinden bir okuma ile genişletilmiş olmalıdır. Kalıcı talepler kullanmak, kimlik doğrulamanın, eksik taleplerde başarısız olmasına neden olur. Oturumdaki talepleri eklemek için `<PersistedClaims>` Teknik profilin öğesini kullanın. Sağlayıcı oturumu yeniden doldurmak için kullanıldığında, kalıcı talepler talep çantasına eklenir.

## <a name="output-claims"></a>Çıkış talepleri

, `<OutputClaims>` Oturumdan talepleri almak için kullanılır.

## <a name="session-providers"></a>Oturum sağlayıcıları

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Ad, bu sağlayıcı hiçbir şey yapmaz. Bu sağlayıcı, belirli bir teknik profilde SSO davranışını gizleme için kullanılabilir. Aşağıdaki `SM-Noop` Teknik profil [özel ilke başlangıç paketine](custom-policy-get-started.md#custom-policy-starter-pack)dahildir.

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Bu sağlayıcı, talepleri bir oturumda depolamak için kullanılabilir. Bu sağlayıcıya genellikle yerel ve Federasyon hesaplarını yönetmek için kullanılan teknik bir profilde başvurulur. Aşağıdaki `SM-AAD` Teknik profil [özel ilke başlangıç paketine](custom-policy-get-started.md#custom-policy-starter-pack)dahildir.

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


Aşağıdaki `SM-MFA` Teknik profil [özel ilke başlangıç paketine](custom-policy-get-started.md#custom-policy-starter-pack) dahildir `SocialAndLocalAccountsWithMfa` . Bu teknik profil, Multi-Factor Authentication oturumunu yönetir.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Bu sağlayıcı, "kimlik sağlayıcısı Seç" ekranını bastırmak ve bir Federasyon kimlik sağlayıcısından oturumu kapatmak için kullanılır. Genellikle Facebook veya Azure Active Directory gibi bir federal kimlik sağlayıcısı için yapılandırılmış teknik bir profilde başvurulur. Aşağıdaki `SM-SocialLogin` Teknik profil [özel ilke başlangıç paketine](custom-policy-get-started.md#custom-policy-starter-pack)dahildir.

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | No | Şu anda kullanılmıyor olabilir. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Bu sağlayıcı, OAuth2 veya OpenID Connect bağlı olan taraf ve Azure AD B2C arasında Azure AD B2C oturumlarını yönetmek için kullanılır.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Bu sağlayıcı, bağlı olan taraf uygulaması veya Federe SAML kimlik sağlayıcısı arasında Azure AD B2C SAML oturumlarını yönetmek için kullanılır. SAML kimlik sağlayıcısı oturumunu depolamak için SSO sağlayıcısını kullanırken, `RegisterServiceProviders` olarak ayarlanması gerekir `false` . Aşağıdaki `SM-Saml-idp` Teknik profil [SAML kimlik sağlayıcısı](identity-provider-generic-saml.md)tarafından kullanılır.

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

B2C SAML oturumunu depolamak için sağlayıcıyı kullanırken, `RegisterServiceProviders` olarak ayarlanmalıdır `true` . SAML oturumu kapatma için `SessionIndex` ve `NameID` işleminin tamamlanmasını gerekir.

Aşağıdaki `SM-Saml-issuer` Teknik profil, [SAML verenin teknik profili](saml-service-provider.md) tarafından kullanılır

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama|
| --- | --- | --- |
| Includesessionındex | No | Şu anda kullanılmıyor olabilir.|
| RegisterServiceProviders | No | Sağlayıcının onay verilen tüm SAML hizmeti sağlayıcılarını kaydetmesi gerektiğini gösterir. Olası değerler: `true` (varsayılan) veya `false` .|


## <a name="next-steps"></a>Sonraki adımlar

[Oturum davranışını yapılandırmayı](session-behavior.md)öğrenin.

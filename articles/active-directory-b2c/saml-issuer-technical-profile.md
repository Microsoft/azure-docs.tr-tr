---
title: Özel ilkede SAML verenler için teknik profil tanımlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkede Security Assertion Markup Language belirteci (SAML) veren için bir teknik profil tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 54869c14cf7c5a7e43f34102f5c95e37689dfee8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095349"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde SAML belirteci veren için teknik profil tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), her kimlik doğrulama akışını işlediği için çeşitli türlerde güvenlik belirteçleri yayar. SAML belirteci veren teknik bir profil, bağlı olan taraf uygulamasına (hizmet sağlayıcısı) geri döndürülen bir SAML belirteci yayar. Genellikle bu teknik profil, Kullanıcı yolculuğunda son düzenleme adımıdır.

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `SAML2` . **Outputtokenformat** öğesini olarak ayarlayın `SAML2` .

Aşağıdaki örnek, için bir teknik profil göstermektedir `Saml2AssertionIssuer` :

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Giriş, çıkış ve kalıcı talepler

**Inputclaim**, **Outputclaim** ve **persistclaim** öğeleri boş veya yok. **Inutputclaimstransformations** ve **outputclaimstransformations** öğeleri de yok.

## <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Issueruri | No | SAML yanıtında görünen veren adı. Değer, bağlı olan taraf uygulamasında yapılandırılan adla aynı olmalıdır. |
| XmlSignatureAlgorithm | No | Azure AD B2C SAML onayını imzalamak için kullanılan yöntem. Olası değerler: `Sha256` , `Sha384` , `Sha512` veya `Sha1` . Aynı değere sahip her iki tarafta de imza algoritmasını yapılandırdığınızdan emin olun. Yalnızca sertifikanızın desteklediği algoritmayı kullanın. SAML yanıtını yapılandırmak için, bkz. [SAML uygulaması kaydetme seçenekleri](saml-service-provider.md)|
|Tokennotbeforeskewınseconds| No| Geçerlilik döneminin başlangıcını işaretleyen zaman damgası için eğriliği, bir tamsayı olarak belirtir. Bu sayı ne kadar yüksekse, geçerlilik süresi, bağlı olan taraf için taleplerin verildiği zamana göre başlar. Örneğin, Tokennotbeforeskewınseconds 60 saniyeye ayarlandığında, belirteç 13:05:10 UTC 'de verildiyse, belirteç 13:04:10 UTC 'den geçerli olur. Varsayılan değer 0’dır. En büyük değer 3600 ' dir (bir saat). |
|TokenLifeTimeInSeconds| No| SAML onaylama süresini belirtir. Bu değer, yukarıda belirtilen NotBefore değerinden saniye cinsinden olur. Varsayılan değer 300 saniyedir (5 dakika). |


## <a name="cryptographic-keys"></a>Şifreleme anahtarları

CryptographicKeys öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| MetadataSigning | Yes | SAML meta verilerini imzalamak için kullanılacak x509 sertifikası (RSA anahtar kümesi). Azure AD B2C meta verileri imzalamak için bu anahtarı kullanır. |
| SamlMessageSigning| Yes| SAML iletilerini imzalamak için kullanılacak x509 sertifikasını (RSA anahtar kümesi) belirtin. Azure AD B2C, `<samlp:Response>` bağlı olan tarafa gönderilen yanıtı imzalamak için bu anahtarı kullanır.|

## <a name="session-management"></a>Oturum yönetimi

Bağlı olan taraf uygulaması arasındaki Azure AD B2C SAML oturumlarını, öğesinin özniteliği, `UseTechnicalProfileForSessionManagement` [Samlssosessionprovider](custom-policy-reference-sso.md#samlssosessionprovider) SSO oturumuna başvuru olarak yapılandırmak için.

## <a name="next-steps"></a>Sonraki adımlar

SAML verenin teknik profilini kullanma örneği için aşağıdaki makaleye bakın:

- [Azure AD B2C bir SAML uygulaması kaydetme](saml-service-provider.md)


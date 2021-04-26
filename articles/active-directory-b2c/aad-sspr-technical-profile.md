---
title: Özel ilkelerde Azure AD SSPR teknik profilleri
titleSuffix: Azure AD B2C
description: Azure AD B2C 'daki Azure AD SSPR teknik profillerinin özel ilke başvurusu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383606"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C özel ilkesinde bir Azure AD SSPR teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), self servis parola sıfırlama (SSPR) için bir e-posta adresini doğrulamaya yönelik destek sağlar. Bir e-posta adresine kod oluşturup göndermek için Azure AD SSPR teknik profilini kullanın ve ardından kodu doğrulayın. Azure AD SSPR teknik profili de bir hata iletisi döndürebilir. Doğrulama teknik profili, Kullanıcı yolculuğu devam etmeden önce Kullanıcı tarafından belirtilen verileri doğrular. Doğrulama teknik profiliyle, otomatik olarak onaylanan bir sayfada bir hata iletisi görüntülenir.

Bu teknik profil:

- Kullanıcıyla etkileşime geçmek için bir arabirim sağlamaz. Bunun yerine, Kullanıcı arabirimi [kendi kendine onaylanan](self-asserted-technical-profile.md) bir teknik profilden veya bir [görüntüleme denetiminden](display-controls.md) [doğrulama teknik profili](validation-technical-profile.md)olarak çağrılır.
- , Bir e-posta adresine kod oluşturup göndermek için Azure AD SSPR hizmetini kullanır ve ardından kodu doğrular.  
- Bir doğrulama kodu aracılığıyla bir e-posta adresini doğrular.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokol

**Protokol** öğesinin **Name** özniteliğinin olarak ayarlanması gerekir `Proprietary` . **Handler** özniteliği, Azure AD B2C tarafından kullanılan protokol işleyici derlemesinin tam adını içermelidir:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Aşağıdaki örnekte bir Azure AD SSPR teknik profili gösterilmektedir:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>E-posta gönder

Bu teknik profilin ilk modu, bir kod oluşturmak ve göndermek için kullanılır. Bu mod için aşağıdaki seçenekler yapılandırılabilir.

### <a name="input-claims"></a>Giriş talepleri

**Inputclaim** öğesi, Azure AD SSPR 'ye göndermek için taleplerin bir listesini içerir. Ayrıca, şirketinizin adını SSPR teknik profilinde tanımlanan adla eşleyebilirsiniz.

| Claimreferenceıd | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| emailAddress | Yes | E-posta adresine sahip kullanıcı için tanımlayıcı. `PartnerClaimType`Giriş talebinin özelliği olarak ayarlanmalıdır `emailAddress` . |


**Inputclaimstransformations** öğesi, giriş taleplerini değiştirmek veya Azure AD SSPR hizmetine göndermeden önce yenilerini oluşturmak Için kullanılan **inputclaimstransınor** öğelerinin bir koleksiyonunu içerebilir.

### <a name="output-claims"></a>Çıkış talepleri

Azure AD SSPR protokol sağlayıcısı herhangi bir **Outputclaim** döndürmüyor, bu nedenle çıkış taleplerini belirtmeniz gerekmez. Ancak, özniteliği ayarladığınız sürece Azure AD SSPR protokol sağlayıcısı tarafından döndürülmeyen talepleri dahil edebilirsiniz `DefaultValue` .

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

### <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| İşlem | Yes | **Sendcode** olmalıdır.  |

#### <a name="ui-elements"></a>Kullanıcı arabirimi öğeleri

Aşağıdaki meta veriler SMS hatası gönderdikten sonra görüntülenecek hata iletilerini yapılandırmak için kullanılabilir. Meta veriler, [kendi kendine onaylanan](self-asserted-technical-profile.md) teknik profilde yapılandırılmalıdır. Hata iletileri [yerelleştirilebilecek](localization-string-ids.md#azure-ad-sspr).

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Usermessageifınternalerror | No | Sunucu bir iç hatayla karşılaştıysa Kullanıcı hata iletisi. |
| Usermessageifkısıtlanıyor| No | İstek kısıtlanmışsa, Kullanıcı hata iletisi.|


### <a name="example-send-an-email"></a>Örnek: e-posta gönder

Aşağıdaki örnekte, bir kodu e-posta ile göndermek için kullanılan bir Azure AD SSPR teknik profili gösterilmektedir.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Kodu doğrula

Bu teknik profilin ikinci modu bir kodu doğrulamadır. Bu mod için aşağıdaki seçenekler yapılandırılabilir.

### <a name="input-claims"></a>Giriş talepleri

**Inputclaim** öğesi, Azure AD SSPR 'ye göndermek için taleplerin bir listesini içerir. Ayrıca, şirketinizin adını SSPR teknik profilinde tanımlanan adla eşleyebilirsiniz.

| Claimreferenceıd | Gerekli | Açıklama |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Yes | Daha önce kod göndermek için kullanılan e-posta adresi. E-posta doğrulama oturumunu bulmak için de kullanılır. `PartnerClaimType`Giriş talebinin özelliği olarak ayarlanmalıdır `emailAddress` .|
| Doğrulama kodu  | Yes | Doğrulanacak Kullanıcı tarafından belirtilen doğrulama kodu. `PartnerClaimType`Giriş talebinin özelliği olarak ayarlanmalıdır `verificationCode` . |

**Inputclaimstransformations** öğesi, giriş taleplerini değiştirmek ya da Azure AD SSPR hizmeti çağrılmadan önce yenilerini oluşturmak Için kullanılan **inputclaimstransınfo** öğelerinin bir koleksiyonunu içerebilir.

### <a name="output-claims"></a>Çıkış talepleri

Azure AD SSPR protokol sağlayıcısı herhangi bir **Outputclaim** döndürmüyor, bu nedenle çıkış taleplerini belirtmeniz gerekmez. Ancak, özniteliği ayarladığınız sürece Azure AD SSPR protokol sağlayıcısı tarafından döndürülmeyen talepleri dahil edebilirsiniz `DefaultValue` .

**Outputclaimstransformations** öğesi, çıkış taleplerini değiştirmek veya yenilerini oluşturmak için kullanılan bir **outputclaimstransreference** öğeleri koleksiyonu içerebilir.

### <a name="metadata"></a>Meta veri

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| İşlem | Yes | **Verifycode** olmalıdır |

#### <a name="ui-elements"></a>Kullanıcı arabirimi öğeleri

Aşağıdaki meta veriler, kod doğrulama hatası üzerine görüntülenecek hata iletilerini yapılandırmak için kullanılabilir. Meta veriler, [kendi kendine onaylanan](self-asserted-technical-profile.md) teknik profilde yapılandırılmalıdır. Hata iletileri [yerelleştirilebilecek](localization-string-ids.md#azure-ad-sspr).

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
|Usermessageifrequesgeerer | Kod doğrulama oturumunun süresi dolmuşsa kullanıcıya görüntülenecek ileti. Kodun süresi doldu veya kod belirli bir tanımlayıcı için hiçbir zaman üretilmedi.|
|Usermessageifınternalerror | Sunucu bir iç hatayla karşılaştıysa Kullanıcı hata iletisi.|
|Usermessageifkısıtlanıyor | İstek kısıtlanmışsa, Kullanıcı hata iletisi.|
|Usermessageifdoğrulamaları Icationfailednoretry | Geçersiz bir kod sağladıklarında kullanıcıya görüntülenecek ileti ve kullanıcının doğru kodu sağlamasına izin verilmez.|
|UserMessageIfVerificationFailedRetryAllowed | Geçersiz bir kod sağladıklarında kullanıcıya görüntülenecek ileti ve kullanıcının doğru kodu sağlamasına izin verilir.|

### <a name="example-verify-a-code"></a>Örnek: bir kodu doğrulama

Aşağıdaki örnekte, kodu doğrulamak için kullanılan bir Azure AD SSPR teknik profili gösterilmektedir.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```
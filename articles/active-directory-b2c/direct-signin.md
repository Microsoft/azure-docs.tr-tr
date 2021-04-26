---
title: Azure Active Directory B2C kullanarak doğrudan oturum açma ayarla | Microsoft Docs
description: Oturum açma adını önceden ayarlamayı veya bir sosyal kimlik sağlayıcısına doğrudan yönlendirmeyi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 35e8efa269ab72477b06e86824d368d0a3dced03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103197320"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak doğrudan oturum açma ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory (AD) B2C kullanarak uygulamanız için oturum açma ayarlarken, oturum açma adını veya doğrudan oturum açmayı Facebook, LinkedIn veya bir Microsoft hesabı gibi belirli bir sosyal kimlik sağlayıcısına önceden girebilirsiniz.

## <a name="prepopulate-the-sign-in-name"></a>Oturum açma adını önceden girme

Bir oturum açma Kullanıcı yolculuğu sırasında, bağlı olan taraf uygulaması belirli bir kullanıcı veya etki alanı adını hedefleyebilir. Bir Kullanıcı hedeflenirken, bir uygulama yetkilendirme isteğinde, `login_hint` sorgu parametresini Kullanıcı oturum açma adı ile belirtebilir. Azure AD B2C oturum açma adını otomatik olarak doldurur, ancak kullanıcının yalnızca parola sağlaması gerekir.

![URL 'de vurgulanan login_hint sorgu parametresi ile oturum açma sayfası](./media/direct-signin/login-hint.png)

Kullanıcı, oturum açma metin kutusundaki değeri değiştirebilir.

::: zone pivot="b2c-custom-policy"

Oturum açma ipucu parametresini desteklemek için `SelfAsserted-LocalAccountSignin-Email` Teknik profili geçersiz kılın. `<InputClaims>`Bölümünde, Signınname talebinin DefaultValue değerini olarak ayarlayın `{OIDC:LoginHint}` . `{OIDC:LoginHint}`Değişkeni, parametresinin değerini içerir `login_hint` . Azure AD B2C, Signınname talebinin değerini okur ve Signınname metin kutusunu önceden doldurur.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>Oturum açma oturumunu bir sosyal sağlayıcıya yönlendirin

Uygulamanızın Facebook, LinkedIn veya Google gibi sosyal hesapları içermesi için oturum açma yolculuğunu yapılandırdıysanız, `domain_hint` parametresini belirtebilirsiniz. Bu sorgu parametresi, oturum açma için kullanılması gereken sosyal kimlik sağlayıcısı hakkında Azure AD B2C için bir ipucu sağlar. Örneğin, uygulama belirtiyorsa `domain_hint=facebook.com` , oturum açma doğrudan Facebook oturum açma sayfasına gider.

![URL 'de vurgulanan domain_hint sorgu parametresi ile oturum açma sayfası](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

Etki alanı ipucu sorgu dizesi parametresi, aşağıdaki etki alanlarından birine ayarlanabilir:

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- [Genel OpenID Connect](identity-provider-generic-openid-connect.md)için bkz. [etki alanı ipucu](identity-provider-generic-openid-connect.md#response-mode).

::: zone-end

::: zone pivot="b2c-custom-policy"

Etki alanı ipucu parametresini desteklemek için, etki alanı adını `<Domain>domain name</Domain>` herhangi bir XML öğesi kullanarak yapılandırabilirsiniz `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end


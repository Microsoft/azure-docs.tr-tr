---
title: Azure Active Directory B2C | özel ilkeleri kullanarak çoklu oturum açma oturumu yönetimi Microsoft Docs
description: Azure AD B2C özel ilkeleri kullanarak SSO oturumlarını yönetmeyi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ae30b316133b7479b66a69a3467497a7151dbc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065387"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'da çoklu oturum açma oturumu yönetimi

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) içindeki çoklu oturum açma (SSO) oturum yönetimi, kullanıcının kimliği doğrulandıktan sonra bir kullanıcının Kullanıcı ile etkileşimini denetlemesine olanak sağlar. Örneğin, yönetici, kimlik sağlayıcılarının seçiminin görüntülenip görüntülenmediğini veya yerel hesap ayrıntılarının yeniden girilmesi gerekip gerekmediğini denetleyebilir. Bu makalede Azure AD B2C için SSO ayarlarının nasıl yapılandırılacağı açıklanır.

SSO oturum yönetiminin iki bölümü vardır. İlk olarak, kullanıcının etkileşimiyle doğrudan Azure AD B2C ve diğeri de Facebook gibi dış taraflarla ilgili etkileşimlerle ilgili etkileşimlerle ilgilidir. Azure AD B2C dış taraflar tarafından tutulabilecek SSO oturumlarını geçersiz kılmaz veya atlamaz. Dış tarafa ulaşmak için Azure AD B2C üzerinden yol "hatırlanır", bunun yerine kullanıcıdan sosyal veya kurumsal kimlik sağlayıcısını seçmesini yeniden sorma gereksinimini ortadan kaldırmaktan kaçının. Son SSO kararı dış tarafla birlikte kalır.

SSO oturum yönetimi, özel ilkelerdeki diğer teknik profille aynı semantiğini kullanır. Bir düzenleme adımı yürütüldüğünde, adımla ilişkili teknik profil bir `UseTechnicalProfileForSessionManagement` başvuru için sorgulanır. Varsa, başvurulan SSO oturum sağlayıcısı, kullanıcının bir oturum katılımcısı olup olmadığını görmek için denetlenir. Öyleyse, oturum yeniden doldurmak için SSO oturum sağlayıcısı kullanılır. Benzer şekilde, bir düzenleme adımının yürütülmesi tamamlandığında, bir SSO oturum sağlayıcısı belirtilmişse, oturum bilgilerini oturumdaki depolamak için sağlayıcı kullanılır.

Azure AD B2C, kullanılabilecek birkaç SSO oturum sağlayıcısı tanımladı:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO yönetim sınıfları, `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` bir teknik profilin öğesi kullanılarak belirtilir.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Ad, bu sağlayıcı hiçbir şey yapmaz. Bu sağlayıcı, belirli bir teknik profilde SSO davranışını gizleme için kullanılabilir.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Bu sağlayıcı, talepleri bir oturumda depolamak için kullanılabilir. Bu sağlayıcıya genellikle yerel hesapları yönetmek için kullanılan bir teknik profilde başvurulur. Talepleri bir oturumda depolamak için DefaultSSOSessionProvider kullanılırken, uygulamaya döndürülmek veya sonraki adımlarda ön koşullar tarafından kullanılması gereken talepler, ' deki Kullanıcı profilinden bir okuma ile oturum veya genişleterek kullanılmak zorunda olduğundan emin olmanız gerekir. dizinden. Bu, kimlik doğrulama yolculuğunun eksik taleplerde başarısız olmamasını sağlayacaktır.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Oturumdaki talepleri eklemek için teknik profilin `<PersistedClaims>` öğesini kullanın. Sağlayıcı oturumu yeniden doldurmak için kullanıldığında, kalıcı talepler talep çantasına eklenir. `<OutputClaims>`, oturumdan talepleri almak için kullanılır.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Bu sağlayıcı, "kimlik sağlayıcısını Seç" ekranının görüntülenmesini sağlamak için kullanılır. Genellikle Facebook gibi bir dış kimlik sağlayıcısı için yapılandırılmış teknik bir profilde başvurulur.

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Bu sağlayıcı, uygulamalar arasında Azure AD B2C SAML oturumlarını ve dış SAML kimlik sağlayıcılarını yönetmek için kullanılır.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Teknik profilde iki meta veri öğesi vardır:

| Öğe | Default Value | Olası Değerler | Açıklama
| --- | --- | --- | --- |
| Includesessionındex | true | doğru/yanlış | Sağlayıcıya, oturum dizininin depolanması gerektiğini belirtir. |
| RegisterServiceProviders | true | doğru/yanlış | Sağlayıcının onay verilen tüm SAML hizmeti sağlayıcılarını kaydetmesi gerektiğini gösterir. |

SAML kimlik sağlayıcısı oturumunu depolamak için sağlayıcıyı kullanırken, yukarıdaki öğelerin her ikisi de false olmalıdır. B2C SAML oturumunu depolamak için sağlayıcıyı kullanırken, yukarıdaki öğelerin doğru olması veya varsayılan değerler doğru olduğundan atlanmalıdır. SAML oturumu kapatma için `SessionIndex` ve `NameID` işleminin tamamlanmasını gerekir.


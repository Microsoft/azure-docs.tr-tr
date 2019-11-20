---
title: Azure Active Directory B2C | özel ilkeleri kullanarak SSO ve belirteç özelleştirmesini yönetme | Microsoft Docs
description: Azure Active Directory B2C özel ilkeleri kullanarak SSO ve belirteç özelleştirmeyi yönetme hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 36a95b502c13ccf360ba4ac56b4837d41ee487c8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296402"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeleri kullanarak SSO ve belirteç özelleştirmesini yönetme

Bu makalede belirteç, oturum ve çoklu oturum açma (SSO) yapılandırmalardan Azure Active Directory B2C (Azure AD B2C) [özel ilkelerini](active-directory-b2c-overview-custom.md) kullanarak nasıl yönetebileceğiniz hakkında bilgi sağlanır.

## <a name="token-lifetimes-and-claims-configuration"></a>Belirteç yaşam süreleri ve talep yapılandırması

Belirteç yaşam sürelerinin ayarlarını değiştirmek için, etkilemek istediğiniz ilkenin bağlı olan taraf dosyasına bir [Claimsproviders](claimsproviders.md) öğesi eklersiniz.  **Claimsproviders** öğesi, [TrustFrameworkPolicy](trustframeworkpolicy.md) öğesinin bir alt öğesidir.

BasePolicy öğesi ve bağlı olan taraf dosyasının RelyingParty öğesi arasına ClaimsProviders öğesini ekleyin.

İçinde, belirteç ömürlerinizi etkileyen bilgileri yerleştirmeniz gerekir. XML şu örneğe benzer şekilde görünür:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

Önceki örnekte aşağıdaki değerler ayarlanır:

- **Erişim belirteci yaşam süreleri** -erişim belirteci yaşam süresi değeri **token_lifetime_secs** meta veri öğesiyle ayarlanır. Varsayılan değer 3600 saniyedir (60 dakika).
- **Kimlik belirteci ömrü** -kimlik belirtecinin yaşam süresi değeri **id_token_lifetime_secs** meta veri öğesiyle ayarlanır. Varsayılan değer 3600 saniyedir (60 dakika).
- **Belirteç ömrünü Yenile** -yenileme belirteci yaşam süresi değeri **refresh_token_lifetime_secs** meta veri öğesiyle ayarlanır. Varsayılan değer 1209600 saniyedir (14 gün).
- **Yenileme belirteci kayan pencere ömrü** -yenileme belirtecinize bir kayan pencere ömrü ayarlamak isterseniz, **rolling_refresh_token_lifetime_secs** meta veri öğesinin değerini ayarlayın. Varsayılan değer 7776000 ' dir (90 gün). Bir kayan pencere ömrü zorlamak istemiyorsanız, öğeyi `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` ile değiştirin.
- **Veren (ISS) talebi** -veren (İSS) talebi **ıssuanceclaımpattern** meta veri öğesiyle ayarlanır. Geçerli değerler `AuthorityAndTenantGuid` ve `AuthorityWithTfp` ' dir.
- **Ilke kimliğini temsil eden talep ayarlama** -bu değeri ayarlama seçenekleri `TFP` (güven çerçevesi ilkesi) ve `ACR` (kimlik doğrulama bağlamı başvurusu). `TFP` önerilen değerdir. **Authenticationcontextreferenceclaımpattern** değerini `None` değeriyle ayarlayın.

    **Claimsschema** öğesinde şu öğeyi ekleyin:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    **Outputclaim** öğesinde şu öğeyi ekleyin:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR için **Authenticationcontextreferenceclaımpattern** öğesini kaldırın.

- **Subject (Sub) talebi** -Bu seçenek varsayılan olarak objectID, bu ayarı `Not Supported` ' e geçmek istiyorsanız, bu satırı değiştirin:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    Bu satırla:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Oturum davranışı ve SSO

Oturum davranışlarını ve SSO yapılandırmasını değiştirmek için [RelyingParty](relyingparty.md) öğesinin Içine bir **usersesneonbehavior** öğesi eklersiniz.  **User, Newıdavranışlar** öğesi hemen **Defaultuseryolculuney**' i izlemelidir. **Kullanıcılarınızın Neydavranstes** öğesinin içindeki içindeki bir örneği şöyle görünmelidir:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Önceki örnekte aşağıdaki değerler yapılandırılır:

- **Çoklu oturum açma (SSO)** -çoklu oturum açma, **SingleSignon**ile yapılandırılır. Geçerli değerler şunlardır `Tenant`, `Application`, `Policy` ve `Suppressed`.
- **Web uygulaması oturumu zaman** aşımı-Web uygulaması oturumu zaman aşımı, **Sessionexpiryıtype** öğesiyle ayarlanır. Geçerli değerler `Absolute` ve `Rolling` ' dir.
- **Web uygulaması oturumu ömrü** -Web uygulaması oturumu ömrü, **Sessionexpirınseconds** öğesi ile ayarlanır. Varsayılan değer 86400 saniyedir (1440 dakika).

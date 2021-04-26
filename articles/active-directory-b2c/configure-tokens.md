---
title: Belirteçleri Yapılandırma-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C ' de belirteç ömrünü ve uyumluluk ayarlarını yapılandırmayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f5586b43143763ebf36adb15d96fdb2a91b5f5c
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443483"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C'de belirteçleri yapılandırma

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) ' de [bir belirtecin yaşam süresini ve uyumluluğunu](tokens-overview.md) yapılandırmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Belirteç ömrü davranışı

Belirteç ömrünü aşağıdakiler de dahil olmak üzere yapılandırabilirsiniz:

- **Erişim ve kimlik belirteci yaşam süreleri (dakika)** -OAuth 2,0 taşıyıcı BELIRTECININ ve kimlik belirteçlerinin yaşam süresi. Varsayılan değer 60 dakikadır (1 saat). En düşük (kapsamlı) 5 dakikadır. Maksimum (dahil) 1.440 dakikadır (24 saat).
- **Yenileme belirteci ömrü (gün)** -uygulamanıza kapsam verildiyse, yenileme belirtecinin yeni bir erişim belirteci almak için kullanılabileceği en uzun süre `offline_access` . Varsayılan değer 14 gündür. En düşük (kapsamlı) bir gündür. Maksimum (dahil) 90 gün.
- **Yenileme belirteci kayan pencere ömrü** -yenileme belirteci kayan pencere türü. `Bounded` yenileme belirtecinin, **yaşam süresi (gün)** cinsinden bir süre içinde belirtildiği şekilde uzatılandığını belirtir. `No expiry` yenileme belirteci kayan pencere ömrünün süresinin dolmadığını gösterir.
- **Yaşam süresi uzunluğu (gün)** -bu süre dolduktan sonra, uygulama tarafından alınan en son yenileme belirtecinin geçerlilik süresinden bağımsız olarak Kullanıcı yeniden kimlik doğrulaması zorlanır. Değer, **yenileme belirteci yaşam süresi** değerinden büyük veya buna eşit olmalıdır.

Aşağıdaki diyagramda, belirteç kayan pencere ömrü davranışının yenilenmesi gösterilmektedir.

![Belirteç ömrünü Yenile](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >PKI CE ile yetkilendirme kodu akışını kullanan tek sayfalı uygulamalar, mobil uygulamalar, masaüstü uygulamaları ve Web uygulamaları bu kısıtlamayla karşılaşmadığından, her zaman 24 saat yenileme belirteci süresi vardır. [Tarayıcıda belirteçleri yenileme güvenlik etkileri hakkında daha fazla bilgi edinin](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).


## <a name="configure-token-lifetime"></a>Belirteç ömrünü yapılandırma

::: zone pivot="b2c-user-flow"

Kullanıcı akış belirteci ömrünü yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kullanıcı akışları (ilkeler)** seçeneğini belirleyin.
1. Daha önce oluşturduğunuz kullanıcı akışını açın.
1. **Özellikler**’i seçin.
1. **Belirteç ömrü** altında özellikleri uygulamanızın gereksinimlerine uyacak şekilde ayarlayın.
1. **Kaydet**’e tıklayın.

::: zone-end

::: zone pivot="b2c-custom-policy"

Belirteç uyumlulukta ayarları değiştirmek için, uzantıdaki [belirteç verenin](jwt-issuer-technical-profile.md) teknik profil meta verilerini veya etkilemek istediğiniz ilkenin bağlı olan taraf dosyasını ayarlarsınız. Belirteç verenin teknik profili aşağıdaki örnekteki gibi görünür:

```xml
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
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Önceki örnekte aşağıdaki değerler ayarlanır:

- **token_lifetime_secs** erişim belirteci yaşam süreleri (saniye). Varsayılan değer 3.600 ' dir (1 saat). Minimum değer 300 ' dir (5 dakikadır). Maksimum değer 86.400 ' dir (24 saat). 
- **id_token_lifetime_secs** kimliği belirteci yaşam süreleri (saniye). Varsayılan değer 3.600 ' dir (1 saat). Minimum değer 300 ' dir (5 dakikadır). Maksimum değer 86.400 ' dir (24 saat). 
- **refresh_token_lifetime_secs** Yenileme belirteci yaşam süresi (saniye). Varsayılan değer 120, 9600 ' dir (14 gün). Minimum değer 86.400 ' dir (24 saat). Maksimum değer 7.776.000 ' dir (90 gün). 
- **rolling_refresh_token_lifetime_secs** -belirteç kayan pencere ömrü (saniye) yenileme. Varsayılan değer 7.776.000 ' dir (90 gün). Minimum değer 86.400 ' dir (24 saat). Maksimum değer 31.536.000 ' dir (365 gün). Bir kayan pencere ömrü zorlamak istemiyorsanız, değerini `allow_infinite_rolling_refresh_token` olarak ayarlayın `true` . 
- **allow_infinite_rolling_refresh_token** -yenileme belirteci kayan pencere ömrü hiçbir zaman dolmaz. 

::: zone-end


## <a name="token-compatibility-settings"></a>Belirteç uyumluluk ayarları

Belirteç uyumluluğunu aşağıdakiler de dahil olmak üzere yapılandırabilirsiniz:

- **Veren (ISS) talebi** -ERIŞIM ve kimlik belirteci veren biçimi.
- **Subject (Sub) talebi** -belirtecin, uygulamanın kullanıcısı gibi bilgileri onaysında olduğu asıl. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Bu, belirtecin bir kaynağa erişmek için ne zaman kullanıldığı gibi, güvenli bir şekilde, yetkilendirme denetimleri gerçekleştirmek için de kullanılabilir. Varsayılan olarak, konu talebi, dizindeki kullanıcının nesne KIMLIĞIYLE doldurulur.
- **Kullanıcı akışını temsil eden talep** -bu talep, yürütülen kullanıcı akışını tanımlar. Olası değerler: `tfp` (varsayılan) veya `acr` .

::: zone pivot="b2c-user-flow"

Kullanıcı akış uyumluluk ayarlarınızı yapılandırmak için:

1. **Kullanıcı akışları (ilkeler)** seçeneğini belirleyin.
1. Daha önce oluşturduğunuz kullanıcı akışını açın.
1. **Özellikler**’i seçin.
1. **Belirteç uyumluluk ayarları** altında özellikleri uygulamanızın gereksinimlerine uyacak şekilde ayarlayın.
1. **Kaydet**’e tıklayın.

::: zone-end

::: zone pivot="b2c-custom-policy"

Belirteç uyumlulukta ayarları değiştirmek için, uzantıdaki [belirteç verenin](jwt-issuer-technical-profile.md) teknik profil meta verilerini veya etkilemek istediğiniz ilkenin bağlı olan taraf dosyasını ayarlarsınız. Belirteç verenin teknik profili aşağıdaki örnekteki gibi görünür:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **Veren (ISS) talebi** -veren (İSS) talebi **ıssuanceclaımpattern** meta veri öğesiyle ayarlanır. Geçerli değerler `AuthorityAndTenantGuid` ve ' dir `AuthorityWithTfp` .
- **Ilke kimliğini temsil eden talep ayarlama** -bu değeri ayarlama seçenekleri `TFP` (güven Framework ilkesi) ve `ACR` (kimlik doğrulama bağlamı başvurusu). `TFP` önerilen değerdir. **Authenticationcontextreferenceclaımpattern** değerini olarak ayarlayın `None` .

    **Claimsschema** öğesinde şu öğeyi ekleyin:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    **Outputclaim** öğesinde şu öğeyi ekleyin:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR için **Authenticationcontextreferenceclaımpattern** öğesini kaldırın.

- **Subject (Sub) talebi** -Bu seçenek, bu ayarı değiştirmek istiyorsanız varsayılan olarak ObjectID olarak `Not Supported` değiştirilir, şu satırı değiştirin:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    Bu satırla:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Uygulamanıza isteğe bağlı talepler sağlama

Uygulama talepleri, uygulamaya döndürülen değerlerdir. Kullanıcı akışınızı istenen talepleri içerecek şekilde güncelleştirin.

::: zone pivot="b2c-user-flow"

1. **Kullanıcı akışları (ilkeler)** seçeneğini belirleyin.
1. Daha önce oluşturduğunuz kullanıcı akışını açın.
1. **Uygulama talepleri**’ni seçin.
1. Uygulamanıza geri göndermek istediğiniz talepleri ve öznitelikleri seçin.
1. **Kaydet**’e tıklayın.

::: zone-end

::: zone pivot="b2c-custom-policy"

[Bağlı olan taraf ilkesi teknik profili](relyingparty.md#technicalprofile) çıkış talepleri, bir uygulamaya döndürülen değerlerdir. Çıkış taleplerini eklemek, başarılı bir Kullanıcı yolculuğuna sonra bu talepleri belirtece dönüştürür ve uygulamaya gönderilir. İstenen talepleri bir çıkış talebi olarak eklemek için bağlı olan taraf bölümündeki teknik profil öğesini değiştirin.

1. Özel ilke dosyanızı açın. Örneğin, SignUpOrSignin.xml.
1. Outputclaim öğesini bulun. Belirtece dahil edilmesini istediğiniz OutputClaim 'i ekleyin. 
1. Çıkış talebi özniteliklerini ayarlayın. 

Aşağıdaki örnek `accountBalance` talebi ekler. Accountbakiye talebi, uygulamaya bir bakiye olarak gönderilir. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

OutputClaim öğesi aşağıdaki öznitelikleri içerir:

- **ClaimTypeReferenceId** -ilke dosyası veya üst Ilke dosyasında [Claimsschema](claimsschema.md) bölümünde zaten tanımlanmış olan bir talep türünün tanımlayıcısı.
- **Partnerclaimtype** -belirteçteki talebin adını değiştirmenize izin verir. 
- **DefaultValue** -varsayılan değer. Varsayılan değeri, kiracı KIMLIĞI gibi bir [talep çözümleyici](claim-resolver-overview.md)olarak da ayarlayabilirsiniz.
- **Alwaysusedefaultvalue** -varsayılan değerin kullanımını zorunlu kılın.

::: zone-end

## <a name="authorization-code-lifetime"></a>Yetkilendirme kodu ömrü

Uygulama, [OAuth 2,0 yetkilendirme kodu akışını](authorization-code-flow.md)kullanırken, bir hedef kaynak için erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Yetkilendirme kodları yaklaşık 10 dakika sonra süresi dolacak olan kısa süreli. Yetkilendirme kodu ömrü yapılandırılamıyor. Uygulamanızın yetkilendirme kodlarını 10 dakika içinde olduğundan emin olun. 

## <a name="next-steps"></a>Sonraki adımlar

[Erişim belirteçleri isteme](access-tokens.md)hakkında daha fazla bilgi edinin.

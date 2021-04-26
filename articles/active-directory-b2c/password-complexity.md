---
title: Parola karmaşıklığı gereksinimlerini yapılandırma
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C ' de tüketiciler tarafından sağlanan parolalar için karmaşıklık gereksinimlerini yapılandırma.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 81c6e58e34f30d5736c40c77a308321dee28ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224274"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Azure Active Directory B2C parolalar için karmaşıklık gereksinimlerini yapılandırın

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) bir hesap oluştururken Son Kullanıcı tarafından sağlanan parolaların karmaşıklık gereksinimlerinin değiştirilmesini destekler. Varsayılan olarak, Azure AD B2C **güçlü** parolalar kullanır. Azure AD B2C Ayrıca, müşterilerin kullanabileceği parolaların karmaşıklığını denetlemek için yapılandırma seçeneklerini destekler.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Parola kuralı zorlaması

Kaydolma veya parola sıfırlama sırasında, son kullanıcının karmaşıklık kurallarını karşılayan bir parola sağlaması gerekir. Parola karmaşıklığı kuralları Kullanıcı akışı başına zorlanır. Kayıt sırasında bir Kullanıcı akışının dört basamaklı PIN olmasını gerektirirken, kayıt sırasında sekiz karakterlik bir dize olması gerekir. Örneğin, çocuklar için alt öğeler için farklı parola karmaşıklığı olan bir Kullanıcı akışı kullanabilirsiniz.

Oturum açma sırasında parola karmaşıklığı hiçbir şekilde zorlanmaz. Kullanıcılar, geçerli karmaşıklık gereksinimini karşılamadığından parolalarını değiştirmek için oturum açma sırasında hiçbir şekilde istenmez.

Parola karmaşıklığı aşağıdaki Kullanıcı akışları türlerinde yapılandırılabilir:

- Kaydolma veya oturum açma Kullanıcı akışı
- Parola sıfırlama Kullanıcı akışı

Özel ilkeler kullanıyorsanız, ([bir özel ilkede parola karmaşıklığı yapılandırabilirsiniz](password-complexity.md)) yapabilirsiniz.

## <a name="configure-password-complexity"></a>Parola karmaşıklığını yapılandırma

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
3. Azure portal, araması yapın ve **Azure AD B2C** seçin.
4. **Kullanıcı akışları ' nı** seçin.
2. Bir Kullanıcı akışı seçin ve **Özellikler**' e tıklayın.
3. **Parola karmaşıklığı** bölümünde, bu kullanıcı akışının parola karmaşıklığını **basit**, **güçlü** veya **özel** olarak değiştirin.

### <a name="comparison-chart"></a>Karşılaştırma grafiği

| Karmaşıklık | Description |
| --- | --- |
| Basit | En az 8 ile 64 karakter uzunluğunda bir parola. |
| Güçlü | En az 8 ile 64 karakter uzunluğunda bir parola. 3/4 küçük harf, büyük harf, sayı veya sembol gerektirir. |
| Özel | Bu seçenek, parola karmaşıklığı kuralları üzerinde en çok denetim sağlar.  Özel bir uzunluk yapılandırmasına izin verir.  Ayrıca yalnızca sayı parolalarının kabul edilmesini sağlar (PIN 'ler). |

## <a name="custom-options"></a>Özel seçenekler

### <a name="character-set"></a>Karakter kümesi

Yalnızca rakamları (PIN) veya tam karakter kümesini kabul etmenizi sağlar.

- **Sayılar yalnızca sayılara** izin verir (0-9) ve bir parola girildiğinde.
- **Tümü** , herhangi bir harf, sayı veya simgeye izin verir.

### <a name="length"></a>Uzunluk

Parolanın uzunluk gereksinimlerini denetlemenize olanak tanır.

- **Minimum uzunluk** en az 4 olmalıdır.
- **Maksimum uzunluk** en düşük uzunluğa eşit veya daha büyük olmalı ve en fazla 256 karakter olabilir.

### <a name="character-classes"></a>Karakter sınıfları

Parolada kullanılan farklı karakter türlerini denetlemenizi sağlar.

- **2/4: küçük harfli karakter, büyük harf, sayı (0-9), simge** parolanın en az iki karakter türü içermesini sağlar. Örneğin, bir sayı ve küçük harfli bir karakter.
- **3/4: küçük harfli karakter, büyük harf, sayı (0-9), simge** parolanın en az üç karakter türü içerdiğini sağlar. Örneğin, bir sayı, küçük harf ve bir büyük harf karakteri.
- **4/4: küçük harfli karakter, büyük harf, sayı (0-9), simge** , parola tüm karakter türlerini içerir.

    > [!NOTE]
    > **4/4** gereksinimi, son kullanıcı rahatsız edilmesine yol açabilir. Bazı çalışmalar bu gereksinimin parola entropi ' i geliştirmediğinden gösterilmemiştir. [NIST parola yönergelerine](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) bakın

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Parola koşulu doğrulama

Parola karmaşıklığını yapılandırmak için, `newPassword` ve `reenterPassword` [talep türlerini](claimsschema.md) [koşul doğrulamaları](predicates.md#predicatevalidations)başvurusuyla geçersiz kılın. Predicatevalidation öğesi, bir talep türüne uygulanabilen bir kullanıcı girişi doğrulaması oluşturmak için bir koşullar kümesi gruplandırır. İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğe yoksa, ekleyin.
1. [Claimsschema](claimsschema.md) öğesini bulun. Öğe yoksa, ekleyin.
1. `newPassword`Ve `reenterPassword` taleplerini **Claimsschema** öğesine ekleyin.

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. [Koşullar](predicates.md) , bir talep türünün değerini denetlemek için temel bir doğrulama tanımlar ve true veya false değerini döndürür. Doğrulama, belirtilen bir yöntem öğesi ve yöntemiyle ilgili bir dizi parametre kullanılarak yapılır. Aşağıdaki koşulları, öğesinin kapanışından hemen sonra **Buildingblocks** öğesine ekleyin `</ClaimsSchema>` :

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <Predicates>
        <Predicate Id="LengthRange" Method="IsLengthRange">
          <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
          <Parameters>
            <Parameter Id="Minimum">6</Parameter>
            <Parameter Id="Maximum">64</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Lowercase" Method="IncludesCharacters">
          <UserHelpText>a lowercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">a-z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Uppercase" Method="IncludesCharacters">
          <UserHelpText>an uppercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">A-Z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Number" Method="IncludesCharacters">
          <UserHelpText>a digit</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">0-9</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Symbol" Method="IncludesCharacters">
          <UserHelpText>a symbol</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
          </Parameters>
        </Predicate>
      </Predicates>
    <!-- 
    </BuildingBlocks>-->
    ```

1. Aşağıdaki koşul doğrulamaları, öğesinin kapanışından hemen sonra **Buildingblocks** öğesine ekleyin `</Predicates>` :

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <PredicateValidations>
        <PredicateValidation Id="CustomPassword">
          <PredicateGroups>
            <PredicateGroup Id="LengthGroup">
              <PredicateReferences MatchAtLeast="1">
                <PredicateReference Id="LengthRange" />
              </PredicateReferences>
            </PredicateGroup>
            <PredicateGroup Id="CharacterClasses">
              <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
              <PredicateReferences MatchAtLeast="3">
                <PredicateReference Id="Lowercase" />
                <PredicateReference Id="Uppercase" />
                <PredicateReference Id="Number" />
                <PredicateReference Id="Symbol" />
              </PredicateReferences>
            </PredicateGroup>
          </PredicateGroups>
        </PredicateValidation>
      </PredicateValidations>
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Güçlü parolayı devre dışı bırak 

Aşağıdaki teknik profiller, Azure Active Directory verileri okuyan ve yazan [Teknik profillerdir Active Directory](active-directory-technical-profile.md). Uzantı dosyasındaki bu teknik profilleri geçersiz kılın. `PersistedClaims`Güçlü parola ilkesini devre dışı bırakmak için kullanın. **Claimsproviders** öğesini bulun.  Aşağıdaki talep sağlayıcılarını şu şekilde ekleyin:

```xml
<!-- 
<ClaimsProviders>-->
  <ClaimsProvider>
    <DisplayName>Azure Active Directory</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders>-->
```

[Kullanıcı adı tabanlı oturum açma](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) ilkesini kullanıyorsanız,, `AAD-UserWriteUsingLogonEmail` `AAD-UserWritePasswordUsingObjectId` ve `LocalAccountWritePasswordUsingObjectId` Teknik profillerini *disablestrongpassword* ilkesiyle güncelleştirin.

İlke dosyasını kaydedin.

## <a name="test-your-policy"></a>İlkenizi test etme

### <a name="upload-the-files"></a>Dosyaları karşıya yükleme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Kimlik deneyimi çerçevesini** seçin.
5. Özel Ilkeler sayfasında, **Ilkeyi karşıya yükle**' ye tıklayın.
6. Varsa **Ilkenin üzerine yaz**' ı seçin ve ardından *TrustFrameworkExtensions.xml* dosyasını arayıp seçin.
7. **Karşıya Yükle**'ye tıklayın.

### <a name="run-the-policy"></a>İlkeyi çalıştırma

1. Kaydolma veya oturum açma ilkesini açın. Örneğin, *B2C_1A_signup_signin*.
2. **Uygulama** için, daha önce kaydetmiş olduğunuz uygulamanızı seçin. Belirteci görmek için, **yanıt URL 'sinin** gösterilmesi gerekir `https://jwt.ms` .
3. **Şimdi çalıştır**’a tıklayın.
4. **Şimdi kaydolun**' ı seçin, bir e-posta adresi girin ve yeni bir parola girin. Yönergeler, parola kısıtlamalarına göre sunulmuştur. Kullanıcı bilgilerini girmeyi ve ardından **Oluştur**' u tıklatın. Döndürülen belirtecin içeriğini görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory B2C parola değişikliğini yapılandırmayı](add-password-change-policy.md)öğrenin.
- IEF başvurusunda [koşullar](predicates.md) ve [predicatedoğrulamaları](predicates.md#predicatevalidations) öğeleri hakkında daha fazla bilgi edinin.


::: zone-end

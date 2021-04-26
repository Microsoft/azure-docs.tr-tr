---
title: Oturum davranışını Yapılandırma-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C 'de oturum davranışını yapılandırmayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a3cdb93ee4cbf4a2e15540b9daf78b6c231d393
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579748"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C'de oturum davranışını yapılandırma

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Çoklu oturum açma (SSO), kullanıcılar Azure Active Directory B2C uygulamalarda (Azure AD B2C) oturum açtığında güvenlik ve kolaylık sağlar. Bu makalede, Azure AD B2C kullanılan çoklu oturum açma yöntemleri açıklanmakta ve ilkenizi yapılandırırken en uygun SSO yöntemini seçmenize yardımcı olur.

Çoklu oturum açma ile kullanıcılar tek bir hesapla oturum açıp birden çok uygulamaya erişim sağlar. Uygulama, platform veya etki alanı adından bağımsız olarak bir Web, mobil veya tek sayfalı uygulama olabilir.

Kullanıcı başlangıçta bir uygulamaya oturum açtığında, Azure AD B2C tanımlama bilgisi tabanlı bir oturumu devam ettirir. Sonraki kimlik doğrulama istekleri üzerine Azure AD B2C, tanımlama bilgisi tabanlı oturumu okuyup doğrular ve kullanıcıdan tekrar oturum açmasını istemeden bir erişim belirteci yayınlar. Tanımlama bilgisi tabanlı oturumun süresi dolarsa veya geçersiz olursa kullanıcıdan tekrar oturum açması istenir.  

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Azure AD B2C oturuma genel bakış

Azure AD B2C tümleştirme üç tür SSO oturumu içerir:

- **Azure AD B2C** -oturum Azure AD B2C tarafından yönetiliyor
- **Federal kimlik sağlayıcısı** -oturum, kimlik sağlayıcısı tarafından yönetilen, örneğin Facebook, Salesforce veya Microsoft hesabı
- Web, mobil veya tek sayfalı uygulama tarafından yönetilen **uygulama** -oturum

![SSO oturumu](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C oturumu 

Bir kullanıcı yerel veya sosyal hesapla kimliğini başarıyla doğruladığında, Azure AD B2C kullanıcının tarayıcısına tanımlama bilgisi tabanlı bir oturum depolar. Tanımlama bilgisi, gibi Azure AD B2C kiracı etki alanı adı altında depolanır `https://contoso.b2clogin.com` .

Bir kullanıcı başlangıçta bir Federasyon hesabıyla oturum açarsa ve oturum zaman penceresi (yaşam süresi veya TTL) sırasında aynı uygulamada veya farklı bir uygulamada oturum açarsa, federasyon kimliği sağlayıcısından yeni bir erişim belirteci almaya çalışır Azure AD B2C. Federal Kimlik sağlayıcısı oturumunun geçerliliği dolmuşsa veya geçersizse, federal kimlik sağlayıcısı kullanıcıdan kimlik bilgilerini ister. Oturum hala etkin ise (veya kullanıcı federe hesap yerine yerel bir hesapla oturum açmışsa), Azure AD B2C kullanıcıyı yetkilendirir ve diğer istemleri ortadan kaldırır.

Oturum TTL 'sini ve Azure AD B2C ilkeleri ve uygulamalar arasında oturumu nasıl paylaştığını de içeren oturum davranışını yapılandırabilirsiniz.

### <a name="federated-identity-provider-session"></a>Federal kimlik sağlayıcı oturumu

Bir sosyal veya kurumsal kimlik sağlayıcısı kendi oturumunu yönetir. Tanımlama bilgisi, kimlik sağlayıcısının etki alanı adı altında depolanır `https://login.salesforce.com` . Azure AD B2C, federal kimlik sağlayıcısı oturumunu denetlemez. Bunun yerine, oturum davranışı federal kimlik sağlayıcısı tarafından belirlenir. 

Şu senaryoyu göz önünde bulundurun:

1. Kullanıcı, akışını denetlemek için Facebook 'ta oturum açar.
2. Daha sonra Kullanıcı, uygulamanızı açar ve oturum açma işlemini başlatır. Uygulama, oturum açma işlemini tamamlaması için kullanıcıyı Azure AD B2C yönlendirir.
3. Azure AD B2C kaydolma veya oturum açma sayfasında Kullanıcı, Facebook hesabıyla oturum açmayı seçer. Kullanıcı Facebook 'a yönlendirilir. Facebook 'ta etkin bir oturum varsa, kullanıcıdan kimlik bilgilerini sağlaması istenmez ve Facebook belirtecine sahip Azure AD B2C hemen yeniden yönlendirilir.

### <a name="application-session"></a>Uygulama oturumu

Web, mobil veya tek sayfalı bir uygulama, OAuth erişimi, KIMLIK belirteçleri veya SAML belirteçleri tarafından korunabilir. Bir Kullanıcı uygulamadaki korumalı bir kaynağa erişmeyi denediğinde uygulama tarafında etkin bir oturum olup olmadığını denetler. Uygulama oturumu yoksa veya oturumun süresi dolmuşsa, uygulama oturum açma sayfasına Azure AD B2C kullanıcıyı alır.

Uygulama oturumu, uygulama etki alanı adı altında depolanan tanımlama bilgisi tabanlı bir oturum olabilir, örneğin `https://contoso.com` . Mobil uygulamalar, oturumu farklı bir şekilde saklayabilir, ancak benzer bir yaklaşım kullanabilir.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Azure AD B2C oturum davranışını yapılandırma

Azure AD B2C oturum davranışını aşağıdakiler dahil olmak üzere yapılandırabilirsiniz:

- **Web uygulaması oturumu yaşam süresi (dakika)** -başarılı bir kimlik doğrulamasından sonra Azure AD B2C oturum tanımlama bilgisinin kullanıcının tarayıcısına depolanacağı zaman miktarı. Oturum ömrünü 24 saate kadar ayarlayabilirsiniz.

- **Web uygulaması oturumu zaman aşımı** -bir oturumun, oturum yaşam süresi ayarı veya Oturumumu Açık tut (KMSI) ayarıyla nasıl uzatıldığını gösterir.
  - Sıralı **-kullanıcının** tanımlama bilgisi tabanlı kimlik doğrulaması (varsayılan) her gerçekleştirdiğinde oturumun genişletildiğini gösterir.
  - **Mutlak** -kullanıcının belirtilen süre sonunda yeniden kimlik doğrulamasına zorlandığını gösterir.

- **Çoklu oturum açma yapılandırması** -Azure AD B2C oturumu aşağıdaki kapsamlar ile yapılandırılabilir:
  - **Kiracı** -Bu ayar varsayılan ayardır. Bu ayarın kullanılması, B2C kiracınızdaki birden fazla uygulamanın ve Kullanıcı akışının aynı kullanıcı oturumunu paylaşmasına izin verir. Örneğin, bir Kullanıcı bir uygulamada oturum açtıktan sonra, Kullanıcı da erişimi daha da sorunsuz bir şekilde oturum açabilir.
  - **Uygulama** -Bu ayar, başka uygulamalardan bağımsız olarak bir uygulama için Kullanıcı oturumunu korumanıza olanak sağlar. Örneğin, kullanıcının contoso Market 'te zaten oturum açmış olup olmamasına bakılmaksızın kullanıcının contoso Ilaç 'da oturum açmasını istiyorsanız bu ayarı kullanabilirsiniz.
  - **İlke** -Bu ayar, bir kullanıcı oturumunun, kendisini kullanan uygulamalardan bağımsız olarak bir Kullanıcı akışı için bakımını yapmanıza olanak sağlar. Örneğin, Kullanıcı zaten oturum açmışsa ve bir Multi-Factor Authentication (MFA) adımını tamamlamışsa, Kullanıcı akışına bağlı olan oturumun süresi dolana kadar, kullanıcıya birden çok uygulamanın daha yüksek güvenlik bölümlerine erişim verilebilir.
  - **Gizlenen** -Bu ayar, kullanıcının ilkenin her yürütülmesinden sonra Kullanıcı akışının tamamı boyunca çalışmaya zorlar.
- **Oturumumu Açık bırak (KMSı)** -sürekli bir tanımlama bilgisinin kullanımıyla oturum ömrünü genişletir. Bu özellik etkinleştirilirse ve Kullanıcı onu seçerse, kullanıcı tarayıcıyı kapatıp yeniden açtıktan sonra bile oturum etkin kalır. Oturum yalnızca Kullanıcı oturumunu kapattığında iptal edilir. KMSI özelliği yalnızca yerel hesaplarla oturum açmak için geçerlidir. KMSI özelliği oturum ömrünün üzerine gelir.

::: zone pivot="b2c-user-flow"

Oturum davranışını yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip Azure AD B2C kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. Daha önce oluşturduğunuz kullanıcı akışını açın.
1. **Özellikler**’i seçin.
1. **Web uygulaması oturumu yaşam süresi (dakika)**, **Web uygulaması oturumu zaman aşımı**, **Çoklu oturum açma yapılandırması** yapılandırın ve gerektiğinde **oturum kapatma isteklerinde kimlik belirteci gerektir** .
1. **Kaydet**’e tıklayın.

::: zone-end

::: zone pivot="b2c-custom-policy"

Oturum davranışlarını ve SSO yapılandırmasını değiştirmek için [RelyingParty](relyingparty.md) öğesinin Içine bir **usersesneonbehavior** öğesi eklersiniz.  **User, Newıdavranışlar** öğesi hemen **Defaultuseryolculuney**' i izlemelidir. **Kullanıcılarınızın Neydavranstes** öğesi aşağıdaki örnekteki gibi görünmelidir:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Oturumumu açık tutmayı etkinleştir (KMSI)

Web kullanıcılarınızın kullanıcıları ve Azure AD B2C dizininizde yerel hesaplara sahip yerel uygulamalarınız için KMSı özelliğini etkinleştirebilirsiniz. Özelliği etkinleştirdiğinizde, kullanıcılar, tarayıcıyı kapattıktan sonra oturumun etkin kalması için oturum açmış olmaya devam edebilir. Ardından, Kullanıcı adını ve parolasını yeniden girmesi istenmeden tarayıcıyı yeniden açabilirsiniz. Kullanıcı oturumu kapattığında bu erişim iptal edilir.

![Oturumumu Açık tut onay kutusunun gösterildiği örnek kaydolma, oturum açma sayfası](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

KMSı, bireysel Kullanıcı akış düzeyinde yapılandırılabilir. Kullanıcı akışlarınız için KMSı 'yi etkinleştirmeden önce aşağıdakileri göz önünde bulundurun:

- KMSı yalnızca, kaydolma ve oturum açma (SUSı) için **Önerilen** sürümler, oturum açma ve profil düzenlemesi Kullanıcı akışları için desteklenir. Şu anda bu kullanıcı akışlarının **Standart** veya **eski Preview-v2** sürümüne sahipseniz ve KMSI 'yi etkinleştirmek istiyorsanız, bu kullanıcı akışlarının yeni, **Önerilen** sürümlerini oluşturmanız gerekir.
- KMSı, parola sıfırlama veya kaydolma Kullanıcı akışlarla desteklenmez.
- Kiracınızdaki tüm uygulamalarda KMSı 'yi etkinleştirmek istiyorsanız, kiracınızdaki tüm Kullanıcı akışları için KMSı 'yi etkinleştirmenizi öneririz. Bir Kullanıcı bir oturum sırasında birden çok ilke sunabileceğinden, KMSı 'nin etkin olmadığı bir sorunla karşılaşabileceğinden, KMSI tanımlama bilgisini oturumdan kaldırabiliriz.
- Genel bilgisayarlarda KMSı etkinleştirilmemelidir.

### <a name="configure-kmsi-for-a-user-flow"></a>Bir Kullanıcı akışı için KMSı 'yi yapılandırma

Kullanıcı akışınız için KMSı 'yi etkinleştirmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik**   filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
3.  ****   Azure Portal sol üst köşesindeki tüm hizmetler ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4.  **Kullanıcı akışları (ilkeler)** seçeneğini belirleyin.
5. Daha önce oluşturduğunuz kullanıcı akışını açın.
6.  **Özellikler**' i seçin.

7.  **Oturum davranışı** altında **Oturumumu Açık tut oturumunu etkinleştir**' i seçin. Oturumumu Açık **tut (gün)** seçeneğinin yanında, bir oturumun açık kalabileceğini belirten gün sayısını belirtmek için 1 ile 90 arasında bir değer girin.


   ![Oturumumu Açık tut oturumunu etkinleştir](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Kullanıcılar bu seçeneği genel bilgisayarlarda etkinleştirmemelidir.

### <a name="configure-the-page-identifier"></a>Sayfa tanımlayıcısını yapılandırma

KMSI 'yi etkinleştirmek için, içerik tanımı `DataUri` öğesini [sayfa tanımlayıcısı](contentdefinitions.md#datauri) `unifiedssp` ve [sayfa sürümü](page-layout.md) *1.1.0* veya üzeri olarak ayarlayın.

1. İlkenizin uzantısı dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Bu uzantı dosyası, önkoşul içinde elde etmeniz gereken özel ilke başlangıç paketine dahil olan ilke dosyalarından biridir, [özel ilkeleri](custom-policy-get-started.md)kullanmaya başlayın.
1. **Buildingblocks** öğesi için arama yapın. Öğe yoksa, ekleyin.
1. **ContentDefinitions** öğesini Ilkenin **buildingblocks** öğesine ekleyin.

    Özel ilkeniz aşağıdaki kod parçacığı gibi görünmelidir:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Otomatik olarak onaylanan teknik profile meta verileri ekleyin

Kayıt ve oturum açma sayfasına KMSI onay kutusunu eklemek için `setting.enableRememberMe` meta verileri doğru olarak ayarlayın. Uzantı dosyasındaki SelfAsserted-Localaccountsignın-email teknik profillerini geçersiz kılın.

1. ClaimsProviders öğesini bulun. Öğe yoksa, ekleyin.
1. Aşağıdaki talep sağlayıcısını ClaimsProviders öğesine ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Uzantı dosyasını kaydedin.

### <a name="configure-a-relying-party-file"></a>Bağlı olan taraf dosyası yapılandırma

Oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf (RP) dosyasını güncelleştirin. Keepaliveındays parametresi, Oturumumu Açık ol (KMSı) oturum tanımlama bilgisinin ne kadar süreyle devam etmesi gerektiğini yapılandırmanıza olanak tanır. Örneğin, değeri 30 olarak ayarlarsanız, KMSı oturum tanımlama bilgisi 30 gün boyunca devam eder. Değerin aralığı 1 ile 90 gün arasındadır.

1. Özel ilke dosyanızı açın. Örneğin, *SignUpOrSignin.xml*.
1. Zaten mevcut değilse, `<UserJourneyBehaviors>` düğüme bir alt düğüm ekleyin `<RelyingParty>` . Hemen sonra yerleştirilmelidir `<DefaultUserJourney ReferenceId="User journey Id" />` , örneğin: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Aşağıdaki düğümü öğesinin alt öğesi olarak ekleyin `<UserJourneyBehaviors>` .

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Aşağıdaki örnekte gösterildiği gibi, Sessionexpirınseconds değerini kısa bir süre (1200 saniye) olarak ayarlamanızı öneririz, ancak Keepaliveındays değeri görece uzun bir döneme (30 gün) ayarlanabilir:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Oturumu kapatma

Kullanıcının uygulamadan oturum açmasını istediğinizde, uygulamanın tanımlama bilgilerini temizlemek veya oturumu Kullanıcı ile sonlandırmak yeterli değildir. Oturumu kapatmak için kullanıcıyı Azure AD B2C yeniden yönlendirmeniz gerekir. Aksi takdirde, Kullanıcı, kimlik bilgilerini tekrar girmeden uygulamalarınızda yeniden kimlik doğrulaması yapabilir.

Bir oturum kapatma isteği sonrasında Azure AD B2C:

1. Azure AD B2C tanımlama bilgisine dayalı oturumu geçersiz kılar.
::: zone pivot="b2c-user-flow"
2. Federal Kimlik sağlayıcılarından oturum açmaya çalışır
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Federal Kimlik sağlayıcılarından oturum açmaya çalışır:
   - OpenID Connect-kimlik sağlayıcısı iyi bilinen yapılandırma uç noktası bir `end_session_endpoint` konum belirtiyorsa. Oturum kapatma isteği `id_token_hint` parametreyi geçirmez. Federal Kimlik sağlayıcısı bu parametreyi gerektiriyorsa, oturum kapatma isteği başarısız olur.
   - OAuth2- [kimlik sağlayıcısı meta verileri](oauth2-technical-profile.md#metadata) `end_session_endpoint` konumu içeriyorsa.
   - SAML- [kimlik sağlayıcısı meta verileri](identity-provider-generic-saml.md) `SingleLogoutService` konumu içeriyorsa.
4. İsteğe bağlı olarak, diğer uygulamalardan oturum kapatır. Daha fazla bilgi için bkz. [Çoklu oturum kapatma](#single-sign-out) bölümü.

> [!NOTE]
> Kimlik sağlayıcısının teknik profil meta verilerini olarak ayarlayarak federal kimlik sağlayıcılarının oturumunu devre dışı bırakabilirsiniz `SingleLogoutEnabled` `false` .
::: zone-end

Oturum açma, kullanıcının çoklu oturum açma durumunu Azure AD B2C temizler, ancak kullanıcıyı sosyal kimlik sağlayıcısı oturumunun dışında imzalayamayabilir. Kullanıcı sonraki oturum açma sırasında aynı kimlik sağlayıcısını seçerse, kimlik bilgilerini girmeden yeniden kimlik doğrulaması sağlayabilir. Kullanıcı uygulamanın oturumunu kapatmak isterse, Facebook hesabında oturumu kapatmak istedikleri anlamına gelmez. Ancak, yerel hesaplar kullanılıyorsa, kullanıcının oturumu doğru şekilde sona erer.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Çoklu oturum kapatma 

Kullanıcıyı Azure AD B2C oturum kapatma uç noktasına yönlendirirsiniz (hem OAuth2 hem de SAML protokolleri için), Azure AD B2C kullanıcının oturumunu tarayıcıdan temizler. Ancak Kullanıcı, kimlik doğrulaması için Azure AD B2C kullanan diğer uygulamalarda oturum açmış olabilir. Bu uygulamaların kullanıcıyı aynı anda imzalamasını sağlamak için, Azure AD B2C `LogoutUrl` kullanıcının oturum açmış olduğu tüm uygulamalara kayıtlı BIR http get isteği gönderir.

Uygulamalar, kullanıcıyı tanıtan ve yanıt döndüren tüm oturumları temizleyerek bu isteğe yanıt vermelidir `200` . Uygulamanızda çoklu oturum açmayı desteklemek istiyorsanız, uygulamanızın kodunda bir uygulamanız gerekir `LogoutUrl` . 

Çoklu oturum açmayı desteklemek için, hem JWT hem de SAML için belirteç verenin teknik profillerinin şunları belirtmesi gerekir:

- Protokol adı, örneğin `<Protocol Name="OpenIdConnect" />`
- Oturum teknik profiline başvuru. Örneğin, `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Aşağıdaki örnekte, çoklu oturum açma işlemiyle birlikte JWT ve SAML belirteci verenler gösterilmektedir:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Logout yeniden yönlendirmenizi güvenli hale getirin

Oturum kapatıldıktan sonra, `post_logout_redirect_uri` uygulama için belirtilen yanıt URL 'lerinden bağımsız olarak, Kullanıcı parametresinde BELIRTILEN URI 'ye yönlendirilir. Ancak, geçerli bir `id_token_hint` değer geçirilirse ve **oturum kapatma Isteklerinde kimlik belirteci iste** açıksa Azure AD B2C, öğesinin `post_logout_redirect_uri` yeniden yönlendirmeyi gerçekleştirmeden önce uygulamanın yapılandırılmış yeniden yönlendirme URI 'lerinden biriyle eşleştiğini doğrular. Uygulama için eşleşen bir yanıt URL 'SI yapılandırılmamışsa, bir hata iletisi görüntülenir ve Kullanıcı yeniden yönlendirilmez. 

::: zone pivot="b2c-user-flow"

Oturum kapatma isteklerinde KIMLIK belirteci istemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip Azure AD B2C kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. Daha önce oluşturduğunuz kullanıcı akışını açın.
1. **Özellikler**’i seçin.
1. **Oturum kapatma ISTEKLERINDE kimlik belirteci gerektir '** i etkinleştirin.
1. **Azure AD B2C**'e geri dönün.
1. **Uygulama kayıtları**' yi seçin ve ardından uygulamanızı seçin.
1. **Kimlik Doğrulaması**'nı seçin.
1. **Logout URL 'si** metin kutusunda, post Logout yeniden yönlendirme URI 'sini yazın ve ardından **Kaydet**' i seçin.

::: zone-end

::: zone pivot="b2c-custom-policy"

Oturum kapatma isteklerinde bir KIMLIK belirteci istemek için, [RelyingParty](relyingparty.md) öğesinin Içine bir **Useran neondavranışlar** öğesi ekleyin. Ardından, **SingleSignon** öğesinin **Enforceıdtokenhintonlogout** değerini olarak ayarlayın `true` . **Kullanıcıbağlantısı Neydavranışlar** öğesi şu örnekteki gibi görünmelidir:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Uygulama oturum kapatma URL 'nizi yapılandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip Azure AD B2C kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Uygulama kayıtları**' yi seçin ve ardından uygulamanızı seçin.
1. **Kimlik Doğrulaması**'nı seçin.
1. **Logout URL 'si** metin kutusunda, post Logout yeniden yönlendirme URI 'sini yazın ve ardından **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C belirteçlerin nasıl yapılandırılacağını](configure-tokens.md)öğrenin.

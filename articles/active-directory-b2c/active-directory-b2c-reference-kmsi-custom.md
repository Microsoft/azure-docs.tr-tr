---
title: Oturumumu Açık tut Azure Active Directory B2C
description: Azure Active Directory B2C 'da Oturumumu Açık tut (KMSI) ayarlamayı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 29cdf5e7723113b4673945bf5db3158680a44b79
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147031"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'da Oturumumu Açık tut (KMSI) özelliğini etkinleştir

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Web kullanıcılarınızın kullanıcıları ve Azure Active Directory B2C (Azure AD B2C) dizininizde yerel hesaplara sahip yerel uygulamalarınız için Oturumumu Açık tut (KMSı) işlevini etkinleştirebilirsiniz. Bu özellik, Kullanıcı adını ve parolasını yeniden girmesi istenmeden, uygulamanıza geri dönen kullanıcılara erişim izni verir. Kullanıcı oturumu kapattığında bu erişim iptal edilir.

Kullanıcılar bu seçeneği genel bilgisayarlarda etkinleştirmemelidir.

![Oturumumu Açık tut onay kutusunun gösterildiği örnek kaydolma, oturum açma sayfası](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Önkoşullar

Yerel hesap oturum açmaya izin verecek şekilde yapılandırılmış bir Azure AD B2C kiracısı. KMSı, dış kimlik sağlayıcısı hesaplarında desteklenmez.

Kiracınız yoksa [öğreticideki adımları kullanarak bir tane oluşturabilirsiniz: Azure Active Directory B2C kiracı](tutorial-create-tenant.md)oluşturun.

## <a name="add-a-content-definition-element"></a>İçerik tanımı öğesi ekleme

Uzantı dosyanızın **Buildingblocks** öğesi altında, bir **ContentDefinitions** öğesi ekleyin.

1. **ContentDefinitions** öğesi altında, tanımlayıcısına `api.signuporsigninwithkmsi`sahip bir **ContentDefinition** öğesi ekleyin.
2. **ContentDefinition** öğesi altında, **loaduri**, **recoveryuri**ve **datauri** öğelerini ekleyin. **Datauri** öğesinin değeri,oturumaçmasayfalarındabirKMSI'yiaçanbirmakinetarafındananlaşılırbirtanıtıcıdır.`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` Bu değer değiştirilmemelidir.

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Yerel hesap için bir oturum açma talep sağlayıcısı ekleme

İlkenizin uzantı dosyasındaki **ClaimsProvider** öğesini kullanarak yerel hesap oturum açma sağlayıcısını bir talep sağlayıcı olarak tanımlayabilirsiniz:

1. Çalışma dizininizdeki *TrustFrameworkExtensions. xml* dosyasını açın.
2. **Claimsproviders** öğesini bulun. Yoksa, kök öğenin altına ekleyin. [Başlangıç paketi](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) bir yerel hesap oturum açma talep sağlayıcısı içerir.
3. Aşağıdaki örnekte gösterildiği gibi **DisplayName** ve **teknisyen alprofıle** Içeren bir **ClaimsProvider** öğesi ekleyin:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Uygulama tanımlayıcılarını özel ilkenize ekleyin

Uygulama tanımlayıcılarını *TrustFrameworkExtensions. xml* dosyasına ekleyin.

1. *TrustFrameworkExtensions. xml* dosyasında, tanıtıcısına sahip olan **teknisyen alprofile** öğesini `login-NonInteractive` ve tanıtıcısı `login-NonInteractive-PasswordChange` ve tüm değerlerini `IdentityExperienceFrameworkAppId` Değiştir [kullanmaya](active-directory-b2c-get-started-custom.md)başlama bölümünde açıklandığı gibi, kimlik deneyimi çerçevesi uygulamasının uygulama tanımlayıcısı ile.

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Tüm değerlerini `ProxyIdentityExperienceFrameworkAppId` , [Başlarken](active-directory-b2c-get-started-custom.md)bölümünde açıklandığı gibi proxy kimlik deneyimi çerçevesi uygulamasının uygulama tanımlayıcısıyla değiştirin.
3. Uzantı dosyasını kaydedin.

## <a name="create-a-kmsi-enabled-user-journey"></a>Bir KMSı etkin kullanıcı yolculuğu oluşturma

Kullanıcı yolculuğuna yerel bir hesap için oturum açma talep sağlayıcısı ekleyin.

1. İlkenizin temel dosyasını açın. Örneğin, *TrustFrameworkBase. xml*.
2. User, **neys** öğesini bulun ve tanımlayıcısını `SignUpOrSignIn`kullanan **useryolculuney** öğesinin tüm içeriğini kopyalayın.
3. Uzantı dosyasını açın. Örneğin, *TrustFrameworkExtensions. xml* ve **Userınewys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
4. Userıda **neys** öğesinin bir alt öğesi olarak kopyaladığınız tüm **Kullanıcı yolculuğu** öğesini yapıştırın.
5. Yeni Kullanıcı yolculuğu için tanımlayıcının değerini değiştirin. Örneğin: `SignUpOrSignInWithKmsi`.
6. Son olarak, ilk düzenleme adımında **Contentdefinitionreferenceıd** değerini olarak `api.signuporsigninwithkmsi`değiştirin. Bu değerin ayarı, Kullanıcı yolculuğunda onay kutusunu sunar.
7. Uzantı dosyasını kaydedin ve karşıya yükleyin ve tüm doğrulamaların başarılı olduğundan emin olun.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Bağlı olan taraf dosyası oluşturma

Oluşturduğunuz Kullanıcı yolculuğunu başlatan bağlı olan taraf (RP) dosyasını güncelleştirin.

1. Çalışma dizininizde *Signuporsignın. xml* dosyasının bir kopyasını oluşturun ve yeniden adlandırın. Örneğin, *Signuporsignınwithkmsi. xml*.
2. Yeni dosyayı açın ve **TrustFrameworkPolicy** Için **PolicyId** özniteliğini benzersiz bir değerle güncelleştirin. Bu, ilkenizin adıdır. Örneğin: `SignUpOrSignInWithKmsi`.
3. **Defaultuseryolculuney** öğesi Için **referenceıd** özniteliğini, oluşturduğunuz yeni Kullanıcı yolculuğunun tanımlayıcısıyla eşleşecek şekilde değiştirin. Örneğin: `SignUpOrSignInWithKmsi`.

    KMSı, ilk alt öğeleri olarak **SingleSignon**, **Sessionexpirytype**ve **Sessionexpirınseconds** ' ı kullanarak **userınneydavranışlar** öğesi kullanılarak yapılandırılır. **Keepaliveındays** özniteliği, kullanıcının oturumu ne kadar süreyle kaldığını denetler. Aşağıdaki örnekte, kullanıcının sessiz kimlik doğrulamasını ne sıklıkta gerçekleştirdiğine bakılmaksızın `7` KMSI oturumu otomatik olarak gün sonra dolar. KMSI işlevlerini devre dışı bırakmak için `0` **keepaliveındays** değerini ayarlama. Varsayılan olarak, bu değer `0`. **Sessionexpiryıtype** değeri ise `Rolling`, kullanıcının sessiz kimlik doğrulaması yaptığı her seferinde KMSI oturumu `7` günlere göre genişletilir.  `Rolling` Seçilirse, gün sayısını minimum tutmanız gerekir.

    **Sessionexpirınınseconds** DEĞERI, SSO oturumunun süre sonu süresini temsil eder. Bu, KMSı oturumunun kullanım dışı olup olmadığını denetlemek için Azure AD B2C tarafından dahili olarak kullanılır. **Keepaliveındays** değeri, Web tarayıcısında SSO tanımlama bilgisinin Expires/Max-Age değerini belirler. **Sessionexpirınseconds**'ın aksine, tarayıcının, kapalıyken tanımlama bilgisini temizlemesini engellemek için **keepaliveındays** kullanılır. Kullanıcı yalnızca SSO oturumu tanımlama bilgisi varsa, bu, **Keepaliveındays**tarafından denetlenen ve süresi dolmayan ve **Sessionexpirınseconds**tarafından denetlenen bir şekilde oturum açmak için sessizce oturum açabilir.

    Bir kullanıcı kayıt ve oturum açma sayfasında Oturumumu Açık olarak etkinleştirmezse, **sessionexpiron saniye** tarafından belirtilen süre geçtikten sonra veya tarayıcı kapatıldıktan sonra bir oturumun süresi dolar. Bir Kullanıcı Oturumumu etkinleştirmesine izin verirseniz, **Keepaliveındays** değeri **sessionexpirınseconds** değerini geçersiz kılar ve oturum sona erme süresini belirler. Kullanıcılar tarayıcıyı kapatıp yeniden açsa bile, yine de **Keepaliveındays**süresi içinde olduğu sürece sessizce oturum açabilirler. Aşağıdaki örnekte gösterildiği gibi, **Sessionexpirınseconds** değerini kısa bir süre (1200 saniye) olarak ayarlamanız önerilir, ancak aşağıdaki örnekte gösterildiği gibi **keepaliveındays** değeri görece uzun bir döneme (7 gün) ayarlanabilir:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
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
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Değişikliklerinizi kaydettikten sonra dosyayı karşıya yükleyin.
5. Karşıya yüklediğiniz özel ilkeyi test etmek için, Azure portal, ilke sayfasına gidin ve **Şimdi Çalıştır**' ı seçin.

Örnek ilkeyi [burada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)bulabilirsiniz.

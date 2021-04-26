---
title: BioCatch 'i Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Riskli ve sahte kullanıcıları belirlemek için BioCatch ile Azure Active Directory B2C yapılandırma öğreticisi
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819463"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C Biyocatch yapılandırma

Bu örnek öğreticide, müşteri kimlik ve erişim yönetimi (CıHAR) güvenlik duruşunuzu daha da artırmak için Azure Active Directory (AD) B2C kimlik doğrulamasını [Biocatch](https://www.biocatch.com/) ile tümleştirmeyi öğrenin. BioCatch, yasal müşterilerin ve siber dolandırıcıların ayırt edilmesini sağlayan Öngörüler oluşturmak için bir kullanıcının fiziksel ve bilişsel dijital davranışlarını analiz eder.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](tutorial-create-tenant.md) .

- Bir [Biocatch](https://www.biocatch.com/contact-us) hesabı.

## <a name="scenario-description"></a>Senaryo açıklaması

BioCatch tümleştirmesi aşağıdaki bileşenleri içerir:

- **Bir Web uygulaması veya Web hizmeti** -Kullanıcı bu Web hizmetine ilk göz atar. Bu Web hizmeti, BioCatch 'e gönderilen benzersiz bir istemci oturumu KIMLIĞI oluşturur. İstemci oturumu KIMLIĞI daha sonra, Kullanıcı davranışı özelliklerinin doğrudan BioCatch 'e iletilmesi başlar.

- **A yöntemi** -Azure AD B2C benzersiz ISTEMCI oturumu kimliğini gönderir. Belirtilen örnekte, JavaScript değerini gizli HTML alanına girmek için kullanılır.

- **Azure AD B2C özelleştirilmiş bir kullanıcı arabirimi** -Yukarıdaki yöntem kullanılıyorsa JavaScript 'ten ISTEMCI oturum kimliği girişi IÇIN bir HTML alanını gizler

- **Özel ilke Azure AD B2C**

  - Özel istemci oturum KIMLIĞINI kullanıcı arabiriminden bir talep biçiminde alır. Bu, kendi kendine onaylanan bir teknik profil aracılığıyla elde edilir

  - , Bir REST API talep sağlayıcısı aracılığıyla BioCatch ile tümleşir ve istemci oturumu KIMLIĞINI BioCatch platformuna geçirir

  - Özel ilke mantığı için BioCatch 'ten daha sonra işlem yapmak için birden çok özel talep döndürülür

  - Örneğin, oturum riskini değerlendiren ve çok faktörlü kimlik doğrulaması (MFA) gibi bir eylemi koşullu olarak yürüten bir Kullanıcı yolculuğu.

![Biyografisi catch mimarisinin diyagramı.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Adım  | Description |
|:---|:-----------------------|
|1a@@     | Kullanıcı Web hizmetine gözatar. Web hizmeti daha sonra HTML, CSS veya JavaScript değerlerini döndürür ve BioCatch JavaScript SDK 'sını yüklemek için yapılandırır. İstemci tarafı JavaScript, BioCatch SDK 'Sı için istemci oturumu KIMLIĞINI yapılandırır/ayarlar. Alternatif olarak, Web hizmeti istemci oturumu KIMLIĞINI önceden yapılandırabilir ve istemciye gönderebilir.        |
|1B    |  Örnek bir BioCatch JavaScript SDK 'sını BioCatch platformunda yapılandırın. Adım 1a 'daki istemci oturumu KIMLIĞINI kullanarak istemci cihazdan Biyocatch 'e hemen bir Kullanıcı davranışı özelliği gönderilmeye başlayın.    |
|2     |  Kullanıcı kaydolma/oturum açma girişiminde bulunur ve Azure AD B2C yeniden yönlendirilir.      |
|3A@@    | Kullanıcı yolculuğunun bir parçası, istemci oturumu KIMLIĞINI girdi olarak alan, kendiliğinden onaylanan bir ClaimsProvider ' dır. Bu alan ekranda gizlidir. Oturum KIMLIĞINI alana girmek için JavaScript 'ı kullanabilirsiniz. Kaydolma/oturum açma işlemine devam etmek için *İleri* düğmesini seçin.|
|3B     | İstemci oturum KIMLIĞI, bir risk puanı tespit etmek için BioCatch platformuna gönderilir. |
|3c     | BioCatch, oturum hakkında risk puanı ve Yapılacaklar – izin verme veya engelleme önerisi gibi bilgileri döndürür |
|3B    |Kullanıcı yolculuğu, döndürülen talepler üzerinde davranan bir koşullu onay adımına sahiptir|
| 4   | Koşullu Denetim sonucuna bağlı olarak, *adım MFA* gibi bir eylem çağrılır|
|5    | Kullanıcının Web hizmeti sayfasını ilk kez ziyaret eden herhangi bir zamanda, Web hizmeti, risk Puanını ve oturum bilgilerini gerçek zamanlı olarak belirleyebilmek için BioCatch API 'sini sorgulamak üzere istemci oturumu KIMLIĞINI kullanabilir. |

## <a name="onboard-with-biocatch"></a>BioCatch ile ekleme

[Biocatch](https://www.biocatch.com/contact-us) ile iletişim kurun ve bir hesap oluşturun.

## <a name="configure-the-custom-ui"></a>Özel Kullanıcı arabirimini yapılandırma

İstemci oturumu KIMLIĞI alanını gizlemeniz önerilir. Alanı gizlemek için CSS, JavaScript veya başka herhangi bir yöntemi kullanın. Test amacıyla, alanı gösterebilir. Örneğin, JavaScript, giriş alanını şu şekilde gizlemek için kullanılır:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Azure AD B2C Identity Experience Framework ilkelerini yapılandırma

1. İlk [özel ilke yapılandırmasını](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)yapılandırın.

2. Uzantılar dosyasından devralan yeni bir dosya oluşturun.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Giriş kutusunu, BuildingBlocks kaynağı altında gizlemek için özel kullanıcı arabirimine bir başvuru oluşturun.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Aşağıdaki talepleri BuildingBlocks kaynağı altına ekleyin.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. İstemci oturum KIMLIĞI alanı için kendi kendine onaylanan talep sağlayıcısını yapılandırın.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. BioCatch için REST API talep sağlayıcısını yapılandırın. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > BioCatch, yapılandırılacak URL, müşteri KIMLIĞI ve benzersiz kullanıcı KIMLIĞI (UUID) sağlar. Müşteri SessionID talebi, BioCatch için bir QueryString parametresi olarak geçirilir. Etkinlik türünü seçebilirsiniz, örneğin *MAKE_PAYMENT*.

7. Kullanıcı yolculuğunu yapılandırın; örneği izleyin

   1. Clientsessionıd 'yi talep olarak alın

   1. Oturum bilgilerini almak için BioCatch API 'sini çağırın

   1. Döndürülen talep *riski* *düşükse*, MFA adımını atlayın, aksi takdirde Kullanıcı MFA 'yı zorlayın

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Bağlı olan taraf yapılandırmasında yapılandırma (isteğe bağlı)

    Özel olarak *risklevel* ve *Score* ile, biocatch tarafından döndürülen bilgilerin uygulamanıza talepler olarak iletilmesi yararlı olur.

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C ile tümleştirme

İlke dosyalarını Azure AD B2C eklemek için aşağıdaki adımları izleyin

1.  [**Azure portal**](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.

2. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik**   filtresi ' ni seçin ve kiracınızı içeren dizini seçin.

3.  ****   Azure Portal sol üst köşesindeki tüm hizmetler ' i seçin, arama yapın ve Azure AD B2C seçin.

4.  **Azure AD B2C**   >  **Identity Experience Framework** 'e gidin

3. Tüm ilke dosyalarını kiracınıza yükleyin.

## <a name="test-the-solution"></a>Çözümü test etme

1. [JWT.MS 'e yönlendiren bir kukla uygulamayı kaydetme](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. **Kimlik deneyimi çerçevesi** altında, oluşturduğunuz ilkeyi seçin

3. İlke penceresinde, sözde JWT.MS uygulamasını seçin ve **Şimdi Çalıştır** ' ı seçin.

4. Kaydolma akışı ' na gidip bir hesap oluşturun. JWT.MS 'e döndürülen belirtecin riskLevel ve Score için 2x talepler olması gerekir. Örneği izleyin.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure AD B2C'deki özel ilkeler](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

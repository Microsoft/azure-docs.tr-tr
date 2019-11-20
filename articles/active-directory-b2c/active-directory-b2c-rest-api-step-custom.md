---
title: Talep değişimlerinin REST API Azure Active Directory B2C
description: Active Directory B2C içindeki özel ilkelere REST API talep alışverişleri ekleyin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 12ddbe9f43baf68f6c11c9b720a0f684316af46a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065326"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içindeki özel ilkelere REST API talep alışverişleri ekleyin

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) [özel ilkelerinize](active-directory-b2c-overview-custom.md) bir restısteki API ile etkileşim ekleyebilirsiniz. Bu makalede, daha fazla hizmet ile etkileşim kuran Azure AD B2C Kullanıcı yolculuğunun nasıl oluşturulacağı gösterilmektedir.

Etkileşim, REST API talepler ve Azure AD B2C arasında bilgi talebi alışverişi içerir. Talep değişimlerinin aşağıdaki özellikleri vardır:

- , Düzenleme adımı olarak tasarlanabilir.
- , Bir dış eylemi tetikleyebilir. Örneğin, bir olayı bir dış veritabanında günlüğe kaydedebilir.
- , Bir değeri getirmek ve sonra Kullanıcı veritabanında depolamak için kullanılabilir.
- Yürütme akışını değiştirebilir.

Bu makalede temsil edilen senaryo aşağıdaki eylemleri içerir:

1. Kullanıcıya bir dış sistemde arama yapın.
2. Kullanıcının kaydedildiği şehri alın.
3. Bu özniteliği bir talep olarak uygulamaya döndürün.

## <a name="prerequisites"></a>Önkoşullar

- [Özel ilkelerle çalışmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın.
- Etkileşimde bulunmak için bir REST API uç noktası. Bu makale örnek olarak basit bir Azure işlevi kullanır. Azure işlevi oluşturmak için, bkz. [Azure Portal ilk işlevinizi oluşturma](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>API 'YI hazırlama

Bu bölümde, Azure işlevini için `email`bir değer alacak şekilde hazırlandınız ve sonra Azure AD B2C tarafından talep olarak kullanılabilecek `city` değeri döndürürler.

Aşağıdaki kodu kullanmak için oluşturduğunuz Azure işlevi için Run. CSX dosyasını değiştirin:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Talep değişimini yapılandırma

Teknik bir profil, talep alışverişi için yapılandırma sağlar.

*TrustFrameworkExtensions. xml* dosyasını açın ve aşağıdaki **ClaimsProvider** XML öğesini **claimsproviders** öğesine ekleyin.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**Inputclaim** öğesi Rest hizmetine gönderilen talepleri tanımlar. Bu örnekte, talebin `givenName` değeri, talep `email`olarak rest hizmetine gönderilir. **Outputclaim** Öğesı, Rest hizmetinden beklenen talepleri tanımlar.

Yukarıdaki `AuthenticationType` açıklamalar ve `AllowInsecureAuthInProduction` bir üretim ortamına geçtiğinizde yapmanız gereken değişiklikleri belirtin. Yeniden yapılan API 'lerinizi üretime yönelik olarak güvenli hale getirme hakkında bilgi edinmek için bkz. [sertifika kimlik doğrulaması ile](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) [temel kimlik doğrulama](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) ve güvenilir API 'ler ile güvenli yeniden API 'ler sağlama.

## <a name="add-the-claim-definition"></a>Talep tanımını ekleyin

**Buildingblocks** öğesinin `city` içine bir tanım ekleyin. Bu öğeyi TrustFrameworkExtensions. xml dosyasının başlangıcında bulabilirsiniz.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Düzenleme adımı ekleme

REST API çağrısının düzenleme adımı olarak kullanılabileceği birçok kullanım durumu vardır. Düzenleme adımı olarak, bir Kullanıcı ilk kez kayıt gibi bir görevi başarıyla tamamladıktan sonra veya bilgileri eşitlenmiş halde tutmak için bir profil güncelleştirmesi olarak bir dış sisteme güncelleştirme olarak kullanılabilir. Bu durumda, profil düzenledikten sonra uygulamaya girilen bilgileri artırmak için kullanılır.

Profile bir adım ekleyin Kullanıcı yolculuğunu düzenleyin. Kullanıcının kimliği doğrulandıktan sonra (aşağıdaki XML 'de Orchestration adımları 1-4) ve Kullanıcı güncelleştirilmiş profil bilgilerini sağlamıştır (5. adım). *TrustFrameworkBase. xml* dosyasındaki profile düzenleme Kullanıcı yolculuğu XML kodunu, **User, neys** öğesinin içindeki *TrustFrameworkExtensions. xml* dosyasına kopyalayın. Ardından, değişikliği 6. adımda yapın.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Kullanıcı yolculuğu için son XML şu örnekteki gibi görünmelidir:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Talebi ekleyin

*Profileedit. xml* dosyasını düzenleyin ve `<OutputClaim ClaimTypeReferenceId="city" />` **outputclaim** öğesine ekleyin.

Yeni talebi ekledikten sonra teknik profil şu örneğe benzer şekilde görünür:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Değişikliklerinizi ve testinizi karşıya yükleyin

1. Seçim Devam etmeden önce dosyaların mevcut sürümünü (indirerek indirerek) kaydedin.
2. *TrustFrameworkExtensions. xml* ve *profileedit. xml* dosyasını karşıya yükleyin ve var olan dosyanın üzerine yazmayı seçin.
3. **B2C_1A_ProfileEdit**öğesini seçin.
4. Özel ilkenin genel bakış sayfasında **Uygulama Seç** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'sinin** `https://jwt.ms`olduğundan emin olun.
4. **Şimdi Çalıştır**' ı seçin. Hesap kimlik bilgilerinizle oturum açın ve **devam**' a tıklayın.

Her şey doğru şekilde ayarlandıysa, belirteç yeni talebi `city`değeri `Redmond`ile birlikte içerir.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Sonraki adımlar

Etkileşimi bir doğrulama profili olarak da tasarlayabilirsiniz. Daha fazla bilgi için bkz [. İzlenecek yol: Kullanıcı girişinde](active-directory-b2c-rest-api-validation-custom.md)Azure AD B2C kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirme yapın.

[Kullanıcılarınızın daha fazla bilgi toplamak için profil düzenlemeyi değiştirin](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Başvurunun Yeniden teknik profil](restful-technical-profile.md)

API 'lerinizi güvenli hale getirme hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Temel kimlik doğrulaması (Kullanıcı adı ve parola) ile yeniden takip eden API 'nizin güvenliğini sağlama](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [İstemci sertifikalarıyla yeniden takip eden API 'nizin güvenliğini sağlama](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

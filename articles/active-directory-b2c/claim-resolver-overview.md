---
title: Azure Active Directory B2C özel ilkelerde talep çözücüler hakkında | Microsoft Docs
description: Talep çözümleyicilerine Azure Active Directory B2C bir özel ilkede nasıl kullanıldığı hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f08c85cee2378f4a879daf197af7a2adf0c20f45
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064407"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C özel ilkelerde talep çözücüler hakkında

Azure Active Directory B2C (Azure AD B2C) [özel ilkelerindeki](active-directory-b2c-overview-custom.md) talep çözücüler, ilke adı, Istek bağıntı kimliği, Kullanıcı arabirimi dili ve daha fazlası gibi bir yetkilendirme isteğiyle ilgili bağlam bilgilerini sağlar.

Bir giriş veya çıkış talebinde bir talep çözümleyici kullanmak için, [Claimsschema](claimsschema.md) öğesi altında bir String **ClaimType**tanımlar ve ardından **DefaultValue** değerini giriş veya çıkış talebi öğesinde talep çözümleyici olarak ayarlarsınız. Azure AD B2C, talep Çözümleyicisinin değerini okur ve teknik profildeki değeri kullanır.

Aşağıdaki örnekte, adlı `correlationId` bir talep türü, bir **veri türü** `string`ile tanımlanmıştır.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Teknik profilde talep çözümleyicisini talep türü ile eşleyin. Azure AD B2C talep Çözümleyicisinin `{Context:CorrelationId}` değerini talebe `correlationId` göre doldurur ve talebi teknik profile gönderir.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Talep çözümleyici türleri

Aşağıdaki bölümlerde, kullanılabilir talep çözücüler listelenmektedir.

### <a name="culture"></a>Kültür

| Talep | Açıklama | Örnek |
| ----- | ----------- | --------|
| {Culture: LanguageName} | Dil için iki harfli ISO kodu. | tr-TR |
| {Culture: LCıD}   | Dil kodunun LCıD 'SI. | 1033 |
| {Culture: RegionName} | Bölgenin iki harfli ISO kodu. | ABD |
| {Culture: RFC5646} | RFC5646 dil kodu. | en-US |

### <a name="policy"></a>İlke

| Talep | Açıklama | Örnek |
| ----- | ----------- | --------|
| {Policy: PolicyId} | Bağlı olan taraf ilkesi adı. | B2C_1A_signup_signin |
| {Policy: RelyingPartyTenantId} | Bağlı olan taraf ilkesinin kiracı KIMLIĞI. | your-tenant.onmicrosoft.com |
| {Policy: Tenantobjectıd} | Bağlı olan taraf ilkesinin kiracı nesne KIMLIĞI. | 00000000-0000-0000-0000-000000000000 |
| {Policy: TrustFrameworkTenantId} | Güven çerçevesinin kiracı KIMLIĞI. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Talep | Açıklama | Örnek |
| ----- | ----------- | --------|
| {OıDC: AuthenticationContextReferences} |`acr_values` Sorgu dizesi parametresi. | Yok |
| {OıDC: ClientID} |`client_id` Sorgu dizesi parametresi. | 00000000-0000-0000-0000-000000000000 |
| {OıDC: Domainipucuyla} |`domain_hint` Sorgu dizesi parametresi. | facebook.com |
| {OıDC: Loginipucu} |  `login_hint` Sorgu dizesi parametresi. | someone@contoso.com |
| {OıDC: MaxAge} | `max_age`. | Yok |
| {OıDC: nonce} |`Nonce` Sorgu dizesi parametresi. | defaultNonce |
| {OıDC: Prompt} | `prompt` Sorgu dizesi parametresi. | oturum açma |
| {OıDC: kaynak} |`resource` Sorgu dizesi parametresi. | Yok |
| {OıDC: scope} |`scope` Sorgu dizesi parametresi. | OpenID |

### <a name="context"></a>Bağlam

| Talep | Açıklama | Örnek |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Kimlik deneyimi çerçevesi sürümü (derleme numarası).  | 1.0.507.0 |
| {Context: CorrelationId} | Bağıntı Kimliği  | 00000000-0000-0000-0000-000000000000 |
| {Context: Datetimeınutc} |UTC olarak tarih saat.  | 10/10/2018 12:00:00 PM |
| {Context: DeploymentMode} |İlke dağıtım modu.  | Üretim |
| {Context: IPAddress} | Kullanıcı IP adresi. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Protokol olmayan parametreler

OıDC veya OAuth2 isteğinin bir parçası olarak dahil edilen herhangi bir parametre adı, Kullanıcı yolculuğunda bir talebe eşleştirilebilir. Örneğin, uygulamadaki istek, veya bir özel sorgu dizesi adına `app_session` `loyalty_number`sahip bir sorgu dizesi parametresi içerebilir.

| Talep | Açıklama | Örnek |
| ----- | ----------------------- | --------|
| {OAUTH-KV: kampanya Nkimliği} | Sorgu dizesi parametresi. | Hawaii |
| {OAUTH-KV: app_session} | Sorgu dizesi parametresi. | A3C5R |
| {OAUTH-KV: loyalty_number} | Sorgu dizesi parametresi. | 1234 |
| {OAUTH-KV: herhangi bir özel sorgu dizesi} | Sorgu dizesi parametresi. | Yok |

### <a name="oauth2"></a>OAuth2

| Talep | Açıklama | Örnek |
| ----- | ----------------------- | --------|
| {OAuth2: access_token} | Erişim belirteci. | Yok |

## <a name="how-to-use-claim-resolvers"></a>Talep çözücüler kullanma

### <a name="restful-technical-profile"></a>Yeniden teknik profil

[Bir teknik](restful-technical-profile.md) profilde, Kullanıcı dilini, ilke adını, kapsamı ve istemci kimliğini göndermek isteyebilirsiniz. Bu talepler temelinde REST API özel iş mantığını çalıştırabilir ve gerekirse yerelleştirilmiş bir hata iletisi oluşturabilir.

Aşağıdaki örnek, bir Restsize teknik profil göstermektedir:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Doğrudan oturum açma

Talep çözümleyicilerine göre, oturum açma adını veya Facebook, LinkedIn veya Microsoft hesabı gibi belirli bir sosyal kimlik sağlayıcısına doğrudan oturum açmayı önceden girebilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory B2C kullanarak doğrudan oturum açma ayarlama](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dinamik UI özelleştirmesi

Azure AD B2C, sayfa içeriğini dinamik olarak işleyebilmeniz için sorgu dizesi parametrelerini HTML içerik tanım uç noktalarınıza geçirmenize olanak sağlar. Örneğin, Azure AD B2C kaydolma veya oturum açma sayfasındaki arka plan görüntüsünü, Web veya mobil uygulamanızdan geçirdiğiniz özel bir parametreye göre değiştirebilirsiniz. Daha fazla bilgi için bkz. [Azure Active Directory B2C içindeki özel ilkeleri kullanarak Kullanıcı arabirimini dinamik olarak yapılandırma](active-directory-b2c-ui-customization-custom-dynamic.md). Ayrıca, HTML sayfanızı bir dil parametresine göre yerelleştirebilirsiniz veya içeriği istemci KIMLIĞINE göre değiştirebilirsiniz.

Aşağıdaki örnek, bir `hawaii`değeri, bir **dil** kodu `en-US`ve istemci kimliğini temsil eden **uygulama** olan **Kampanya NID** adlı bir parametre sorgu dizesinde geçirilir:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Sonuç olarak Azure AD B2C yukarıdaki parametreleri HTML içerik sayfasına gönderir:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Application Insights teknik profili

Azure Application Insights ve talep Çözümleyenler sayesinde Kullanıcı davranışında Öngörüler elde edebilirsiniz. Application Insights teknik profilinde, Azure Application Insights kalıcı olan giriş taleplerini gönderirsiniz. Daha fazla bilgi için bkz. [Application Insights kullanarak Azure AD B2C yolculuğa yönelik kullanıcı davranışını izleme](active-directory-b2c-custom-guide-eventlogger-appins.md). Aşağıdaki örnek, ilke KIMLIĞI, bağıntı KIMLIĞI, dil ve istemci KIMLIĞINI Azure Application Insights gönderir.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

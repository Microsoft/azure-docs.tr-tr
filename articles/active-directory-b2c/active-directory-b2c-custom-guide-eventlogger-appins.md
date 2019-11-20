---
title: Azure Active Directory B2C Application Insights içindeki olayları kullanarak Kullanıcı davranışını izleme | Microsoft Docs
description: Özel ilkeler (Önizleme) kullanarak Azure AD B2C Kullanıcı ilerlemesinden Application Insights olay günlüklerini nasıl etkinleştirebileceğinizi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c02757fb4b48ebf1220a5826bc9699741faa5170
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066180"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Application Insights kullanarak Azure Active Directory B2C Kullanıcı davranışını izleme

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Application Insights ile birlikte Azure Active Directory B2C (Azure AD B2C) kullandığınızda, Kullanıcı yolculuğa yönelik ayrıntılı ve özelleştirilmiş olay günlükleri alabilirsiniz. Bu makalede şunları öğreneceksiniz:

* Kullanıcı davranışında Öngörüler elde edin.
* Geliştirme veya üretimde kendi ilkelerinizin sorunlarını giderin.
* Ölçüm performansı.
* Application Insights bildirimler oluşturun.

## <a name="how-it-works"></a>Nasıl çalışır?

Azure AD B2C ' deki kimlik deneyimi çerçevesi sağlayıcıyı `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`içerir. Azure AD B2C için sunulan izleme anahtarını kullanarak doğrudan Application Insights olay verileri gönderir.

Teknik bir profil, Azure AD B2C bir olay tanımlamak için bu sağlayıcıyı kullanır. Profil, olayın adını, kaydedilen talepleri ve izleme anahtarını belirtir. Bir olayı göndermek için, teknik profil bir olarak `orchestration step`veya bir `validation technical profile` özel Kullanıcı yolculuğu içinde olarak eklenir.

Application Insights, bir Kullanıcı oturumu kaydetmek için bir bağıntı KIMLIĞI kullanarak olayları birleştirebilirsiniz. Application Insights, olay ve oturumu Saniyeler içinde kullanılabilir hale getirir ve birçok görselleştirme, dışarı aktarma ve analitik araç sunar.

## <a name="prerequisites"></a>Önkoşullar

[Özel ilkelerle çalışmaya başlama](active-directory-b2c-get-started-custom.md)bölümündeki adımları uygulayın. Bu makalede özel ilke başlangıç paketini kullandığınız varsayılır. Ancak, başlangıç paketi gerekli değildir.

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

Azure AD B2C Application Insights kullanırken, tek yapmanız gereken bir kaynak oluşturur ve izleme anahtarını alır.

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip aboneliğinizi içeren dizini seçerek Azure aboneliğinizi içeren dizini kullandığınızdan emin olun. Bu kiracı Azure AD B2C kiracınız değil.
3. Azure portal, sol üst köşedeki **kaynak oluştur** ' u seçin ve **Application Insights**arayıp seçin.
4. **Oluştur**'a tıklayın.
5. Kaynak için bir **ad** girin.
6. **Uygulama türü**için **ASP.NET Web uygulaması**' nı seçin.
7. **Kaynak grubu**için mevcut bir grubu seçin veya yeni bir grup için bir ad girin.
8. **Oluştur**'a tıklayın.
4. Application Insights kaynağını oluşturduktan sonra açın, **temel**bileşenler ' i genişletin ve izleme anahtarını kopyalayın.

![Application Insights genel bakış ve Izleme anahtarı](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Yeni ClaimType tanımları Ekle

*TrustFrameworkExtensions. xml* dosyasını Starter paketinden açın ve aşağıdaki öğeleri [buildingblocks](buildingblocks.md) öğesine ekleyin:

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>Yeni teknik profiller ekleyin

Teknik profiller, Azure AD B2C kimlik deneyimi çerçevesinde işlevler olarak kabul edilebilir. Bu tabloda, bir oturumu açmak ve olayları göndermek için kullanılan teknik profiller tanımlanmaktadır.

| Teknik profil | Görev |
| ----------------- | -----|
| AzureInsights-Common | Tüm AzureInsights teknik profillerine dahil edilecek ortak bir parametre kümesi oluşturur. |
| AzureInsights-SignInRequest | Bir oturum açma isteği alındığında bir talep kümesiyle birlikte oturum açma olayı oluşturur. |
| AzureInsights-UserSignup | Kullanıcı kaydolma/oturum açma yolculuğunda oturum açma seçeneğini tetiklediğinde bir UserSignup olayı oluşturur. |
| AzureInsights-Signcomplete | Bağlı olan taraf uygulamasına bir belirteç gönderildiğinde bir kimlik doğrulamanın başarıyla tamamlandığını kaydeder. |

Profilleri, başlatıcı paketinden *TrustFrameworkExtensions. xml* dosyasına ekleyin. Bu öğeleri **Claimsproviders** öğesine ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> `AzureInsights-Common` Teknik profildeki izleme anahtarını Application Insights kaynağınızın sağladığı GUID ile değiştirin.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Teknik profilleri düzenleme adımları olarak ekleme

Bir `Azure-Insights-SignInRequest` oturum açma/kaydolma isteğinin alındığını izlemek için düzenleme adımı 2 olarak çağırın:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` Düzenleme adımından hemen *önce* , çağıran `Azure-Insights-UserSignup`yeni bir adım ekleyin. Kullanıcı kaydolma/oturum açma yolculuğunda oturum açma düğmesini seçtiğinde tetiklenir.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` Düzenleme adımından hemen sonra öğesini çağırın `Azure-Insights-SignInComplete`. Bu adımda başarıyla tamamlanan bir yolculuğun bulunduğu gösterilmektedir.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Yeni düzenleme adımlarını ekledikten sonra, 1 ile N arasında bir tamsayı atlamadan adımları ardışık olarak yeniden numaralandırın.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Dosyanızı karşıya yükleyin, ilkeyi çalıştırın ve olayları görüntüleyin

*TrustFrameworkExtensions. xml* dosyasını kaydedin ve karşıya yükleyin. Ardından, bağlı olan taraf ilkesini uygulamanızdan çağırın veya Azure portal **Şimdi Çalıştır** ' ı kullanın. Saniyeler içinde, olaylarınız Application Insights kullanılabilir.

1. Azure Active Directory kiracınızda **Application Insights** kaynağını açın.
2. **Kullanım** > **olaylarını**seçin.
3. **Son saate** **ve** **3 dakikaya** **kadar ayarlayın.**  Sonuçları görüntülemek için **Yenile** ' yi seçmeniz gerekebilir.

![Application Insights kullanımı-etkinlik öncesi](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Sonraki adımlar

Gereksinimlerinize uyacak şekilde Kullanıcı yolculuğuna talep türleri ve olaylar ekleyin. [Talep çözümleyicilerine](claim-resolver-overview.md) veya herhangi bir dize talep türüne, Application Insights olayına bir **giriş talep** öğesi ekleyerek veya AzureInsights-Common Technical profile ' i kullanarak talep ekleyebilirsiniz.

- **ClaimTypeReferenceId** , bir talep türüne başvurudur.
- **Partnerclaimtype** , Azure Insights 'ta görünen özelliğin adıdır. Öğesinin sözdizimini kullanın; `{property:NAME}`burada `NAME` , olaya bir özellik eklenir.
- **DefaultValue** herhangi bir dize değeri veya talep çözümleyici kullanır.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```


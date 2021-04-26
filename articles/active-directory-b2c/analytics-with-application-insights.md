---
title: Application Insights kullanarak Kullanıcı davranışını izleme
titleSuffix: Azure AD B2C
description: Application Insights Azure AD B2C Kullanıcı ilerinizden olay günlüklerini nasıl etkinleştirebileceğinizi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2cde44ddb49ede8002b8a25ab47ae92ccd602a9d
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226379"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Application Insights kullanarak Azure AD B2C Kullanıcı davranışını izleme

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) ' de, Azure AD B2C için sunulan izleme anahtarını kullanarak doğrudan olay verilerini [Application Insights](../azure-monitor/app/app-insights-overview.md) gönderebilirsiniz. Application Insights teknik bir profille, Kullanıcı yolculukları için ayrıntılı ve özelleştirilmiş olay günlükleri edinebilirsiniz:

- Kullanıcı davranışında Öngörüler elde edin.
- Geliştirme veya üretimde kendi ilkelerinizin sorunlarını giderin.
- Ölçüm performansı.
- Application Insights bildirimler oluşturun.

## <a name="overview"></a>Genel Bakış

Özel olay günlüklerini etkinleştirmek için bir Application Insights teknik profili ekleyin. Teknik profilde, Application Insights izleme anahtarını, olay adını ve kaydedilecek talepleri tanımlarsınız. Bir olay göndermek için, teknik profili [Kullanıcı yolculuğunda](userjourneys.md)düzenleme adımı olarak ekleyin.

Application Insights kullandığınızda aşağıdakileri göz önünde bulundurun:

- Application Insights yeni günlüklerin kullanılabilmesi için genellikle beş dakikadan kısa bir gecikme vardır.
- Azure AD B2C hangi taleplerin kayıt için seçim yapmanıza olanak sağlar. Kişisel verilerle talepler eklemeyin.
- Bir Kullanıcı oturumu kaydetmek için olayları birleştirme için bir bağıntı KIMLIĞI kullanabilirsiniz.
- Application Insights teknik profilini doğrudan bir [Kullanıcı yolculuğu](userjourneys.md) veya bir [alt yolculuğa](subjourneys.md)göre çağırın. Bir Application Insights teknik profilini [doğrulama teknik profili](validation-technical-profile.md)olarak kullanmayın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

Azure AD B2C Application Insights kullandığınızda, tek yapmanız gereken bir kaynak oluşturur ve izleme anahtarını alır. Bilgi için bkz. [Application Insights kaynağı oluşturma](../azure-monitor/app/create-new-resource.md).

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure aboneliğiniz olan dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure aboneliğinizi içeren dizini seçin. Bu kiracı Azure AD B2C kiracınız değil.
1. Azure portal, sol üst köşedeki **kaynak oluştur** ' u seçin ve **Application Insights** arayıp seçin.
1. **Oluştur**’u seçin.
1. **Ad** için kaynak için bir ad girin.
1. **Uygulama türü** için **ASP.NET Web uygulaması**' nı seçin.
1. **Kaynak grubu** için mevcut bir grubu seçin veya yeni bir grup için bir ad girin.
1. **Oluştur**’u seçin.
1. Yeni Application Insights kaynağını açın, **temel** bileşenler ' i genişletin ve izleme anahtarını kopyalayın.

![Application Insights genel bakış sekmesindeki Izleme anahtarını gösteren ekran görüntüsü.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Talepleri tanımlama

Bir talep, Azure AD B2C ilkesi yürütmesi sırasında verilerin geçici olarak depolanmasını sağlar. Taleplerinizi [Claimsschema öğesinde](claimsschema.md)bildirirsiniz.

1. İlkenizin uzantıları dosyasını açın. Dosya şuna benzer görünebilir `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğeyi görmüyorsanız, ekleyin.
1. **Claimsschema** öğesini bulun. Öğeyi görmüyorsanız, ekleyin.
1. Aşağıdaki talepleri **Claimsschema** öğesine ekleyin:

   ```xml
   <ClaimType Id="EventType">
     <DisplayName>Event type</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="EventTimestamp">
     <DisplayName>Event timestamp</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="PolicyId">
     <DisplayName>Policy Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="Culture">
     <DisplayName>Culture ID</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="CorrelationId">
     <DisplayName>Correlation Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="federatedUser">
     <DisplayName>Federated user</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   <ClaimType Id="parsedDomain">
     <DisplayName>Domain name</DisplayName>
     <DataType>string</DataType>
     <UserHelpText>The domain portion of the email address.</UserHelpText>
   </ClaimType>
   <ClaimType Id="userInLocalDirectory">
     <DisplayName>userInLocalDirectory</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   ```

## <a name="add-new-technical-profiles"></a>Yeni teknik profiller ekleyin

Teknik profiller, özel ilkedeki işlevleri kabul edebilir. Bu işlevler teknik profil [içerme](technicalprofiles.md#include-technical-profile) yaklaşımını kullanır, burada teknik bir profil başka bir teknik profil içerir ve değişiklik ayarları veya yeni işlevsellik ekler. Aşağıdaki tabloda, bir oturumu açmak ve olayları göndermek için kullanılan teknik profiller tanımlanmaktadır.

| Teknik profil | Görev |
| ----------------- | -----|
| AppInsights-Common | Tipik yapılandırmayla ortak teknik profil. Application Insights izleme anahtarını, kaydedilecek talepler koleksiyonunu ve Geliştirici modunu içerir. Diğer teknik profiller ortak teknik profili içerir ve olay adı gibi daha fazla talep ekler. |
| AppInsights-SignInRequest | Bir oturum açma isteği alındığında bir talep kümesi ile **Signınrequest** olayını kaydeder. |
| AppInsights-UserSignUp | Kullanıcı kaydolma veya oturum açma yolculuğunda oturum açma seçeneğini tetiklediğinde bir **Usersignup** olayını kaydeder. |
| AppInsights-SignInComplete | Bağlı olan taraf uygulamasına bir belirteç gönderildiğinde, başarılı kimlik doğrulamasından sonra **Signcomplete** olayını kaydeder. |

*TrustFrameworkExtensions.xml* dosyasını başlangıç paketinden açın. Teknik profilleri **ClaimsProvider** öğesine ekleyin:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> `AppInsights-Common`Teknik profildeki izleme anahtarını Application Insights kaynağınızın SAĞLADıĞı GUID ile değiştirin.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Teknik profilleri düzenleme adımları olarak ekleme

Teknik profillere başvuran yeni düzenleme adımları ekleyin.

> [!IMPORTANT]
> Yeni düzenleme adımlarını ekledikten sonra, 1 ile N arasında bir tamsayı atlamadan adımları ardışık olarak yeniden numaralandırın.

1. `AppInsights-SignInRequest`İkinci düzenleme adımı olarak çağırın. Bu adım bir kaydolma veya oturum açma isteğinin alındığını izler.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. `SendClaims`Düzenleme adımından önce, çağıran yeni bir adım ekleyin `AppInsights-UserSignup` . Kullanıcı kaydolma veya oturum açma yolculuğunda oturum açma düğmesini seçtiğinde tetiklenir.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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
       <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. `SendClaims`Düzenleme adımından sonra çağrısı yapın `AppInsights-SignInComplete` . Bu adımda başarıyla tamamlanan bir yolculuğun bulunduğu gösterilmektedir.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Dosyanızı karşıya yükleyin, ilkeyi çalıştırın ve olayları görüntüleyin

*TrustFrameworkExtensions.xml* dosyasını kaydedin ve karşıya yükleyin. Ardından, bağlı olan taraf ilkesini uygulamanızdan çağırın veya Azure portal **Şimdi Çalıştır** ' ı kullanın. Application Insights ' de olaylarınızın kullanılabilir olmasını bekleyin.

1. Azure Active Directory kiracınızda **Application Insights** kaynağını açın.
1. **Kullanım** öğesini seçin ve ardından **Olaylar**' ı seçin.
1. **Son saate** **ve** **3 dakikaya** **kadar ayarlayın.** Sonuçları görmek için pencereyi yenilemeniz gerekebilir.

![Application Insights olay istatistiklerini gösteren ekran görüntüsü.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Daha fazla veri toplayın

İşletmenizin ihtiyaçlarını karşılamak için daha fazla talep kaydetmek isteyebilirsiniz. Talep eklemek için, önce [bir talep tanımlayın](#define-claims), sonra talep giriş talep koleksiyonuna ekleyin. **Appınsights-ortak** teknik profiline eklediğiniz talepler tüm olaylarda görüntülenir. Belirli bir teknik profile eklediğiniz talepler yalnızca o olayda görüntülenir. Giriş talep öğesi aşağıdaki öznitelikleri içerir:

- **ClaimTypeReferenceId** , bir talep türüne başvurudur.
- **Partnerclaimtype** , Azure Insights 'ta görünen özelliğin adıdır. ' Nin sözdizimini kullanın; `{property:NAME}` burada, `NAME` olaya eklenen bir özelliktir.
- **DefaultValue** , bir olay adı gibi, kaydedilecek önceden tanımlanmış bir değerdir. Kullanıcı yolculuğunda kullanılan bir talep boşsa, varsayılan değer kullanılır. Örneğin, `identityProvider` talep Facebook gibi Federasyon teknik profilleri tarafından ayarlanır. Talep boşsa, kullanıcının yerel hesapla oturum açmadığını gösterir. Bu nedenle, varsayılan değer **Yerel** olarak ayarlanır. Ayrıca, uygulama KIMLIĞI veya Kullanıcı IP adresi gibi bağlamsal bir değere sahip bir [talep çözümleyici](claim-resolver-overview.md) de kaydedebilirsiniz.

### <a name="manipulate-claims"></a>Talepleri işleme

Giriş taleplerini değiştirmek veya Application Insights göndermeden önce yenilerini oluşturmak için [giriş talep dönüşümlerini](custom-policy-overview.md#manipulating-your-claims) kullanabilirsiniz. Aşağıdaki örnekte, teknik profil, `CheckIsAdmin` giriş talep dönüşümünü içerir.

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Olay Ekle

Bir olay eklemek için teknik profili içeren yeni bir teknik profil oluşturun `AppInsights-Common` . Ardından yeni teknik profili, [Kullanıcı yolculuğuna](custom-policy-overview.md#orchestration-steps)düzenleme adımı olarak ekleyin. Hazırlanıyor olayını tetiklemek için [Önkoşul](userjourneys.md#preconditions) öğesini kullanın. Örneğin, yalnızca kullanıcılar çok faktörlü kimlik doğrulaması aracılığıyla çalıştırıldığında olayı rapor edin.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Kullanıcı yolculuğuna bir olay eklediğinizde, düzenleme adımlarını sırayla yeniden numaralandırabilirsiniz unutmayın.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Geliştirici modunu etkinleştir

Olayları tanımlamak için Application Insights kullandığınızda, geliştirici modunun etkin olup olmadığını belirtebilirsiniz. Geliştirici modu olayların nasıl arabelleğe alınacağını denetler. En az olay hacmi olan bir geliştirme ortamında, geliştirici modunun etkinleştirilmesi, Application Insights ' a anında gönderilen olaylarda sonuçlanır. `false` varsayılan değerdir. Üretim ortamlarında Geliştirici modunu etkinleştirmeyin.

Geliştirici modunu etkinleştirmek için `DeveloperMode` meta verileri `true` `AppInsights-Common` Teknik profilde değiştirin:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Telemetrisi devre dışı bırak

Application Insights günlüklerini devre dışı bırakmak için `DisableTelemetry` meta verileri `true` `AppInsights-Common` Teknik profilde değiştirin:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Application Insights kullanarak özel KPI panoları oluşturmayı](../azure-monitor/app/tutorial-app-dashboards.md)öğrenin.

::: zone-end
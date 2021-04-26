---
title: Azure AD B2C bir parola sıfırlama akışı ayarlayın
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir parola sıfırlama akışını ayarlamayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f451d08dfbde643d91705f54296e9757a51c9d88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798402"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C bir parola sıfırlama akışı ayarlayın

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Parola sıfırlama akışı

[Kaydolma ve oturum açma yolculuğu](add-sign-up-and-sign-in-policy.md) , kullanıcıların **parolanızı unuttum mı?** bağlantısını kullanarak kendi parolalarını sıfırlamasına olanak sağlar. Parola sıfırlama akışı aşağıdaki adımları içerir:

1. Kaydolma ve oturum açma sayfasından Kullanıcı **parolanızı unuttum?** bağlantısına tıklar. Azure AD B2C parola sıfırlama akışını başlatır. 
2. Kullanıcı, zaman aşımı süresi bir saat geçiş kodu ile e-posta adreslerini sağlar ve doğrular.
3. Kullanıcı daha sonra yeni bir parola girebilir.

![Parola sıfırlama akışı](./media/add-password-reset-policy/password-reset-flow.png)

Parola sıfırlama akışı, oturum açma için parola ile bir [e-posta adresi](identity-provider-local.md#email-sign-in) veya [kullanıcı adı](identity-provider-local.md#username-sign-in) kullanan Azure AD B2C yerel hesaplar için geçerlidir.

> [!TIP]
> Self servis parola sıfırlama akışı, Kullanıcı parolasını unuttuğunda ve sıfırlamak istediğinde kullanıcıların parolalarını değiştirmesine izin verir. Bir [parola değişiklik akışını](add-password-change-policy.md) , bir kullanıcının parolasını bildiği ve değiştirmek istediği durumları destekleyecek şekilde yapılandırmayı düşünün.

Kullanıcıları rastgele parolalarla Azure AD B2C geçirmeden önce, kullanıcıların kendi e-posta adreslerini doğrulaması ve ilk oturum açma işlemi sırasında parolalarını sıfırlamalarından daha yaygın bir uygulamadır. Ayrıca, bir yönetici parolasını değiştirdikten sonra kullanıcıyı parolasını sıfırlamasına zorlamak için de yaygındır. Bu özelliği etkinleştirmek için [parola sıfırlamayı zorla](force-password-reset.md) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Self servis parola sıfırlama (önerilir)

Yeni parola sıfırlama deneyimi artık kaydolma veya oturum açma ilkesinin bir parçasıdır. Kullanıcı **parolanızı unuttum?** bağlantısını seçtiğinde, bu kişiler hemen Unutulan parola deneyimine gönderilir. Uygulamanız artık [AADB2C90118 hata kodunu](#password-reset-policy-legacy)işlemek için gerekli değildir ve parola sıfırlama için ayrı bir ilkeye gerek kalmaz.

::: zone pivot="b2c-user-flow"

Self servis parola sıfırlama deneyimi, **oturum açma (önerilen)** veya **kaydolma ve oturum açma (önerilen)** Kullanıcı akışları için yapılandırılabilir. Bu tür bir Kullanıcı akışınız yoksa, [oturum açın ve](add-sign-up-and-sign-in-policy.md) Kullanıcı akışı oluşturun. 

Kaydolma veya oturum açma Kullanıcı akışı için self servis parola sıfırlamayı etkinleştirmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. Özelleştirmek istediğiniz kaydolma veya oturum açma kullanıcı akışını ( **Önerilen** türde) seçin.
1. Sol menüdeki **Ayarlar** ' ın altında **Özellikler**' i seçin.
1. **Parola karmaşıklığı** bölümünde **self servis parola sıfırlama**' yı seçin.
1. **Kaydet**’i seçin.
1. Sol menüdeki **Özelleştir** altında **sayfa düzenleri**' ni seçin.
1. **Sayfa düzeni sürümünde**, **2.1.2 'yi-Current** veya yukarıdaki ' ı seçin.
1. **Kaydet**’i seçin.

::: zone-end

::: zone pivot="b2c-custom-policy"

Aşağıdaki bölümlerde, bir özel ilkeye self servis parola deneyiminin nasıl ekleneceği açıklanır. Örnek, [özel ilke başlangıç paketine](./custom-policy-get-started.md)dahil edilen ilke dosyalarını temel alır. 

> [!TIP]
> [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset)'da "parola sıfırlama ile kaydolma veya oturum açma" ilkesinin tam bir örneğini bulabilirsiniz.

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Bir kullanıcının seçili olduğunu belirtin parolanızı mı unuttunuz? bağlantısının

Kullanıcının, **parolayı unuttunuz mu?** bağlantısını seçtiği ilkeyi belirtmek için bir Boole talebi tanımlayın. Bu talep, Kullanıcı yolculuğu parolasını unuttum teknik profiline yönlendirmek için kullanılacaktır. Bu talep belirtece da verilebilir, böylece uygulamanın kullanıcının parolayı unuttum akışı aracılığıyla oturum açdığının farkında olur.

Taleplerinizi [talep şemasında](claimsschema.md)bildirirsiniz. İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğe yoksa, ekleyin.
1. [Claimsschema](claimsschema.md) öğesini bulun. Öğe yoksa, ekleyin.
1. Aşağıdaki talebi **Claimsschema** öğesine ekleyin. 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Sayfa düzeni sürümünü yükseltme

[Sayfa düzeni sürümü](contentdefinitions.md#migrating-to-page-layout) `2.1.2` kaydolma veya oturum açma yolculuğunun içinde self servis parola sıfırlama akışını etkinleştirmek için gereklidir.

1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğe yoksa, ekleyin.
1. [ContentDefinitions](contentdefinitions.md) öğesini bulun. Öğe yoksa, ekleyin.
1. **ContentDefinition** öğesi Içindeki **datauri** öğesini aşağıda gösterildiği gibi kimlik **API. signuporsignın** ile değiştirin.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

Talebi başlatmak için `isForgotPassword` bir talep dönüştürme teknik profili kullanılır. Bu teknik profile daha sonra başvurulacaktır. Çağrıldığında, `isForgotPassword` talebin değerini olarak ayarlar `true` . Öğesini bulun `ClaimsProviders` . Öğe yoksa, ekleyin. Ardından aşağıdaki talep sağlayıcısını ekleyin:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

`SelfAsserted-LocalAccountSignin-Email`Teknik profil, `setting.forgotPasswordLinkOverride` parola sıfırlama talep değişimini Kullanıcı yolculuğunda yürütülecek şekilde anlatıyor. 

### <a name="add-the-password-reset-sub-journey"></a>Parola sıfırlama alt yolculuğunu ekleyin

Artık yolculukta Kullanıcı oturum açma, kaydolma ve parola sıfırlama gerçekleştirme özelliği de dahil edilir. Kullanıcı yolculuğunu daha iyi düzenlemek için, parola sıfırlama akışını işlemek üzere bir [alt yolculuğun](subjourneys.md) kullanılması gerekir.

Alt yolculuğa Kullanıcı yolculuğunda çağrılacaktır ve parola sıfırlama deneyimini kullanıcıya teslim etmek için belirli adımları gerçekleştirecek. Alt yolculuğun tamamlanmasını sağlamak için, alt yolculuğa `Call` yazın. bu sayede, denetim alt yolculuğa başlatılan düzenleme adımına döndürülür.

Öğesini bulun `SubJourneys` . Öğe yoksa, öğesinden sonra ekleyin `User Journeys` . Ardından aşağıdaki alt yolculuğa ekleyin:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Kullanıcı yolculuğuna hazırlama

**Parolanızı unuttum** veya Unutulan parola alt yolculuğuna bağlamanız gerekir. Bunu yapmak için **CombinedSignInAndSignUp** adımının **Claimsproviderselection** öğesi içindeki unuttum Password alt yolculuğu kimliğine başvurun.

Bir **CombinedSignInAndSignUp** adımla kendi özel Kullanıcı yolculuğu yoksa, mevcut bir kaydolma veya oturum açma Kullanıcı yolculuğunu yinelemek için aşağıdaki yordamı kullanın. Aksi takdirde, sonraki bölüme geçin.

1. *TrustFrameworkBase.xml* dosyasını başlangıç paketinden açın.
2. Dahil olan **Userelde ney** öğesinin tüm içeriğini bulup kopyalayın `Id="SignUpOrSignIn"` .
3. *TrustFrameworkExtensions.xml* açın ve **User, neys** öğesini bulun. Öğe yoksa, bir tane ekleyin.
4. 2. adımda kopyaladığınız **Useryolculuney** öğesinin tüm Içeriğini yapıştırarak **User, neys** öğesinin bir alt öğesi oluşturun.
5. Kullanıcı yolculuğunun kimliğini yeniden adlandırın. Örneğin, `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Parolayı unuttum bağlantısını unuttum alt yolculuğuna bağlayın 

Kullanıcı yolculuğunda, Unutulan parola alt **yolculuğunu bir Claimsproviderselection** olarak temsil edebilirsiniz. Bu öğe eklendiğinde **parolanızı unuttum mı?** parolayı unuttum alt yolculuğa bağlayın.

1. Kullanıcı yolculuğunda, veya içeren Orchestration Step öğesini bulun `Type="CombinedSignInAndSignUp"` `Type="ClaimsProviderSelection"` . Genellikle ilk düzenleme adımıdır. **Claimsproviderseçimlere** öğesi, bir kullanıcının oturum açmak için kullanabileceği kimlik sağlayıcılarının bir listesini içerir. Aşağıdaki satırı ekleyin:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. Sonraki düzenleme adımında bir **Claimsexchange** öğesi ekleyin. Aşağıdaki satırı ekleyin:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Aşağıdaki düzenleme adımını geçerli adım ve bir sonraki adım arasında ekleyin. Eklediğiniz yeni düzenleme adımı, talebin mevcut olup olmadığını denetler `isForgotPassword` . Talep varsa, [parola sıfırlama alt yolculuğunu](#add-the-password-reset-sub-journey)çağırır. 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Yeni düzenleme adımını ekledikten sonra, 1 ile N arasında bir tamsayı atlamadan adımları ardışık olarak yeniden numaralandırın.

### <a name="set-the-user-journey-to-be-executed"></a>Yürütülecek Kullanıcı yolculuğu ayarla

Artık bir Kullanıcı yolculuğunu değiştirdiğiniz veya oluşturduğunuza göre, **bağlı olan taraf** bölümünde Azure AD B2C bu özel ilke için yürütecektir. [RelyingParty](relyingparty.md) öğesi Içinde, **Defaultuseryolculuney** öğesini bulun. **Defaultuseryolculuney Referenceıd** değerini, **Claimsproviderseçimlerinizi** EKLEDIĞINIZ Kullanıcı yolculuğu kimliğiyle eşleşecek şekilde güncelleştirin.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Uygulamanıza Unutulan parola akışını belirtin

Uygulamanızın, kullanıcının parolayı unuttum Kullanıcı akışından oturum açmış olup olmadığını algılaması gerekebilir. **İsForgotPassword** talebi, uygulamanıza gönderilen belirtece verilebilen bunu belirten bir Boole değeri içerir. Gerekirse, `isForgotPassword` **bağlı olan taraf** bölümündeki çıkış taleplerine ekleyin. Uygulamanız, `isForgotPassword` kullanıcının parolasını sıfırlayıp sıfırlamayacağını belirleme talebini denetleyebilir.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Özel ilkeyi karşıya yükle

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından aşağıdaki sırada değiştirdiğiniz iki ilke dosyasını karşıya yükleyin:
   1. Örneğin uzantı ilkesi `TrustFrameworkExtensions.xml` .
   2. Örneğin, bağlı olan taraf ilkesi `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>Parola sıfırlama akışını test etme

1. Test etmek istediğiniz kaydolma veya oturum açma kullanıcı akışını (önerilen türde) seçin.
1. **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' ı seçin.
1. Kaydolma veya oturum açma sayfasından **parolanızı unuttum?** seçeneğini belirleyin.
1. Daha önce oluşturduğunuz hesabın e-posta adresini doğrulayın ve sonra **devam**' ı seçin.
1. Artık Kullanıcı parolasını değiştirme fırsatına sahipsiniz. Parolayı değiştirin ve **devam**' ı seçin. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.
1. Dönüş belirtecinin `isForgotPassword` talep değerini denetleyin. Varsa ve true olarak ayarlanırsa, kullanıcının parolayı sıfırladığını gösterir.

## <a name="password-reset-policy-legacy"></a>Parola sıfırlama ilkesi (eski)

[Self servis parola sıfırlama](#self-service-password-reset-recommended) deneyimi etkinleştirilmemişse, bu bağlantıya tıkladığınızda parola sıfırlama Kullanıcı akışı otomatik olarak tetiklemez. Bunun yerine, hata kodu `AADB2C90118` uygulamanıza döndürülür. Uygulamanızın, Azure AD B2C parola sıfırlama Kullanıcı akışının kimliğini doğrulamak üzere kimlik doğrulama kitaplığını yeniden başlatarak bu hata kodunu işlemesi gerekir.

Aşağıdaki diyagramda:

1. Uygulamadan Kullanıcı oturum açma seçeneğine tıklamalar. Uygulama bir yetkilendirme isteği başlatır ve kullanıcıdan oturum açmayı tamamlaması için Azure AD B2C alır. Yetkilendirme isteği, **B2C_1_signup_signin** gibi kaydolma veya oturum açma ilkesi adını belirtir.
1. Kullanıcı **parolanızı unuttum?** bağlantısını seçer. Azure AD B2C, uygulamaya AADB2C90118 hata kodunu döndürür.
1. Uygulama, hata kodunu işler ve yeni bir yetkilendirme isteği başlatır. Yetkilendirme isteği, **B2C_1_pwd_reset** gibi parola sıfırlama ilkesi adını belirtir.

![Eski parola sıfırlama Kullanıcı akışı](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Bir örneği görmek için, Kullanıcı akışlarının bağlantısını gösteren [basit bir ASP.net örneğine](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)göz atın.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Parola sıfırlama Kullanıcı akışı oluşturma

Uygulamanızın kullanıcılarının parolasını sıfırlamasına izin vermek için bir parola sıfırlama Kullanıcı akışı oluşturursunuz.

1. Azure AD B2C kiracı genel bakış menüsünde **Kullanıcı akışları**' nı seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.
1. **Kullanıcı akışı oluştur** sayfasında, **parola sıfırlama** Kullanıcı akışını seçin. 
1. **Sürüm seçin** altında **Önerilen**' i seçin ve ardından **Oluştur**' u seçin.
1. Kullanıcı akışı için bir **ad** girin. Örneğin, *passwordreset1*.
1. **Kimlik sağlayıcıları** için **e-posta adresini kullanarak parolayı sıfırlamayı** etkinleştirin.
1. **Uygulama talepleri** bölümünde **daha fazla göster** ' i seçin ve uygulamanıza geri gönderilen yetkilendirme belirteçlerinde döndürülmesini istediğiniz talepleri seçin. Örneğin, **Kullanıcının Nesne Kimliği**’ni seçin.
1. **Tamam**’ı seçin.
1. Kullanıcı akışını eklemek için **Oluştur** ' u seçin. *B2C_1* bir ön eki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' a tıklayın, daha önce oluşturduğunuz hesabın e-posta adresini doğrulayın ve sonra **devam**' ı seçin.
1. Artık Kullanıcı parolasını değiştirebilirsiniz. Parolayı değiştirin ve **devam**' ı seçin. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Parola sıfırlama ilkesi oluşturma

Özel ilkeler, Kullanıcı, neys tanımlamak için Azure AD B2C kiracınıza yüklediğiniz XML dosyaları kümesidir. Kaydolma ve oturum açma, parola sıfırlama ve profil düzenlemesi ilkesi dahil olmak üzere önceden oluşturulmuş birkaç ilke ile başlangıç paketleri sunuyoruz. Daha fazla bilgi için, bkz. [Azure AD B2C özel ilkelerle çalışmaya başlama](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

[Parola sıfırlamayı zorla](force-password-reset.md)ayarlayın.

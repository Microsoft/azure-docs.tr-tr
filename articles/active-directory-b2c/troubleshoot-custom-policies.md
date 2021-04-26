---
title: Azure Active Directory B2C özel ilkelerle ilgili sorunları giderme
description: Azure Active Directory B2C özel ilkelerle çalışırken hataları çözmeye yönelik yaklaşımlar hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103974"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Özel ilkelerin Azure AD B2C sorunlarını giderme

Azure Active Directory B2C (Azure AD B2C) [özel ilkeleri](custom-policy-overview.md)kullanıyorsanız, Ilke dili XML biçimi veya çalışma zamanı sorunlarıyla ilgili güçlüklerle karşılaşabilirsiniz. Bu makalede, sorunları keşfetmenize ve çözmenize yardımcı olabilecek bazı araçlar ve ipuçları açıklanmaktadır. 

Bu makale Azure AD B2C özel ilke yapılandırmanızda sorun gidermeye odaklanır. Bağlı olan taraf uygulamasının veya kimlik kitaplığının kimliğini gidermez.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Azure AD B2C bağıntı KIMLIĞINE genel bakış

Azure AD B2C bağıntı KIMLIĞI, yetkilendirme isteklerine bağlı benzersiz bir tanımlayıcı değeridir. Bir kullanıcının aldığı tüm düzenleme adımlarını geçirir. Bağıntı KIMLIĞIYLE şunları yapabilirsiniz:

- Uygulamanızdaki oturum açma etkinliğini belirleyip ilkenizin performansını izleyin.
- Oturum açma isteğinin Azure Application Insights günlüklerini bulun.
- Bağıntı KIMLIĞINI REST API geçirin ve oturum açma akışını tanımlamak için kullanın. 

Bağıntı KIMLIĞI, her yeni oturum oluşturulduğunda değiştirilir. İlkelerinizi hata ayıkladığınızda, var olan tarayıcı sekmelerini kapatın emin olun. Veya yeni bir özel mod tarayıcısı açın.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Azure AD B2C bağıntı KIMLIĞINI alın

Bağıntı KIMLIĞINI Azure AD B2C kaydolma veya oturum açma sayfasında bulabilirsiniz. Tarayıcınızda **kaynağı görüntüle**' yi seçin. Bağıntı, sayfanın üst kısmında bir yorum olarak görünür.

![Azure AD B2C oturum açma sayfası görünüm kaynağının ekran görüntüsü.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Bağıntı KIMLIĞINI kopyalayın ve ardından oturum açma akışına devam edin. Oturum açma davranışını gözlemlemek için bağıntı KIMLIĞINI kullanın. Daha fazla bilgi için bkz. [Application Insights Ile sorun giderme](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Azure AD B2C bağıntı KIMLIĞINI yankılandırma

Azure AD B2C belirteçlerine bağıntı KIMLIĞI ekleyebilirsiniz. Bağıntı KIMLIĞINI eklemek için:

1. İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğe yoksa, ekleyin.
1. [Claimsschema](claimsschema.md) öğesini bulun. Öğe yoksa, ekleyin.
1. Bir şehir talebini **Claimsschema** öğesine ekleyin.  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. İlkenizin bağlı olan taraf dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> dosyası. Çıkış talebi, başarılı bir Kullanıcı yolculuğunda ve uygulamaya gönderildikten sonra belirtece eklenecektir. Şehri çıkış talebi olarak eklemek için bağlı olan taraf bölümündeki teknik profil öğesini değiştirin.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Application Insights sorunlarını giderme

Özel ilkeleriniz ile ilgili sorunları tanılamak için [Application Insights](troubleshoot-with-application-insights.md)kullanın. Application Insights, özel ilke Kullanıcı yolculuğunun etkinliğini izler. Bu, özel durumları tanılamanıza ve kimlik sağlayıcıları, API tabanlı hizmetler, Azure AD B2C Kullanıcı dizini ve diğer hizmetler gibi teknik profiller tarafından tanımlanan çeşitli talep sağlayıcıları Azure AD B2C arasındaki taleplerin değişimini gözlemlemek için bir yol sağlar.  

[Vs Code](https://code.visualstudio.com/)için [Azure AD B2C uzantısını](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) yüklemenizi öneririz. Azure AD B2C Uzantısı ile, Günlükler sizin için ilke adı, bağıntı KIMLIĞI (Application Insights bağıntı KIMLIĞININ ilk basamağını gösterir) ve günlük zaman damgası tarafından düzenlenir. Bu özellik, yerel zaman damgasına göre ilgili günlüğü bulmanıza ve Azure AD B2C tarafından yürütülen kullanıcının yolculuğunu görmenizi sağlar. 

> [!NOTE]
> - Application Insights yeni Günlükler görebilmeniz için genellikle beş dakikadan kısa bir gecikme olur.
> - Topluluk, kimlik geliştiricilerine yardımcı olmak için Azure AD B2C Visual Studio Code uzantısını geliştirmiştir. Uzantı Microsoft tarafından desteklenmez ve tamamen olduğu gibi kullanıma sunulur.

Çoklu oturum açma akışı, birden fazla Azure Application Insights izlemesi verebilir. Aşağıdaki ekran görüntüsünde, *B2C_1A_signup_signin* ilkesinin üç günlüğü vardır. Her günlük, oturum açma akışının bir parçasını temsil eder.

Aşağıdaki ekran görüntüsünde Azure Application Insights izleme Gezgini ile VS Code için Azure AD B2C uzantısı gösterilmektedir.

![Azure Application Insights Trace ile VS Code için Azure AD B2C uzantısının ekran görüntüsü.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>İzleme günlüğünü filtrele

Azure AD B2C izleme Gezgini ' ne odaklanarak, bağıntı KIMLIĞININ ilk basamağını veya bulmak istediğiniz bir süreyi yazmak için başlatın. Azure AD B2C izleme Gezgini ' nin sağ üst köşesinde bir filtre kutusunu görürsünüz ve şimdiye kadar yazdığınızı gösterir ve eşleşen izleme günlükleri vurgulanacaktır.  

![Azure AD B2C uzantısı Azure AD B2C izleme Gezgini filtre vurgulamanın ekran görüntüsü.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Filtre kutusunun üzerine gelindiğinde ve **tür üzerinde filtreyi etkinleştir** seçeneğinin belirlenmesi yalnızca eşleşen izleme günlüklerini gösterir. Filtreyi temizlemek için **' X ' Clear düğmesini** kullanın.

![Azure AD B2C uzantısı Azure AD B2C Trace Explorer filtresi ekran görüntüsü.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>İzleme günlüğü ayrıntılarını Application Insights

Azure Application Insights izlemesini seçtiğinizde, uzantı **Application Insights Ayrıntılar** penceresini aşağıdaki bilgilerle açar:

- İlke adı, bağıntı KIMLIĞI, Azure Application Insights izleme KIMLIĞI ve izleme zaman damgası dahil olmak üzere izleme günlüğü hakkında genel bilgiler **Application Insights** .
- **Teknik profiller** -izleme günlüğünde görüntülenen teknik profillerin listesi.
- İzleme günlüğünde ve değerlerinde **görünen taleplerin alfabetik** listesi. Bir talep izleme günlüğünde farklı değerlerle birden çok kez görünürse, bir `=>` işaret en yeni değeri belirler. Beklenen talep değerlerinin doğru şekilde ayarlandığını öğrenmek için bu talepleri gözden geçirebilirsiniz. Örneğin, bir talep değerini denetleyen bir önkoşulu varsa, talep bölümü beklenen akışın neden farklı davrandığını belirlemenize yardımcı olabilir.
- **Talep dönüştürme** -izleme günlüğünde görüntülenen talep dönüştürmelerinin listesi. Her talep dönüştürmesi, giriş taleplerini, giriş parametrelerini ve çıkış taleplerini içerir. Talep dönüştürme bölümü, içinde gönderilen verilere ilişkin Öngörüler ve talep dönüştürmesinin sonucunu verir.
- **Belirteçler** -izleme günlüğünde görünen belirteçlerin listesi. Belirteçler, temel alınan Federasyon OAuth ve OpenID Connect kimlik sağlayıcısının belirteçlerini içerir. Federal Kimlik sağlayıcısının belirteci, kimlik sağlayıcısı teknik profil çıkış taleplerini eşleyebileceğiniz kimlik sağlayıcısı 'nın talepleri Azure AD B2C nasıl döndürdüğünü gösteren ayrıntıları verir. 
- **Özel durumlar** -izleme günlüğünde görüntülenen özel durumların veya önemli hataların listesi.
- **APPLICATION INSIGHTS JSON** -Application Insights döndürdüğü ham veri.

Aşağıdaki ekran görüntüsünde Application Insights izleme günlüğü ayrıntıları penceresi örneği gösterilmektedir.  

![Azure AD B2C uzantısı Azure AD B2C İzleme raporunun ekran görüntüsü.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>JWT belirteçlerinin sorunlarını giderme

JWT belirteci doğrulama ve hata ayıklama amacıyla, gibi bir site kullanarak JWTs 'nin kodunu çözebilir [https://jwt.ms](https://jwt.ms) . Belirteç incelemesi için yeniden yönlendirebileceğinizi bir test uygulaması oluşturun `https://jwt.ms` . Henüz yapmadıysanız, [bir Web uygulamasını kaydedin](tutorial-register-applications.md)ve [kimlik belirteci örtük izni](tutorial-register-applications.md#enable-id-token-implicit-grant)' nı etkinleştirin. 

![JWT belirteci önizlemenin ekran görüntüsü.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

**Şimdi Çalıştır** 'ı kullanın ve `https://jwt.ms` ilkelerinizi Web veya mobil uygulamanızdan bağımsız olarak test edin. Bu web sitesi, bağlı olan taraf uygulaması gibi davranır. Azure AD B2C ilkeniz tarafından oluşturulan JSON Web belirtecinin (JWT) içeriğini görüntüler.

## <a name="troubleshoot-saml-protocol"></a>SAML protokolünde sorun giderme

Hizmet sağlayıcınızda tümleştirmeyi yapılandırmaya ve hata ayıklamanıza yardımcı olmak için, SAML protokolü için bir tarayıcı uzantısı kullanabilirsiniz; Örneğin, Chrome için [SAML DevTools uzantısı](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) , Firefox için [SAML izleyici](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) veya [Edge ya da IE geliştirici araçları](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Aşağıdaki ekran görüntüsünde, SAML DevTools uzantısının, kimlik sağlayıcısına ve SAML yanıtına Azure AD B2C SAML isteğini nasıl gösterdiği gösterilmektedir.

![SAML protokol izleme günlüğünün ekran görüntüsü.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Bu araçları kullanarak, uygulamanız ve Azure AD B2C arasındaki tümleştirmeyi kontrol edebilirsiniz. Örnek:

- SAML isteğinin imza içerip içermediğini denetleyin ve yetkilendirme isteğinde oturum açmak için hangi algoritmanın kullanılacağını saptayın.
- Azure AD B2C bir hata iletisi döndürdüğünden emin olun.
- Onaylama bölümünün şifrelenip şifrelenmediğini denetleyin.
- Taleplerin adı, kimlik sağlayıcısını döndürür.

Ayrıca, istemci tarayıcınızla birlikte Azure AD B2C, [Fiddler](https://www.telerik.com/fiddler)ile ileti alışverişi de izleyebilirsiniz. Bu, Kullanıcı yolculuğunun düzenleme adımlarınızın başarısız olduğunu belirten bir bildirim almanıza yardımcı olabilir.

## <a name="troubleshoot-policy-validity"></a>İlke geçerliliği sorunlarını giderme

İlkenizi geliştirmeyi bitirdikten sonra ilkeyi Azure AD B2C karşıya yüklersiniz. ilkenize bazı sorunlar olabilir. İlke bütünlüğünden/doğruluğuna emin olmak için aşağıdaki yöntemleri kullanın.

Özel ilkeleri ayarlamadaki en yaygın hata hatalı biçimli XML 'dir. İyi bir XML Düzenleyicisi neredeyse önemlidir. XML yerel olarak, renk kodları içeriği, önceden dolgular Ortak terimleri görüntüler, XML öğelerinin dizinlenmesini önler ve bir XML şemasına göre doğrulayabilir.

[Visual Studio Code](https://code.visualstudio.com/)kullanmanızı öneririz. Ardından, [Red Hat Ile XML dil desteği](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml)gıbı bir xml uzantısı yüklersiniz. Xml uzantısı, XML dosyanızı karşıya yüklemeden önce özel ilke [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) şema tanımı kullanarak XML şemasını doğrulamanızı sağlar.

Aşağıdaki ayarları VS Code dosyanıza ekleyerek XML dosyasını XSD 'e bağlamak için XML dosya ilişkilendirme stratejisini kullanabilirsiniz `settings.json` . Bunun için:

1. VS Code, **Ayarlar**' a tıklayın. Daha fazla bilgi için bkz. [Kullanıcı ve çalışma alanı ayarları](https://code.visualstudio.com/docs/getstarted/settings).
1. **FileAssociation** için arama yapın, ardından **uzantı** altında **XML**' i seçin.
1. **Üzerinde settings.jsDüzenle '** yi seçin.

    ![VS Code XML şema doğrulamasının ekran görüntüsü.](./media/troubleshoot-custom-policies/xml-validation.png)
1. settings.js, aşağıdaki JSON kodunu ekleyin:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

Aşağıdaki örnek bir XML doğrulama hatası gösterir. Farenizi öğe adının üzerine getirdiğinizde, uzantı beklenen öğeleri listeler.

![XML şeması doğrulama hatası göstergesinin VS Code ekran görüntüsü.](./media/troubleshoot-custom-policies/xml-validation-error.png)

Aşağıdaki örnekte, `DisplayName` öğesi doğrudur. Ancak yanlış sırada. `DisplayName` `Protocol` Öğesi öğesinden önce olmalıdır. Sorunu gidermek için farenizi `DisplayName` öğenin üzerine doğru öğe sırasına taşıyın.

![VS Code XML şema doğrulama sırası hatası ekran görüntüsü.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>İlkeleri ve ilke doğrulamasını karşıya yükle

XML ilke dosyasının doğrulanması, karşıya yükleme sırasında otomatik olarak gerçekleştirilir. Çoğu hata yüklemenin başarısız olmasına neden olur. Doğrulama, karşıya yüklediğiniz ilke dosyasını içerir. Ayrıca, karşıya yükleme dosyasının başvurduğu dosya zincirini (bağlı olan taraf ilke dosyası, uzantılar dosyası ve temel dosya) içerir.

> [!TIP]
> Azure AD B2C, bağlı olan taraf ilkesi için ek doğrulama çalıştırır. İlkenize bir sorun olduğunda, yalnızca uzantı ilkesini düzenleyeseniz bile, bağlı olan taraf ilkesini de karşıya yüklemek iyi bir uygulamadır. 

Bu bölüm, yaygın doğrulama hatalarını ve olası çözümleri içerir.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Şema doğrulama hatası bulundu... ' {name} ' adlı geçersiz alt öğeye sahip

İlkeniz geçersiz bir XML öğesi içeriyor veya XML öğesi geçerli, ancak yanlış sırada görünüyor. Bu tür bir hatayı gidermek için [ilke geçerliliği sorunlarını giderme](#troubleshoot-policy-validity) bölümüne bakın.

### <a name="there-is-a-duplicate-key-sequence-number"></a>Yinelenen bir anahtar sırası ' {number} ' var 

Kullanıcı [yolculuğu](userjourneys.md) veya [Sub yolculuğu](subjourneys.md) , sırayla yürütülen düzenleme adımlarının sıralı bir listesinden oluşur. Yolculuğunu değiştirdikten sonra, 1 ile N arasında bir tamsayı atlamadan adımları ardışık olarak yeniden numaralandırın.

> [!TIP]
> [Vs Code](https://code.visualstudio.com/) komutu için [Azure AD B2C uzantısını](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) , `(Shift+Ctrl+r)` ilkenizde tüm Kullanıcı ilerliklerini ve alt bağlantı düzenleme adımlarını yeniden numaralandırmak için kullanabilirsiniz.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... "{Number}" sırasıyla bir adım olması bekleniyordu, ancak bulunamadı...

Önceki hatayı kontrol edin.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>"{Name}" Kullanıcı yolculuğunda düzenleme adımı sırası "{Number}"... sonrasında bir talep sağlayıcı seçimi adımı gelir ve bir talep değişim olması gerekir, ancak bu tür...

Düzenleme adımları türü `ClaimsProviderSelection` ve `CombinedSignInAndSignUp` kullanıcının aralarından seçim yapabileceğiniz seçeneklerin bir listesini içerir. `ClaimsExchange`Bir veya daha fazla talep değiş tokuşiyle, türü ile gelmelidir.

Aşağıdaki düzenleme adımları bu tür veya hataya neden olur. İkinci düzenleme adımının türü `ClaimsExchange` , değil olmalıdır `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... 2 talep alışverişi ile {Number} adımı. Hangi talep değiş tokuşu 'nin kullanılabileceğini belirleyebilmek için önce bir talep sağlayıcı seçimi gelmelidir

Bir Orchestration adım türü `ClaimsExchange` `ClaimsExchange` , bir önceki adım türü veya olan olmadığı müddetçe tek bir olmalıdır `ClaimsProviderSelection` `CombinedSignInAndSignUp` . Aşağıdaki düzenleme adımları bu tür hataya neden olur. Altıncı adım iki talep değişimi içerir. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Bu tür bir hatayı onarmak için iki düzenleme adımını kullanın. Tek bir talep alışverişi ile her düzenleme adımı.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Yinelenen bir anahtar sırası ' {name} ' var

Bir yolculuğa sahip bir yolculuğa sahip birden fazla `ClaimsExchange` `Id` . Aşağıdaki adımlar bu tür bir hataya neden olur. KIMLIK *Aaduserwrite* , Kullanıcı yolculuğunda iki kez görünür.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Bu tür bir hatayı onarmak için, sekizlik düzenleme adımlarının talep değişimini, *çağrı-REST-API* gibi benzersiz bir adla değiştirin.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... "{Claim Name}" kimlikli ClaimType başvurusu yapar, ancak ilke veya temel ilkelerinin hiçbiri böyle bir öğe içermiyor

Bu tür bir hata, ilkeniz [talep şemasında](claimsschema.md)bildirilmeyen bir talebe başvuru yaptığında oluşur. Talepler, ilkedeki dosyalardan en az birinde tanımlanmalıdır. 

Örneğin, *Sseçimi kapağı* çıkış talebine sahip teknik bir profil. Ancak *, çıktı talebi bir* ilke içinde veya bir üst ilkede hiçbir şekilde bildirilmemiş.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Bu tür bir hatayı onarmak için, `ClaimTypeReferenceId` değerin yanlış yazılmış veya şemada mevcut olmadığından emin olun. Talep uzantılar ilkesinde tanımlıysa, ancak temel ilkede de kullanılıyorsa. Talebin kullanılmakta olan ilkede veya üst düzey ilkede tanımlandığından emin olun.

Talep şemasına talep eklemek bu hata türünü çözer.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... KIMLIĞI olan bir Claimstranssize başvurusunu oluşturur...

Bu hatanın nedeni, talep hatası için olana benzerdir. Önceki hatayı kontrol edin.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>Kullanıcı şu anda ' yourtenant.onmicrosoft.com ' kiracısı kullanıcısı olarak oturum açtı...

Bir kiracıdan, karşıya yüklemeyi deneyen ilkeden farklı bir hesapla oturum açın. Örneğin, admin@contoso.onmicrosoft.com ilkeniz olarak ayarlandığında, ile oturum açın `TenantId` `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- `TenantId` `<TrustFrameworkPolicy\>` Ve `<BasePolicy\>` öğelerindeki değerin hedef Azure AD B2C kiracınızla eşleşip eşleştiğinden emin olun.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>"{Name}" talep türü, bağlı olan tarafın teknik profilinin çıkış talebinde bulunur, ancak Kullanıcı yolculuğunun adımlarında herhangi birinde bir çıkış talebi değildir...

Bağlı olan taraf ilkesinde, bir çıkış talebi eklediniz, ancak çıktı talebi Kullanıcı yolculuğunun herhangi bir adımlarında bir çıkış talebi değildir. Azure AD B2C talep çantasından talep değerini okuyamıyor.

Aşağıdaki örnekte, *Sseçimi kapağı* talebi, bağlı olan tarafın teknik profilinin çıkış talebinde bulunur, ancak *oturum açma* ve Kullanıcı yolculuğunun herhangi bir adımlarında bir çıkış talebi değildir.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Bu tür bir hatayı onarmak için, çıkış taleplerinin en az bir düzenleme adımlarının teknik profil çıkış talepleri koleksiyonunda göründüğünden emin olun. Kullanıcı yolculuğu talebi çıktıbir durumda değilse, bağlı olan taraf teknik profilinde boş dize gibi bir varsayılan değer ayarlayın.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>Giriş dizesi doğru biçimde değil

Yanlış değer türünü başka bir türden talep olarak ayarlarsınız. Örneğin, bir tamsayı talebi tanımlarsınız.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Daha sonra bir dize değeri ayarlamaya çalışırsınız:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Bu tür bir hatayı düzeltmek için, gibi doğru değeri ayarladığınızdan emin olun `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>"{Name}" kiracısı "{Name}" kimlikli bir ilkeye zaten sahip. Aynı kimliğe sahip başka bir ilke depolanamaz

Kiracınıza bir ilke yüklemeye çalışırsınız, ancak kiracınıza aynı ada sahip bir ilke zaten yüklenmiş. 

Bu tür bir hatayı onarmak için, ilkeyi karşıya yüklediğinizde, **zaten varsa özel Ilkenin üzerine yaz** onay kutusunu seçin.

![Zaten varsa, özel ilkenin üzerine yazmayı gösteren ekran görüntüsü.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Sonraki adımlar

- [Application Insights ile Azure Active Directory B2C günlüklerini nasıl toplayacağınızı](troubleshoot-with-application-insights.md)öğrenin.


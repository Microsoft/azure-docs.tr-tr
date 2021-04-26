---
title: Azure Active Directory B2C TypingDNA
titleSuffix: Azure AD B2C
description: Kullanıcı yazma düzenine göre kimlik doğrulama ve sağlama konusunda yardım için TypingDNA ile Azure AD B2C kimlik doğrulamasını tümleştirmeyi öğrenin, çok faktörlü kimlik doğrulamasını zorluyor KIMLIK doğrulama çözümleri sağlar ve ödeme hizmetleri yönergesi 2 (PSD2) için SCA gereksinimleriyle uyumlu hale getirmenize yardımcı olur.
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: afaac48d263082a17c7ba58996f4000366de0211
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256557"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Azure Active Directory B2C TypingDNA yapılandırma öğreticisi

Bu kılavuzda, Azure Active Directory B2C ' de bir örnek çevrimiçi ödeme uygulamasını TypingDNA UYGULAMASı ile tümleştirmeyi öğrenin. TypingDNA App ' i kullanarak, Azure AD B2C müşteriler [ödeme hizmetleri yönerge 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) (PSD2) işlem gereksinimleriyle, bir tuş vuruşu dinamikleri ve güçlü müşteri kimlik doğrulaması aracılığıyla uyum sağlayabilir. TypingDNA hakkında daha fazla bilgiyi [burada](https://www.typingdna.com/)bulabilirsiniz.

 Azure AD B2C, kullanıcıların yazma özelliklerini yakalamak ve bunların her bir kimlik doğrulaması için, kaydedilmesini ve çözümlenmelerini sağlamak için TypingDNA 'in teknolojilerini kullanır. Bu, bir kimlik doğrulamanın riskiness ilişkili bir koruma katmanı ekler ve risk düzeylerini değerlendirir. Azure AD B2C, kullanıcının Azure AD MFA 'yı çağırarak, e-posta doğrulamasını zorlayarak veya senaryonuz için başka özel bir mantığa göre daha fazla güven sağlamak üzere diğer mekanizmaları çağırabilir.

>[!NOTE]
> Bu örnek ilke, [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) başlangıç paketini temel alır.

## <a name="scenario-description"></a>Senaryo açıklaması

![TypingDNA mimari diyagramının ekran görüntüsü](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Kaydolma

1. Azure AD B2C sayfalar, kullanıcının yazma modelini kaydetmek için TypingDNA 'un JavaScript kitaplığını kullanır. Örneğin, Kullanıcı adı ve parola ilk kayıt için kayıt sırasında kaydedilir ve ardından her oturum için doğrulama için oturum açın.

2. Kullanıcı sayfayı gönderdiğinde, TypingDNA kitaplığı kullanıcının yazma özelliğini hesaplacaktır. Bundan sonra, Azure AD B2C işlenen gizli bir metin alanına bilgileri ekleyin. Bu alan CSS ile gizlidir.  

    Örnek, JavaScript ve CSS değişiklikleriyle birlikte [HTML dosyaları içerir](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) ve `api.selfasserted.tdnasignin` ve `api.selfasserted.tdnasignup` içerik tanımları tarafından başvurulur. HTML dosyalarınızı barındırmak için [sayfa içeriğini barındırma](./customize-ui-with-html.md#hosting-the-page-content) bölümüne bakın.

3. Azure AD B2C artık Kullanıcı kimlik bilgilerini gönderdiğinde talep paketi içinde yazma düzenine sahiptir. Bu verileri TypingDNA REST API uç noktasına geçirmek için bir API (sizinki) çağrısı yapılmalıdır. Bu API, [örneğe dahildir (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. Orta katman API 'SI daha sonra, yazma deseninin verilerini TypingDNA REST API 'e geçirir. Kaydolma sırasında kullanıcının mevcut olmadığını onaylamak için [Kullanıcı uç noktası](https://api.typingdna.com/index.html#api-API_Services-GetUser) çağrılır ve sonra kullanıcının ilk yazma modelini kaydetmek için [model kaydet](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) uç noktası çağırılır.

> [!NOTE]
> TypingDNA REST API uç noktasına yapılan tüm çağrılar bir kullanıcı kimliği gönderir. Bu bir karma değer olmalıdır. Azure AD B2C `HashObjectIdWithEmail` , bir rastgele anahtar ve gizli dizi ile e-postayı sağlaması için talep dönüşümünü kullanır.  

REST API çağrıları, içinde ile modellenir `validationTechnicalProfiles` `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Oturum açma

Sonraki oturum açma sırasında, kullanıcının yazma şekli, [özel HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml)kullanılarak kaydolma ile aynı şekilde hesaplanır. Yazma profili Azure AD B2C talep paketi dahilinde olduktan sonra, Azure AD B2C TypingDNA REST API uç noktasını çağırmak için API 'nizi çağırır. Kullanıcının var olduğunu onaylamak için [Kullanıcı](https://api.typingdna.com/index.html#api-API_Services-GetUser) uç noktası çağırılır. Sonra, öğesini döndürmek için, model uç noktasının çağrıldığından [emin olun](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) `net_score` . Bu, `net_score` kayıt sırasında yazma deseninin orijinaline ne kadar yakın olduğunu gösteren bir göstergesidir.

Bu yazma modeli, içinde ile modellenir `validationTechnicalProfiles` `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Kullanıcı yüksek bir yazma modelini alırsa `net_score` , TypingDNA [Save yazma deseninin](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) uç noktasını kullanarak bunu kaydedebilirsiniz.  

`saveTypingPattern`TypingDNA Save yazma deseninin uç noktasının Azure AD B2C (API 'niz aracılığıyla) tarafından çağrılması ISTENIRSE API 'niz bir talep döndürmelidir.

Depodaki örnek, aşağıdaki özelliklerle yapılandırılan bir API (TypingDNA-API-Interface) içerir.

- Eğitim modu-kullanıcının ikiden az deseni kaydedildiyse, her zaman MFA 'ya sor.

- Kullanıcının 2-5 deseni kaydedildiyse ve, `net_score` 50 ' den düşükse MFA için istem.

- Kullanıcının 5 + deseni kaydedildiyse ve `net_score` 65 ' den düşükse, MFA için istemde bulunur.

Bu eşikler kullanım durumunda ayarlanmalıdır.

- API 'niz değerlendirdikten sonra `net_score` B2C 'ye bir Boole talebi döndürmelidir `promptMFA` .

- `promptMFA`Talep, Azure AD MFA 'yı koşullu olarak yürütmek için bir ön koşul içinde kullanılır.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>TypingDNA ile ekleme

1. TypingDNA için [buraya](https://www.typingdna.com/) kaydolun
2. TypingDNA panosu ' nda oturum açın ve **API anahtarı** ve **API gizli** anahtarını edinin. Bu, daha sonra API arabirimi kurulumunda gerekecektir

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>TypingDNA Azure AD B2C ile tümleştirin

1. [Typingdna-API arabirimini](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) tercih ettiğiniz barındırma sağlayıcıınızda barındırın
2. `apiKey` `apiSecret` [TYPINGDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) çözümünde ve Içindeki tüm örnekleri typingdna panonuzdaki kimlik bilgileriyle değiştirin
3. [Buradakı](./customize-ui-with-html.md#3-configure-cors) CORS gereksinimlerini takip eden SAĞLAYıCııNıZDA HTML dosyalarını barındırın.
4. `api.selfasserted.tdnasignup`Dosyasındaki ve içerik tanımlarının loaduri öğelerini `api.selfasserted.tdnasignin` `TrustFrameworkExtensions.xml` sırasıyla barındırılan HTML dosyalarınızın URI 'siyle değiştirin.
5. **Azure Portal** Azure AD dikey penceresinde kimlik deneyimi ÇERÇEVESI altında B2C ilke anahtarı oluşturun. Seçeneğini kullanın `Generate` ve bu anahtarı adlandırın `tdnaHashedId` .
6. İlke dosyalarında Tenantıd 'yi değiştirme
7. Tüm TypingDNA REST API teknik profillerindeki (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) ServiceUrl 'Lerini [Typingdna-api-ıNTERFACE API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)'niz için uç noktayla değiştirin.
8. [İlke dosyalarını](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) kiracınıza yükleyin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. B2C kiracısını açın ve kimlik deneyimi çerçevesi ' ni seçin.
2. Daha önce oluşturduğunuz **Kullanıcı akışınızı** seçin.
3. Kullanıcı akışını **Çalıştır** ' ı seçin

    a. **Uygulama** -kayıtlı uygulamayı seçin (örnek JWT)

    b. **Yanıt URL 'si** -YENIDEN yönlendirme URL 'sini seçin

    c. **Kullanıcı akışını Çalıştır**' ı seçin.
  
4. Kaydolma akışına gidin ve hesap oluşturun
5. Oturumu kapat
6. Oturum açma akışına git
7. Sonuç JWT sonucu, TypingDNA sonuçlarını gösterir

## <a name="live-version"></a>Canlı sürüm

• MFA bu test sürümünde devre dışı bırakıldı, ancak kimlik doğrulamasından sonra MFA 'nın talep tarafından istenip istenmeyeceğine ilişkin sonucu görebilirsiniz `promptMFA` .

• [Buradan](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) kaydolun ve [burada](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) oturum açın

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [AAD B2C özel ilkeler](./custom-policy-overview.md)

- [AAD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
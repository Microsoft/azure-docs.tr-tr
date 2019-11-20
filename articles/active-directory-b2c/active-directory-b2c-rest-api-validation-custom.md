---
title: Talep değişimlerinin Azure Active Directory B2C doğrulama olarak REST API
description: Yeniden çalışan hizmetlerle etkileşim kuran Azure AD B2C Kullanıcı yolculuğu oluşturmaya yönelik bir yol.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 45fad1fab419c448febb3f3b760996fba278e154
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644965"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Çözüm: Kullanıcı girişinde Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirme yapın

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) olan kimlik deneyimi çerçevesi (ıEF), kimlik geliştiricisinin Kullanıcı yolculuğu 'ndaki bir etkileşimi bir API ile tümleştirmesini sağlar.

Bu izlenecek yolun sonunda, yeniden çalışan hizmetlerle etkileşim kuran Azure AD B2C bir Kullanıcı yolculuğu oluşturabilirsiniz.

IEF talepler halinde veri gönderir ve verileri taleplerde geri alır. API ile etkileşim:

- , Bir düzenleme adımında gerçekleşen REST API talep değişimi veya doğrulama profili olarak tasarlanabilir.
- Genellikle kullanıcıdan girişi doğrular. Kullanıcı değeri reddedildiyse, Kullanıcı bir hata mesajı döndürme fırsatına sahip geçerli bir değer girmeyi deneyebilir.

Ayrıca, etkileşimi bir düzenleme adımı olarak tasarlayabilirler. Daha fazla bilgi için bkz [. İzlenecek yol: Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde bir düzenleme adımı](active-directory-b2c-rest-api-step-custom.md)olarak tümleştirin.

Doğrulama profili örneği için, ProfileEdit. xml ' de Başlatıcı paketi dosyasındaki Kullanıcı yolculuğunu düzenleme profilini kullanacağız.

Profil düzenleme içindeki kullanıcı tarafından girilen adın bir çıkarma listesinin parçası olmadığından emin olabilir.

## <a name="prerequisites"></a>Önkoşullar

- Başlarken ' de açıklandığı gibi yerel bir hesap kaydolma/oturum açma [işlemi](active-directory-b2c-get-started-custom.md)için yapılandırılmış bir Azure AD B2C kiracısı.
- Etkileşimde bulunmak için bir REST API uç noktası. Bu izlenecek yol için, REST API hizmetiyle [Wingtipgames](https://wingtipgamesb2c.azurewebsites.net/) adlı bir demo sitesi ayarladık.

## <a name="step-1-prepare-the-rest-api-function"></a>1\. adım: REST API işlevini hazırlama

> [!NOTE]
> REST API işlevlerinin kurulumu Bu makalenin kapsamı dışındadır. [Azure işlevleri](https://docs.microsoft.com/azure/azure-functions/functions-reference) , bulutta yeniden bir hizmet oluşturmak için mükemmel bir araç seti sağlar.

Beklediği bir talebi alan bir Azure işlevi oluşturduk `playerTag`. İşlevi, bu talebin varolup olmadığını doğrular. [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples)'Daki tüm Azure işlev koduna erişebilirsiniz.

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

IEF, Azure işlevinin `userMessage` döndürdüğü talebi bekliyor. Bu talep, önceki örnekte bir 409 çakışma durumu döndürüldüğünde, doğrulama başarısız olursa kullanıcıya bir dize olarak sunulur.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>2\. adım: TrustFrameworkExtensions. xml dosyanızda teknik bir profil olarak yeniden, API talep alışverişini yapılandırma

Teknik bir profil, daha fazla hizmet ile istenen Exchange 'in tam yapılandırmadır. TrustFrameworkExtensions. xml dosyasını açın ve aşağıdaki XML kod parçacığını `<ClaimsProviders>` öğesine ekleyin.

> [!NOTE]
> Aşağıdaki XML 'de, yeniden deneme sağlayıcı `Version=1.0.0.0` protokol olarak açıklanmaktadır. Bunu, dış hizmetle etkileşimde olacak bir işlev olarak düşünün. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` Öğesi IEF 'ten Rest hizmetine gönderilecek talepleri tanımlar. Bu örnekte, talebin `givenName` içeriği Rest hizmetine olarak `playerTag`gönderilir. Bu örnekte, ıEF talepleri geri beklemez. Bunun yerine, REST hizmetinden bir yanıt bekler ve aldığı durum kodlarına göre hareket eder.

Yukarıdaki `AuthenticationType` açıklamalar ve `AllowInsecureAuthInProduction` bir üretim ortamına geçtiğinizde yapmanız gereken değişiklikleri belirtin. Yeniden yapılan API 'lerinizi üretime yönelik olarak güvenli hale getirme hakkında bilgi edinmek için bkz. [sertifika kimlik doğrulaması ile](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) [temel kimlik doğrulama](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) ve güvenilir API 'ler ile güvenli yeniden API 'ler sağlama.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>3\. adım: Kullanıcı girişini doğrulamak istediğiniz, kendi kendine onaylanan hizmet talebi alışverişini kendi kendini onaylanan teknik profilinde ekleyin

Doğrulama adımının en yaygın kullanımı bir kullanıcıyla etkileşime geçerdir. Kullanıcının giriş sağlaması beklenen tüm etkileşimler *kendi kendine onaylanan teknik profillerdir*. Bu örnekte, doğrulamayı otomatik olarak onaylanan ProfileUpdate teknik profiline ekleyeceğiz. Bu, bağlı olan taraf (RP) ilke dosyasının `Profile Edit` kullandığı teknik profildir.

Talep değişimini kendi kendine onaylanan teknik Profile eklemek için:

1. TrustFrameworkBase. xml dosyasını açın ve için `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`arama yapın.
2. Bu teknik profilin yapılandırmasını gözden geçirin. Kullanıcıya ait Exchange 'in, Kullanıcı (giriş talepleri) ve kendinden onaylanan sağlayıcıdan (çıkış talepleri) geri dönmesi beklenen talepler olarak nasıl tanımlandığını gözlemleyin.
3. İçin `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`arama yapın ve bu profilin düzenleme adımı `<UserJourney Id="ProfileEdit">`5 olarak çağrıldığından emin olun.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>4\. Adım: Profil düzenleme RP ilke dosyasını karşıya yükleme ve test etme

1. TrustFrameworkExtensions. xml dosyasının yeni sürümünü karşıya yükleyin.
2. Profil düzenleme RP ilke dosyasını sınamak için **Şimdi Çalıştır** 'ı kullanın.
3. **Belirtilen ad** alanına mevcut adlardan birini (örneğin, mcvinny) sağlayarak doğrulamayı test edin. Her şey doğru şekilde ayarlandıysa, kullanıcıya oynatıcı etiketinin zaten kullanıldığını bildiren bir ileti alırsınız.

## <a name="next-steps"></a>Sonraki adımlar

[Kullanıcılarınızın daha fazla bilgi toplamak için profil düzenleme ve Kullanıcı kaydını değiştirme](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Gidiş Düzenleme adımı olarak Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirin](active-directory-b2c-rest-api-step-custom.md)

[Başvurunun Yeniden teknik profil](restful-technical-profile.md)

API 'lerinizi güvenli hale getirme hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Temel kimlik doğrulaması (Kullanıcı adı ve parola) ile yeniden takip eden API 'nizin güvenliğini sağlama](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [İstemci sertifikalarıyla yeniden takip eden API 'nizin güvenliğini sağlama](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

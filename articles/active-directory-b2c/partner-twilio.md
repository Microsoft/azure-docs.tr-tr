---
title: Twilio Azure Active Directory B2C uygulamayı doğrula
titleSuffix: Azure AD B2C
description: Örnek bir çevrimiçi ödeme uygulamasını Twilio Verify API 'siyle Azure AD B2C tümleştirme hakkında bilgi edinin. Dinamik bağlama ve güçlü müşteri kimlik doğrulaması aracılığıyla PSD2 (ödeme hizmetleri yönergesi 2) işlem gereksinimleriyle uyumlu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a7f22b05238fb495422a02edd6ca382b4df041a
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257815"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Twilio Verify uygulamasını Azure Active Directory B2C ile tümleştirme

Bu kılavuzda, Azure Active Directory B2C (Azure AD B2C) ' de bir örnek çevrimiçi ödeme uygulamasını Twilio Verify API 'siyle tümleştirmeyi öğrenin. Azure AD B2C müşteriler, Twilio Verify uygulamasını kullanarak, dinamik bağlama ve güçlü müşteri kimlik doğrulaması aracılığıyla PSD2 (ödeme hizmetleri yönerge 2) işlem gereksinimleriyle uyumlu olabilir.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](tutorial-create-tenant.md) .
* Twilio adresinde bir [deneme hesabı](https://www.twilio.com/try-twilio) .

## <a name="scenario-description"></a>Senaryo açıklaması

Aşağıdaki bileşenler Twilio çözümünü yapar:

- .NET [PSD2 demo Web uygulaması](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), oturum açma veya kaydolma ve kukla yüksek riskli bir işlem gerçekleştirme yeteneği sağlar.
- Azure AD B2C Birleşik [oturum açma ve kaydolma ilkesi](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy).
- Twilio Verify API ile tümleşik Azure AD B2C ilkesi `id_token_hint` .
- `.well-known`Bir openıdconnect uç noktasını bir, doğrulamaya izin verecek şekilde barındıran .NET Web uygulaması `id_token_hint` .


    ![Twilio akışı](media/partner-twilio/twilio-flow.png)

| Adım | Açıklama |
|------|------|
| 1     | Kullanıcı oturum açma veya PSD2 demo uygulamasına kaydolma işlemini başlatır. Kullanıcının kimliği, Azure AD B2C Birleşik oturum açma ve kaydolma ilkesi aracılığıyla doğrulanır. Uygulamaya bir belirteç döndürülür. Kaydolma sırasında kullanıcının telefon numarası SMS/Phone kullanılarak doğrulanır ve Azure AD B2C hesabına kaydedilir.     |
| 2     | Kullanıcı, $50,00 aktarımı gibi yüksek riskli bir işlem başlatır. Kullanıcının geçerli erişim belirteci, kullanıcının kimlik doğrulamasının Step-Up özel bir ilke aracılığıyla zaten yapılıp kalmadığını tespit etmek üzere PolicyId için değerlendirilir.     |
| 3     | Uygulama, işlem değerini ve alacaklı, $50,00 ve John tikan değerlerini kaydeder ve imzalı bir belirteç oluşturur. Bu belirteç, olarak adlandırılır `id_token_hint` ve talebi içerir `amount:$500, payee:john doe` . , `id_token_hint` Twilio ile tümleştirilmiş Azure AD B2C özel ilkesi isteğiyle birlikte gönderilir.     |
| 4     | Azure AD B2C, uygulamalar `/.well-known` OpenID Connect uç noktasını denetleyerek id_token_hint imzasını doğrular. Doğruladıktan sonra, bu belirteçteki talepleri, özellikle ve olarak ayıklar `amount` `payee` . Kullanıcı, SMS iletisi aracılığıyla cep telefonu numaralarını doğrulamak için bir sayfa görür.     |
| 5     | Kullanıcı, SMS iletisi aracılığıyla telefon numaralarını doğrulamayı ister ve Azure AD B2C Twilio 'in API uç noktasını doğrulama REST API isteği yapar. Ayrıca, `amount` `payee` bir kerelik geçiş kodu (OTP) oluşturmak için IŞLEMI ve PSD2 işleminin bir parçası olarak gönderir. Twilio kullanıcının kayıtlı telefon numarasına SMS iletisi gönderir.     |
| 6     |  Kullanıcı, SMS mesajlarına alınan OTP 'yi girer ve Azure AD B2C ' a gönderir. Azure AD B2C OTP 'nin doğru olduğunu doğrulamak için bu OTP 'e Twilio 'in API 'sine sahip bir API isteği oluşturur. Son olarak, uygulamaya bir belirteç verildiğinde, kullanıcının kimlik doğrulaması, Kullanıcı kimlik doğrulamasını işgal eden yeni bir PolicyId ile yapılır.    |
|      |      |

## <a name="onboard-with-twilio"></a>Twilio ile ekleme

1. Twilio adresinde bir [deneme hesabı](https://www.twilio.com/try-twilio) edinin.

2. [Bu makalede](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console) açıklandığı gibi Twilio adresinde bir telefon numarası satın alın

3. Twilio konsolundaki [API 'Yi doğrula](https://www.twilio.com/console/verify/services) ' ya gidin ve bir hizmet oluşturmak ve PSD2 seçeneğini etkinleştirmek için [yönergeleri](https://www.twilio.com/docs/verify/verifying-transactions-psd2) izleyin.  

## <a name="configure-the-psd2-demo-app"></a>PSD2 demo uygulamasını yapılandırma

1. B2C-WebAPI-DotNet çözümünü açın ve aşağıdaki değerleri web.config kendi kiracıya özgü değerlerinizle değiştirin:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. [Web uygulaması](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) , kimlik belirteci ipucu oluşturucuyu ve meta veri uç noktasını da barındırır.
   - İmza sertifikanızı bu [örnek açıklamasında](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)açıklandığı gibi oluşturun.
   - web.config sertifikanıza göre aşağıdaki satırları güncelleştirin:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Demo uygulamasını tercih ettiğiniz barındırma sağlayıcınıza yükleyin. Azure App Service için rehberlik, sertifikanızı karşıya yükleme yönergeleri de dahil olmak üzere [Bu örnek açıklamasında](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)sunulmaktadır.

4. Uygulamanın barındırıldığı URL 'ye eşdeğer bir yanıt URL 'SI ekleyerek Azure AD B2C uygulamanızın kaydını güncelleştirin.

5. [İlke dosyalarını](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) açın ve tüm örneklerini  `contoso` kiracı adınızla değiştirin.

6. Teknik profil **özel-SMS-kayıt** Twilio REST API bulun. Öğesini  `ServiceURL`   Twilio AccountSid Ile ve Kimden numarası ile satın aldığınız telefon numaranız ile güncelleştirin.

7. Twilio REST API teknik profillerini bulun, **TwilioRestAPI-Verify-adım**   ve **TwilioRestAPI-Verify-Step2** ve öğesini  `ServiceURL`   Twilio accountsıd 'unuzla güncelleştirin.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C ile tümleştirme

İlke dosyalarını Azure AD B2C ekleyin:

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.

2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.

3. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.

4. **Azure AD B2C**  >  **Identity Experience Framework**  >  **ilke anahtarlarına** gidin.

5. **B2cRestTwilioClientId** adlı yeni bir anahtar ekleyin.  **El ile**' yi seçin ve Twilio accountsıd değerini sağlayın.

6. **B2cRestTwilioClientSecret** adlı yeni bir anahtar ekleyin.  **El ile**' yi seçin ve TWILIO auth belirtecinin değerini sağlayın.

7. Tüm ilke dosyalarını kiracınıza yükleyin.

8. Kayıt SMS metniniz için GenerateOTPMessageEnrol talep dönüşümünden dizeyi özelleştirin.

## <a name="test-the-solution"></a>Çözümü test etme

* Uygulamanıza gidin ve oturum açma, kaydolma ve para gönderme eylemlerini test edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Twilio tümleştirme kodu örnekleri](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2) için GitHub 'a başvurun  

- [AAD B2C özel ilkeler](custom-policy-overview.md)

- [AAD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

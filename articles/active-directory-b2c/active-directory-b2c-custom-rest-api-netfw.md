---
title: Azure Active Directory B2C Kullanıcı yolculuğunda REST API talep alışverişlerini tümleştirin
description: Kullanıcı girişinin doğrulanması sırasında Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 49cd049c56e0c1d80318f9323aefe2d128774f3f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645119"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Kullanıcı girişinin doğrulanması sırasında Azure AD B2C Kullanıcı yolculuğunda REST API talep alışverişlerinde tümleştirin

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) olan kimlik deneyimi çerçevesi ile, Kullanıcı yolculuğu içindeki bir Rehirsiz API ile tümleştirilebilir. Bu kılavuzda, Azure AD B2C .NET Framework yeniden hizmet Hizmetleri (Web API 'SI) ile nasıl etkileşime gireceğini öğreneceksiniz.

## <a name="introduction"></a>Giriş

Azure AD B2C kullanarak, kendi iş mantığınızı bir Kullanıcı yolculuğuna ekleyerek kendi Rehirsiz hizmetinizi çağırarak bu hizmeti kullanabilirsiniz. Kimlik deneyimi çerçevesi, verileri bir *giriş talep* koleksiyonundaki yeniden takip eden hizmete gönderir ve bir *Çıkış talep* koleksiyonundaki verileri yeniden alır. Yeniden hizmet tümleştirmesiyle şunları yapabilirsiniz:

* **Kullanıcı giriş verilerini doğrula**: Bu eylem, hatalı oluşturulmuş verilerin Azure AD 'ye kalıcı olmasını engeller. Kullanıcının değeri geçerli değilse, yeniden deneme hizmetiniz, kullanıcıdan bir giriş sağlamasını bildiren bir hata mesajı döndürür. Örneğin, Kullanıcı tarafından girilen e-posta adresinin müşterinizin veritabanında mevcut olduğunu doğrulayabilirsiniz.
* **Giriş taleplerinin üzerine yaz**: Örneğin, bir Kullanıcı ilk adı küçük harfle veya tüm büyük harflerde girerse, adı yalnızca ilk harfi büyük harfle biçimlendirebilirsiniz.
* **Şirket iş kolu uygulamalarıyla daha fazla tümleştirerek Kullanıcı verilerini zenginleştirin**: Yeniden deneme hizmetiniz, kullanıcının e-posta adresini alabilir, müşterinin veritabanını sorgulayabilir ve Azure AD B2C için kullanıcının bağlılık programı numarasını döndürebilir. Dönüş talepleri kullanıcının Azure AD hesabında depolanabilir, sonraki *düzenleme adımlarında*değerlendirilir veya erişim belirtecine dahil edilebilir.
* **Özel iş mantığını Çalıştır**: Anında iletme bildirimleri gönderebilir, kurumsal veritabanlarını güncelleştirebilir, bir kullanıcı geçiş işlemi çalıştırabilir, izinleri yönetebilir, veritabanlarını denetleyebilir ve diğer işlemleri gerçekleştirebilirsiniz.

Aşağıdaki yollarla, yeniden kullanılabilen hizmetlerle tümleştirmeyi tasarlayabilirsiniz:

* **Doğrulama teknik profili**: Yeniden gerçekleşen hizmete yapılan çağrı, belirtilen teknik profilin doğrulama teknik profili içinde gerçekleşir. Doğrulama teknik profili, Kullanıcı yolculuğu ileründen önce Kullanıcı tarafından belirtilen verileri doğrular. Doğrulama teknik profiliyle şunları yapabilirsiniz:
  * Giriş talepleri gönderin.
  * Giriş taleplerini doğrulayın ve özel hata iletileri oluşturun.
  * Çıkış taleplerini geri gönderin.

* **Talep değişimi**: Bu tasarım, doğrulama teknik profiline benzer, ancak bir düzenleme adımı içinde olur. Bu tanım şu şekilde sınırlıdır:
  * Giriş talepleri gönderin.
  * Çıkış taleplerini geri gönderin.

## <a name="restful-walkthrough"></a>Yeniden izlenecek yol

Bu kılavuzda, Kullanıcı girişini doğrulayan ve Kullanıcı bağlılık programı numarası sağlayan bir .NET Framework Web API 'SI geliştirirsiniz. Örneğin, uygulamanız bağlılık programı numarasına göre *Platinum avantajlarına* erişim izni verebilir.

Genel Bakış:

* Yeniden işleme hizmeti geliştirme (.NET Framework Web API 'SI)
* Kullanıcı yolculuğu 'ndaki yeniden kullanılabilir hizmeti kullanma
* Giriş taleplerini gönderin ve kodunuzda okuyun
* Kullanıcının adını doğrulama
* Bağlılık programı numarası geri gönder
* Bağlılık programı numarasını bir JSON Web Token (JWT) ekleme

## <a name="prerequisites"></a>Önkoşullar

[Özel Ilkelerle Başlarken](active-directory-b2c-get-started-custom.md) makalesindeki adımları uygulayın.

## <a name="step-1-create-an-aspnet-web-api"></a>1\. adım: ASP.NET Web API 'SI oluşturma

1. Visual Studio 'da **Dosya** > **Yeni** > **Proje**' yi seçerek bir proje oluşturun.
1. **Yeni proje** penceresinde,  >  **Visual C#**  **Web** > **ASP.NET Web uygulaması (.NET Framework)** öğesini seçin.
1. **Ad** kutusuna uygulama için bir ad yazın (örneğin, *contoso. AADB2C. API*) ve ardından **Tamam**' ı seçin.

    ![Visual Studio 'da yeni bir Visual Studio projesi oluşturma](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

1. **Yeni ASP.NET Web uygulaması** penceresinde, bir **Web API 'SI** veya **Azure API uygulaması** şablonu seçin.

    ![Visual Studio 'da Web API şablonu seçme](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. Kimlik doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığından emin olun.
1. Seçin **Tamam** projeyi oluşturmak için.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>2\. adım: REST API uç noktasını hazırlama

### <a name="step-21-add-data-models"></a>Adım 2,1: Veri modelleri ekleme

Modeller, yeniden takip eden hizmetinizdeki giriş taleplerini ve çıkış talep verilerini temsil eder. Kodunuz, giriş talep modelini bir JSON dizesinden bir C# nesneye (modelinize) serisini kaldırarak giriş verilerini okur. ASP.NET Web API 'SI, çıkış talep modelini otomatik olarak JSON 'a yeniden çıkarır ve ardından seri hale getirilmiş verileri HTTP yanıt iletisinin gövdesine yazar.

Aşağıdakileri yaparak giriş taleplerini temsil eden bir model oluşturun:

1. Çözüm Gezgini zaten açık değilse **Görünüm** > **Çözüm Gezgini**' i seçin.
1. Çözüm Gezgini'nde **Modeller** klasörüne sağ tıklayın, **Ekle**'yi ve ardından **Sınıf**'ı seçin.

    ![Visual Studio 'da seçili sınıf menü öğesi Ekle](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

1. Sınıfı `InputClaimsModel`adlandırın ve aşağıdaki özellikleri `InputClaimsModel` sınıfına ekleyin:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

1. Yeni bir model `OutputClaimsModel`oluşturun ve sonra `OutputClaimsModel` sınıfına aşağıdaki özellikleri ekleyin:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. Giriş doğrulama hata iletilerini oluşturmak `B2CResponseContent`için kullandığınız bir model daha oluşturun. `B2CResponseContent` Sınıfına aşağıdaki özellikleri ekleyin, eksik başvuruları sağlayın ve sonra dosyayı kaydedin:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Adım 2,2: Denetleyici ekleme

Web API 'sinde, _DENETLEYICI_ http isteklerini işleyen bir nesnedir. Denetleyici çıkış taleplerini döndürür veya ad geçerli değilse, bir çakışma HTTP hata iletisi oluşturur.

1. Çözüm Gezgini'nde **Denetleyiciler** klasörüne sağ tıklayın, **Ekle**'yi ve ardından **Denetleyici**'yi seçin.

    ![Visual Studio 'da yeni bir denetleyici ekleme](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. **Yapı Iskelesi Ekle** PENCERESINDE **Web API denetleyici-boş**' ı seçin ve ardından **Ekle**' yi seçin.

    ![Web API 2 denetleyicisini seçme-Visual Studio 'da boş](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. **Denetleyici Ekle** penceresinde, denetleyiciyi **ıdentitycontroller**olarak adlandırın ve **Ekle**' yi seçin.

    ![Visual Studio 'da denetleyici adını girme](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Yapı iskelesi, *denetleyiciler* klasöründe *IdentityController.cs* adlı bir dosya oluşturur.

1. *IdentityController.cs* dosyası henüz açık değilse, çift tıklayın ve ardından dosyadaki kodu aşağıdaki kodla değiştirin:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>3\. adım: Projeyi Azure'da yayımlama

1. Çözüm Gezgini, **contoso. AADB2C. API** projesine sağ tıklayın ve ardından **Yayımla**' yı seçin.

    ![Visual Studio ile Microsoft Azure App Service yayımlama](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. **Yayımla** penceresinde **Microsoft Azure App Service**' i seçin ve ardından **Yayımla**' yı seçin.

    ![Visual Studio ile yeni Microsoft Azure App Service oluşturma](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **App Service oluştur** penceresi açılır. Bu durumda, ASP.NET Web uygulamasını Azure 'da çalıştırmak için gereken tüm Azure kaynaklarını oluşturursunuz.

    > [!TIP]
    > Yayımlama hakkında daha fazla bilgi için bkz. [Azure 'da ASP.NET Web uygulaması oluşturma](../app-service/app-service-web-get-started-dotnet-framework.md).

1. **Web uygulaması adı** kutusuna benzersiz bir uygulama adı yazın (geçerli karakterler a-z, 0-9 ve kısa çizgi (-) yazın. Web uygulamasının URL 'SI http://< app_name >. azurewebsites. NET ' dir; burada *app_name* , Web uygulamanızın adıdır. Otomatik oluşturulmuş benzersiz adı kabul edebilirsiniz.

    ![App Service özelliklerini yapılandırma](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Azure kaynaklarını oluşturmaya başlamak için **Oluştur**' u seçin.
    ASP.NET Web uygulaması oluşturulduktan sonra, sihirbaz bu uygulamayı Azure 'da yayımlar ve ardından uygulamayı varsayılan tarayıcıda başlatır.

1. Web uygulamasının URL 'sini kopyalayın.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>4\. Adım: Yeni `loyaltyNumber` talebi TrustFrameworkExtensions. XML dosyanızın şemasına ekleyin

`loyaltyNumber` Talep henüz şemada tanımlı değil. `<BuildingBlocks>` Öğesi içinde, *TrustFrameworkExtensions. xml* dosyasının başlangıcında bulabileceğiniz bir tanım ekleyin.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>5\. Adım: Talep sağlayıcısı ekleme

Her talep sağlayıcının, talep sağlayıcısıyla iletişim kurmak için gereken uç noktaları ve protokolleri belirleyen bir veya daha fazla teknik profili olmalıdır.

Bir talep sağlayıcısında çeşitli nedenlerle birden çok teknik profil bulunabilir. Örneğin, talep sağlayıcı birden çok protokolü desteklediğinden, uç noktaların farklı yeteneklere sahip olabileceği veya sürümlerde çeşitli güvence düzeylerine sahip talepler olabileceği için birden çok teknik profil tanımlanabilir. Hassas talepleri tek bir Kullanıcı yolculuğunda serbest bırakmak kabul edilebilir ancak başka bir kullanıcı tarafından kullanılamaz.

Aşağıdaki XML kod parçacığı, iki teknik profille bir talep sağlayıcı düğümü içerir:

* **Teknisyen kimliği = "REST-API-kaydolma"** : , Yeniden takip eden hizmetinizi tanımlar.
  * `Proprietary`, Re, temelli bir sağlayıcıya yönelik protokol olarak açıklanmaktadır.
  * `InputClaims`REST hizmetine Azure AD B2C gönderilecek talepleri tanımlar.

    `givenName` Bu örnekte, talebin içeriği Rest `firstName`hizmetine olarak gönderilir, talebin `surname` içeriği Rest hizmetine olarak `lastName`gönderilir ve `email` olduğu gibi gönderir. `OutputClaims` Öğesi, geri ödeme hizmetinden alınan talepleri Azure AD B2C olarak tanımlar.

* **Onealprofile ID = "LocalAccountSignUpWithLogonEmail"** : Mevcut bir teknik profile (temel ilkede tanımlanan) bir doğrulama teknik profili ekler. Kayıt yolculuğunda, doğrulama teknik profili önceki teknik profili çağırır. Yeniden deneme hizmeti bir HTTP hatası 409 (bir çakışma hatası) döndürürse, hata iletisi kullanıcıya gösterilir.

Düğümünü bulun ve `<ClaimsProviders>` düğüm altına aşağıdaki XML kod parçacığını ekleyin: `<ClaimsProviders>`

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>

    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Yukarıdaki `AuthenticationType` açıklamalar ve `AllowInsecureAuthInProduction` bir üretim ortamına geçtiğinizde yapmanız gereken değişiklikleri belirtin. Yeniden yapılan API 'lerinizi üretime yönelik olarak güvenli hale getirme hakkında bilgi edinmek için bkz. [sertifika kimlik doğrulaması ile](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) [temel kimlik doğrulama](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) ve güvenilir API 'ler ile güvenli yeniden API 'ler sağlama.

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>6\. Adım: Talebin uygulamanıza gönderilmesi için talebibağlıolantarafilkesidosyanızaekleyin`loyaltyNumber`

*Signuporsignın. xml* bağlı olan taraf (RP) dosyanızı düzenleyin ve aşağıdaki öğeleri eklemek Için teknisyen kimliği = "policyprofile" öğesini değiştirin: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Yeni talebi ekledikten sonra, bağlı olan taraf kodu şöyle görünür:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>7\. Adım: İlkeyi kiracınıza yükleyin

1. [Azure Portal](https://portal.azure.com), [Azure AD B2C kiracınızın bağlamına](active-directory-b2c-navigate-to-b2c-context.md)geçin ve ardından **Azure AD B2C**' yı açın.

1. **Kimlik deneyimi çerçevesini**seçin.

1. **Tüm ilkeleri**açın.

1. **Ilkeyi karşıya yükle**' yi seçin.

1. Varsa **Ilkenin üzerine yaz** onay kutusunu seçin.

1. TrustFrameworkExtensions. xml dosyasını karşıya yükleyin ve doğrulamayı geçirdiğinizden emin olun.

1. Önceki adımı Signuporsignın. XML dosyasıyla tekrarlayın.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>8\. Adım: Şimdi Çalıştır 'ı kullanarak özel ilkeyi test etme

1. **Azure AD B2C ayarları**' nı seçin ve ardından **kimlik deneyimi çerçevesi**' ne gidin.

    > [!NOTE]
    > **Şimdi Çalıştır** , kiracıya en az bir uygulamanın önceden bağlı olmasını gerektirir. Uygulamaları nasıl kaydedeceğinizi öğrenmek için Azure AD B2C [Başlarken](active-directory-b2c-get-started.md) makalesine veya [uygulama kaydı](active-directory-b2c-app-registration.md) makalesine bakın.

2. Karşıya yüklediğiniz **B2C_1A_signup_signin**, bağlı olan taraf (RP) özel ilkesini açın ve **Şimdi Çalıştır**' ı seçin.

    ![Azure portal B2C_1A_signup_signin özel ilke sayfası](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. **Verilen ad** kutusuna **Test** yazarak işlemi test edin.
    Azure AD B2C pencerenin üst kısmında bir hata mesajı görüntüler.

    ![Kaydolma oturum açma sayfasında verilen ad giriş doğrulamasını test etme](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. **Verilen ad** kutusuna bir ad yazın ("test" dışında).
    Azure AD B2C kullanıcıyı imzalar ve sonra uygulamanıza bir loyaltyNumber gönderir. Bu JWT içindeki sayıyı aklınızda edin.

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>Seçim Tüm ilke dosyalarını ve kodunu indirin

* [Özel ilkeleri kullanmaya başlama talimatlarını](active-directory-b2c-get-started-custom.md) tamamladıktan sonra, kendi özel ilke dosyalarınızı kullanarak senaryonuzu oluşturmanızı öneririz. Başvurunuz için [örnek ilke dosyaları](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)sağladık.

* [Başvuru Için örnek Visual Studio çözümünden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)tüm kodu indirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki göreviniz, temel veya istemci sertifikası kimlik doğrulamasını kullanarak, yeniden takip eden API 'nizin güvenliğini sağlar. API 'lerinizi güvenli hale getirme hakkında bilgi edinmek için aşağıdaki makalelere bakın:

* [Temel kimlik doğrulaması (Kullanıcı adı ve parola) ile yeniden takip eden API 'nizin güvenliğini sağlama](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [İstemci sertifikalarıyla yeniden takip eden API 'nizin güvenliğini sağlama](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

Bir teknik profilde bulunan tüm öğeler hakkında daha fazla bilgi için bkz [. başvuru: Teknik profil](restful-technical-profile.md)yeniden oluşturuluyor.

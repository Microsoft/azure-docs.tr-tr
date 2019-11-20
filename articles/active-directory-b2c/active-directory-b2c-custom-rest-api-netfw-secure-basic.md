---
title: RESTful hizmetlerinizi Azure Active Directory B2C'de HTTP temel kimlik doğrulaması kullanarak güvenli hale getirme | Microsoft Docs
description: Azure AD B2C'yi özel, REST API talep alışverişlerine HTTP temel kimlik doğrulaması kullanarak güvenli hale getirin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8c1251056ad816af664f95abcd18d50ceca4619d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835273"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>HTTP temel kimlik doğrulaması kullanarak, RESTful Hizmetleri güvenli hale getirme

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

İçinde bir [ilişkili Azure AD B2C makale](active-directory-b2c-custom-rest-api-netfw.md), Azure Active Directory (Azure AD B2C) kullanıcı yolculuklarından kimlik doğrulaması olmadan B2C ile tümleşen bir RESTful hizmeti (web API'si) oluşturun.

Doğrulanmış kullanıcıların yalnızca, B2C'yi de dahil olmak üzere erişim API'nizi bu makalede, HTTP temel kimlik doğrulaması RESTful hizmetinize ekleyin. HTTP temel kimlik doğrulaması ile özel ilkeniz (uygulama kimliği ve uygulama gizli anahtarı) kullanıcı kimlik bilgilerini ayarlayın.

Daha fazla bilgi için [ASP.NET web API'de temel kimlik doğrulaması](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Önkoşullar

Bölümündeki adımları tamamlamanız [tümleştirme REST API, Azure AD B2C kullanıcı yolculuğu alışverişlerine talep](active-directory-b2c-custom-rest-api-netfw.md) makalesi.

## <a name="step-1-add-authentication-support"></a>1\. adım: Kimlik doğrulama desteği ekleme

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Adım 1.1: Uygulama ayarları, projenin web.config dosyasına ekleyin.

1. Daha önce oluşturduğunuz Visual Studio projesini açın.

2. Aşağıdaki uygulama ayarları altında web.config dosyasına ekleyerek `appSettings` öğesi:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Bir parola oluşturun ve ardından `WebApp:ClientSecret` değeri.

    Karmaşık bir parola oluşturmak için şu PowerShell kodunu çalıştırın. Herhangi bir keyfi değere kullanabilirsiniz.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Adım 1.2: OWIN kitaplıklarını yükleme

Başlamak için Visual Studio Paket Yöneticisi Konsolu'nu kullanarak OWIN ara yazılımı NuGet paketleri projeye ekleyin:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>1\.3. adım: Bir kimlik doğrulaması ara yazılım sınıfı Ekle

Ekleme `ClientAuthMiddleware.cs` altında sınıfı *App_Start* klasör. Bunu yapmak için:

1. Sağ *App_Start* klasörüne **Ekle**ve ardından **sınıfı**.

   ![App_Start klasöründe ClientAuthMiddleware.cs sınıfı Ekle](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. İçinde **adı** kutusuna **ClientAuthMiddleware.cs**.

   ![Yeni bir oluşturma C# Visual Studio'da yeni öğe Ekle iletişim kutusu sınıfında](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Açık *App_Start\ClientAuthMiddleware.cs* dosyasını ve dosya içeriğini aşağıdaki kodla değiştirin:

    ```csharp

    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;

    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }

            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }

            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);

                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }

                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;

                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }

                return Next(environment);
            }

            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;

                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }

                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }

                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);

                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Adım 1.4: OWIN başlangıç sınıfı ekleme

Adlı bir OWIN başlangıç sınıfı ekleyin `Startup.cs` API. Bunu yapmak için:
1. Projeye sağ tıklayın, **Ekle** > **yeni öğe**ve ardından arama **OWIN**.

   ![Visual Studio'da yeni öğe Ekle iletişim kutusunda OWIN başlangıç sınıfı oluşturma](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Açık *Startup.cs* dosyasını ve dosya içeriğini aşağıdaki kodla değiştirin:

    ```csharp
    using Microsoft.Owin;
    using Owin;

    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>1\.5. adım: Kimlik API sınıfı koruyun

Controllers\IdentityController.cs açın ve eklemek `[Authorize]` ve denetleyici sınıfına etiketi. Bu etiket Denetleyicisi kimlik doğrulama gereksinimini karşılayan kullanıcılar için erişimi kısıtlar.

![Denetleyiciye Authorize etiketi Ekle](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>2\. adım: Azure'a Yayımlama

Çözüm Gezgini'nde projenize yayımlamak için sağ **Contoso.AADB2C.API** proje ve ardından **Yayımla**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>3\. adım: Azure AD B2C için RESTful Hizmetleri uygulama kimliği ve uygulama gizli anahtarı Ekle

RESTful hizmetiniz istemci kimliği (kullanıcı adı) ve gizli dizi tarafından korunduktan sonra kimlik bilgilerinin Azure AD B2C kiracınızda depolamanız gerekir. RESTful hizmetlerinizi çağırdığında, özel ilkeniz kimlik bilgilerini sağlar.

### <a name="step-31-add-a-restful-services-client-id"></a>Adım 3.1: Bir RESTful Hizmetleri istemci Kimliğini ekleyin

1. Azure AD B2C kiracınızı seçin **B2C ayarlarını** > **kimlik deneyimi çerçevesi**.


2. Seçin **ilke anahtarları** kiracınızda kullanılabilir tuşlarını görmek için.

3. **Add (Ekle)** seçeneğini belirleyin.

4. İçin **seçenekleri**seçin **el ile**.

5. İçin **adı**, türü **B2cRestClientId**.
    Önek *B2C_1A_* otomatik olarak eklenebilir.

6. İçinde **gizli** kutusunda, daha önce tanımlanan uygulama kimliği girin.

7. İçin **anahtar kullanımı**seçin **imza**.

8. **Oluştur**’u seçin.

9. Oluşturduğunuzu doğrulayın `B2C_1A_B2cRestClientId` anahtarı.

### <a name="step-32-add-a-restful-services-client-secret"></a>Adım 3.2: Bir RESTful Hizmetleri istemci gizli anahtarı Ekle

1. Azure AD B2C kiracınızı seçin **B2C ayarlarını** > **kimlik deneyimi çerçevesi**.

2. Seçin **ilke anahtarları** kiracınızda kullanılabilir tuşlarını görmek için.

3. **Add (Ekle)** seçeneğini belirleyin.

4. İçin **seçenekleri**seçin **el ile**.

5. İçin **adı**, türü **B2cRestClientSecret**.
    Önek *B2C_1A_* otomatik olarak eklenebilir.

6. İçinde **gizli** kutusunda, daha önce tanımlanan uygulama gizli anahtarı girin.

7. İçin **anahtar kullanımı**seçin **imza**.

8. **Oluştur**’u seçin.

9. Oluşturduğunuzu doğrulayın `B2C_1A_B2cRestClientSecret` anahtarı.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>4\. Adım: Temel kimlik doğrulaması uzantısı ilkenizde desteklemek için teknik profilini değiştirme

1. Çalışma dizininizde uzantı ilke dosyası (TrustFrameworkExtensions.xml) açın.

2. Arama `<TechnicalProfile>` içeren düğüm `Id="REST-API-SignUp"`.

3. Bulun `<Metadata>` öğesi.

4. Değişiklik *AuthenticationType* için *temel*gibi:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Kapatma sonrasında hemen `<Metadata>` öğesi, aşağıdaki XML parçacığını ekleyin:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Kod parçacığı eklendikten sonra teknik profilinizi şu XML kod gibi görünmelidir:

    ![TechnicalProfile için temel kimlik doğrulaması XML öğeleri Ekle](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5\. Adım: Kiracınız için ilkeyi karşıya yükle

1. İçinde [Azure portalında](https://portal.azure.com), geçiş [Azure AD B2C kiracınızın bağlamında](active-directory-b2c-navigate-to-b2c-context.md)ve ardından açın **Azure AD B2C**.

2. Seçin **kimlik deneyimi çerçevesi**.

3. Açık **tüm ilkeleri**.

4. Seçin **karşıya yükleme İlkesi**.

5. Seçin **ilke varsa üzerine** onay kutusu.

6. Karşıya yükleme *TrustFrameworkExtensions.xml* dosyasını ve ardından doğrulama başarılı emin olun.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6\. Adım: Şimdi Çalıştır'i kullanarak özel bir ilkeyi test

1. Açık **Azure AD B2C ayarlarını**ve ardından **kimlik deneyimi çerçevesi**.

    >[!NOTE]
    >Çalıştırma artık Kiracı'da önceden kayıtlı için en az bir uygulama gerektirir. Azure AD B2C uygulamaları kaydetme hakkında bilgi için bkz [başlama](active-directory-b2c-get-started.md) makale veya [uygulama kaydı](active-directory-b2c-app-registration.md) makalesi.

2. Açık **B2C_1A_signup_signin**, yüklenmiş ve ardından bağlı olan taraf (RP) özel ilke **Şimdi Çalıştır**.

3. İşlem yazarak test edin **Test** içinde **verilen ad** kutusu.
    Azure AD B2C, pencerenin en üstünde bir hata iletisi görüntüler.

    ![Kimliğinizi API verilen ad giriş doğrulama sınaması](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. İçinde **verilen ad** ("Test" dışında) bir ad yazın.
    Azure AD B2C kullanıcı oturum açtığında ve uygulamanızı bir bağlılık sayı gönderir. Bu örnekte numarasını not edin:

    ```
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(İsteğe bağlı) Tüm ilke dosyaları ve kodu indirin

* Tamamladıktan sonra [özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md) izlenecek yol, öneririz senaryonuz kendi özel ilke dosyalarını kullanarak oluşturun. Referans olması açısından sağladık [örnek ilke dosyaları](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Tam koddan indirebileceğiniz [başvuru için örnek Visual Studio çözümü](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Sonraki adımlar

* [RESTful API'NİZİN güvenliğini sağlamak için istemci sertifikaları kullan](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

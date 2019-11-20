---
title: Azure Active Directory B2C'de istemci sertifikaları kullanarak, RESTful hizmeti güvenli hale getirme | Microsoft Docs
description: Azure AD B2C'yi özel, REST API talep alışverişlerine istemci sertifikaları kullanarak güvenli hale getirme
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1690adfe5336ea85328e16755c5e3bc82b6d240a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835619"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>İstemci sertifikaları kullanarak, RESTful hizmeti güvenli hale getirme

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

İlgili bir makalede, [bir RESTful hizmeti oluşturma](active-directory-b2c-custom-rest-api-netfw.md) Azure Active Directory B2C ile etkileşime geçer (Azure AD B2C).

Bu makalede, bir istemci sertifikası kullanarak Azure web uygulamanıza (RESTful API) erişimi kısıtlama öğrenin. TLS karşılıklı kimlik doğrulaması, bu mekanizma olarak adlandırılan veya *istemci sertifikası kimlik doğrulaması*. Azure AD B2C'yi gibi uygun sertifikalar olan hizmetleri, hizmetinizin erişebilirsiniz.

>[!NOTE]
>Kullanarak Ayrıca, bir RESTful hizmeti güvenli hale getirebilirsiniz [HTTP temel kimlik doğrulaması](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Ancak, HTTP temel kimlik doğrulaması bir istemci sertifikası daha az güvenli olarak kabul edilir. Bu makalede anlatıldığı gibi istemci sertifikası kimlik doğrulaması kullanarak bir RESTful hizmeti güvenli hale getirmek için sunduğumuz önerilir.

Bu makalede ayrıntıları nasıl yapılır:
* İstemci sertifikası kimlik doğrulaması kullanmak için web uygulamanızı ayarlayın.
* Sertifikayı karşıya yüklemek için Azure AD B2C İlkesi anahtarları.
* İstemci sertifikasını kullanmak için özel bir ilke yapılandırın.

## <a name="prerequisites"></a>Önkoşullar
* Bölümündeki adımları tamamlamanız [tümleştirme REST API talep değişimleri](active-directory-b2c-custom-rest-api-netfw.md) makalesi.
* Geçerli bir sertifika (.pfx dosyası özel bir anahtarla) alın.

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>1\. adım: İstemci sertifikası kimlik doğrulaması için bir web uygulamasını yapılandırma
Ayarlamak için **Azure App Service** istemci sertifikaları gerektirmek için web uygulamasını ayarlama `clientCertEnabled` site ayarını *true*. Azure portalında bu değişikliği yapmak için web uygulaması sayfanızın açın. Sol gezinti bölmesindeki altında **ayarları** seçin **SSL ayarları**. İçinde **istemci sertifikaları** bölümünde, açma **gelen istemci sertifikası** seçeneği.

>[!NOTE]
>Azure App Service planınızın standart veya daha büyük olduğundan emin olun. Daha fazla bilgi için [Azure App Service planlarına ayrıntılı genel bakış](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Ayar hakkında daha fazla bilgi için **clientCertEnabled** özelliği bkz [web uygulamaları için yapılandırma TLS karşılıklı kimlik doğrulamayı](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>2\. adım: Sertifikanızı karşıya yüklemek için Azure AD B2C İlkesi anahtarları
Ayarlandıktan sonra `clientCertEnabled` için *true*, RESTful API'niz ile iletişimi bir istemci sertifikası gerektirir. Edinme, yükleme ve istemci sertifikası, Azure AD B2C kiracınızda depolamak için aşağıdakileri yapın:
1. Azure AD B2C kiracınızı seçin **B2C ayarlarını** > **kimlik deneyimi çerçevesi**.

2. Kiracınızda kullanılabilir anahtarlarını görüntülemek için seçin **ilke anahtarları**.

3. **Add (Ekle)** seçeneğini belirleyin.
    **Anahtar oluşturma** penceresi açılır.

4. İçinde **seçenekleri** kutusunda **karşıya**.

5. İçinde **adı** kutusuna **B2cRestClientCertificate**.
    Önek *B2C_1A_* otomatik olarak eklenir.

6. İçinde **karşıya dosya yükleme** kutusunda, özel bir anahtarla sertifikanızın .pfx dosyasını seçin.

7. İçinde **parola** sertifikanın parolası yazın.

    ![Azure portalında bir anahtar sayfası oluşturma ilke anahtarını karşıya yükleyin](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. **Oluştur**’u seçin.

8. Kiracınızda kullanılabilir anahtarlarını görüntüleyebilir ve oluşturduğunuzu doğrulayın `B2C_1A_B2cRestClientCertificate` anahtar seçeneğini **ilke anahtarları**.

## <a name="step-3-change-the-technical-profile"></a>3\. adım: Teknik profilini değiştirme
İstemci sertifikası kimlik doğrulaması, özel ilkeniz desteklemek için aşağıdakileri yaparak teknik profil değiştirin:

1. Çalışma dizininizde açın *TrustFrameworkExtensions.xml* uzantı ilke dosyası.

2. Arama `<TechnicalProfile>` içeren düğüm `Id="REST-API-SignUp"`.

3. Bulun `<Metadata>` öğesi.

4. Değişiklik *AuthenticationType* için *ClientCertificate*gibi:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Kapatma sonrasında hemen `<Metadata>` öğesi, aşağıdaki XML parçacığını ekleyin:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Kod parçacığı eklendikten sonra teknik profilinizi şu XML kod gibi görünmelidir:

    ![ClientCertificate kimlik doğrulaması XML öğeleri ayarlayın](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>4\. Adım: Kiracınız için ilkeyi karşıya yükle

1. İçinde [Azure portalında](https://portal.azure.com), geçiş [Azure AD B2C kiracınızın bağlamında](active-directory-b2c-navigate-to-b2c-context.md)ve ardından **Azure AD B2C**.

2. Seçin **kimlik deneyimi çerçevesi**.

3. Seçin **tüm ilkeleri**.

4. Seçin **karşıya yükleme İlkesi**.

5. Seçin **ilke varsa üzerine** onay kutusu.

6. Karşıya yükleme *TrustFrameworkExtensions.xml* dosyasını ve ardından doğrulama başarılı emin olun.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>5\. Adım: Şimdi Çalıştır'i kullanarak özel bir ilkeyi test
1. Açık **Azure AD B2C ayarlarını**ve ardından **kimlik deneyimi çerçevesi**.

    >[!NOTE]
    >Çalıştırma artık Kiracı'da önceden kayıtlı için en az bir uygulama gerektirir. Azure AD B2C uygulamaları kaydetme hakkında bilgi için bkz [başlama](active-directory-b2c-get-started.md) makale veya [uygulama kaydı](active-directory-b2c-app-registration.md) makalesi.

2. Açık **B2C_1A_signup_signin**, yüklenmiş ve ardından bağlı olan taraf (RP) özel ilke **Şimdi Çalıştır**.

3. İşlem yazarak test edin **Test** içinde **verilen ad** kutusu.
    Azure AD B2C, pencerenin en üstünde bir hata iletisi görüntüler.

    ![Verilen ad metin kutusu vurgulanır ve doğrulama hatası gösterilen giriş](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. İçinde **verilen ad** ("Test" dışında) bir ad yazın.
    Azure AD B2C kullanıcı oturum açtığında ve uygulamanızı bir bağlılık sayı gönderir. Bu JWT örnek sayısında dikkat edin:

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

   >[!NOTE]
   >Hata iletisi alırsanız *adı geçerli değil, Lütfen geçerli bir ad sağlayın*, istemci sertifikası sunulan sırasında Azure AD B2C'yi başarıyla RESTful hizmetiniz çağrılan anlamına gelir. Sonraki adım, sertifika doğrulamaktır.

## <a name="step-6-add-certificate-validation"></a>6\. Adım: Sertifika doğrulama ekleme
Azure AD B2C, RESTful hizmetinize gönderen istemci sertifikası doğrulama sertifikası var olup olmadığını denetlemek dışında Azure App Service platformu tarafından geçeriz değil. Sertifika doğrulanırken web uygulamasının sorumluluğundadır.

Bu bölümde, kimlik doğrulama amacıyla sertifika özellikleri doğrular örnek ASP.NET kodunu ekleyin.

> [!NOTE]
>Azure App Service için istemci sertifikası kimlik doğrulaması yapılandırma hakkında daha fazla bilgi için bkz. [web uygulamaları için yapılandırma TLS karşılıklı kimlik doğrulamayı](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 uygulama ayarları, projenin web.config dosyasına ekleyin.
Daha önce oluşturduğunuz Visual Studio projede aşağıdaki uygulama ayarlarına eklemeniz *web.config* sonra dosya `appSettings` öğesi:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Sertifikanın değiştirin **konu adı**, **verenin adı**, ve **sertifika parmak izi** değerlerini, sertifika değerlere sahip.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 IsValidClientCertificate işlevi Ekle
Açık *Controllers\IdentityController.cs* dosya ve ardından eklemek `Identity` denetleyici sınıfı aşağıdaki işlevi:

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

Yukarıdaki örnek kodda, biz yalnızca aşağıdaki tüm koşullar karşılanırsa geçerli olarak sertifikayı kabul edin:
* Sertifika süresinin dolmadığını ve sunucusunda geçerli zaman için etkindir.
* `Subject` Sertifikanın adını eşittir `ClientCertificate:Subject` uygulama ayarı değeri.
* `Issuer` Sertifikanın adını eşittir `ClientCertificate:Issuer` uygulama ayarı değeri.
* `thumbprint` Sertifikasını eşittir `ClientCertificate:Thumbprint` uygulama ayarı değeri.

>[!IMPORTANT]
>Hizmetinizi duyarlılığına bağlı olarak, daha fazla doğrulamaları eklemeniz gerekebilir. Örneğin, güvenilir kök yetkilisi, veren kuruluş adı doğrulama ve benzeri sertifika zincir olup olmadığını sınamak gerekebilir.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 IsValidClientCertificate işlevi çağırın.
Açık *Controllers\IdentityController.cs* dosyasını ve ardından başındaki `SignUp()` işlev, aşağıdaki kod parçacığını ekleyin:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Kod parçacığı eklendikten sonra `Identity` denetleyicisi, aşağıdaki kod gibi görünmelidir:

![Sertifika doğrulama kodu ekleyin](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>7\. Adım: Projenizi Azure'a yayımlama ve test
1. İçinde **Çözüm Gezgini**, sağ **Contoso.AADB2C.API** proje ve ardından **Yayımla**.

2. "Adım 6" yineleyin ve özel ilkenizi sertifika doğrulama ile yeniden test edin. İlke çalıştırma ve doğrulama ekledikten sonra her şeyin çalıştığından emin olmak deneyin.

3. İçinde *web.config* dosyası, değiştirin `ClientCertificate:Subject` için **geçersiz**. İlkeyi yeniden çalıştırın ve bir hata iletisi görürsünüz.

4. Değer durumuna döndürün **geçerli**, ilke, REST API'si çağırabilirsiniz emin olun.

Bu adım sorun gidermeniz gerekiyorsa, bkz. [Application Insights'ı kullanarak günlük toplama](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(İsteğe bağlı) Tüm ilke dosyaları ve kodu indirin
* Tamamladıktan sonra [özel ilkeleri kullanmaya başlama](active-directory-b2c-get-started-custom.md) izlenecek yol, öneririz senaryonuz kendi özel ilke dosyalarını kullanarak oluşturun. Referans olması açısından sağladık [örnek ilke dosyaları](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Tam koddan indirebileceğiniz [başvuru için örnek Visual Studio çözümü](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

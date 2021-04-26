---
title: Apple ile oturum açma yapılandırma (Önizleme)
description: Apple ile oturum açma 'nın App Service veya Azure Işlevleri uygulamanız için bir kimlik sağlayıcısı olarak nasıl yapılandırılacağını öğrenin.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603365"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>App Service veya Azure Işlevleri uygulamanızı, Apple sağlayıcısıyla oturum açma kullanarak oturum açmak için yapılandırma (Önizleme)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Bu makalede, kimlik doğrulama sağlayıcısı olarak Apple ile oturum açma kullanımı için Azure App Service veya Azure Işlevlerinin nasıl yapılandırılacağı gösterilir. 

Bu makaledeki yordamı tamamlayabilmeniz için Apple geliştirici programına kaydolmuş olmanız gerekir. Apple geliştirici programı 'na kaydolmak için [Developer.Apple.com/programs/enroll](https://developer.apple.com/programs/enroll/)adresine gidin.

> [!CAUTION]
> Apple ile oturum açmayı etkinleştirmek, uygulamanız için Azure portal, Azure CLı ve Azure PowerShell gibi bazı istemciler aracılığıyla App Service kimlik doğrulaması/yetkilendirme özelliğinin yönetimini devre dışı bırakır. Özelliği, Önizleme süresince tüm yönetim deneyimlerinde henüz hesaba katılmaz olan yeni bir API yüzeyine bağımlıdır.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Apple Geliştirici Portalında uygulama oluşturma
Apple Geliştirici Portalında bir uygulama KIMLIĞI ve hizmet KIMLIĞI oluşturmanız gerekir.

1. Apple Geliştirici Portalında **Sertifikalar, tanımlayıcılar, & profiller**' e gidin.
2. **Tanımlayıcılar** sekmesinde **(+)** düğmesini seçin.
3. **Yeni bir tanımlayıcı kaydet** sayfasında, **uygulama kimlikleri** ' ni seçin ve **devam**' ı seçin. (Uygulama kimlikleri bir veya daha fazla hizmet kimliği içerir.) ![Apple geliştirici portalı 'nda yeni bir uygulama tanımlayıcısı kaydetme](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. **Uygulama kimliğini kaydetme** sayfasında, bir açıklama ve paket kimliği sağlayın ve yetenekler listesinden **Apple ile oturum aç** ' ı seçin. Daha sonra **Devam** seçeneğini belirleyin. Bu adımdan **uygulama kimliği ön ekini (EKIP kimliği)** bir yere göz atın, daha sonra ihtiyacınız olacaktır.
![Apple Geliştirici Portalında yeni bir uygulama tanımlayıcısı yapılandırma](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Uygulama kayıt bilgilerini gözden geçirin ve **Kaydet**' i seçin.
6. Tekrar, **tanımlayıcılar** sekmesinde **(+)** düğmesini seçin.
![Apple Geliştirici Portalında yeni bir hizmet tanımlayıcısı oluşturma](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. **Yeni bir tanımlayıcı kaydet** sayfasında, **Hizmetler kimlikler** ' i seçin ve **devam**' ı seçin.
![Apple geliştirici portalı 'nda yeni bir hizmet tanımlayıcısı kaydetme](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. **HIZMET kimliğini kaydetme** sayfasında, bir açıklama ve tanımlayıcı sağlayın. Açıklama, kullanıcıya onay ekranında gösterilecek şeydir. Tanımlayıcı, Apple sağlayıcısını App Service ile yapılandırırken kullanılan istemci KIMLIĞINIZ olacaktır. Ardından **Yapılandır**' ı seçin.
![Açıklama ve tanımlayıcı sağlama](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. Açılır pencerede, birincil uygulama kimliği ' ni daha önce oluşturduğunuz uygulama kimliğine ayarlayın. Etki alanı bölümünde uygulamanızın etki alanını belirtin. Dönüş URL 'si için URL 'yi kullanın `<app-url>/.auth/login/apple/callback` . Örneğin, `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Sonra **Ekle** ve **Kaydet**' i seçin.
![Kayıt için etki alanını ve dönüş URL 'sini belirtme](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Hizmet kayıt bilgilerini gözden geçirin ve **Kaydet**' i seçin.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>İstemci parolasını oluştur
Apple, uygulama geliştiricilerinin bir JWT belirtecini istemci gizli değeri olarak oluşturmasını ve imzalamasını gerektirir. Bu gizli dizi oluşturmak için, önce Apple geliştirici portalından bir eliptik eğri özel anahtarı oluşturun ve indirin. Ardından, [belirli bir yük](#structure-the-client-secret-jwt)Ile [bir JWT imzalamak](#sign-the-client-secret-jwt) için bu anahtarı kullanın.

### <a name="create-and-download-the-private-key"></a>Özel anahtarı oluşturma ve indirme
1. Apple geliştirici portalındaki **anahtarlar** sekmesinde, **anahtar oluştur** ' u seçin veya **(+)** düğmesini seçin.
2. **Yeni anahtarı kaydet** sayfasında, anahtara bir ad verin, **Apple ile oturum açın ' ın** yanındaki kutuyu işaretleyin ve **Yapılandır**' ı seçin.
3. **Anahtar Yapılandır** sayfasında, anahtarı daha önce oluşturduğunuz BIRINCIL uygulama kimliğine bağlayın ve **Kaydet**' i seçin.
4. Bilgileri onayladıktan sonra **devam** ' ı seçerek ve ardından bilgileri Inceleyerek ve Kaydet ' i seçerek anahtarı oluşturmayı **tamamlayın**.
5. **Anahtarınızı indirin** sayfasında anahtarı indirin. `.p8`(PKCS # 8) dosyası olarak indirilir. istemci gızlı JWT oturumunuzu imzalamak için dosya içeriğini kullanırsınız.

### <a name="structure-the-client-secret-jwt"></a>İstemci gizli JWT yapısını yapılandırma
Apple, istemci parolasının bir JWT belirtecinin Base64 kodlaması olmasını gerektirir. Kodu çözülen JWT belirtecinin aşağıdaki örnek gibi yapılandırılmış bir yükü olmalıdır:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **Sub**: Apple istemci kimliği (Ayrıca hizmet kimliği)
- **ISS**: Apple GELIŞTIRICI ekibi kimliğiniz
- **AUD**: Apple belirteci alıyor, bu nedenle hedef kitle
- **Exp**: **NBF** sonrasında altı aydan daha fazla

Yukarıdaki yükün Base64 olarak kodlanmış sürümü şöyle görünür: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Note: Apple, oluşturma (veya NBF) tarihinden sonra altı aydan daha fazla süre sonra istemci gizli JWTs 'leri kabul etmez. Yani, en az altı ayda bir istemci gizli anahtarını döndürmeniz gerekecektir._

Belirteçleri oluşturma ve doğrulama hakkında daha fazla bilgi [Apple 'ın geliştirici belgelerinde](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)bulunabilir. 

### <a name="sign-the-client-secret-jwt"></a>İstemci gizli JWT imzala
`.p8`Daha önce indirmiş olduğunuz dosyayı, istemci gızlı JWT imzalamak için kullanacaksınız. Bu dosya, pek biçimindeki özel imza anahtarını içeren bir [Pcks # 8 dosyasıdır](https://en.wikipedia.org/wiki/PKCS_8) . Sizin için JWT oluşturup imzalayabilmeniz gereken birçok kitaplık vardır. 

JWT belirteçleri oluşturmak ve imzalamak için çevrimiçi olarak kullanılabilecek farklı türlerde açık kaynaklı kitaplıklar vardır. JWT belirteçleri oluşturma hakkında daha fazla bilgi için bkz. jwt.io. Örneğin, istemci gizliliğini oluşturmanın bir yolu [Microsoft. IdentityModel. Tokens NuGet paketini](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) içeri aktarıp aşağıda gösterildiği gibi küçük bir C# kodu çalıştırılarak gerçekleştirilir.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamıd**: Apple GELIŞTIRICI ekibi kimliğiniz
- **ClientID**: Apple istemci kimliği (Ayrıca hizmet kimliği)
- **p8key**: pek biçim anahtarı- `.p8` dosyayı bir metin düzenleyicisinde açıp `-----BEGIN PRIVATE KEY-----` satır sonları arasındaki ve olmayan her şeyi kopyalayarak bu anahtarı elde edebilirsiniz `-----END PRIVATE KEY-----`
- **keyId**: INDIRILEN anahtarın kimliği

Döndürülen bu belirteç, Apple sağlayıcısını yapılandırmak için kullanacağınız istemci gizli değeridir.

> [!IMPORTANT]
> İstemci parolası önemli bir güvenlik kimlik bilgileridir. Bu parolayı kimseyle paylaşmayın veya bir istemci uygulaması içinde dağıtmayın.
>

Tercih ettiğiniz bir ayar adı kullanarak, istemci parolasını uygulama [ayarı](./configure-common.md#configure-app-settings) olarak ekleyin. Bu adı daha sonra bir yere getirin.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Uygulamanıza sağlayıcı bilgileri ekleyin

> [!NOTE]
> Gerekli yapılandırma yeni bir API biçiminde ve şu anda yalnızca [dosya tabanlı yapılandırma (Önizleme)](.\app-service-authentication-how-to.md#config-file)tarafından destekleniyor. Bu tür bir dosyayı kullanarak aşağıdaki adımları izlemeniz gerekir.

Bu bölüm, yeni ıDP 'nizi dahil etmek için yapılandırmayı güncelleştirme konusunda size kılavuzluk eder. Örnek bir yapılandırma aşağıda verilmiştir.

1. Nesne içinde `identityProviders` , zaten yoksa bir `apple` nesne ekleyin.
2. Bu anahtara bir nesne içinde bir nesne `registration` ve isteğe bağlı olarak bir nesnesi atayın `login` :
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. Nesnesi içinde `registration` , öğesini `clientId` TOPLADıĞıNıZ istemci kimliği olarak ayarlayın.
    
    b. Nesnesi içinde `registration` , `clientSecretSettingName` istemci gizliliğini depoladığınız uygulama ayarının adına ayarlanır.
    
    c. Nesnesi içinde `login` , `scopes` "ad" ve "e-posta" gibi Apple ile kimlik doğrulaması yaparken kullanılan kapsamların bir listesini içerecek şekilde diziyi ayarlamayı tercih edebilirsiniz. Kapsamlar yapılandırılırsa, kullanıcılar ilk kez oturum açtığında izin ekranında açıkça istenir.

Bu yapılandırma ayarlandıktan sonra, uygulamanızda kimlik doğrulaması için Apple sağlayıcınızı kullanmaya hazırsınız demektir.

Tüm yapılandırmalar aşağıdaki örneğe benzeyebilir (APPLE_GENERATED_CLIENT_SECRET ayarı, oluşturulan bir JWT içeren bir uygulama ayarına işaret eder):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Sonraki adımlar

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

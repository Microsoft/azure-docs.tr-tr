---
title: Korumalı Web API 'SI-uygulama kodu yapılandırması
titleSuffix: Microsoft identity platform
description: Korumalı bir Web API 'SI oluşturmayı ve uygulamanızın kodunu yapılandırmayı öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb562caf2dfc83841762748f37d1a7ee325de10b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882637"
---
# <a name="protected-web-api-code-configuration"></a>Korumalı Web API 'SI: kod yapılandırması

Korunan Web API 'niz için kodu yapılandırmak üzere API 'Leri korumalı olarak tanımlar, bir taşıyıcı belirteci yapılandırma ve belirtecin nasıl doğrulanacağı hakkında bilgi almanız gerekir.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>ASP.NET/ASP.NET Core API 'Leri korumalı olarak tanımlar?

Web Apps gibi, ASP.NET/ASP.NET Core Web API 'Leri, denetleyici eylemlerine `[Authorize]` özniteliğiyle ön eki eklendiği için "korumalıdır". Bu nedenle, denetleyici eylemleri yalnızca API 'nin yetkilendirilmiş bir kimlikle çağrılması durumunda çağrılabilir.

Aşağıdaki soruları göz önünde bulundurun:

- Web API 'SI onu çağıran uygulamanın kimliğini nasıl bilir? (Yalnızca bir uygulama, bir Web API 'SI çağırabilir.)
- Uygulama bir kullanıcı adına Web API 'SI olarak adlandırıldıysanız, kullanıcının kimliği nedir?

## <a name="bearer-token"></a>Taşıyıcı belirteci

Uygulamanın kimliği ve Kullanıcı hakkında bilgiler (Web uygulaması, bir Daemon uygulamasından hizmetten hizmete çağrılar kabul etmediği takdirde), uygulama çağrıldığında üst bilgide ayarlanan taşıyıcı belirtecinde tutulur.

Aşağıda, .NET C# Için Microsoft kimlik doğrulama kitaplığı ile belirteç aldıktan sonra API 'yi çağıran bir istemciyi gösteren bir kod örneği (msal.net):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Taşıyıcı belirteci, bir istemci uygulaması tarafından *Web API 'si Için*Microsoft Identity platform uç noktasına istendi. Web API 'SI, belirtecin doğrulanması ve içerdiği talepleri görüntülemesi gereken tek uygulamadır. İstemci uygulamaları, belirteçlerdeki talepleri incelemeye asla denememelidir. (Web API 'SI gelecekte belirtecin şifrelenmesini gerektirebilir. Bu gereksinim, erişim belirteçlerini görüntüleyebilen istemci uygulamalarına erişimi önler.)

## <a name="jwtbearer-configuration"></a>Jwttaşıyıcı yapılandırması

Bu bölümde, bir taşıyıcı belirtecinin nasıl yapılandırılacağı açıklanmaktadır.

### <a name="config-file"></a>Yapılandırma dosyası

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Kod başlatma

Bir uygulama bir `[Authorize]` özniteliği tutan bir denetleyici eyleminde çağrıldığında, ASP.NET/ASP.NET Core, çağıran isteğin yetkilendirme üstbilgisindeki taşıyıcı belirtecine bakar ve erişim belirtecini ayıklar. Belirteç daha sonra .NET için Microsoft IdentityModel uzantılarını çağıran Jwttaşıyıcı ara yazılıma iletilir.

ASP.NET Core, bu ara yazılım Startup.cs dosyasında başlatılır:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Bu yönerge, ara yazılım Web API 'sine eklenir:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Şu anda ASP.NET Core şablonları, kuruluşunuzda veya kişisel hesaplarla değil, kuruluşunuzdaki kullanıcıların oturum açmasını sağlayan Azure Active Directory (Azure AD) Web API 'Leri oluşturur. Ancak, bu kodu Startup.cs dosyasına ekleyerek, bunları Microsoft Identity platform uç noktasını kullanacak şekilde kolayca değiştirebilirsiniz:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Bu kod parçacığı, [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63)' deki ASP.NET Core Web API artımlı öğreticiden ayıklanır. Çok daha fazla `AddProtectedWebApi` yöntemi Startup.cs öğesinden çağrılır

## <a name="token-validation"></a>Belirteç doğrulama

Web Apps 'teki OpenID Connect ara yazılımı gibi Jwttaşıyıcı ara yazılımı, belirteci doğrulamak için `TokenValidationParameters` tarafından yönlendirilir. Belirtecin şifresi çözülür (gerektiğinde), talepler ayıklanır ve imza doğrulanır. Bu durumda, ara yazılım bu verileri denetleyerek belirteci doğrular:

- Web API 'sine (hedef kitle) yöneliktir.
- Web API 'sini (Sub) çağırmaya izin verilen bir uygulama için verilmıştı.
- Güvenilir bir güvenlik belirteci hizmeti (STS) tarafından verilmıştı (veren).
- Ömrü Aralık (süre sonu) olarak değişir.
- (İmza) ile oynanmadı.

Özel doğrulamalar de olabilir. Örneğin, imzalama anahtarlarının (bir belirteçte gömülü olduğunda) güvenildiğini ve belirtecin yeniden çalınmayacağını doğrulamak mümkündür. Son olarak, bazı protokoller belirli doğrulamalar gerektirir.

### <a name="validators"></a>Metninin

Doğrulama adımları, .NET açık kaynak kitaplığı [Için Microsoft IdentityModel uzantılarında](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) , tek bir kaynak dosyasında ( [Microsoft. IdentityModel. Tokens/doğrulayıcılar. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)), tümü olan doğrulayıcılar içinde yakalanır.

Doğrulayıcılar bu tabloda açıklanmıştır:

| Doğrulayıcı | Açıklama |
|---------|---------|
| `ValidateAudience` | Belirtecin belirteci doğrulayan uygulama için olduğunu sağlar (benim için). |
| `ValidateIssuer` | Belirtecin güvenilir bir STS tarafından (bir kişiden güvenmediğiniz) verilmiş olmasını sağlar. |
| `ValidateIssuerSigningKey` | Belirteç, belirteci imzalamak için kullanılan anahtara güvendiğini belirten uygulamayı sağlar. (Anahtarın belirtece gömülü olduğu özel durum. Genellikle gerekli değildir.) |
| `ValidateLifetime` | Belirtecin hala (veya zaten) geçerli olduğundan emin olur. Doğrulayıcı, belirtecin yaşam süresinin (`notbefore` ve `expires` talepler) Aralık içinde olup olmadığını denetler. |
| `ValidateSignature` | Belirtecin oynanmamasını sağlar. |
| `ValidateTokenReplay` | Belirtecin yeniden çalınmamasını sağlar. (Bazı kerelik protokolleri için özel durum kullanılır.) |

Doğrulayıcılar, ASP.NET/ASP.NET çekirdek yapılandırmasından başlatılan `TokenValidationParameters` sınıfının özellikleriyle ilişkilendirilir. Çoğu durumda, parametreleri değiştirmek zorunda kalmazsınız. Tek kiracılar olmayan uygulamalar için bir özel durum vardır. (Diğer bir deyişle, kullanıcıları herhangi bir kuruluştan veya kişisel Microsoft hesaplarından kabul eden Web Apps.) Bu durumda, verenin doğrulanması gerekir.

## <a name="token-validation-in-azure-functions"></a>Azure Işlevlerinde belirteç doğrulama

Azure işlevlerinde gelen erişim belirteçlerini doğrulamak da mümkündür. [DotNet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)ve [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)'daki Azure işlevlerinde belirteçleri doğrulama örneklerini bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kodunuzda kapsamları ve uygulama rollerini doğrulama](scenario-protected-web-api-verification-scope-app-roles.md)

---
title: Aşağı akış Web API 'Lerini (uygulamanın kod yapılandırması) çağıran Web API 'SI-Microsoft Identity platform
description: Web API 'Lerini (uygulamanın kod yapılandırması) çağıran bir Web API 'SI oluşturmayı öğrenin
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bae9f565dd37fbd3bcae38833662e13e0b7ac6d
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960645"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web API 'Lerini çağıran Web API 'SI-kod yapılandırması

Web API 'nizi kaydettikten sonra, uygulamanın kodunu yapılandırabilirsiniz.

Web API 'nizi, Web API 'SINI korumak için kullanılan kodun üstüne, akış Web API 'Lerini çağıran şekilde yapılandırma kodu. Daha fazla bilgi için bkz. [korumalı Web API-uygulama yapılandırması](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Ontokendoğrulamalı 'e abone olunan kod

Korunan Web API 'Leri için kod yapılandırmasının üstünde, API 'niz çağrıldığında alınan taşıyıcı belirtecinin doğrulanmasına abone olmanız gerekir:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>On-behalf-of akışı

AddAccountToCacheFromJwt () yöntemi şunları gerektirir:

- MSAL gizli istemci uygulaması örneğini oluşturun.
- Web API 'SI için istemci tarafından alınan taşıyıcı belirtecini, aynı kullanıcı için bir taşıyıcı belirtecine karşı, ancak API 'imizin bir aşağı akış API 'sini çağırması için bir taşıyıcı belirtece göre Exchange için `AcquireTokenOnBehalf` çağırın.

### <a name="instantiate-a-confidential-client-application"></a>Gizli bir istemci uygulaması örneği oluşturma

Bu akış yalnızca gizli istemci akışında kullanılabilir, böylece korunan Web API 'SI, sırasıyla `WithClientSecret` veya `WithCertificate` yöntemleri aracılığıyla istemci kimlik bilgilerini (istemci gizli anahtarı veya sertifikası) [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) için sağlar.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Son olarak, bir istemci parolası veya bir sertifika yerine gizli istemci uygulamaları, istemci onayları kullanarak kimliklerini kanıtlayabilirler.
Bu gelişmiş senaryo [istemci onaylamaları](msal-net-client-assertions.md) hakkında ayrıntılı

### <a name="how-to-call-on-behalf-of"></a>-Adına çağırma

Adına (OBO) çağrısı, `IConfidentialClientApplication` arabiriminde [Acquiretokenonon](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) yöntemi çağırarak yapılır.

`UserAssertion`, kendi istemcilerinden Web API 'SI tarafından alınan taşıyıcı belirtecinden oluşturulur. [İki Oluşturucu](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)vardır, bırı bir JWT taşıyıcı belirtecini alır ve bir tür Kullanıcı Onayı (başka bir tür güvenlik belirteci) alır ve bu tür, daha sonra `assertionType`) adlı ek bir parametrede belirtilir.

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Pratikte OBO akışı, genellikle bir aşağı akış API 'SI için belirteç almak ve MSAL.NET Kullanıcı belirteci önbelleğinde depolamak için kullanılır. böylece Web API 'sinin diğer bölümleri daha sonra aşağı akış API 'Lerini çağırmak için ``AcquireTokenOnSilent`` [Geçersiz Kılmalara](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) çağrı yapabilir. Bu çağrının, gerekirse belirteçleri yenileme etkisi vardır.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

Ayrıca, [NodeJS ve Azure işlevleri](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)'nde Flow uygulamasının adına bir örnek görebilirsiniz.

## <a name="protocol"></a>Protokol

Şirket içi protokol hakkında daha fazla bilgi için bkz. [Microsoft Identity platform ve OAuth 2,0-adına akışı](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama için bir belirteç alınıyor](scenario-web-api-call-api-acquire-token.md)

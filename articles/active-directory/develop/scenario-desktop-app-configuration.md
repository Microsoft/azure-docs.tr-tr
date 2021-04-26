---
title: Web API 'Lerini çağıran masaüstü uygulamalarını yapılandırma | Mavisi
titleSuffix: Microsoft identity platform
description: Web API 'Lerini çağıran bir masaüstü uygulamasının kodunu yapılandırmayı öğrenin
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 27ee58a19191c6f8232a62b8251816784a98d373
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799063"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Web API 'Lerini çağıran masaüstü uygulaması: kod yapılandırması

Uygulamanızı oluşturduğunuza göre, kodun uygulamanın koordinatlarıyla nasıl yapılandırılacağını öğreneceksiniz.

## <a name="microsoft-libraries-supporting-desktop-apps"></a>Masaüstü uygulamalarını destekleyen Microsoft kitaplıkları

Aşağıdaki Microsoft kitaplıkları masaüstü uygulamalarını destekler:

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="public-client-application"></a>Genel istemci uygulaması

Bir görünüm noktasından, masaüstü uygulamaları ortak istemci uygulamalardır. Yapılandırma etkileşimli kimlik doğrulaması kullanmanıza bakılmaksızın bir bit farklı olacaktır.

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET oluşturmanız ve güncelleştirmeniz gerekir `IPublicClientApplication` .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Yalnızca kodla

Aşağıdaki kod, bir ortak istemci uygulaması başlatır ve Microsoft Azure genel buluttaki kullanıcılara bir iş veya okul hesabı ya da kişisel Microsoft hesabı işaretler.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Daha önce görüldüğü gibi etkileşimli kimlik doğrulama veya cihaz kod akışı kullanmayı düşünüyorsanız, `.WithRedirectUri` değiştiricisini kullanın.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Yapılandırma dosyalarını kullanma

Aşağıdaki kod, bir yapılandırma nesnesinden program aracılığıyla doldurulabilen veya bir yapılandırma dosyasından okunan bir yapılandırma nesnesinden ortak bir istemci uygulaması başlatır.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Daha fazla ayrıntılı yapılandırma

Birçok değiştirici ekleyerek uygulama oluşturmayı ayrıntılı hale getirebilirsiniz. Örneğin, uygulamanızın ulusal bir bulutta, burada gösterilen ABD devleti gibi çok kiracılı bir uygulama olmasını istiyorsanız şunu yazabilirsiniz:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET ayrıca Active Directory Federasyon Hizmetleri (AD FS) 2019 için bir değiştirici içerir:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Son olarak, bir Azure Active Directory (Azure AD) B2C kiracısı için belirteçler edinmek istiyorsanız, aşağıdaki kod parçacığında gösterildiği gibi kiracınızı belirtin:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Daha fazla bilgi edinin

MSAL.NET masaüstü uygulamasını yapılandırma hakkında daha fazla bilgi edinmek için:

- Üzerinde kullanılabilen tüm değiştiricilerin bir listesi için `PublicClientApplicationBuilder` bkz. Reference documentation [Publicclientapplicationbuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- İçinde gösterilen tüm seçeneklerin bir açıklaması için `PublicClientApplicationOptions` başvuru belgelerindeki [Publicclientapplicationoptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) bölümüne bakın.

### <a name="complete-example-with-configuration-options"></a>Yapılandırma seçenekleriyle birlikte tüm örnek

Aşağıdaki yapılandırma dosyasına sahip bir .NET Core konsol uygulaması düşünün `appsettings.json` :

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Kullanarak bu dosyada okunması gereken küçük bir koddur. NET tarafından sağlanmış yapılandırma çerçevesi:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Şimdi, uygulamanızı oluşturmak için aşağıdaki kodu yazın:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Yönteme çağrıdan önce `.Build()` , `.WithXXX` daha önce görüldüğü gibi yöntemlere yapılan çağrılar ile yapılandırmanızı geçersiz kılabilirsiniz.

# <a name="java"></a>[Java](#tab/java)

Örnekleri yapılandırmak için MSAL Java geliştirme örneklerinde kullanılan sınıf şunlardır: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Aşağıdaki kod, bir ortak istemci uygulaması başlatır ve Microsoft Azure genel buluttaki kullanıcılara bir iş veya okul hesabı ya da kişisel Microsoft hesabı işaretler.

### <a name="quick-configuration"></a>Hızlı yapılandırma

Amaç-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

SWIFT
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Daha fazla ayrıntılı yapılandırma

Birçok değiştirici ekleyerek uygulama oluşturmayı ayrıntılı hale getirebilirsiniz. Örneğin, uygulamanızın ulusal bir bulutta, burada gösterilen ABD devleti gibi çok kiracılı bir uygulama olmasını istiyorsanız şunu yazabilirsiniz:

Amaç-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

SWIFT

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Yapılandırma parametreleri, bir JSON dosyası veya ortam değişkenleri gibi birçok kaynaktan yüklenebilir. Aşağıda bir *. env* dosyası kullanılır. 

```Text
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Info_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

*. Env* dosyasını ortam değişkenlerine yükleyin. MSAL düğümü, aşağıda belirtildiği gibi en az bir şekilde başlatılabilir. Kullanılabilir [yapılandırma seçeneklerine](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)bakın.  

```JavaScript
const { PublicClientApplication } = require('@azure/msal-node');

const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

clientApplication = new PublicClientApplication(MSAL_CONFIG);
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu senaryodaki bir sonraki makaleye geçiş yapın, [Masaüstü uygulaması için bir belirteç alın](scenario-desktop-acquire-token.md).
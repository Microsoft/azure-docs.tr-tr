---
title: Ortak istemci uygulaması örneğini oluşturma (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL.NET) kullanarak yapılandırma seçenekleriyle ortak bir istemci uygulamasını nasıl örnekleyeceğinizi öğrenin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3e2ffebf0b414d4b59178fe04fb109530365786b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064717"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET kullanarak yapılandırma seçenekleriyle ortak bir istemci uygulaması örneğini oluşturma

Bu makalede, .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) kullanılarak [genel bir istemci uygulamasının](msal-client-applications.md) nasıl oluşturulacağı açıklanır.  Uygulama, bir ayar dosyasında tanımlanan yapılandırma seçenekleriyle birlikte oluşturulur.

Uygulamayı başlatmadan önce, uygulamanızın Microsoft Identity platformu ile tümleştirilebilmesi için öncelikle [kaydetmeniz](quickstart-register-app.md) gerekir. Kayıttan sonra, aşağıdaki bilgiler (Azure portal bulunabilir) gerekebilir:

- İstemci KIMLIĞI (bir GUID 'YI temsil eden dize)
- Kimlik sağlayıcısı URL 'SI (örnek olarak adlandırılır) ve uygulamanız için oturum açma hedef kitlesi. Bu iki parametre, her topluca yetkili olarak bilinir.
- Yalnızca kuruluşunuz için bir iş kolu uygulaması yazıyorsanız (tek kiracılı uygulama olarak da adlandırılır) kiracı KIMLIĞI.
- Web uygulamaları için ve bazen genel istemci uygulamaları için (uygulamanızın bir aracı kullanması gerektiğinde), kimlik sağlayıcısının güvenlik belirteçleriyle uygulamanızı geri yükleyeceğim yeniden yönlendirilebilir.


Bir .NET Core konsol uygulaması yapılandırma dosyasında aşağıdaki *appsettings.js* olabilir:

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

Aşağıdaki kod, .NET yapılandırma çerçevesini kullanarak bu dosyayı okur:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
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

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Aşağıdaki kod, ayarlar dosyasındaki yapılandırmayı kullanarak uygulamanızı oluşturur:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```


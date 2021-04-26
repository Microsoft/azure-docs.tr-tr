---
title: Azure Media Services v3 API-.NET ' e bağlanın
description: Bu makalede, .NET ile Media Services v3 API 'sine nasıl bağlanulacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: e4d1ed0c015b75cc058c7d6136069a8858d835e2
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492537"
---
# <a name="connect-to-media-services-v3-api---net"></a>Media Services v3 API-.NET ' e bağlanın

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makalede hizmet sorumlusu oturum açma yöntemi kullanılarak Azure Media Services v3 .NET SDK 'sına nasıl bağlanabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- [Media Services hesabı oluşturun](./account-create-how-to.md). Kaynak grubu adını ve Media Services hesap adını hatırlayacağınızdan emin olun
- .NET geliştirme için kullanmak istediğiniz bir araç yükler. Bu makaledeki adımlarda, [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)'ın nasıl kullanılacağı gösterilmektedir. Visual Studio Code kullanabilirsiniz, bkz. [C# ile çalışma](https://code.visualstudio.com/docs/languages/csharp). Ya da farklı bir kod düzenleyicisi kullanabilirsiniz.

> [!IMPORTANT]
> [Adlandırma kurallarını](media-services-apis-overview.md#naming-conventions)gözden geçirin.

## <a name="create-a-console-application"></a>Konsol uygulaması oluşturma

1. Visual Studio’yu çalıştırın. 
1. **Dosya** menüsünde **Yeni**  >  **Proje**' ye tıklayın. 
1. **.NET Core** konsol uygulaması oluşturun.

Bu konudaki örnek uygulama, hedefler `netcoreapp2.0` . Kod, C# 7,1 ile başlayarak kullanılabilir olan ' Async Main ' kullanır. Daha fazla ayrıntı için bu [bloga](/archive/blogs/benwilli/async-main-is-available-but-hidden) bakın.

## <a name="add-required-nuget-packagesassemblies"></a>Gerekli NuGet paketlerini/derlemelerini ekleyin

1. Visual Studio 'da **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **NuGet Yönetici Konsolu**' nu seçin.
2. **Paket Yöneticisi konsolu** penceresinde, `Install-Package` aşağıdaki NuGet paketlerini eklemek için komutunu kullanın. Örneğin, `Install-Package Microsoft.Azure.Management.Media`.

|Paket|Description|
|---|---|
|`Microsoft.Azure.Management.Media`|SDK Azure Media Services. <br/>En son Azure Media Services paketini kullandığınızdan emin olmak için [Microsoft. Azure. Management. Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)' i kontrol edin.|

### <a name="other-required-assemblies"></a>Diğer gerekli derlemeler

- Azure. Storage. blob 'Ları
- Microsoft.Extensions.Configurlama
- Microsoft.Extensions.Configurlama. EnvironmentVariables
- Üzerinde Microsoft.Extensions.Configuration.Js
- Microsoft. Rest. ClientRuntime. Azure. Authentication

## <a name="create-and-configure-the-app-settings-file"></a>Uygulama ayarları dosyası oluşturma ve yapılandırma

### <a name="create-appsettingsjson"></a>appsettings.jsoluşturma

1. Git **genel**  >  **metin dosyası**.
1. "appsettings.json" olarak adlandırın.
1. . Json dosyasının "çıkış dizinine Kopyala" özelliğini "daha sonra Kopyala" olarak ayarlayın (böylece uygulamanın yayımlandığında erişebilmesi için).

### <a name="set-values-in-appsettingsjson"></a>Üzerinde appsettings.jsdeğerleri ayarlama

`az ams account sp create`Komutunu [erişim API 'lerinde](./access-api-howto.md)açıklandığı gibi çalıştırın. Komutu, "appsettings.json" uygulamanıza kopyalamanız gereken JSON döndürür.
 
## <a name="add-configuration-file"></a>Yapılandırma dosyasını ekleme

Kolaylık sağlaması için, "appsettings.json" kaynağından gelen değerleri okumaktan sorumlu bir yapılandırma dosyası ekleyin.

1. Projenize yeni bir. cs sınıfı ekleyin. Bunu, `ConfigWrapper` olarak adlandırın. 
1. Aşağıdaki kodu bu dosyaya yapıştırın (Bu örnek, ad alanının olduğunu varsayar `ConsoleApp1` ).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>.NET istemcisine bağlanma

.NET ile Media Services API’lerini kullanmaya başlamak için bir **AzureMediaServicesClient** nesnesi oluşturmanız gerekir. Nesneyi oluşturmak için, Azure AD kullanarak Azure’a bağlanmak üzere istemcinin ihtiyaç duyduğu kimlik bilgilerini sağlamanız gerekir. Aşağıdaki kodda, GetCredentialsAsync işlevi, yerel yapılandırma dosyasında sağlanan kimlik bilgilerine göre ServiceClientCredentials nesnesini oluşturur.

1. `Program.cs` dosyasını açın.
1. Aşağıdaki kodu yapıştırın:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: videoları karşıya yükleme, kodlama ve akışla-.NET](stream-files-tutorial-with-api.md) 
- [Öğretici: Media Services v3-.NET ile canlı akış](stream-live-tutorial-with-api.md)
- [Öğretici: Media Services v3-.NET ile videoları analiz etme](analyze-videos-tutorial.md)
- [Yerel dosyadan iş girişi oluşturma-.NET](job-input-from-local-file-how-to.md)
- [HTTPS URL 'sinden iş girişi oluşturma-.NET](job-input-from-http-how-to.md)
- [Özel dönüşümle kodlama-.NET](transform-custom-presets-how-to.md)
- [AES-128 dinamik şifrelemesini ve anahtar teslim hizmetini kullanın-.NET](drm-playready-license-template-concept.md)
- [DRM dinamik şifrelemesini ve lisans teslim hizmetini kullanma-.NET](drm-protect-with-drm-tutorial.md)
- [Var olan ilkeden bir imzalama anahtarı al-.NET](drm-get-content-key-policy-dotnet-how-to.md)
- [Media Services .NET ile filtre oluşturma](filters-dynamic-manifest-dotnet-how-to.md)
- [Media Services v3 ile Azure Işlevleri v2 'ye yönelik gelişmiş video isteğe bağlı örnekleri](https://aka.ms/ams3functions)

## <a name="see-also"></a>Ayrıca bkz.

* [.NET başvurusu](/dotnet/api/overview/azure/mediaservices/management)
* Daha fazla kod örneği için bkz. [.NET SDK örnek](https://github.com/Azure-Samples/media-services-v3-dotnet) deposu.

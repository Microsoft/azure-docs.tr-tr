---
title: .NET ile Azure Media Services API 'sine erişmek için Azure AD kimlik doğrulamasını kullanma | Microsoft Docs
description: Bu konuda, .NET ile Azure Media Services (AMS) API 'sine erişmek için Azure Active Directory (Azure AD) kimlik doğrulamasının nasıl kullanılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014001"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>.NET ile Azure Media Services API 'sine erişmek için Azure AD kimlik doğrulamasını kullanma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Windowsazure. mediaservices 4.0.0.4 ile başlayan Azure Media Services, Azure Active Directory (Azure AD) tabanlı kimlik doğrulamasını destekler. Bu konuda, Microsoft .NET ile Azure Media Services API 'sine erişmek için Azure AD kimlik doğrulamasının nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı. Ayrıntılar için bkz. [Azure Ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/).
- Bir Media Services hesabı. Daha fazla bilgi için [Azure Portal kullanarak Azure Media Services hesabı oluşturma](media-services-portal-create-account.md)konusuna bakın.
- En son [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) paketi.
- [Azure AD kimlik doğrulamasına genel bakış ile Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md)konusuyla benzerlik.

Azure Media Services ile Azure AD kimlik doğrulaması kullanırken, iki şekilde kimlik doğrulaması yapabilirsiniz:

- **Kullanıcı kimlik doğrulaması** , Azure Media Services kaynaklarla etkileşim kurmak için uygulamayı kullanan bir kişinin kimliğini doğrular. Etkileşimli uygulama öncelikle kullanıcıdan kimlik bilgilerini istemelidir. Bu örnek, yetkili kullanıcılar tarafından kodlama işlerini veya canlı akışı izlemek için kullanılan bir yönetim konsolu uygulamasıdır.
- **Hizmet sorumlusu kimlik doğrulaması** bir hizmetin kimliğini doğrular. Bu kimlik doğrulama yöntemini yaygın olarak kullanan uygulamalar, Web uygulamaları, işlev uygulamaları, Logic Apps, API 'Ler veya mikro hizmetler gibi Daemon Hizmetleri, orta katman hizmetler veya zamanlanan işleri çalıştıran uygulamalardır.

>[!IMPORTANT]
>Azure Media Service şu anda bir Azure Access Control Service kimlik doğrulama modelini desteklemektedir. Ancak, Access Control yetkilendirmesi 22 Haziran 2018 ' de kullanım dışı olacaktır. Azure Active Directory bir kimlik doğrulama modeline mümkün olan en kısa sürede geçiş yapmanızı öneririz.

## <a name="get-an-azure-ad-access-token"></a>Azure AD erişim belirteci alma

Azure AD kimlik doğrulamasıyla Azure Media Services API 'sine bağlanmak için, istemci uygulamanın bir Azure AD erişim belirteci istemesi gerekir. Media Services .NET istemci SDK 'sını kullandığınızda, bir Azure AD erişim belirtecinin nasıl ele alınacağını gösteren pek çok ayrıntı, [Azureadtokenprovider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) ve [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) sınıflarında sizin için sarmalanır ve basitleştirilmiştir.

Örneğin, Azure AD yetkilisini, Media Services kaynak URI 'sini veya yerel Azure AD uygulama ayrıntılarını sağlamanız gerekmez. Bunlar, Azure AD erişim belirteci sağlayıcısı sınıfı tarafından zaten yapılandırılmış olan iyi bilinen değerlerdir.

Azure Media Service .NET SDK kullanmıyorsanız [Azure AD kimlik doğrulama kitaplığı](../../active-directory/azuread-dev/active-directory-authentication-libraries.md)' nı kullanmanızı öneririz. Azure AD kimlik doğrulama kitaplığı ile kullanmanız gereken parametrelerin değerlerini almak için bkz. [Azure AD kimlik doğrulaması ayarlarına erişmek için Azure Portal kullanma](media-services-portal-get-started-with-aad.md).

Ayrıca, **Azureadtokenprovider** öğesinin varsayılan uygulamasını kendi uygulamanız ile değiştirme seçeneğiniz de vardır.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Azure Media Services .NET SDK 'sını yükleyip yapılandırın

>[!NOTE]
>Media Services .NET SDK ile Azure AD kimlik doğrulamasını kullanmak için en son [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) paketine sahip olmanız gerekir. Ayrıca, **Microsoft. IdentityModel. clients. ActiveDirectory** derlemesine bir başvuru ekleyin. Mevcut bir uygulamayı kullanıyorsanız, **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** derlemesini dahil edin.

1. Visual Studio 'da yeni bir C# konsol uygulaması oluşturun.
2. **.NET SDK Azure Media Services** yüklemek için [windowsazure. mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet paketini kullanın.

    NuGet kullanarak başvuru eklemek için aşağıdaki adımları uygulayın: **Çözüm Gezgini**, proje adına sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin. Ardından, **windowsazure. mediaservices** araması yapın ve **yüklemeyi** seçin.

    -veya-

    Visual Studio 'da **Paket Yöneticisi konsolunda** aşağıdaki komutu çalıştırın.

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. Kaynak kodunuza **kullanarak** ekleyin.

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>Kullanıcı kimlik doğrulaması kullan

Azure Media Service API 'sine Kullanıcı kimlik doğrulaması seçeneğiyle bağlanmak için, istemci uygulamanın aşağıdaki parametreleri kullanarak bir Azure AD belirteci istemesi gerekir:

- Azure AD kiracı uç noktası. Kiracı bilgileri Azure portal elde edilebilir. Sağ üst köşedeki oturum açmış kullanıcının üzerine gelin.
- Kaynak URI 'sini Media Services.
- Media Services (yerel) uygulama istemci KIMLIĞI.
- Media Services (yerel) uygulama yeniden yönlendirme URI 'SI.

Bu parametrelerin değerleri **AzureEnvironments. AzureCloudEnvironment** içinde bulunabilir. **AzureEnvironments. AzureCloudEnvironment** sabiti, bir genel Azure veri merkezine yönelik doğru ortam değişkeni ayarlarını almak için .NET SDK 'sında yardımcı olur.

Yalnızca ortak veri merkezlerinde Media Services erişmek için önceden tanımlanmış ortam ayarlarını içerir. Sogeign veya kamu bulut bölgeleri için sırasıyla **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** veya **AzureGermanCloudEnvironment** kullanabilirsiniz.

Aşağıdaki kod örneği bir belirteç oluşturur:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Programlamaya Media Services karşı başlamak için sunucu bağlamını temsil eden bir **Cloudmediacontext** örneği oluşturmanız gerekir. **Cloudmediacontext** , işler, varlıklar, dosyalar, erişim ilkeleri ve Konumlandırıcı dahil olmak üzere önemli koleksiyonlara yönelik başvurular içerir.

Ayrıca, **medya Rest Hizmetleri için kaynak URI** 'Sini **cloudmediacontext** oluşturucusuna geçirmeniz gerekir. Medya REST Hizmetleri için kaynak URI 'sini almak için Azure portal oturum açın, Azure Media Services hesabınızı seçin, **API erişimi**' ni seçin ve ardından **kullanıcı kimlik doğrulamasıyla Azure Media Services Bağlan**' ı seçin.

Aşağıdaki kod örneği bir **Cloudmediacontext** örneği oluşturur:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Aşağıdaki örnek, Azure AD belirtecinin ve bağlamının nasıl oluşturulacağını gösterir:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }
        }
    }
}
```

>[!NOTE]
>"Uzak sunucu bir hata döndürdü: (401) yetkilendirilmemiş" ifadesini içeren bir özel durum alırsanız, Azure AD kimlik doğrulamasına genel bakış ile Azure Media Services API 'sine erişme konusunun [erişim denetimi](media-services-use-aad-auth-to-access-ams-api.md#access-control) bölümüne bakın.

## <a name="use-service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulamasını kullanma

Hizmet sorumlusu seçeneğiyle Azure Media Services API 'sine bağlanmak için, orta katman uygulamanızın (Web API 'SI veya Web uygulaması) aşağıdaki parametrelerle bir Azure AD belirteci istemesi gerekir:

- Azure AD kiracı uç noktası. Kiracı bilgileri Azure portal elde edilebilir. Sağ üst köşedeki oturum açmış kullanıcının üzerine gelin.
- Kaynak URI 'sini Media Services.
- Azure AD uygulama değerleri: **ISTEMCI kimliği** ve **istemci parolası**.

**ISTEMCI kimliği** ve **istemci gizli** parametrelerinin değerleri Azure Portal bulunabilir. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure AD kimlik doğrulaması ile çalışmaya](media-services-portal-get-started-with-aad.md)başlama.

Aşağıdaki kod örneği, bir parametre olarak **Azureadclientsymmetrickey** alan **AzureAdTokenCredentials** oluşturucusunu kullanarak bir belirteç oluşturur:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Ayrıca, **Azureadclientcertificate** öğesini parametre olarak alan **AzureAdTokenCredentials** oluşturucusunu belirtebilirsiniz.

Azure AD tarafından kullanılabilecek bir formda sertifika oluşturma ve yapılandırma hakkında yönergeler için bkz. [sertifikalarla Azure AD 'de kimlik doğrulaması, sertifikalarla birlikte, el ile yapılandırma adımları](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2).

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

Programlamaya Media Services karşı başlamak için sunucu bağlamını temsil eden bir **Cloudmediacontext** örneği oluşturmanız gerekir. Ayrıca, **medya Rest Hizmetleri için kaynak URI** 'Sini **cloudmediacontext** oluşturucusuna geçirmeniz gerekir. **Medya Rest Hizmetleri için kaynak URI** 'sini Azure Portal de alabilirsiniz.

Aşağıdaki kod örneği bir **Cloudmediacontext** örneği oluşturur:

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Aşağıdaki örnek, Azure AD belirtecinin ve bağlamının nasıl oluşturulacağını gösterir:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                        AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Hesabınıza dosya yüklemeye](media-services-dotnet-upload-files.md)başlayın.

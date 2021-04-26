---
title: Media Services V2 'den v3 'e geçiş kurulumu
description: Bu makale, Azure Media Services V2 'den v3 'e geçiş için ortamınızı ayarlamanıza yardımcı olur.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, geçiş, akış, yayın, canlı, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 9d61e9ff753c37268be19e95db9450e2cd923d96
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279689"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>3. adım-v3 REST API veya istemci SDK 'sına geçirilecek şekilde ayarlama

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![geçiş adımları 2](./media/migration-guide/steps-3.svg)

Aşağıda, ortamınızı Media Services v3 API 'sini kullanacak şekilde ayarlamak için gereken adımlar açıklanmaktadır.

## <a name="sdk-model"></a>SDK modeli

V2 API 'sinde, biri yönetim API 'SI için bir tane olmak üzere, hesap oluşturmaya izin verilen ve kaynak yönetimi için bir tane olmak üzere iki farklı istemci SDK 'Sı vardı.

Daha önce, geliştiriciler bir Azure hizmet sorumlusu istemci KIMLIĞI ve istemci gizli anahtarı ile birlikte çalışarak, AMS hesabı için belirli bir v2 REST API uç noktası ile çalışır.

V3 API 'SI, Azure Kaynak Yönetimi (ARM) tabanlıdır. API 'ye bağlanmak için Azure Active Directory (Azure AD) hizmet sorumlusu kimliklerini ve anahtarlarını kullanır. Geliştiricilerin, API 'ye bağlanmak için hizmet sorumluları veya yönetilen kimlikler oluşturması gerekir. V3 API 'sinde, API standart ARM uç noktaları kullanır, diğer tüm Azure hizmetlerine benzer ve tutarlı bir model kullanır.

Daha önce ARM yönetim API 'sinin 2015-10-01 sürümünü kullanan müşteriler, v2 hesaplarını yönetmek için desteklenen ARM yönetim API 'sinin 2020-05-01 sürümünü kullanmalıdır, v3 API erişimi için desteklenir.

## <a name="create-a-new-media-services-account-for-testing"></a>Test için yeni bir Media Services hesabı oluşturun

Azure portal kullanarak [ortamınızı ayarlamaya](setup-azure-subscription-how-to.md?tabs=portal) yönelik hızlı başlangıç adımlarını izleyin. Bu test hesabıyla kullanılmak üzere yeni bir Azure AD uygulama KIMLIĞI ve gizli dizileri oluşturmak için API erişimi ve hizmet sorumlusu kimlik doğrulaması ' nı seçin.

[Bir Media Services hesabı oluşturun](account-create-how-to.md?tabs=portal).
[MEDIA SERVICES API 'sine erişmek için kimlik bilgilerini alın](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>İstediğiniz istemci SDK 'sını indirin ve ortamınızı ayarlayın

-  [.Net](/dotnet/api/overview/azure/mediaservices/management), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](/python/api/overview/azure/mediaservices/management), [Java](/java/api/overview/azure/mediaservices/management), [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)ve [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)için kullanılabilir SDK 'lar.
- [Azure CLI](/cli/azure/ams)   basit betik desteği için tümleştirme.

> [!NOTE]
> Artık v3 üzerinde Azure Media Services için bir topluluk PHP SDK 'Sı bulunmamaktadır. V2 'de PHP kullanıyorsanız, kodunuzda doğrudan REST API geçirmeniz gerekir.

## <a name="open-api-specifications"></a>API belirtimlerini aç

- V3, Azure Resource Manager üzerinde oluşturulmuş yönetim ve işlem işlevlerini kullanıma sunan Birleşik bir API yüzeyini temel alır. Azure Resource Manager şablonlar, dönüşümler, akış uç noktaları, canlı olaylar ve daha fazlasını oluşturmak ve dağıtmak için kullanılabilir.

- [Openapı belirtimi](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (eski adıyla Swagger) belgesi, tüm hizmet bileşenlerinin şemasını açıklar.

- Tüm istemci SDK 'Ları GitHub 'da yayımlanan açık API belirtiminde türetilir ve oluşturulur. Bu makalenin yayımlanması sırasında, en son açık API belirtimleri GitHub 'da genel olarak korunur. 2020-05-01 sürümü [en son kararlı sürümdür](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Media Services v3 REST API çağrıları için [Postman](./setup-postman-rest-how-to.md) kullanın.
[REST API başvuru sayfalarını](/rest/api/media/)okuyun.

Postman koleksiyonunda 2020-05-01 sürüm dizesini kullanmanız gerekir.

## <a name="net"></a>[.NET](#tab/net)

Ortamınızı ayarlamak için, [.NET ile Media Services v3 API 'ye bağlanın](configure-connect-dotnet-howto.md) .

Yalnızca PackageManager 'ı kullanarak en son SDK 'yı yüklemek istiyorsanız aşağıdaki komutu kullanın:

```Install-Package Microsoft.Azure.Management.Media```

Veya .NET CLı kullanarak en son SDK 'Yı yüklemek için aşağıdaki komutu kullanın:

```dotnet add package Microsoft.Azure.Management.Media```

Ayrıca, çeşitli senaryolar için [Azure-Samples/Media-Services-v3-DotNet](https://github.com/Azure-Samples/media-services-v3-dotnet) ' de tam .NET örnekleri mevcuttur. Bu depodaki projeler, v3 sürümünü kullanarak farklı Azure Media Services senaryolarına nasıl uygulanacağını gösterir.

### <a name="get-started-adjusting-your-code"></a>Kodunuzu ayarlamaya başlayın

`CloudMediaContext`V3 API 'sine yükseltme işlemini başlatmak için kod tabanınızı kullanım örnekleri için arayın.

Aşağıdaki kod, v2 API 'sinin daha önce v2 .NET SDK kullanılarak nasıl erişildiğini gösterir. Geliştiriciler bir nesnesi oluşturarak ve bir `CloudMediaContext` nesne ile bir örnek oluşturmaya başlar `AzureAdTokenCredentials` .

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Ortamınızı ayarlamak için, [Java ile Media Services v3 API 'Sine bağlanın](configure-connect-java-howto.md) .

## <a name="python"></a>[Python](#tab/python)

Ortamınızı ayarlamak için [Azure Media Services v3 API-Python](configure-connect-python-howto.md) ' a bağlanın. makaleyi okuyun.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ortamınızı ayarlamak için [Azure Media Services v3 API 'Sine bağlanın-Node.js](configure-connect-nodejs-howto.md) makalesini okuyun.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- GitHub 'da [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK 'sını alın.

## <a name="go"></a>[Git](#tab/go)

[Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK 'sını indirin.

---

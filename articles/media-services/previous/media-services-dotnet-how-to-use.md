---
title: Bilgisayarı .NET ile Media Services geliştirme için ayarlama
description: .NET için Media Services SDK 'sını kullanarak Media Services önkoşulları hakkında bilgi edinin. Ayrıca, Visual Studio uygulaması oluşturmayı öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a2a9ecbcafa89b4981fe403d116ac0288e60b6b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013899"
---
# <a name="media-services-development-with-net"></a>.NET ile Media Services geliştirme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Bu makalede, .NET kullanarak Media Services uygulamaları geliştirmeye nasıl başlayabileceği açıklanır.

**Azure Media Services .NET SDK** kitaplığı, .net kullanarak Media Services karşı programlama yapmanızı sağlar. .NET ile geliştirmeyi daha da kolaylaştırmak için **Azure Media Services .NET SDK uzantıları** kitaplığı sağlanır. Bu kitaplık, .NET kodunuzu basitleştirecek bir uzantı yöntemleri ve yardımcı işlevler kümesi içerir. Her iki kitaplık da **NuGet** ve **GitHub** aracılığıyla kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar
* Yeni veya mevcut bir Azure aboneliğinde bir Media Services hesabı. [Media Services hesabı oluşturma](media-services-portal-create-account.md)makalesine bakın.
* İşletim sistemleri: Windows 10, Windows 7, Windows 2008 R2 veya Windows 8.
* .NET Framework 4,5 veya üzeri.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma
Bu bölümde, Visual Studio 'da bir proje oluşturma ve Media Services geliştirme için ayarlama gösterilmektedir.  Bu durumda, proje bir C# Windows konsol uygulamasıdır, ancak burada gösterilen kurulum adımları, Media Services uygulamalar için oluşturabileceğiniz diğer proje türleri için de geçerlidir (örneğin, bir Windows Forms uygulaması veya bir ASP.NET Web uygulaması).

Bu bölümde, Media Services .NET SDK uzantıları ve diğer bağımlı kitaplıkları eklemek için **NuGet** 'in nasıl kullanılacağı gösterilmektedir.

Alternatif olarak, GitHub 'dan ([GitHub.com/Azure/Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services) veya [GitHub.com/Azure/Azure-SDK-for-Media-Services-Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)) en son Media Services .NET SDK bitlerini alabilir, çözümü oluşturabilir ve başvuruları istemci projesine ekleyebilirsiniz. Tüm gerekli bağımlılıklar otomatik olarak indirilir ve ayıklanır.

1. Visual Studio’da yeni bir C# Konsol Uygulaması oluşturun. **Ad**, **konum** ve **çözüm adını** girin ve ardından Tamam ' a tıklayın.
2. Çözümü derleyin.
3. **Azure Media Services .NET SDK uzantıları** yüklemek ve eklemek için **NuGet** kullanın (**windowsazure. mediaservices. Extensions**). Bu paketin yüklenmesiyle **Media Services .NET SDK** da yüklenir ve diğer tüm gerekli bağımlılıklar eklenir.
   
    NuGet 'in en yeni sürümünün yüklü olduğundan emin olun. Daha fazla bilgi ve yükleme yönergeleri için bkz. [NuGet](https://nuget.codeplex.com/).

    1. Çözüm Gezgini, projenin adına sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

    2. NuGet Paketlerini Yönet iletişim kutusu görünür.

    3. Çevrimiçi galeride Azure MediaServices uzantıları için arama yapın **Azure Media Services .NET SDK uzantıları** ' nı (**windowsazure. mediaservices. Extensions**) seçin ve ardından **Install** düğmesine tıklayın.
   
    4. Proje değiştirilir ve Media Services .NET SDK uzantıları, Media Services .NET SDK ve diğer bağımlı derlemeler eklenir.
4. Bir temizleyici geliştirme ortamını yükseltmek için, NuGet paket geri yüklemeyi etkinleştirmeyi göz önünde bulundurun. Daha fazla bilgi için bkz. [NuGet paketini geri yükleme "](https://docs.nuget.org/consume/package-restore).
5. **System.Configurlama** derlemesine bir başvuru ekleyin. Bu derleme, System.Configurnu içerir. Yapılandırma dosyalarına erişmek için kullanılan (örneğin, App.config **) sınıf.**
   
    1. Başvuruları Yönet iletişim kutusunu kullanarak başvurular eklemek için Çözüm Gezgini proje adına sağ tıklayın. Ardından **Ekle**' ye ve ardından **başvuru...** öğesine tıklayın.
   
    2. Başvuruları Yönet iletişim kutusu görüntülenir.
    3. .NET Framework derlemeleri altında, System.Configurlama derlemesini bulun ve seçin ve **Tamam**' a basın.
6. App.config dosyasını açın ve dosyaya bir **appSettings** bölümü ekleyin. Media Services API 'sine bağlanmak için gereken değerleri ayarlayın. Daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure Media Services API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

    **Hizmet sorumlusu** kimlik doğrulama yöntemini kullanarak bağlanmak için gereken değerleri ayarlayın.

    ```xml
    <configuration>
    ...
        <appSettings>
            <add key="AMSAADTenantDomain" value="tenant"/>
            <add key="AMSRESTAPIEndpoint" value="endpoint"/>
            <add key="AMSClientId" value="id"/>
            <add key="AMSClientSecret" value="secret"/>
        </appSettings>
    </configuration>
    ```

7. Projenize **System.Configuration** başvurusunu ekleyin.
8. Program. cs dosyasının başındaki mevcut **using** deyimlerinin üzerine aşağıdaki kodu yazın:

    ```csharp      
    using System;
    using System.Configuration;
    using System.IO;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Collections.Generic;
    using System.Linq;
    ```

    Bu noktada, bir Media Services uygulaması geliştirmeye başlamaya başlayabilirsiniz.    

## <a name="example"></a>Örnek

AMS API 'sine bağlanan ve tüm kullanılabilir medya Işlemcilerini listeleyen küçük bir örnek aşağıda verilmiştir.

```csharp
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
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
        // List all available Media Processors
        foreach (var mp in _context.MediaProcessors)
        {
            Console.WriteLine(mp.Name);
        }
        
    }
 ```

## <a name="next-steps"></a>Sonraki adımlar

Artık [AMS API 'sine bağlanabilir](media-services-use-aad-auth-to-access-ams-api.md) ve [geliştirmeye](media-services-dotnet-get-started.md)başlayabilirsiniz.


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

---
title: .NET Core ile Azure Uygulama yapılandırması için hızlı başlangıç | Microsoft Docs
description: .NET Core uygulamalarıyla Azure Uygulama yapılandırması 'nı kullanmaya yönelik hızlı başlangıç
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 149542965737b2703bc18e985bfaf4d90c8dab29
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709407"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Hızlı başlangıç: uygulama yapılandırmasıyla .NET Core uygulaması oluşturma

Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure uygulama yapılandırmasını bir .NET Core konsol uygulamasına katabilirsiniz.

## <a name="prerequisites"></a>Prerequisites

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Aşağıdaki anahtar-değer çiftlerini eklemek için **yapılandırma gezgini** >  **+ Oluştur** ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

## <a name="create-a-net-core-console-app"></a>.NET Core konsol uygulaması oluşturma

[.NET Core komut satırı arabirimi 'ni (CLI)](https://docs.microsoft.com/dotnet/core/tools/) kullanarak yeni bir .NET Core konsol uygulaması projesi oluşturun. Visual Studio üzerinden .NET Core CLI kullanmanın avantajı, Windows, macOS ve Linux platformları genelinde kullanılabilir hale gelir.

1. Projeniz için yeni bir klasör oluşturun.

2. Yeni klasörde, yeni bir ASP.NET Core konsol uygulama projesi oluşturmak için aşağıdaki komutu çalıştırın:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. Aşağıdaki komutu çalıştırarak `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet paketine bir başvuru ekleyin:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 2.0.0-preview-009470001-1371

2. Projenizin paketlerini geri yüklemek için aşağıdaki komutu çalıştırın:

        dotnet restore

3. *Program.cs*'i açın ve .NET Core uygulama yapılandırma sağlayıcısına bir başvuru ekleyin.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. @No__t-0 yöntemini, `builder.AddAzureAppConfiguration()` yöntemini çağırarak uygulama yapılandırmasını kullanacak şekilde güncelleştirin.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. **ConnectionString**adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Konsol uygulamasını derlemek için aşağıdaki komutu çalıştırın:

        dotnet build

3. Oluşturma başarıyla tamamlandıktan sonra, uygulamayı yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

        dotnet run

## <a name="clean-up-resources"></a>Kaynakları Temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, yeni bir uygulama yapılandırma deposu oluşturdunuz ve [uygulama yapılandırma sağlayıcısı](https://go.microsoft.com/fwlink/?linkid=2074664)aracılığıyla bir .NET Core konsol uygulamasıyla kullandınız. Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi edinmek için, kimlik doğrulamasını gösteren bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

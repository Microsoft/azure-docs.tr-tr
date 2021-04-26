---
title: .NET Core ile Azure Uygulama yapılandırması için hızlı başlangıç | Microsoft Docs
description: Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure Uygulama yapılandırması ile bir .NET Core uygulaması oluşturun.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 0ff80287971365b1477be319dc7a04760687f6a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663409"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Hızlı başlangıç: uygulama yapılandırmasıyla .NET Core uygulaması oluşturma

Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure uygulama yapılandırmasını bir .NET Core konsol uygulamasına katabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/dotnet)
- [.NET Core SDK](https://dotnet.microsoft.com/download) , [Azure Cloud Shell](https://shell.azure.com)de mevcuttur.

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  Aşağıdaki anahtar-değer çiftlerini eklemek için yapılandırma Gezgini **anahtar değeri** oluştur ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

8. **Uygula**’yı seçin.

## <a name="create-a-net-core-console-app"></a>.NET Core konsol uygulaması oluşturma

[.NET Core komut satırı arabirimi 'ni (CLI)](/dotnet/core/tools/) kullanarak yeni bir .NET Core konsol uygulaması projesi oluşturun. Visual Studio üzerinden .NET Core CLI kullanmanın avantajı, Windows, macOS ve Linux platformları genelinde kullanılabilir hale gelir.  Alternatif olarak, [Azure Cloud Shell](https://shell.azure.com)bulunan önceden yüklenmiş araçları kullanın.

1. Projeniz için yeni bir klasör oluşturun.

2. Yeni bir klasörde, yeni bir .NET Core konsol uygulaması projesi oluşturmak için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. `Microsoft.Extensions.Configuration.AzureAppConfiguration`Aşağıdaki komutu çalıştırarak NuGet paketine bir başvuru ekleyin:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Projenizin paketlerini geri yüklemek için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet restore
    ```

3. *Program. cs*' yi açın ve .NET Core uygulama yapılandırma sağlayıcısına bir başvuru ekleyin.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Yöntemini `Main` çağırarak uygulama yapılandırmasını kullanmak için yöntemi güncelleştirin `builder.AddAzureAppConfiguration()` .

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

1. **ConnectionString** adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Komut satırında aşağıdaki komutu çalıştırın:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Değişikliğin etkili olması için komut istemi ' ni yeniden başlatın. Doğru ayarlandığını doğrulamak için ortam değişkeninin değerini yazdırın.

2. Konsol uygulamasını derlemek için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet build
    ```

3. Oluşturma başarıyla tamamlandıktan sonra, uygulamayı yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, yeni bir uygulama yapılandırma deposu oluşturdunuz ve [uygulama yapılandırma sağlayıcısı](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)aracılığıyla bir .NET Core konsol uygulamasıyla kullandınız. .NET Core uygulamanızı yapılandırma ayarlarını dinamik olarak yenilemek üzere nasıl yapılandıracağınızı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Dinamik yapılandırmayı etkinleştirme](./enable-dynamic-configuration-dotnet-core.md)
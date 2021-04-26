---
title: 'Öğretici: .NET Core uygulamasında dinamik yapılandırma kullanma'
titleSuffix: Azure App Configuration
description: Bu öğreticide, .NET Core uygulamaları için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edineceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: jken
ms.openlocfilehash: bb0c83536f8973a07d656844439a8816dea83346
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625715"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Öğretici: .NET Core uygulamasında dinamik yapılandırma kullanma

Uygulama yapılandırması .NET Core istemci kitaplığı, bir uygulamanın yeniden başlatılmasına neden olmadan isteğe bağlı yapılandırmayı güncelleştirmeyi destekler. Bu, öncelikle yapılandırma sağlayıcısına ait seçeneklerden bir örneği alarak `IConfigurationRefresher` ve sonra `TryRefreshAsync` Bu örneğe kodunuzda herhangi bir yere çağrı yaparak uygulanabilir.

Ayarları güncel tutmak ve yapılandırma deposuna çok fazla çağrı önlemek için, her bir ayar için bir önbellek kullanılır. Bir ayarın önbelleğe alınmış değeri sona erene kadar, yenileme işlemi değeri, değer yapılandırma deposunda değiştiği zaman bile güncelleştirmez. Her istek için varsayılan kullanım süresi 30 saniyedir, ancak gerekirse geçersiz kılınabilir.

Bu öğreticide, kodunuzda dinamik yapılandırma güncelleştirmelerini nasıl uygulayabileceğinizi gösterir. Hızlı başlangıçlarda tanıtılan uygulamada oluşturulur. Devam etmeden önce, önce [uygulama yapılandırması ile bir .NET Core uygulaması oluşturun](./quickstart-dotnet-core-app.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. [Visual Studio Code](https://code.visualstudio.com/) , Windows, MacOS ve Linux platformlarında kullanılabilen harika bir seçenektir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir uygulama yapılandırma deposundaki değişikliklere yanıt olarak yapılandırmasını güncelleştirmek için .NET Core uygulamanızı ayarlayın.
> * Uygulamanızdaki en son yapılandırmayı tükettin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi yapmak için [.NET Core SDK](https://dotnet.microsoft.com/download)' yi yüklemelisiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Uygulama yapılandırmasından verileri yeniden yükleme

*Program. cs* dosyasını açın ve ad alanına bir başvuru eklemek `System.Threading.Tasks` , yöntemde yenileme yapılandırmasını belirtmek `AddAzureAppConfiguration` ve yöntemi kullanarak el ile yenilemeyi tetiklemek için dosyayı güncelleştirin `TryRefreshAsync` .

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

`ConfigureRefresh`Yöntemi, bir yenileme işlemi tetiklendiğinde, yapılandırma verilerini uygulama yapılandırma deposu ile güncelleştirmek için kullanılan ayarları belirtmek için kullanılır. Bir örneği `IConfigurationRefresher` `GetRefresher` yöntemine sunulan seçeneklere çağrı yöntemiyle alınabilir `AddAzureAppConfiguration` ve `TryRefreshAsync` Bu örnekteki yöntem kodunuzda herhangi bir yerde yenileme işlemini tetiklemek için kullanılabilir.
    
> [!NOTE]
> Yapılandırma ayarı için varsayılan önbellek süre sonu zamanı 30 saniyedir, ancak yöntem `SetCacheExpiration` için bağımsız değişken olarak geçirilen seçenek başlatıcısında yöntemi çağırarak geçersiz kılınabilir `ConfigureRefresh` .

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. **ConnectionString** adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Konsol uygulamasını derlemek için aşağıdaki komutu çalıştırın:

    ```console
     dotnet build
    ```

1. Oluşturma başarıyla tamamlandıktan sonra, uygulamayı yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```console
     dotnet run
    ```

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/dotnet-core-app-run.png)

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin ve aşağıdaki anahtarların değerlerini güncelleştirin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından alınan veriler-güncelleştirildi |

1. Yenileme tetiklemek ve komut Istemi veya PowerShell penceresinde güncelleştirilmiş değeri yazdırmak için ENTER tuşuna basın.

    ![Hızlı başlangıç uygulaması yerel yenileme](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Önbellek sona erme zamanı, `SetCacheExpiration` yenileme işlemi için yapılandırmayı belirtirken yöntemi kullanılarak 10 saniye olarak ayarlandığından, yapılandırma ayarı değeri yalnızca bu ayar için en son yenilemeden bu yana en az 10 saniye geçtiğinde güncelleştirilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasından yapılandırma ayarlarını dinamik olarak yenilemek için .NET Core uygulamanızı etkinleştirdiniz. Azure yönetilen kimliğin uygulama yapılandırmasına erişimi kolaylaştırmak için nasıl kullanılacağını öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

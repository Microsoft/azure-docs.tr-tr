---
title: '.NET Framework öğreticisi: Azure Uygulama yapılandırmasında dinamik yapılandırma'
description: Bu öğreticide, Azure Uygulama yapılandırması 'nı kullanarak .NET Framework uygulamaları için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edineceksiniz.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932770"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Öğretici: .NET Framework uygulamasında dinamik yapılandırmayı kullanma

Uygulama yapılandırması .NET istemci kitaplığı, bir uygulamanın yeniden başlatılmasına gerek kalmadan isteğe bağlı olarak bir yapılandırma ayarları kümesinin güncelleştirilmesini destekler. Bu, öncelikle yapılandırma sağlayıcısına ait seçeneklerden bir örneği alarak `IConfigurationRefresher` ve sonra `TryRefreshAsync` Bu örneğe kodunuzda herhangi bir yere çağrı yaparak uygulanabilir.

Ayarları güncel tutmak ve yapılandırma deposuna çok fazla çağrı önlemek için, her bir ayar için bir önbellek kullanılır. Bir ayarın önbelleğe alınmış değeri sona erene kadar, yenileme işlemi değeri, değer yapılandırma deposunda değiştiği zaman bile güncelleştirmez. Her istek için varsayılan kullanım süresi 30 saniyedir, ancak gerekirse geçersiz kılınabilir.

Bu öğreticide, kodunuzda dinamik yapılandırma güncelleştirmelerini nasıl uygulayabileceğinizi gösterir. Hızlı başlangıçlarda tanıtılan uygulamada oluşturulur. Devam etmeden önce, önce [uygulama yapılandırması ile bir .NET Framework uygulaması oluşturun](./quickstart-dotnet-app.md) .

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama yapılandırma deposundaki değişikliklere yanıt olarak yapılandırmasını güncelleştirmek için .NET Framework uygulamanızı ayarlayın.
> * Uygulamanıza en son yapılandırmayı ekleme.
## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 veya üzeri](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  Aşağıdaki anahtar-değer çiftlerini eklemek için yapılandırma Gezgini +**anahtar-değer** oluştur ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

8. **Uygula**’yı seçin.

## <a name="create-a-net-framework-console-app"></a>.NET Framework konsol uygulaması oluşturma

1. Visual Studio 'yu başlatın ve **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.

1. **Yeni proje oluştur**' da **konsol** proje türü ' ne filtre uygulayın ve konsol uygulaması ' na tıklayın **(.NET Framework)**. **İleri**’ye tıklayın.

1. **Yeni projenizi yapılandırma** bölümünde bir proje adı girin. **Framework** altında **.NET Framework 4.7.1** veya üstünü seçin. **Oluştur**’a tıklayın.

## <a name="reload-data-from-app-configuration"></a>Uygulama yapılandırmasından verileri yeniden yükleme
1. Projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Araştır** sekmesindeMicrosoft.Extensions.Configurma 'yı arayın ve ekleyin *. AzureAppConfiguration* NuGet paketini projenize. Bulamıyorsanız, **ön sürümü dahil** et onay kutusunu seçin.

1. *Program. cs*' yi açın ve .NET Core uygulama yapılandırma sağlayıcısına bir başvuru ekleyin.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Yapılandırmayla ilgili nesneleri depolamak için iki değişken ekleyin.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. `Main`Belirtilen yenileme seçenekleriyle uygulama yapılandırmasına bağlanmak için yöntemi güncelleştirin.

    ```csharp
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
    ```
    `ConfigureRefresh`Yöntemi, bir yenileme işlemi tetiklendiğinde, yapılandırma verilerini uygulama yapılandırma deposu ile güncelleştirmek için kullanılan ayarları belirtmek için kullanılır. Bir örneği `IConfigurationRefresher` `GetRefresher` yöntemine sunulan seçeneklere çağrı yöntemiyle alınabilir `AddAzureAppConfiguration` ve `TryRefreshAsync` Bu örnekteki yöntem kodunuzda herhangi bir yerde yenileme işlemini tetiklemek için kullanılabilir.

    > [!NOTE]
    > Yapılandırma ayarı için varsayılan önbellek süre sonu zamanı 30 saniyedir, ancak yöntem `SetCacheExpiration` için bağımsız değişken olarak geçirilen seçenek başlatıcısında yöntemi çağırarak geçersiz kılınabilir `ConfigureRefresh` .

1. `PrintMessage()`Yapılandırma verilerinin uygulama yapılandırmasından el ile yenilenmesini tetikleyen adlı bir yöntem ekleyin.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. **ConnectionString** adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Değişikliğin etkili olması için Visual Studio 'Yu yeniden başlatın. 

1. Konsol uygulamasını derlemek ve çalıştırmak için CTRL + F5 tuşlarına basın.

    ![Uygulama başlatma yerel](./media/dotnet-app-run.png)

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin ve aşağıdaki anahtarların değerlerini güncelleştirin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından alınan veriler-güncelleştirildi |

1. Çalışan uygulamaya geri dönmek için ENTER tuşuna basarak yenileme tetikleyin ve güncelleştirilmiş değeri komut Istemi veya PowerShell penceresinde yazdırın.

    ![Uygulama yerel olarak Yenile](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Önbellek sona erme zamanı, `SetCacheExpiration` yenileme işlemi için yapılandırmayı belirtirken yöntemi kullanılarak 10 saniye olarak ayarlandığından, yapılandırma ayarı değeri yalnızca bu ayar için en son yenilemeden bu yana en az 10 saniye geçtiğinde güncelleştirilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasından yapılandırma ayarlarını dinamik olarak yenilemek için .NET Framework uygulamanızı etkinleştirdiniz. Azure yönetilen kimliğin uygulama yapılandırmasına erişimi kolaylaştırmak için nasıl kullanılacağını öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

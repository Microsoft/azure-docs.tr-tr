---
title: .NET Framework uygulamasında Azure Uygulama yapılandırması dinamik yapılandırmasını kullanma öğreticisi | Microsoft Docs
description: Bu öğreticide .NET Framework uygulamalar için yapılandırma verilerini dinamik olarak güncelleştirme hakkında bilgi edineceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7e28cdacce8eac4774683013ae1c30ca34ebfaad
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821714"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Öğretici: .NET Framework uygulamasında dinamik yapılandırmayı kullanma

Uygulama yapılandırması .NET istemci kitaplığı, bir uygulamanın yeniden başlatılmasına gerek kalmadan isteğe bağlı olarak bir yapılandırma ayarları kümesinin güncelleştirilmesini destekler. Bu, önce yapılandırma sağlayıcısı seçeneklerinden bir `IConfigurationRefresher` örneği alınarak ve sonra bu örneğe kodunuzda herhangi bir yerde `Refresh` çağırarak uygulanabilir.

Ayarları güncel tutmak ve yapılandırma deposuna çok fazla çağrı önlemek için, her bir ayar için bir önbellek kullanılır. Bir ayarın önbelleğe alınmış değeri sona erene kadar, yenileme işlemi değeri, değer yapılandırma deposunda değiştiği zaman bile güncelleştirmez. Her istek için varsayılan kullanım süresi 30 saniyedir, ancak gerekirse geçersiz kılınabilir.

Bu öğreticide, kodunuzda dinamik yapılandırma güncelleştirmelerini nasıl uygulayabileceğinizi gösterir. Hızlı başlangıçlarda tanıtılan uygulamada oluşturulur. Devam etmeden önce, önce [uygulama yapılandırması ile bir .NET Framework uygulaması oluşturun](./quickstart-dotnet-app.md) .

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Uygulamanızı isteğe bağlı olarak bir uygulama yapılandırma deposu ile güncelleştirmek için uygulamanızı ayarlayın.
> * En son yapılandırmayı uygulamanızın denetleyicilerine ekleme.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 veya üzeri](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Aşağıdaki anahtar-değer çiftlerini eklemek için **yapılandırma gezgini** >  **+ Oluştur** ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

## <a name="create-a-net-console-app"></a>.NET konsol uygulaması oluşturma

1. Visual Studio 'yu başlatın ve **dosya** > **Yeni** > **Proje**' yi seçin.

1. **Yeni proje oluştur**' da **konsol** proje türü ' ne filtre uygulayın ve konsol uygulaması ' na tıklayın **(.NET Framework)** . **İleri**’ye tıklayın.

1. **Yeni projenizi yapılandırma**bölümünde bir proje adı girin. **Framework**altında **.NET Framework 4.7.1** veya üstünü seçin. **Oluştur**’a tıklayın.

## <a name="reload-data-from-app-configuration"></a>Uygulama yapılandırmasından verileri yeniden yükleme
1. Projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Araştır** sekmesinde, *Microsoft. Extensions. Configuration. AzureAppConfiguration* NuGet paketini arayın ve projenize ekleyin. Bulamıyorsanız, **ön sürümü dahil** et onay kutusunu seçin.

1. *Program.cs*'i açın ve .NET Core uygulama yapılandırma sağlayıcısına bir başvuru ekleyin.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Yapılandırmayla ilgili nesneleri depolamak için iki değişken ekleyin.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Belirtilen yenileme seçenekleriyle uygulama yapılandırmasına bağlanmak için `Main` yöntemini güncelleştirin.

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
    `ConfigureRefresh` yöntemi, bir yenileme işlemi tetiklendiğinde, yapılandırma verilerini uygulama yapılandırma deposu ile güncelleştirmek için kullanılan ayarları belirtmek için kullanılır. `IConfigurationRefresher` örneği, `AddAzureAppConfiguration` yöntemine sunulan seçeneklere `GetRefresher` yöntemi çağırarak alınabilir ve bu örnekteki `Refresh` yöntemi kodunuzda herhangi bir yerde yenileme işlemini tetiklemek için kullanılabilir.

    > [!NOTE]
    > Yapılandırma ayarı için varsayılan önbellek süre sonu zamanı 30 saniyedir, ancak `SetCacheExpiration` yöntemi bir bağımsız değişken olarak geçirilen seçenekler başlatıcısında `ConfigureRefresh` yöntemine çağırarak geçersiz kılınabilir.

1. Yapılandırma verilerinin uygulama yapılandırmasından el ile yenilenmesini tetikleyen `PrintMessage()` adlı bir yöntem ekleyin.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. **ConnectionString**adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Değişikliğin etkili olması için Visual Studio 'Yu yeniden başlatın. 

1. Konsol uygulamasını derlemek ve çalıştırmak için CTRL + F5 tuşlarına basın.

    ![Uygulama başlatma yerel](./media/dotnet-app-run.png)

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. **Tüm kaynaklar**' ı seçin ve hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin ve aşağıdaki anahtarların değerlerini güncelleştirin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından alınan veriler-güncelleştirildi |

1. Çalışan uygulamaya geri dönmek için ENTER tuşuna basarak yenileme tetikleyin ve güncelleştirilmiş değeri komut Istemi veya PowerShell penceresinde yazdırın.

    ![Uygulama yerel olarak Yenile](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Yenileme işleminin yapılandırmasını belirtirken `SetCacheExpiration` yöntemi kullanılarak önbelleğin süre sonu zamanı 10 saniye olarak ayarlandığı için, yapılandırma ayarı değeri yalnızca en son yenilemeden sonra en az 10 saniye geçtiğinde güncelleştirilir. Bu ayar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, uygulama yapılandırmasına erişimi kolaylaştırmak ve uygulamanız için kimlik bilgisi yönetimini geliştirmek üzere bir Azure yönetilen hizmet kimliği eklediniz. Uygulama yapılandırmasına erişimi kolaylaştıran Azure tarafından yönetilen bir hizmet kimliği ekleme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

---
title: .NET Framework uygulamalarına özellik bayrakları eklemeye yönelik hızlı başlangıç | Microsoft Docs | Microsoft Docs
description: .NET Framework uygulamalara özellik bayrakları eklemeye ve bunları Azure Uygulama yapılandırmasında yönetmeye yönelik bir hızlı başlangıç
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: cba0a9a003b53eeb102e14c00cb91f344ebbe3d6
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821735"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Hızlı başlangıç: .NET Framework uygulamasına özellik bayrakları ekleme

Bu hızlı başlangıçta, özellik yönetiminin uçtan uca bir uygulamasını oluşturmak için Azure uygulama yapılandırmasını bir .NET Framework uygulamasına katabilirsiniz. Tüm özellik Bayraklarınızı merkezi olarak depolamak ve durumlarını denetlemek için uygulama yapılandırma hizmetini kullanabilirsiniz. 

.NET Özellik Yönetimi kitaplıkları Framework 'ü kapsamlı özellik bayrağı desteğiyle genişletir. Bu kitaplıklar, .NET yapılandırma sisteminin üzerine kurulmuştur. Bunlar, .NET yapılandırma sağlayıcısı aracılığıyla uygulama yapılandırmasıyla sorunsuz bir şekilde tümleşir.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>.NET konsol uygulaması oluşturma

1. Visual Studio 'yu başlatın ve **dosya** > **Yeni** > **Proje**' yi seçin.

1. **Yeni proje oluştur**' da **konsol** proje türü ' ne filtre uygulayın ve konsol uygulaması ' na tıklayın **(.NET Framework)** . **İleri**’ye tıklayın.

1. **Yeni projenizi yapılandırma**bölümünde bir proje adı girin. **Framework**altında **.NET Framework 4.7.1** veya üstünü seçin. **Oluştur**’a tıklayın.

## <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

1. Projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Araştır** sekmesinde, aşağıdaki NuGet paketlerini arayıp projenize ekleyin. Bunları bulamıyorsanız, **ön sürümü dahil et** onay kutusunu seçin.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. *Program.cs* açın ve aşağıdaki deyimleri ekleyin:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Özellik bayraklarının alınabilmesi için `UseFeatureFlags` seçeneğini belirterek uygulama yapılandırmasına bağlanmak üzere `Main` yöntemini güncelleştirin. Sonra `Beta` Özellik bayrağı etkinse bir ileti görüntülenir.

    ```csharp
        static void Main(string[] args)
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options => 
                { 
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags(); 
                }).Build();
            
            IServiceCollection services = new ServiceCollection(); 
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement(); 
            IFeatureManager featureManager = services.BuildServiceProvider().GetRequiredService<IFeatureManager>(); 
            
            if (featureManager.IsEnabled("Beta")) 
            { 
                Console.WriteLine("Welcome to the beta"); 
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. **ConnectionString** adlı bir ortam değişkenini uygulama yapılandırma deponuzın bağlantı dizesine ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Değişikliğin etkili olması için Visual Studio 'Yu yeniden başlatın. 

1. Konsol uygulamasını derlemek ve çalıştırmak için CTRL + F5 tuşlarına basın.

    ![Özellik bayrağı etkin olan uygulama](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, uygulama yapılandırmasında bir özellik bayrağı oluşturdunuz ve bunu bir .NET Framework konsol uygulamasıyla kullandınız. Uygulama yeniden başlatılmadan Özellik bayraklarını ve diğer yapılandırma değerlerini dinamik olarak güncelleştirme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Dinamik yapılandırmayı etkinleştir](./enable-dynamic-configuration-dotnet.md)
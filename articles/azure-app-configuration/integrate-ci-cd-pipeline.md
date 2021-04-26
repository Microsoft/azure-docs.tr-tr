---
title: Sürekli tümleştirme ve teslim işlem hattı kullanarak Azure uygulama yapılandırmasını tümleştirme
description: Azure Uygulama yapılandırması 'nı kullanarak sürekli tümleştirme ve teslimi uygulamayı öğrenin
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 04/19/2020
ms.author: alkemper
ms.openlocfilehash: 3a4d171f0e3225db195c5c2b71ca99a3386e3a36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979853"
---
# <a name="integrate-with-a-cicd-pipeline"></a>CI/CD işlem hattıyla tümleştirme

Bu makalede, Azure Uygulama yapılandırmasından sürekli tümleştirme ve sürekli dağıtım sisteminde verilerin nasıl kullanılacağı açıklanmaktadır.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Azure DevOps işlem hattınızda uygulama yapılandırması 'nı kullanma

Bir Azure DevOps işlem hattına sahipseniz, anahtar değerlerini uygulama yapılandırmasından getirip görev değişkenleri olarak ayarlayabilirsiniz. [Azure Uygulama yapılandırması DevOps uzantısı](https://go.microsoft.com/fwlink/?linkid=2091063) , bu işlevselliği sağlayan bir eklenti modülüdür. Uzantıyı derleme veya yayın görev dizisinde kullanmak için yönergelerini izleyin.

## <a name="deploy-app-configuration-data-with-your-application"></a>Uygulamanızla uygulama yapılandırma verilerini dağıtma

Uygulamanız Azure Uygulama yapılandırmasına bağımlıysa ve bu uygulamaya ulaşamadıysanız, çalışmayabilir. Yapılandırma verilerini uygulamayla dağıtılan ve uygulamanın başlatılması sırasında yerel olarak yüklenen bir dosyaya paketleyerek uygulamanızın dayanıklılığını geliştirin. Bu yaklaşım, uygulamanızın başlangıçta varsayılan ayar değerlerini olmasını sağlar. Bu değerler, kullanılabilir olduğunda, uygulama yapılandırma deposundaki daha yeni değişikliklerle üzerine yazılır.

Azure Uygulama yapılandırması 'nın [dışa aktarma](./howto-import-export-data.md#export-data) işlevini kullanarak, geçerli yapılandırma verilerini tek bir dosya olarak alma işlemini otomatikleştirebilir. Böylece, bu dosyayı sürekli tümleştirme ve sürekli dağıtım (CI/CD) işlem hattınızdaki bir derleme veya dağıtım adımına katabilirsiniz.

Aşağıdaki örnek, uygulama yapılandırma verilerinin hızlı başlangıçlarda tanıtılan web uygulamasına yönelik derleme adımı olarak nasıl ekleneceğini gösterir. Devam etmeden önce, önce [uygulama yapılandırması ile bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. [Visual Studio Code](https://code.visualstudio.com/) , Windows, MacOS ve Linux platformlarında kullanılabilen harika bir seçenektir.

### <a name="prerequisites"></a>Önkoşullar

Yerel olarak oluşturursanız, henüz yapmadıysanız [Azure CLI](/cli/azure/install-azure-cli) 'yi indirip yükleyin.

Azure DevOps ile bir bulut derlemesi yapmak için, [Azure CLI](/cli/azure/install-azure-cli) 'nın derleme sisteminizde yüklü olduğundan emin olun.

### <a name="export-an-app-configuration-store"></a>Uygulama yapılandırma deposunu dışa aktarma

1. *. Csproj* dosyanızı açın ve aşağıdaki betiği ekleyin:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. *Program. cs* dosyasını açın ve yöntemini `CreateWebHostBuilder` çağırarak, dışarıya aktarılmış json dosyasını kullanmak için yöntemi güncelleştirin `config.AddJsonFile()` .  `System.Reflection`Ad alanını da ekleyin.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. **ConnectionString** adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. 
    Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

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

2. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

    ```console
     dotnet build
    ```

3. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

    ```console
     dotnet run
    ```

4. Bir tarayıcı penceresi açın ve `http://localhost:5000` yerel olarak barındırılan Web uygulaması için varsayılan URL olan adresine gidin.

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir dağıtım ardışık düzeninde kullanılacak Azure uygulama yapılandırma verilerini aktarmış olursunuz. Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi için Azure CLı örneklerine devam edin.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)

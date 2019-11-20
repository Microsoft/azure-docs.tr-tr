---
title: Azure Uygulama yapılandırması 'nı kullanarak sürekli tümleştirme ve teslim işlem hattı ile tümleştirme öğreticisi | Microsoft Docs
description: Bu öğreticide sürekli tümleştirme ve teslim sırasında Azure Uygulama yapılandırmasındaki verileri kullanarak yapılandırma dosyası oluşturmayı öğreneceksiniz.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e2f682a2782eb1a61dd44e02d665175e31c441f8
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357022"
---
# <a name="integrate-with-a-cicd-pipeline"></a>CI/CD işlem hattıyla tümleştirme

Bu makalede, Azure Uygulama yapılandırmasından sürekli tümleştirme ve sürekli dağıtım sisteminde veri kullanmanın çeşitli yolları açıklanmaktadır.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Azure DevOps işlem hattınızda uygulama yapılandırması 'nı kullanma

Bir Azure DevOps işlem hattına sahipseniz, anahtar değerlerini uygulama yapılandırmasından getirip görev değişkenleri olarak ayarlayabilirsiniz. [Azure Uygulama yapılandırması DevOps uzantısı](https://go.microsoft.com/fwlink/?linkid=2091063) , bu işlevselliği sağlayan bir eklenti modülüdür. Bir derleme veya yayın görev dizisinde uzantıyı kullanmak için yönergelerini izlemeniz yeterlidir.

## <a name="deploy-app-configuration-data-with-your-application"></a>Uygulamanızla uygulama yapılandırma verilerini dağıtma

Uygulamanız Azure Uygulama yapılandırmasına bağımlıysa ve bu uygulamaya ulaşamadıysanız, çalışmayabilir. Uygulamanızın dayanıklılığını, bu tür bir olayla uğraşmak üzere geliştirebilirsiniz, ancak bu durum büyük olasılıkla gerçekleşmektir. Bunu yapmak için, geçerli yapılandırma verilerini uygulamayla birlikte dağıtılan bir dosyaya paketleyin ve başlatma sırasında yerel olarak yüklenir. Bu yaklaşım, uygulamanızın varsayılan ayar değerlerini en az bir değere sahip olmasını güvence altına alır. Bu değerler, kullanılabilir olduğunda, uygulama yapılandırma deposundaki daha yeni değişikliklerle üzerine yazılır.

Azure Uygulama yapılandırması 'nın [dışa aktarma](./howto-import-export-data.md#export-data) işlevini kullanarak, geçerli yapılandırma verilerini tek bir dosya olarak alma işlemini otomatikleştirebilir. Sonra bu dosyayı sürekli tümleştirme ve sürekli dağıtım (CI/CD) işlem hattınızdaki bir derleme veya dağıtım adımına ekleyin.

Aşağıdaki örnek, uygulama yapılandırma verilerinin hızlı başlangıçlarda tanıtılan web uygulamasına yönelik derleme adımı olarak nasıl ekleneceğini gösterir. Devam etmeden önce, önce [uygulama yapılandırması ile bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. [Visual Studio Code](https://code.visualstudio.com/) , Windows, MacOS ve Linux platformlarında kullanılabilen harika bir seçenektir.

### <a name="prerequisites"></a>Önkoşullar

Yerel olarak oluşturursanız, henüz yapmadıysanız [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 'yi indirip yükleyin.

Azure DevOps ile bir bulut derlemesi yapmak için, [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 'nın derleme sisteminizde yüklü olduğundan emin olun.

### <a name="export-an-app-configuration-store"></a>Uygulama yapılandırma deposunu dışa aktarma

1. *. Csproj* dosyanızı açın ve aşağıdaki betiği ekleyin:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Uygulama yapılandırma deponuzda ilişkili *ConnectionString* öğesini bir ortam değişkeni olarak ekleyin.

2. *Program.cs*'i açın ve yöntemi çağırarak `CreateWebHostBuilder` `config.AddJsonFile()` , bu JSON dosyasını kullanmak için yöntemi güncelleştirin.

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

1. **ConnectionString**adlı bir ortam değişkeni ayarlayın ve uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Windows komut istemi 'ni kullanırsanız, aşağıdaki komutu çalıştırın ve değişikliğin etkili olması için komut istemi ' ni yeniden başlatın:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

        dotnet build

3. Oluşturma başarıyla tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu çalıştırın:

        dotnet run

4. Bir tarayıcı penceresi açın ve yerel olarak `http://localhost:5000`barındırılan Web uygulaması için varsayılan URL olan adresine gidin.

    ![Hızlı başlangıç uygulaması başlatma yerel](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir dağıtım ardışık düzeninde kullanılacak Azure uygulama yapılandırma verilerini aktarmış olursunuz. Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi için Azure CLı örneklerine devam edin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

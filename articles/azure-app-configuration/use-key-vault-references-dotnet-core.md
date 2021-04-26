---
title: ASP.NET Core uygulamasındaki Azure uygulama yapılandırma Key Vault başvurularını kullanma öğreticisi | Microsoft Docs
description: Bu öğreticide, Azure Uygulama yapılandırması 'nın Key Vault başvurularını ASP.NET Core bir uygulamadan nasıl kullanacağınızı öğrenirsiniz.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 57abbeefe8e3f2abe527f2b282d643db766b9dc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775754"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Öğretici: ASP.NET Core uygulamasındaki Key Vault başvurularını kullanma

Bu öğreticide, Azure Key Vault ile birlikte Azure uygulama yapılandırma hizmetini kullanmayı öğreneceksiniz. Uygulama yapılandırması ve Key Vault, çoğu uygulama dağıtımında yan yana kullanılan tamamlayıcı hizmetlerdir.

Uygulama yapılandırması, Key Vault depolanan değerlere başvuruda bulunan anahtarlar oluşturarak Hizmetleri birlikte kullanmanıza yardımcı olur. Uygulama yapılandırması böyle anahtarlar oluşturduğunda, değerlerinin kendileri yerine Key Vault değerlerinin URI 'Lerini depolar.

Uygulamanız, uygulama yapılandırmasında depolanan diğer anahtarların yaptığı gibi Key Vault başvurularını almak için uygulama yapılandırma istemci sağlayıcısını kullanır. Bu durumda, uygulama yapılandırmasında depolanan değerler Key Vault değerlere başvuran URI 'Lerdir. Bunlar Key Vault değerler veya kimlik bilgileri değildir. İstemci sağlayıcısı anahtarları Key Vault başvuru olarak tanıdığından, değerlerini almak için Key Vault kullanır.

Uygulamanız hem uygulama yapılandırması hem de Key Vault için düzgün şekilde kimlik doğrulaması yapmaktan sorumludur. İki hizmet doğrudan iletişim kurmaz.

Bu öğretici kodunuzda Key Vault başvuruların nasıl uygulanacağını gösterir. Hızlı başlangıçlarda tanıtılan web uygulamasında oluşturulur. Devam etmeden önce, önce [uygulama yapılandırması ile bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md) .

Bu öğreticideki adımları uygulamak için herhangi bir kod düzenleyicisi kullanabilirsiniz. Örneğin, [Visual Studio Code](https://code.visualstudio.com/) Windows, MacOS ve Linux işletim sistemleri için kullanılabilen platformlar arası bir kod düzenleyicisidir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Key Vault depolanan bir değere başvuran bir uygulama yapılandırma anahtarı oluşturun.
> * Bu anahtarın değerine bir ASP.NET Core Web uygulamasından erişin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce [.NET Core SDK](https://dotnet.microsoft.com/download)yükleyebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Kasa oluşturma

1. Azure portal, sol üst köşedeki **kaynak oluştur** seçeneğini belirleyin:

    ![Ekran görüntüsü Azure portal kaynak oluştur seçeneğini gösterir.](./media/quickstarts/search-services.png)
1. Arama kutusuna **Key Vault** yazın.
1. Sonuçlar listesinden sol taraftaki **Anahtar kasaları** ' nı seçin.
1. **Anahtar kasaları**' nda **Ekle**' yi seçin.
1. **Anahtar Kasası oluşturma**' da sağ tarafta aşağıdaki bilgileri sağlayın:
    - Abonelik seçmek için **abonelik** ' ı seçin.
    - **Kaynak grubu**' nda **Yeni oluştur** ' u seçin ve bir kaynak grubu adı girin.
    - **Anahtar Kasası adında**, benzersiz bir ad gereklidir. Bu öğretici için **contoso-vault2** girin.
    - **Bölge** açılan listesinde bir konum seçin.
1. Diğer **Anahtar Kasası oluşturma** seçeneklerini varsayılan değerleriyle bırakın.
1. **Oluştur**’u seçin.

Bu noktada, Azure hesabınız, bu yeni kasaya erişme yetkisine sahip tek bir hesaptır.

![Ekran görüntüsü anahtar kasanızı gösterir.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault’a gizli dizi ekleme

Kasaya bir gizli dizi eklemek için yalnızca birkaç ek adım gerçekleştirmeniz gerekir. Bu durumda, Key Vault alımı test etmek için kullanabileceğiniz bir ileti ekleyin. İleti **ileti** olarak adlandırılır ve "Hello 'dan Key Vault" değerini depolar.

1. Key Vault Özellikler sayfalarında **gizli** dizileri ' ni seçin.
1. **Oluştur/Içeri aktar**' ı seçin.
1. Gizli dizi **Oluştur** bölmesinde aşağıdaki değerleri girin:
    - **Karşıya yükleme seçenekleri**: **el ile** girin.
    - **Ad**: **ileti** girin.
    - **Değer**: **Key Vault Merhaba** yazın.
1. Diğerini varsayılan değerleriyle **gizli bir özellikler oluşturmamaya** ayrılın.
1. **Oluştur**’u seçin.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Uygulama yapılandırmasına Key Vault başvurusu ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve ardından hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin.

1. **+**  >  **Anahtar Kasası başvurusu** oluştur ' u seçin ve ardından aşağıdaki değerleri belirtin:
    - **Anahtar**: **TestApp: Settings: keyvaultmessage** öğesini seçin.
    - **Etiket**: Bu değeri boş bırakın.
    - **Abonelik**, **kaynak grubu** ve **Anahtar Kasası**: önceki bölümde oluşturduğunuz anahtar kasasında bunlara karşılık gelen değerleri girin.
    - **Gizli**: önceki bölümde oluşturduğunuz gizli anahtar adlı **iletiyi** seçin.

## <a name="connect-to-key-vault"></a>Key Vault Bağlan

1. Bu öğreticide, Key Vault kimlik doğrulaması için bir hizmet sorumlusu kullanırsınız. Bu hizmet sorumlusunu oluşturmak için Azure CLı [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutunu kullanın:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Bu işlem bir dizi anahtar/değer çiftini döndürür:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Hizmet sorumlusunun anahtar kasanıza erişmesine izin vermek için aşağıdaki komutu çalıştırın:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. *ClientID*, *ClientSecret* ve *tenantıd* değerlerini depolamak için ortam değişkenleri ekleyin.

    #### <a name="windows-command-prompt"></a>[Windows komut istemi](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Bu Key Vault kimlik bilgileri yalnızca uygulamanız içinde kullanılır. Uygulamanız bu kimlik bilgileriyle Key Vault doğrudan kimlik doğrulaması yapar. Bunlar hiçbir şekilde uygulama yapılandırma hizmetine geçirilmez.

1. Bu yeni ortam değişkenlerini yüklemek için terminalinizi yeniden başlatın.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Kodunuzu Key Vault bir başvuru kullanacak şekilde güncelleştirin

1. Aşağıdaki komutu çalıştırarak gerekli NuGet paketlerine bir başvuru ekleyin:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. *Program. cs*' yi açın ve aşağıdaki gerekli paketlere başvuruları ekleyin:

    ```csharp
    using Azure.Identity;
    ```

1. Yöntemini `CreateWebHostBuilder` çağırarak uygulama yapılandırmasını kullanmak için yöntemi güncelleştirin `config.AddAzureAppConfiguration` . Seçeneğini ekleyin `ConfigureKeyVault` ve Key Vault doğru kimlik bilgilerini geçirin.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Uygulama yapılandırmasına bağlantıyı başlattığınızda, yöntemini çağırarak Key Vault bağlantısını ayarlarsınız `ConfigureKeyVault` . Başlangıçtan sonra, Key Vault başvuruların değerlerine, normal uygulama yapılandırma anahtarlarının değerlerine erişirken aynı şekilde erişebilirsiniz.

    Bu işlemi eylemde görmek için, **Görünümler** giriş klasöründeki *Index. cshtml* dosyasını açın  >   . Dosyanın içeriğini aşağıdaki kod ile değiştirin:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    **TestApp: Settings: Message** yapılandırma değeri ile aynı şekilde, **TestApp: Settings: keyvaultmessage** Key Vault Reference değerine erişirsiniz.

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet build
    ```

1. Oluşturma işlemi tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu kullanın:

    ```dotnetcli
    dotnet run
    ```

1. Bir tarayıcı penceresi açın ve `http://localhost:5000` yerel olarak barındırılan Web uygulaması için varsayılan URL olan öğesine gidin.

    ![Hızlı başlangıç yerel uygulama başlatma](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Key Vault depolanan bir değere başvuran bir uygulama yapılandırma anahtarı oluşturdunuz. Uygulama yapılandırmasına ve Key Vault erişimi kolaylaştıran Azure tarafından yönetilen bir hizmet kimliği ekleme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

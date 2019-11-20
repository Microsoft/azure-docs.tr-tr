---
title: ASP.NET Core uygulamasındaki Azure uygulama yapılandırma Key Vault başvurularını kullanma öğreticisi | Microsoft Docs
description: Bu öğreticide, Azure Uygulama yapılandırması 'nın Key Vault başvurularını ASP.NET Core bir uygulamadan nasıl kullanacağınızı öğrenirsiniz.
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
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 992cface653bf3fe52afc7efa3f17573fcf91399
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469653"
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

    ![Anahtar Kasası oluşturma işlemi tamamlandıktan sonra çıkış](./media/quickstarts/search-services.png)
1. Arama kutusuna **Key Vault**girin.
1. Sonuçlar listesinden sol taraftaki **Anahtar kasaları** ' nı seçin.
1. **Anahtar kasaları**' nda **Ekle**' yi seçin.
1. **Anahtar Kasası oluşturma**' da sağ tarafta aşağıdaki bilgileri sağlayın:
    - Abonelik seçmek için **abonelik** ' ı seçin.
    - **Kaynak grubu**' nda **Yeni oluştur** ' u seçin ve bir kaynak grubu adı girin.
    - **Anahtar Kasası adında**, benzersiz bir ad gereklidir. Bu öğretici için **contoso-vault2**girin.
    - **Bölge** açılan listesinde bir konum seçin.
1. Diğer **Anahtar Kasası oluşturma** seçeneklerini varsayılan değerleriyle bırakın.
1. **Oluştur**'u seçin.

Bu noktada, Azure hesabınız, bu yeni kasaya erişme yetkisine sahip tek bir hesaptır.

![Anahtar Kasası oluşturma işlemi tamamlandıktan sonra çıkış](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault’a gizli dizi ekleme

Kasaya bir gizli dizi eklemek için yalnızca birkaç ek adım gerçekleştirmeniz gerekir. Bu durumda, Key Vault alımı test etmek için kullanabileceğiniz bir ileti ekleyin. İleti **ileti**olarak adlandırılır ve "Hello 'dan Key Vault" değerini depolar.

1. Key Vault Özellikler sayfalarında **gizli**dizileri ' ni seçin.
1. **Oluştur/Içeri aktar**' ı seçin.
1. Gizli dizi **Oluştur** bölmesinde aşağıdaki değerleri girin:
    - **Karşıya yükleme seçenekleri**: **el ile**girin.
    - **Ad**: **ileti**girin.
    - **Değer**: **Key Vault Merhaba**yazın.
1. Diğerini varsayılan değerleriyle **gizli bir özellikler oluşturmamaya** ayrılın.
1. **Oluştur**'u seçin.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Uygulama yapılandırmasına Key Vault başvurusu ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın. **Tüm kaynaklar**' ı seçin ve ardından hızlı başlangıçta oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

1. **Yapılandırma Gezgini**' ni seçin.

1. **+ Oluştur** > **Anahtar Kasası başvurusu**' nu seçin ve ardından aşağıdaki değerleri belirtin:
    - **Anahtar**: **TestApp: Settings: keyvaultmessage**öğesini seçin.
    - **Etiket**: Bu değeri boş bırakın.
    - **Abonelik**, **kaynak grubu**ve **Anahtar Kasası**: önceki bölümde oluşturduğunuz anahtar kasasında bunlara karşılık gelen değerleri girin.
    - **Gizli**: önceki bölümde oluşturduğunuz gizli anahtar adlı **iletiyi** seçin.

## <a name="connect-to-key-vault"></a>Key Vault Bağlan

1. Bu öğreticide, Key Vault kimlik doğrulaması için bir hizmet sorumlusu kullanırsınız. Bu hizmet sorumlusunu oluşturmak için Azure CLı [az ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) komutunu kullanın:

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
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Hizmet sorumlusunun anahtar kasanıza erişmesine izin vermek için aşağıdaki komutu çalıştırın:

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Gizli verileri ve hızlı başlangıç bölümünde *. csproj* dosyasına eklediğiniz gizli verileri *depolamaya yönelik araç* [hızlı başlangıç: Azure Uygulama yapılandırması ile bir ASP.NET Core uygulaması oluşturun](./quickstart-aspnet-core-app.md). Bu komutların *. csproj* dosyasıyla aynı dizinde yürütülmesi gerekir.

    ```
    dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>
    dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>
    ```

> [!NOTE]
> Bu Key Vault kimlik bilgileri yalnızca uygulamanız içinde kullanılır. Uygulamanız bu kimlik bilgileriyle Key Vault doğrudan kimlik doğrulaması yapar. Bunlar hiçbir şekilde uygulama yapılandırma hizmetine geçirilmez.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Kodunuzu Key Vault bir başvuru kullanacak şekilde güncelleştirin

1. *Program.cs*'i açın ve aşağıdaki gerekli paketlere başvuruları ekleyin:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. `config.AddAzureAppConfiguration` metodunu çağırarak uygulama yapılandırmasını kullanmak için `CreateWebHostBuilder` yöntemini güncelleştirin. Key Vault yeni bir `KeyVaultClient` başvuruya geçirilecek `UseAzureKeyVault` seçeneğini ekleyin.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Uygulama yapılandırmasına bağlantıyı başlattığınızda, `UseAzureKeyVault` yöntemine `KeyVaultClient` başvurusunu geçirtiniz. Başlangıçtan sonra, Key Vault başvuruların değerlerine, normal uygulama yapılandırma anahtarlarının değerlerine erişirken aynı şekilde erişebilirsiniz.

    Bu işlemi eylemde görmek için, **görünümler** > **giriş** klasöründe *Index. cshtml* dosyasını açın. İçeriğini şu kodla değiştirin:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    **TestApp: Settings: Message**yapılandırma değeri ile aynı şekilde, **TestApp: Settings: keyvaultmessage** Key Vault Reference değerine erişirsiniz.

## <a name="build-and-run-the-app-locally"></a>Uygulamayı yerel olarak derleyin ve çalıştırın

1. .NET Core CLI kullanarak uygulamayı derlemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

    ```
    dotnet build
    ```

1. Oluşturma işlemi tamamlandıktan sonra, Web uygulamasını yerel olarak çalıştırmak için aşağıdaki komutu kullanın:

    ```
    dotnet run
    ```

1. Bir tarayıcı penceresi açın ve yerel olarak barındırılan Web uygulamasının varsayılan URL 'SI olan `http://localhost:5000`' a gidin.

    ![Hızlı başlangıç yerel uygulama başlatma](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Key Vault depolanan bir değere başvuran bir uygulama yapılandırma anahtarı oluşturdunuz. Uygulama yapılandırmasına ve Key Vault erişimi kolaylaştıran Azure tarafından yönetilen bir hizmet kimliği ekleme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Yönetilen kimlik tümleştirmesi](./howto-integrate-azure-managed-service-identity.md)

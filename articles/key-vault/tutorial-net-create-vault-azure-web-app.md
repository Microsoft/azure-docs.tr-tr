---
title: Öğretici-.NET ' te Azure Web App ile Azure Key Vault kullanma | Microsoft Docs
description: Bu öğreticide, anahtar kasaınızdan gizli dizi okumak için bir ASP.NET Core uygulaması yapılandırırsınız.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 84256d79ec543d038b4d3d3f3dc6901bbd003871
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003374"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Öğretici: .NET ' te Azure Web App ile Azure Key Vault kullanma

Azure Key Vault, API anahtarları ve veritabanı bağlantı dizeleri gibi gizli dizileri korumanıza yardımcı olur. Uygulamalarınız, hizmetleriniz ve BT kaynaklarınıza erişim sağlar.

Bu öğreticide, Azure anahtar kasasından bilgi okuyabilen bir Azure Web uygulaması oluşturmayı öğreneceksiniz. İşlem, Azure kaynakları için Yönetilen kimlikler kullanır. Azure Web uygulamaları hakkında daha fazla bilgi için bkz. [Azure App Service](../app-service/overview.md).

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Bir anahtar kasası oluşturma.
> * Anahtar kasasına gizli dizi ekleyin.
> * Anahtar kasasından bir gizli dizi alma.
> * Bir Azure Web uygulaması oluşturun.
> * Web uygulaması için yönetilen bir kimliği etkinleştirin.
> * Web uygulaması için izin atama.
> * Web uygulamasını Azure 'da çalıştırın.

Başlamadan önce [temel kavramları Key Vault](basic-concepts.md)okuyun. 

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Windows için: [.NET Core 2,1 SDK veya üzeri](https://www.microsoft.com/net/download/windows)
* Mac için: [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)
* Windows, Mac ve Linux için:
  * [Git](https://git-scm.com/downloads)
  * Bu öğretici, Azure CLı 'yı yerel olarak çalıştırmanızı gerektirir. Azure CLı sürüm 2.0.4 veya daha yeni bir sürümün yüklü olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. CLI’yı yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 2.0’ı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Yönetilen Hizmet Kimliği hakkında

Azure Key Vault, kimlik bilgilerini güvenli bir şekilde depolar, bu nedenle kodunuzda görüntülenmezler. Ancak, anahtarlarınızı almak için Azure Key Vault kimlik doğrulaması yapmanız gerekir. Key Vault kimlik doğrulaması yapmak için bir kimlik bilgisi gerekir. Bu, klasik bir önyükleme divma. Yönetilen Hizmet Kimliği (MSI), işlemi basitleştiren bir _önyükleme kimliği_ sağlayarak bu sorunu çözer.

Azure sanal makineler, Azure App Service veya Azure Işlevleri gibi bir Azure hizmeti için MSI 'yi etkinleştirdiğinizde Azure bir [hizmet sorumlusu](basic-concepts.md)oluşturur. MSI bunu, Azure Active Directory (Azure AD) içindeki hizmetin örneği için yapar ve hizmet sorumlusu kimlik bilgilerini bu örneğe çıkartır.

![MSI diyagramı](media/MSI.png)

Ardından, bir erişim belirteci almak için, kodunuz Azure kaynağında bulunan bir yerel meta veri hizmetini çağırır. Kodunuz, Azure Key Vault bir hizmette kimlik doğrulamak için yerel MSI uç noktasından aldığı erişim belirtecini kullanır.

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Azure CLI'yi kullanarak Azure'da oturum açmak için, şunları girin:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[az group create](/cli/azure/group#az-group-create) komutunu kullanarak bir kaynak grubu oluşturun.

Ardından, bir kaynak grubu adı seçin ve yer tutucuyu doldurmanız gerekir. Aşağıdaki örnekte Batı ABD konumunda bir kaynak grubu oluşturulur:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Bu öğreticide bu kaynak grubunu kullanırsınız.

## <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma

Kaynak grubunuzda bir Anahtar Kasası oluşturmak için aşağıdaki bilgileri sağlayın:

* Anahtar Kasası adı: yalnızca rakam (0-9), harf (a-z, A-Z) ve kısa çizgi (-) içerebilen 3 ile 24 karakter arasında bir dize
* Kaynak grubu adı
* Konumuna **Batı ABD**

Azure CLı 'de şu komutu girin:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Bu noktada, Azure hesabınız, bu yeni kasada işlem gerçekleştirme yetkisine sahip tek bir hesaptır.

## <a name="add-a-secret-to-the-key-vault"></a>Anahtar kasasına gizli dizi ekleme

Artık bir gizli dizi ekleyebilirsiniz. Bu bir SQL bağlantı dizesi veya hem güvenli hem de uygulamanız için kullanılabilir durumda tutmanız gereken başka bilgiler olabilir.

**Appsecret**adlı anahtar kasasında bir gizli dizi oluşturmak için aşağıdaki komutu girin: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Bu gizli anahtar, **MySecret**değerini depolar.

Gizli dizi içinde bulunan değeri düz metin olarak görüntülemek için aşağıdaki komutu girin:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Bu komut, URI dahil olmak üzere gizli bilgileri görüntüler. 

Bu adımları tamamladıktan sonra, bir anahtar kasasında gizli dizi URI'sine sahip olmanız gerekir. Bu öğreticide daha sonra kullanmak üzere bu bilgileri unutmayın. 

## <a name="create-a-net-core-web-app"></a>.NET Core Web uygulaması oluşturma

Bir .NET Core Web uygulaması oluşturmak ve Azure 'a yayımlamak için [Azure 'da ASP.NET Core Web uygulaması oluşturma](../app-service/app-service-web-get-started-dotnet.md)' daki yönergeleri izleyin. 

Ayrıca şu videoyu izleyebilirsiniz:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Çözümü açma ve düzenleme

1. **Pages** > **About.cshtml.cs** dosyasına gidin.

1. Şu NuGet paketlerini yükler:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Aşağıdaki kodu *About.cshtml.cs* dosyasına aktarın:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   AboutModel sınıfındaki kodunuzun şöyle görünmesi gerekir:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Web uygulamasını çalıştırma

1. Visual Studio 2019 ' in ana menüsünde hata ayıklama**başlangıcı**, hata ayıklama ile veya olmadan ' **ı seçin.**  >  
1. Tarayıcıda **hakkında** sayfasına gidin.  
    **AppSecret** değeri görüntülenir.

## <a name="enable-a-managed-identity"></a>Yönetilen bir kimliği etkinleştirme

Azure Key Vault, kimlik bilgilerini ve diğer gizli dizileri güvenli bir şekilde depolamanıza olanak tanır, ancak kodunuzun bunları almak için Key Vault kimlik doğrulaması gerekir. Azure [kaynaklarına genel bakış Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md) , Azure hIzmetlerIne Azure AD 'de otomatik olarak yönetilen bir kimlik vererek bu sorunu çözmeye yardımcı olur. Kodunuzda kimlik bilgilerini görüntülemesi gerekmeden Key Vault dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için bu kimliği kullanabilirsiniz.

Azure CLı 'de, bu uygulamanın kimliğini oluşturmak için, Assign-Identity komutunu çalıştırın:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

YourAppName > öğesini Azure 'da yayınlanan uygulamanın adıyla değiştirin \<.  
    Örneğin, yayınlanmış uygulamanızın adı **MyAwesomeapp.azurewebsites.net**ise, YourAppName > \< **myawesomeapp**ile değiştirin.

Uygulamayı Azure 'da yayımladığınızda bunu `PrincipalId` bir yere unutmayın. Adım 1 ' deki komutun çıktısı aşağıdaki biçimde olmalıdır:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Bu yordamdaki komut, [Azure Portal](https://portal.azure.com) gidip Web uygulaması özelliklerinde **kimlik/sistem atanmış** ayarını **Açık** olarak değiştirme ' nin eşdeğeridir.

## <a name="assign-permissions-to-your-app"></a>Uygulamanıza izin atama

Yourkeyvaultname > değerini anahtar kasanızın adıyla değiştirin \<ve PrincipalId > öğesini aşağıdaki komutta **PrincipalId** değeri ile değiştirin: \<

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Bu komut, uygulama hizmeti izninin kimliğini (MSI) Anahtar Kasanızda **Get** ve **Listeleme** işlemlerini yapmak için verir.

## <a name="publish-the-web-app-to-azure"></a>Web uygulamasını Azure’da yayımlama

Canlı Web uygulamanızın gizli değeri getirip getirdiğini doğrulamak için Web uygulamanızı bir kez Azure 'da yayımlayın.

1. Visual Studio'da **key-vault-dotnet-core-quickstart** projesini seçin.
2. **Yayımla** > **Başlat** seçeneğini belirleyin.
3. **Oluştur**’u seçin.

Uygulamayı çalıştırdığınızda, gizli değeri alacağının sizin için olduğunu görmeniz gerekir.

Şimdi .NET ' de anahtar kasaınızdan gizli dizileri depolayan ve getiren bir Web uygulamasını başarıyla oluşturdunuz.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli olmadığında, sanal makineyi ve anahtar kasanızı silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

>[!div class="nextstepaction"]
>[Azure Key Vault Geliştirici Kılavuzu](key-vault-developers-guide.md)

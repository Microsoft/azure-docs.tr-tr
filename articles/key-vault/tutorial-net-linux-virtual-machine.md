---
title: Öğretici-Azure Key Vault gizli dizileri depolamak için bir Linux sanal makinesi ve ASP.NET konsol uygulaması kullanma | Microsoft Docs
description: Bu öğreticide, Azure Anahtar Kasası 'ndan gizli dizi okumak için bir ASP.NET Core uygulamasının nasıl yapılandırılacağını öğreneceksiniz.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 65c59ba299490ee2bbef849b6f7354abc05ad885
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003351"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Öğretici: Azure Key Vault gizli dizileri depolamak için bir Linux sanal makinesi ve .NET uygulaması kullanın

Azure Key Vault, API anahtarları ve uygulamalarınıza, hizmetlerinize ve BT kaynaklarınıza erişmesi gereken veritabanı bağlantı dizeleri gibi gizli dizileri korumanıza yardımcı olur.

Bu öğreticide, Azure kaynakları için Yönetilen kimlikler kullanarak Azure Key Vault bilgi okumak üzere bir .NET konsol uygulaması ayarlarsınız. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir anahtar kasası oluşturma
> * Key Vault bir gizli dizi depolayın
> * Azure Linux sanal makinesi oluşturma
> * Sanal makine için [yönetilen kimliği](../active-directory/managed-identities-azure-resources/overview.md) etkinleştir
> * Key Vault verileri okumak için konsol uygulaması için gerekli izinleri verme
> * Key Vault bir gizli dizi alma

Daha fazla ilerleyebilmemiz için [Anahtar Kasası temel kavramları](basic-concepts.md)hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

* [Git](https://git-scm.com/downloads).
* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* [Azure clı 2,0 veya üzeri](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ya da Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Yönetilen Hizmet Kimliği anlayın

Azure Key Vault kimlik bilgilerini güvenle depolayabilir ve bunlar kodunuzda yer almaz, ama bunları almak için Azure Key Vault'ta kimliğinizi doğrulamanız gerekir. Ancak, Key Vault kimlik doğrulaması yapmak için bir kimlik bilgisi gerekir. Bu, klasik bir önyükleme sorunudur. Azure ve Azure Active Directory (Azure AD) ile, Yönetilen Hizmet Kimliği (MSI), işlemlerin başlatılmasını çok daha kolay hale getiren bir önyükleme kimliği sağlayabilir.

Sanal makineler, App Service veya Işlevler gibi bir Azure hizmeti için MSI 'yi etkinleştirdiğinizde, Azure Azure Active Directory hizmet örneği için bir hizmet sorumlusu oluşturur. Hizmet sorumlusunun kimlik bilgilerini hizmet örneğine çıkartır.

![MSI](media/MSI.png)

Daha sonra, kodunuz, bir erişim belirteci almak için Azure kaynağında bulunan bir yerel meta veri hizmetini çağırır.
Kodunuz yerel MSI_ENDPOINT'ten aldığı erişim belirtecini kullanarak Azure Key Vault hizmetinde kimlik doğrulaması yapar.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure CLı kullanarak Azure 'da oturum açmak için şunu girin:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

`az group create` Komutunu kullanarak bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Batı ABD konumunda bir kaynak grubu oluşturun. Kaynak grubunuz için bir ad seçin ve aşağıdaki örnekte `YourResourceGroupName` değiştirin:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Bu kaynak grubunu öğretici genelinde kullanırsınız.

## <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma

Sonra, kaynak grubunuzda bir Anahtar Kasası oluşturun. Şu bilgileri belirtin:

* Anahtar Kasası adı: yalnızca rakam, harf ve kısa çizgi (0-9, a-z, A-Z ve \- ) içerebilen 3 ile 24 karakter arasında bir dize olabilir.
* Kaynak grubu adı
* Konumuna **Batı ABD**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Bu noktada, bu yeni kasada yalnızca Azure hesabınız herhangi bir işlem gerçekleştirmeye yetkilidir.

## <a name="add-a-secret-to-the-key-vault"></a>Anahtar kasasına gizli dizi ekleme

Şimdi bir gizli dizi eklersiniz. Gerçek dünyada bir senaryoda, bir SQL bağlantı dizesi veya güvenli tutmanız gereken ancak uygulamanız için kullanılabilir olan başka herhangi bir bilgiyi depolukmış olabilirsiniz.

Bu öğreticide, anahtar kasasında bir gizli dizi oluşturmak için aşağıdaki komutları yazın. Gizli dizi **Appsecret** olarak adlandırılır ve değeri **MySecret**olur.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linux sanal makinesi oluşturun

`az vm create` Komutuyla bir VM oluşturun.

Aşağıdaki örnek, **myVM** adlı bir VM oluşturur ve **azureuser** adlı bir kullanıcı hesabı ekler. Otomatik olarak bir SSH anahtarı oluşturmak ve varsayılan anahtar konumuna koymak için kullanılan parametre(~/PST`--generate-ssh-keys` ). Bunun yerine belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer. Aşağıdaki örnek çıktı, VM oluşturma işleminin başarılı olduğunu gösterir.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

VM 'nizden gelen çıktıyı bir `publicIpAddress` yere iade edin. Bu adresi sonraki adımlarda VM 'ye erişmek için kullanacaksınız.

## <a name="assign-an-identity-to-the-vm"></a>VM 'ye bir kimlik atama

Aşağıdaki komutu çalıştırarak sanal makineye sistem tarafından atanan bir kimlik oluşturun:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Komutun çıkışı şu olmalıdır:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

' İ bir yere unutmayın `systemAssignedIdentity`. Bunu bir sonraki adımda kullanırsınız.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Key Vault için VM kimliği izni verin

Artık oluşturduğunuz kimliğe Key Vault izin verebilirsiniz. Şu komutu çalıştırın:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM 'de oturum açma

Bir Terminal kullanarak sanal makinede oturum açın.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Linux 'ta .NET Core 'u yükler

Linux sanal makinenizde:

Aşağıdaki komutları çalıştırarak Microsoft ürün anahtarını güvenilir olarak kaydedin:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

İşletim sistemine bağlı olarak istenen sürüm ana bilgisayar paketi akışını ayarlayın:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

.NET 'i yükleyip sürümü denetleyin:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Örnek bir .NET uygulaması oluşturma ve çalıştırma

Aşağıdaki komutları çalıştırın. Konsolda "Merhaba Dünya" yazılı olduğunu görmeniz gerekir.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Gizli anahtarı getirmek için konsol uygulamasını düzenleyin

Program.cs dosyasını açın ve şu paketleri ekleyin:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Bu, uygulamanın anahtar kasasındaki gizli anahtara erişmesini sağlamak üzere sınıf dosyasını değiştirmek için iki adımlı bir işlemdir.

1. VM 'deki yerel MSI uç noktasından, Azure Active Directory bir belirteci getiren bir belirteç getirir.
1. Belirteci Key Vault geçirin ve gizli dizinizi getirin.

   Sınıf dosyasını aşağıdaki kodu içerecek şekilde düzenleyin:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
               System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
           }

           static string GetToken()
           {
               WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
               request.Headers.Add("Metadata", "true");
               WebResponse response = request.GetResponse();
               return ParseWebResponse(response, "access_token");
           }

           static string FetchSecretValueFromKeyVault(string token)
           {
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
               kvRequest.Headers.Add("Authorization", "Bearer "+  token);
               WebResponse kvResponse = kvRequest.GetResponse();
               return ParseWebResponse(kvResponse, "value");
           }

           private static string ParseWebResponse(WebResponse response, string tokenName)
           {
               string token = String.Empty;
               using (Stream stream = response.GetResponseStream())
               {
                   StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                   String responseString = reader.ReadToEnd();

                   JObject joResponse = JObject.Parse(responseString);
                   JValue ojObject = (JValue)joResponse[tokenName];
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

Artık Azure Linux sanal makinesi üzerinde çalışan bir .NET uygulamasında Azure Key Vault işlem yapmayı öğrendiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç kalmadığında kaynak grubunu, sanal makineyi ve tüm ilgili kaynakları silin. Bunu yapmak için VM 'nin kaynak grubunu seçin ve **Sil**' i seçin.

Şu `az keyvault delete` komutu kullanarak anahtar kasasını silin:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)

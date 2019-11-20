---
title: Öğretici-.NET 'te Windows sanal makinesiyle Azure Key Vault kullanma | Microsoft Docs
description: Bu öğreticide, anahtar kasaınızdan gizli dizi okumak için bir ASP.NET Core uygulaması yapılandırırsınız.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: fbda2f645308e30a6f408335b7a1b37095522921
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003310"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Öğretici: .NET içinde Windows sanal makinesiyle Azure Key Vault kullanma

Azure Key Vault, API anahtarları, uygulamalarınıza, hizmetlerinize ve BT kaynaklarınıza erişmeniz gereken veritabanı bağlantı dizeleri gibi gizli dizileri korumanıza yardımcı olur.

Bu öğreticide, Azure Key Vault bilgileri okumak için bir konsol uygulamasının nasıl alınacağını öğreneceksiniz. Bunu yapmak için Azure kaynakları için Yönetilen kimlikler kullanırsınız. 

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Bir kaynak grubu oluşturun.
> * Bir anahtar kasası oluşturma.
> * Anahtar kasasına gizli dizi ekleyin.
> * Anahtar kasasından bir gizli dizi alma.
> * Bir Azure sanal makinesi oluşturun.
> * Sanal makine için [yönetilen kimliği](../active-directory/managed-identities-azure-resources/overview.md) etkinleştirin.
> * VM kimliğine izin atayın.

Başlamadan önce [temel kavramları Key Vault](basic-concepts.md)okuyun. 

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Windows, Mac ve Linux için:
  * [Git](https://git-scm.com/downloads)
  * Bu öğretici, Azure CLı 'yı yerel olarak çalıştırmanızı gerektirir. Azure CLı sürüm 2.0.4 veya daha yeni bir sürümün yüklü olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. CLI’yı yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 2.0’ı yükleme](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Yönetilen Hizmet Kimliği hakkında

Azure Key Vault, kimlik bilgilerini güvenli bir şekilde depolar, bu nedenle kodunuzda görüntülenmezler. Ancak, anahtarlarınızı almak için Azure Key Vault kimlik doğrulaması yapmanız gerekir. Key Vault kimlik doğrulaması yapmak için bir kimlik bilgisi gerekir. Bu, klasik bir önyükleme divma. Yönetilen Hizmet Kimliği (MSI), işlemi basitleştiren bir _önyükleme kimliği_ sağlayarak bu sorunu çözer.

Azure sanal makineler, Azure App Service veya Azure Işlevleri gibi bir Azure hizmeti için MSI 'yi etkinleştirdiğinizde Azure bir [hizmet sorumlusu](basic-concepts.md)oluşturur. MSI bunu, Azure Active Directory (Azure AD) içindeki hizmetin örneği için yapar ve hizmet sorumlusu kimlik bilgilerini bu örneğe çıkartır. 

![MSI](media/MSI.png)

Ardından, bir erişim belirteci almak için, kodunuz Azure kaynağında bulunan bir yerel meta veri hizmetini çağırır. Azure Key Vault bir hizmette kimlik doğrulaması yapmak için, kodunuz yerel MSI uç noktasından aldığı erişim belirtecini kullanır. 

## <a name="create-resources-and-assign-permissions"></a>Kaynak oluşturma ve izin atama

Kodlamaya başlamadan önce bazı kaynaklar oluşturmanız, anahtar kasanıza bir gizli anahtar koymanız ve izinler atamanız gerekir.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure CLı kullanarak Azure 'da oturum açmak için şunu girin:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. [az group create](/cli/azure/group#az-group-create) komutunu kullanarak bir kaynak grubu oluşturun. 

Bu örnek Batı ABD konumunda bir kaynak grubu oluşturur:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Yeni oluşturduğunuz kaynak grubunuz Bu öğretici boyunca kullanılacaktır.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Bir Anahtar Kasası oluşturun ve gizli dizi ile doldurun

Aşağıdaki bilgilerle [az keykasa Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) komutunu sağlayarak kaynak grubunuzda bir Anahtar Kasası oluşturun:

* Anahtar Kasası adı: yalnızca rakam (0-9), harf (a-z, A-Z) ve kısa çizgi (-) içerebilen 3 ile 24 karakter arasında bir dize
* Kaynak grubu adı
* Konumuna **Batı ABD**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Bu noktada, Azure hesabınız, bu yeni anahtar kasası üzerinde işlem gerçekleştirme yetkisine sahip tek bir hesaptır.

Şimdi [az keykasasecret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) komutunu kullanarak anahtar kasanıza gizli dizi ekleyin


**Appsecret**adlı anahtar kasasında bir gizli dizi oluşturmak için aşağıdaki komutu girin:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Bu gizli anahtar, **MySecret**değerini depolar.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma
Aşağıdaki yöntemlerden birini kullanarak bir sanal makine oluşturun:

* [Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure portalı](../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>VM 'ye bir kimlik atama
[Az VM Identity Assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) komutuyla sanal makine için sistem tarafından atanan bir kimlik oluşturun:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Aşağıdaki kodda görüntülenen sistem tarafından atanan kimliği unutmayın. Yukarıdaki komutun çıkışı şöyle olacaktır: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>VM kimliğine izin atama
[Az keykasası Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) komutuyla anahtar kasanıza önceden oluşturulmuş kimlik izinlerini atayın:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Sanal makinede oturum açın

Sanal makinede oturum açmak için bağlanma bölümündeki yönergeleri izleyin [ve Windows çalıştıran bir Azure sanal makinesinde oturum açın](../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Konsol uygulamasını ayarlama

Bir konsol uygulaması oluşturun ve `dotnet` komutunu kullanarak gerekli paketleri yükler.

### <a name="install-net-core"></a>.NET Core'u yükleyin

.NET Core 'u yüklemek için [.net İndirmeleri](https://www.microsoft.com/net/download) sayfasına gidin.

### <a name="create-and-run-a-sample-net-app"></a>Örnek bir .NET uygulaması oluşturma ve çalıştırma

Bir komut istemi açın.

Aşağıdaki komutları çalıştırarak konsola "Merhaba Dünya" yazdırabilirsiniz:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Paketleri yükler

 Konsol penceresinde, bu hızlı başlangıç için gerekli .NET paketlerini yükler:

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Konsol uygulamasını düzenleme

*Program.cs* dosyasını açın ve şu paketleri ekleyin:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Aşağıdaki iki adımlı işlemde kodu içerecek sınıf dosyasını düzenleyin:

1. VM 'deki yerel MSI uç noktasından bir belirteç getirir. Bunun yapılması Azure AD 'den de bir belirteç getirir.
1. Belirteci anahtar kasanıza geçirin ve sonra gizli dizinizi getirin. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
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
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
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

Yukarıdaki kod, Windows sanal makinesinde Azure Key Vault işlemleri nasıl yapılacağını gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, sanal makineyi ve anahtar kasanızı silin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)

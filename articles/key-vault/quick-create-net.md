---
title: Hızlı başlangıç-.NET için Azure Key Vault istemci kitaplığı (v4)
description: .NET istemci kitaplığı 'nı (v4) kullanarak bir Azure Anahtar Kasası 'ndan gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: b91554269ab7ddf15458545715e7a3a40cc1ceb6
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158712"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Hızlı başlangıç: .NET için Azure Key Vault istemci kitaplığı (SDK v4)

.NET için Azure Key Vault istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere aşağıdaki adımları izleyin.

Azure Anahtar Kasası, bulut uygulamaları ve hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. .NET için Key Vault istemci kitaplığı 'nı kullanın:

- Anahtarlar ve parolalar üzerinde güvenlik ve denetim düzeyini artırın.
- Şifreleme anahtarlarını dakikalar içinde oluşturun ve içeri aktarın.
- Bulut ölçeği ve küresel yedeklilik ile gecikme süresini azaltın.
- SSL/TLS sertifikaları için görevleri basitleştirme ve otomatikleştirme.
- FIPS 140-2 düzey 2 doğrulanan HSM 'leri kullanın.

[API başvuru belgeleri](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [paketi (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 2,1 SDK veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) veya [Azure PowerShell](/powershell/azure/overview)

Bu hızlı başlangıçta, Windows terminalinde `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)ve Windows komutlarının çalıştırdığınız varsayılır ( [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)veya [Azure Cloud Shell](https://shell.azure.com/)gibi).

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-new-net-console-app"></a>Yeni .NET konsol uygulaması oluşturma

Konsol penceresinde, `akv-dotnet`adlı yeni bir .NET konsol uygulaması oluşturmak için `dotnet new` komutunu kullanın.

```console
dotnet new console -n key-vault-console-app
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. Uygulamayı ile oluşturabilirsiniz:

```console
dotnet build
```

Derleme çıktısı hiçbir uyarı veya hata içermemelidir.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Paketi yükler

Konsol penceresinde, .NET için Azure Key Vault istemci Kitaplığı ' nı yükledikten sonra:

```console
dotnet add package Azure.Security.KeyVault.Secrets --version 4.0.0
```

Bu hızlı başlangıçta, aşağıdaki paketleri de yüklemeniz gerekecektir:

```console
dotnet add package Azure.Identity --version 1.0.0
```

### <a name="create-a-resource-group-and-key-vault"></a>Kaynak grubu ve Anahtar Kasası oluşturma

Bu hızlı başlangıçta önceden oluşturulmuş bir Azure Anahtar Kasası kullanılmaktadır. [Azure CLI hızlı başlangıç](quick-create-cli.md), [Azure PowerShell hızlı](quick-create-powershell.md)başlangıç veya [Azure Portal Hızlı Başlangıç](quick-create-portal.md)adımlarını izleyerek bir Anahtar Kasası oluşturabilirsiniz. Alternatif olarak, yalnızca aşağıdaki Azure CLı komutlarını çalıştırabilirsiniz.

> [!Important]
> Her Anahtar Kasası benzersiz bir ada sahip olmalıdır. -Unique-keykasa-adı > < Aşağıdaki örneklerde anahtar kasanızın adıyla değiştirin.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

Bulut tabanlı bir .NET uygulamasının kimlik doğrulamasının en kolay yolu, yönetilen bir kimliktir; Ayrıntılar için [Azure Key Vault erişmek üzere App Service yönetilen bir kimlik kullanma](managed-identity.md) konusuna bakın. Kolaylık sağlaması için bu hızlı başlangıç, bir .NET konsol uygulaması oluşturur. Azure ile bir masaüstü uygulamasının kimlik doğrulaması için hizmet sorumlusu ve erişim denetimi ilkesi kullanılması gerekir.

Azure CLı [az ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) komutunu kullanarak bir hizmet ilkesi oluşturun:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Bu işlem, bir dizi anahtar/değer çifti döndürür. 

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

Aşağıdaki adımlarda kullanabilmemiz için ClientID, clientSecret ve Tenantıd ' yi göz önünde ayırın.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Anahtar kasanıza hizmet sorumlusu erişimi verin

ClientID 'yi [az keykasa Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) komutuna geçirerek, hizmet sorumlusuna izin veren Anahtar Kasası için bir erişim ilkesi oluşturun. Her iki anahtar ve gizli dizi için hizmet sorumlusu al, Listele ve ayarla izinlerini verin.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ortam değişkenlerini ayarlama

Uygulamamızda DefaultAzureCredential yöntemi üç ortam değişkenine dayanır: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`ve `AZURE_TENANT_ID`. Bu değişkenleri, yukarıdaki [bir hizmet sorumlusu oluşturma](#create-a-service-principal) adımında not ettiğiniz ClientID, ClientSecret ve tenantıd değerlerine ayarla ' yı kullanın.

Ayrıca, Anahtar Kasası adınızı `KEY_VAULT_NAME`adlı bir ortam değişkeni olarak kaydetmeniz gerekir.

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

`setx`her çağırdığınızda, "SUCCESS: belirtilen değer kaydedildi" yanıtını almalısınız.

## <a name="object-model"></a>Nesne modeli

.NET için Azure Key Vault istemci kitaplığı, sertifikaları ve gizli dizileri gibi anahtarları ve ilgili varlıkları yönetmenizi sağlar. Aşağıdaki kod örnekleri, bir istemci oluşturma, gizli anahtar ayarlama, gizli anahtar alma ve gizli dizi silme işlemlerinin nasıl yapılacağını gösterir.

Konsol uygulamasının tamamı https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appkullanılabilir.

## <a name="code-examples"></a>Kod örnekleri

### <a name="add-directives"></a>Yönergeler ekleme

Aşağıdaki yönergeleri kodunuzun en üstüne ekleyin:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Anahtar kasanıza kimlik doğrulama ve Anahtar Kasası istemcisi oluşturma, yukarıdaki [ortam değişkenlerini ayarla](#set-environmental-variables) adımında bulunan ortam değişkenlerine bağlıdır. Anahtar kasanızın adı, Anahtar Kasası URI 'sine, "https://<-anahtar-kasa-adı >. kasa. Azure. net" biçiminde genişletilir.

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Gizli dizi Kaydet

Uygulamanızın kimliği doğrulandığına göre, istemciyi kullanarak anahtar kasanıza gizli dizi ekleyebilirsiniz [. SetSecret yöntemi](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) bu, gizli dizi için bir ad gerektirir; Bu örnekte "mySecret" kullanıyoruz.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Parolanın [az keykasası Secret Show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) komutuyla ayarlandığını doğrulayabilirsiniz:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Gizli dizi alma

Artık önceden ayarlanan değeri istemcisiyle elde edebilirsiniz [. GetSecret yöntemi](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Gizli bilgileriniz artık `secret.Value`olarak kaydedilir.

### <a name="delete-a-secret"></a>Gizli anahtarı silme

Son olarak, anahtar kasanızdan parolayı istemciyle silelim [. DeleteSecret yöntemi](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Parolanın [az keykasasecret Show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) komutuyla yapıldığını doğrulayabilirsiniz:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, anahtar Kasanızı ve ilgili kaynak grubunu kaldırmak için Azure CLı veya Azure PowerShell kullanabilirsiniz.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Örnek kod

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Anahtar Kasası oluşturdunuz, gizli dizi depolandı ve bu gizli dizi alındı. [GitHub 'da konsol uygulamasının tamamına](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)bakın.

Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [.NET kullanarak Azure Key Vault Için hizmetten hizmete kimlik doğrulaması](service-to-service-authentication.md) uygulama
- [Azure Key Vault genel bakışını](key-vault-overview.md) okuyun
- [Azure Key Vault geliştirici kılavuzuna](key-vault-developers-guide.md) bakın
- [Anahtarlar, gizli diziler ve sertifikalar](about-keys-secrets-and-certificates.md) hakkında bilgi edinin
- [En iyi uygulamaları](key-vault-best-practices.md) gözden geçirin Azure Key Vault

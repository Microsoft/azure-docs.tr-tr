---
title: Hızlı başlangıç-.NET için Azure Key Vault gizlilikler istemci kitaplığı (sürüm 4)
description: .NET istemci kitaplığı 'nı kullanarak bir Azure Anahtar Kasası 'nda gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin (sürüm 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 6338b76e2f14e9f842b3395113badadcd37c8d32
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814181"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Hızlı başlangıç: .NET için Azure Key Vault gizli istemci kitaplığı (SDK v4)

.NET için Azure Key Vault gizli istemci kitaplığı ile çalışmaya başlayın. [Azure Key Vault](../general/overview.md) , gizli dizileri için güvenli bir mağaza sağlayan bir bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu hızlı başlangıçta, .NET istemci kitaplığını kullanarak bir Azure Anahtar Kasası 'ndan gizli dizileri oluşturmayı, almayı ve silmeyi öğreneceksiniz

Key Vault istemci kitaplığı kaynakları:

[API başvuru belgeleri](/dotnet/api/azure.security.keyvault.secrets)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Key Vault ve gizli dizileri hakkında daha fazla bilgi için bkz.:
- [Key Vault genel bakış](../general/overview.md)
- [Gizli dizi genel bakış](about-secrets.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault- [Azure clı](../general/quick-create-cli.md) [Azure Portal](../general/quick-create-portal.md) veya [Azure PowerShell](../general/quick-create-powershell.md) kullanarak bir tane oluşturabilirsiniz.

Bu hızlı başlangıç, `dotnet` Azure CLI 'yi kullanıyor

## <a name="setup"></a>Kurulum

Bu hızlı başlangıç, Azure 'da kullanıcının kimliğini doğrulamak için Azure CLı ile Azure Identity Library kullanıyor. Geliştiriciler, aramalarını doğrulamak için Visual Studio veya Visual Studio Code de kullanabilir. daha fazla bilgi için bkz. [Azure Identity istemci kitaplığı ile Istemci kimlik doğrulaması](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

1. `login` komutunu çalıştırın.

    ```azurecli-interactive
    az login
    ```

    CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

    Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin.

2. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

### <a name="grant-access-to-your-key-vault"></a>Anahtar kasanıza erişim izni verin

Anahtar kasanız için Kullanıcı hesabınıza gizli izinler veren bir erişim ilkesi oluşturun

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

### <a name="create-new-net-console-app"></a>Yeni .NET konsol uygulaması oluşturma

1. Bir komut kabuğunda, aşağıdaki komutu çalıştırarak adında bir proje oluşturun `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Yeni oluşturulan *Anahtar Kasası-konsol-uygulama* dizinine geçin ve projeyi derlemek için aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet build
    ```

    Derleme çıktısı hiçbir uyarı veya hata içermemelidir.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Paketleri yükler

Komut kabuğundan, .NET için Azure Key Vault gizli istemci Kitaplığı ' nı yükledikten sonra:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Bu hızlı başlangıçta Azure Identity için Azure SDK istemci kitaplığı 'nı yüklemeniz gerekir:

```dotnetcli
dotnet add package Azure.Identity
```
#### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Bu uygulama, Anahtar Kasası adını adında bir ortam değişkeni olarak kullanıyor `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS veya Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Nesne modeli

.NET için Azure Key Vault gizli istemci kitaplığı, gizli dizileri yönetmenizi sağlar. [Kod örnekleri](#code-examples) bölümünde, bir istemci oluşturma, gizli anahtar ayarlama, gizli anahtar alma ve gizli dizi silme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="code-examples"></a>Kod örnekleri

### <a name="add-directives"></a>Yönergeler ekleme

Aşağıdaki yönergeleri *program. cs*' nin en üstüne ekleyin:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) [' DefaultAzureCredential () '](/dotnet/api/azure.identity.defaultazurecredential) sınıfını kullanarak kimlik sağlamak için farklı seçeneklerle aynı kodu farklı ortamlarda kullanmanıza olanak tanır. Anahtar Kasası kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Geliştirici Kılavuzu](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Gizli dizi Kaydet

Konsol uygulamasının kimliğinin doğrulandığına göre, anahtar kasasına bir gizli dizi ekleyin. Bu görev için [Setsecretasync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) yöntemini kullanın. Yöntemin ilk parametresi, &mdash; Bu örnekteki "MySecret" gizli anahtarı için bir ad kabul eder.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Gizli dizi adı varsa, yukarıdaki kod bu gizli dizinin yeni bir sürümünü oluşturur.


### <a name="retrieve-a-secret"></a>Gizli dizi alma

Artık [Getsecretasync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) yöntemiyle daha önce ayarlanan değeri alabilirsiniz.

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Gizli anahtar artık olarak kaydedilir `secret.Value` .

### <a name="delete-a-secret"></a>Gizli anahtarı silme

Son olarak, [Startdeletesecretasync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) ve [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient) yöntemleriyle anahtar kasanızdan parolayı silelim.

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Örnek kod

Aşağıdaki adımları tamamlayarak, .NET Core konsol uygulamasını Key Vault etkileşimde bulunmak üzere değiştirin:

1. *Program. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Test ve doğrulama

1. Uygulamayı çalıştırmak için aşağıdaki komutu yürütün.

    ```dotnetcli
    dotnet run
    ```

1. İstendiğinde, gizli bir değer girin. Örneğin, mySecretPassword.

Aşağıdaki çıkışın bir varyasyonu görüntülenir:

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>Sonraki adımlar

Key Vault ve bunu uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [App Service uygulama öğreticisindeki bir erişim Key Vault](../general/tutorial-net-create-vault-azure-web-app.md) görün
- [Sanal makine öğreticiden bir erişim Key Vault](../general/tutorial-net-virtual-machine.md) görün
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin

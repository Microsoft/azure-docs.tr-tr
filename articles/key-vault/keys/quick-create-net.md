---
title: Hızlı başlangıç-.NET için Azure Key Vault anahtarları istemci kitaplığı (sürüm 4)
description: .NET istemci kitaplığı 'nı kullanarak Azure Anahtar Kasası 'ndan anahtar oluşturmayı, almayı ve silmeyi öğrenin (sürüm 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 5c1e4d64ba3359a07dddbbf89774e31815935230
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818431"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Hızlı başlangıç: .NET için Azure Key Vault anahtar istemci kitaplığı (SDK v4)

.NET için Azure Key Vault anahtar istemci kitaplığı ile çalışmaya başlayın. [Azure Key Vault](../general/overview.md) , şifreleme anahtarları için güvenli bir mağaza sağlayan bir bulut hizmetidir. Şifreleme anahtarlarını, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu hızlı başlangıçta, .NET anahtar istemci kitaplığını kullanarak Azure Anahtar Kasası 'ndan anahtar oluşturmayı, almayı ve silmeyi öğreneceksiniz

Key Vault anahtar istemci kitaplığı kaynakları:

[API başvuru belgeleri](/dotnet/api/azure.security.keyvault.keys)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Key Vault ve anahtarları hakkında daha fazla bilgi için bkz.:
- [Key Vault genel bakış](../general/overview.md)
- [Anahtarlara genel bakış](about-keys.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK veya üzeri](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault- [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)veya [Azure PowerShell](../general/quick-create-powershell.md)kullanarak bir tane oluşturabilirsiniz.

## <a name="setup"></a>Kurulum

Bu hızlı başlangıç, Azure hizmetlerine kullanıcının kimliğini doğrulamak için Azure kimlik kitaplığı 'nı kullanıyor. Geliştiriciler, aramalarını doğrulamak için Visual Studio veya Visual Studio Code de kullanabilir. daha fazla bilgi için bkz. [Azure Identity istemci kitaplığı ile Istemci kimlik doğrulaması](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

1. `login` komutunu çalıştırın.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli-interactive
    az login
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
    ---

    Azure CLı veya Azure PowerShell varsayılan tarayıcınızı açabilirseniz, bunu yapılır ve bir Azure oturum açma sayfası yükler.

    Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin.

2. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

#### <a name="grant-access-to-your-key-vault"></a>Anahtar kasanıza erişim izni verin

Anahtar kasanız için Kullanıcı hesabınıza anahtar izinleri veren bir erişim ilkesi oluşturun

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -UserPrincipalName user@domain.com -PermissionsToSecrets delete,get,list,set,purge
```
---

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

Komut kabuğundan, .NET için Azure Key Vault anahtar istemci Kitaplığı ' nı yükledikten sonra:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
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
```azurepowershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS veya Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Nesne modeli

.NET için Azure Key Vault anahtar istemci kitaplığı, anahtarları yönetmenizi sağlar. [Kod örnekleri](#code-examples) bölümünde, bir istemci oluşturma, anahtar ayarlama, anahtar alma ve bir anahtarı silme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="code-examples"></a>Kod örnekleri

### <a name="add-directives"></a>Yönergeler ekleme

Aşağıdaki yönergeleri *program. cs*' nin en üstüne ekleyin:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) [' DefaultAzureCredential () '](/dotnet/api/azure.identity.defaultazurecredential) sınıfını kullanarak kimlik sağlamak için farklı seçeneklerle aynı kodu farklı ortamlarda kullanmanıza olanak tanır. Anahtar Kasası kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Geliştirici Kılavuzu](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Anahtar kaydetme

Bu görev için [Createkeyasync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) yöntemini kullanın. Metodun parametreleri bir anahtar adı ve [anahtar türü](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype)kabul eder.

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Anahtar adı varsa, yukarıdaki kod bu anahtarın yeni bir sürümünü oluşturur.

### <a name="retrieve-a-key"></a>Anahtar alma

Artık, daha önce oluşturulan anahtarı [Getkeyasync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync) yöntemiyle elde edebilirsiniz.

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Bir anahtarı silme

Son olarak, [Startdeletekeyasync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) ve [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync) yöntemleriyle anahtar kasaınızdan anahtarı silip temizlim.

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Örnek kod

Aşağıdaki adımları tamamlayarak, .NET Core konsol uygulamasını Key Vault etkileşimde bulunmak üzere değiştirin:

- *Program. cs* dosyasındaki kodu aşağıdaki kodla değiştirin:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Test ve doğrulama

1.  Projeyi derlemek için aşağıdaki komutu yürütün

    ```dotnetcli
    dotnet build
    ```

1. Uygulamayı çalıştırmak için aşağıdaki komutu yürütün.

    ```dotnetcli
    dotnet run
    ```

1. İstendiğinde, gizli bir değer girin. Örneğin, mySecretPassword.

    Aşağıdaki çıkışın bir varyasyonu görüntülenir:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Anahtar Kasası oluşturdunuz, bir anahtar depo, ve bu anahtarı almıştır. 

Key Vault ve bunu uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Anahtarlara genel bir bakış](about-keys.md) okuyun
- [App Service uygulama öğreticisindeki bir erişim Key Vault](../general/tutorial-net-create-vault-azure-web-app.md) görün
- [Sanal makine öğreticiden bir erişim Key Vault](../general/tutorial-net-virtual-machine.md) görün
- [Azure Key Vault geliştirici kılavuzuna](../general/developers-guide.md) bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin

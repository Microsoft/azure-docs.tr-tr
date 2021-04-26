---
title: Öğretici-.NET 'teki bir sanal makine ile Azure Key Vault kullanma | Microsoft Docs
description: Bu öğreticide, anahtar kasaınızdan gizli dizi okumak için bir sanal makineyi ASP.NET Core uygulamasına yapılandırırsınız.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: ba299ff52ccdf811d739efdb1d379889444a02b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868402"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Öğretici: .NET 'teki bir sanal makine ile Azure Key Vault kullanma

Azure Key Vault, API anahtarları, uygulamalarınıza, hizmetlerinize ve BT kaynaklarınıza erişmeniz gereken veritabanı bağlantı dizeleri gibi gizli dizileri korumanıza yardımcı olur.

Bu öğreticide, Azure Key Vault bilgileri okumak için bir konsol uygulamasının nasıl alınacağını öğreneceksiniz. Uygulama, Key Vault kimlik doğrulaması yapmak için sanal makine tarafından yönetilen kimliği kullanır. 

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Bir kaynak grubu oluşturun.
> * Anahtar kasası oluşturma.
> * Anahtar kasasına gizli dizi ekleyin.
> * Anahtar kasasından bir gizli dizi alma.
> * Bir Azure sanal makinesi oluşturun.
> * Sanal makine için [yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md) etkinleştirin.
> * VM kimliğine izin atayın.

Başlamadan önce [temel kavramları Key Vault](basic-concepts.md)okuyun. 

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Windows, Mac ve Linux için:
  * [Git](https://git-scm.com/downloads)
  * [.NET Core 3,1 SDK veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli) veya [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-resources-and-assign-permissions"></a>Kaynak oluşturma ve izin atama

Kodlamaya başlamadan önce bazı kaynaklar oluşturmanız, anahtar kasanıza bir gizli anahtar koymanız ve izinler atamanız gerekir.

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

Aşağıdaki komutu kullanarak Azure 'da oturum açmak için:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Connect-AzAccount
```
---

## <a name="create-a-resource-group-and-key-vault"></a>Kaynak grubu ve Anahtar Kasası oluşturma

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Anahtar kasanızı gizli dizi ile doldurma

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma
Aşağıdaki yöntemlerden birini kullanarak bir Windows veya Linux sanal makinesi oluşturun:

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure portalı](../../virtual-machines/windows/quick-create-portal.md) | [Azure portalı](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>VM 'ye bir kimlik atama
Aşağıdaki örneğe sahip sanal makine için sistem tarafından atanan bir kimlik oluşturun:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Aşağıdaki kodda görüntülenen sistem tarafından atanan kimliği unutmayın. Yukarıdaki komutun çıkışı şöyle olacaktır:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$vm = Get-AzVM -Name <NameOfYourVirtualMachine>
Update-AzVM -ResourceGroupName <YourResourceGroupName> -VM $vm -IdentityType SystemAssigned
```

Aşağıdaki kodda görüntülenen PrincipalId ' i unutmayın. Yukarıdaki komutun çıkışı şöyle olacaktır: 


```output
PrincipalId          TenantId             Type             UserAssignedIdentities
-----------          --------             ----             ----------------------
xxxxxxxx-xx-xxxxxx   xxxxxxxx-xxxx-xxxx   SystemAssigned
```
---

## <a name="assign-permissions-to-the-vm-identity"></a>VM kimliğine izin atama
[Az keykasası Set-Policy](/cli/azure/keyvault#az_keyvault_set_policy) komutuyla anahtar kasanıza önceden oluşturulmuş kimlik izinlerini atayın:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions  get list set delete
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -ResourceGroupName <YourResourceGroupName> -VaultName '<your-unique-key-vault-name>' -ObjectId '<VMSystemAssignedIdentity>' -PermissionsToSecrets  get,list,set,delete
```
---

## <a name="sign-in-to-the-virtual-machine"></a>Sanal makinede oturum açın

Sanal makinede oturum açmak için bağlanma bölümündeki yönergeleri izleyin [ve bir Azure Windows sanal makinesinde oturum açın](../../virtual-machines/windows/connect-logon.md) veya [Azure Linux sanal makinesine bağlanın ve oturum açın](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>Konsol uygulamasını ayarlama

Bir konsol uygulaması oluşturun ve komutunu kullanarak gerekli paketleri yükler `dotnet` .

### <a name="install-net-core"></a>.NET Core'u yükleyin

.NET Core 'u yüklemek için [.net İndirmeleri](https://dotnet.microsoft.com/download) sayfasına gidin.

### <a name="create-and-run-a-sample-net-app"></a>Örnek bir .NET uygulaması oluşturma ve çalıştırma

Bir komut istemi açın.

Aşağıdaki komutları çalıştırarak konsola "Merhaba Dünya" yazdırabilirsiniz:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Paketi yükler

Konsol penceresinde, .NET için Azure Key Vault gizli dizi istemci Kitaplığı ' nı yükledikten sonra:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Bu hızlı başlangıçta, Azure Key Vault kimlik doğrulaması yapmak için aşağıdaki kimlik paketini yüklemeniz gerekir:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Konsol uygulamasını düzenleme

*Program. cs* dosyasını açın ve şu paketleri ekleyin:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Bu satırları ekleyin ve URI 'yi `vaultUri` anahtar kasanızın sayısını yansıtacak şekilde güncelleyerek. Aşağıdaki kod, kimlik doğrulaması için uygulama tarafından yönetilen kimliğin belirtecini kullanan Anahtar Kasası kimlik doğrulaması için  [' DefaultAzureCredential () '](/dotnet/api/azure.identity.defaultazurecredential) kullanıyor. Ayrıca, Anahtar Kasası kısıtlanmadan yeniden denemeler için üstel geri alma özelliği de kullanılıyor.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";
            string keyVaultName = "<your-key-vault-name>";
            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

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
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, sanal makineyi ve anahtar kasanızı silin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](/rest/api/keyvault/)

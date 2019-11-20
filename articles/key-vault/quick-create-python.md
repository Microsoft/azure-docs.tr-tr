---
title: Hızlı başlangıç-Python için Azure Key Vault istemci kitaplığı
description: Python istemci kitaplığını kullanarak bir Azure Anahtar Kasası 'nda gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: b8a6099bb4beb7a3c3b6b132584dcb397f69ead5
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158686"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Hızlı başlangıç: Python için Azure Key Vault istemci kitaplığı

Python için Azure Key Vault istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere aşağıdaki adımları izleyin.

Azure Anahtar Kasası, bulut uygulamaları ve hizmetleri tarafından kullanılan şifreleme anahtarlarının ve gizli anahtarların korunmasına yardımcı olur. Python için Key Vault istemci kitaplığını şu şekilde kullanın:

- Anahtarlar ve parolalar üzerinde güvenlik ve denetim düzeyini artırın.
- Şifreleme anahtarlarını dakikalar içinde oluşturun ve içeri aktarın.
- Bulut ölçeği ve küresel yedeklilik ile gecikme süresini azaltın.
- SSL/TLS sertifikaları için görevleri basitleştirme ve otomatikleştirme.
- FIPS 140-2 düzey 2 doğrulanan HSM 'leri kullanın.

[API başvuru belgeleri](/python/api/overview/azure/key-vault?view=azure-python) | [kitaplığı kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [paketi (Python paket dizini)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 veya üzeri
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) veya [Azure PowerShell](/powershell/azure/overview)

Bu hızlı başlangıçta, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 'Yi bir Linux Terminal penceresinde çalıştırdığınız varsayılır.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-package"></a>Paketi yükler

Konsol penceresinde, Python için Azure Key Vault gizli dizi Kitaplığı ' nı yükler.

```console
pip install azure-keyvault-secrets
```

Bu hızlı başlangıçta, Azure. Identity paketini de yüklemeniz gerekecektir:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Kaynak grubu ve Anahtar Kasası oluşturma

Bu hızlı başlangıçta önceden oluşturulmuş bir Azure Anahtar Kasası kullanılmaktadır. [Azure CLI hızlı başlangıç](quick-create-cli.md), [Azure PowerShell hızlı](quick-create-powershell.md)başlangıç veya [Azure Portal Hızlı Başlangıç](quick-create-portal.md)adımlarını izleyerek bir Anahtar Kasası oluşturabilirsiniz. Alternatif olarak, aşağıdaki Azure CLı komutlarını çalıştırabilirsiniz.

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

Aşağıdaki [ortam değişkenini ayarla](#set-environmental-variables) adımında kullanabilmemiz Için ClientID ve ClientSecret değerlerini göz önünde ayırın.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Anahtar kasanıza hizmet sorumlusu erişimi verin

ClientID 'yi [az keykasa Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) komutuna geçirerek, hizmet sorumlusuna izin veren Anahtar Kasası için bir erişim ilkesi oluşturun. Her iki anahtar ve gizli dizi için hizmet sorumlusu al, Listele ve ayarla izinlerini verin.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Ortam değişkenlerini ayarlama

Uygulamamızda DefaultAzureCredential yöntemi üç ortam değişkenine dayanır: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`ve `AZURE_TENANT_ID`. Bu değişkenleri, `export VARNAME=VALUE` biçimini kullanarak [hizmet sorumlusu oluşturma](#create-a-service-principal) adımında not ettiğiniz ClientID, ClientSecret ve tenantıd değerlerine ayarlayın. (Bu yöntem yalnızca geçerli kabuğunuzun ve kabuktan oluşturulan süreçlerin değişkenlerini ayarlar; bu değişkenleri ortamınıza kalıcı olarak eklemek için `/etc/environment ` dosyanızı düzenleyin.) 

Ayrıca, Anahtar Kasası adınızı `KEY_VAULT_NAME`adlı bir ortam değişkeni olarak kaydetmeniz gerekir.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Nesne modeli

Python için Azure Key Vault istemci kitaplığı, sertifikaları ve gizli dizileri gibi anahtarları ve ilgili varlıkları yönetmenizi sağlar. Aşağıdaki kod örnekleri, bir istemci oluşturma, gizli anahtar ayarlama, gizli anahtar alma ve gizli dizi silme işlemlerinin nasıl yapılacağını gösterir.

Konsol uygulamasının tamamı https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appkullanılabilir.

## <a name="code-examples"></a>Kod örnekleri

### <a name="add-directives"></a>Yönergeler ekleme

Aşağıdaki yönergeleri kodunuzun en üstüne ekleyin:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Anahtar kasanıza kimlik doğrulama ve Anahtar Kasası istemcisi oluşturma, yukarıdaki [ortam değişkenlerini ayarla](#set-environmental-variables) adımında bulunan ortam değişkenlerine bağlıdır. Anahtar kasanızın adı, Anahtar Kasası URI 'sine, "https://<-anahtar-kasa-adı >. kasa. Azure. net" biçiminde genişletilir.

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_endpoint=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Gizli dizi Kaydet

Uygulamanızın kimliği doğrulandığına göre, istemciyi kullanarak anahtar kasanıza gizli dizi ekleyebilirsiniz. SetSecret yöntemi] (/DotNet/api/Microsoft.Azure.keyvault.keyvaultclientextensions.setsecretasync) Bu, gizli anahtar için bir ad gerektirir; Bu örnekte "mySecret" kullanıyoruz.  

```python
client.set_secret(secretName, secretValue);
```

Parolanın [az keykasası Secret Show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) komutuyla ayarlandığını doğrulayabilirsiniz:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Gizli dizi alma

Artık önceden ayarlanan değeri istemcisiyle elde edebilirsiniz [. GetSecret yöntemi](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Gizli bilgileriniz artık `retrieved_secret.value`olarak kaydedilir.

### <a name="delete-a-secret"></a>Gizli anahtarı silme

Son olarak, anahtar kasanızdan parolayı istemciyle silelim [. DeleteSecret yöntemi](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName);
```

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

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

secretName = "mySecret";

keyVaultName = os.environ["KEY_VAULT_NAME"];
KVUri = "https://" + keyVaultName + ".vault.azure.net";

credential = DefaultAzureCredential()

client = SecretClient(vault_endpoint=KVUri, credential=credential)

print("Input the value of your secret > ");
secretValue = raw_input();

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

client.set_secret(secretName, secretValue);

print(" done.");

print("Forgetting your secret.");
secretValue = "";
print("Your secret is '" + secretValue + "'.");

print("Retrieving your secret from " + keyVaultName + ".");

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.");
print("Deleting your secret from " + keyVaultName + " ...");

client.delete_secret(secretName);

print(" done.");
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Anahtar Kasası oluşturdunuz, gizli dizi depolandı ve bu gizli dizi alındı. [GitHub 'da konsol uygulamasının tamamına](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)bakın.

Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault genel bakışını](key-vault-overview.md) okuyun
- [Azure Key Vault geliştirici kılavuzuna](key-vault-developers-guide.md) bakın
- [Anahtarlar, gizli diziler ve sertifikalar](about-keys-secrets-and-certificates.md) hakkında bilgi edinin
- [En iyi uygulamaları](key-vault-best-practices.md) gözden geçirin Azure Key Vault
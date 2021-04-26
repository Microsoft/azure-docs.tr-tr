---
title: Hızlı başlangıç – Azure Key Vault Python istemci kitaplığı – anahtarları Yönet
description: Python istemci kitaplığını kullanarak Azure Anahtar Kasası 'ndan anahtar oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: dbff065fc4ee0f4618cae3fa0b012a286d4a0645
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815423"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Hızlı başlangıç: Python için Azure Key Vault anahtarlar istemci kitaplığı

Python için Azure Key Vault istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere aşağıdaki adımları izleyin. Şifreleme anahtarlarını depolamak için Key Vault kullanarak, kodunuzda bu anahtarların depolanmasını önleyin ve bu sayede uygulamanızın güvenliği artar.

[API başvuru belgeleri](/python/api/overview/azure/keyvault-keys-readme)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Paket (Python paket dizini)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + veya 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Bu hızlı başlangıçta, [Azure CLI](/cli/azure/install-azure-cli) 'Yi bir Linux Terminal penceresinde çalıştırdığınız varsayılır.

## <a name="set-up-your-local-environment"></a>Yerel ortamınızı ayarlama

Bu hızlı başlangıç, Azure 'da kullanıcının kimliğini doğrulamak için Azure CLı ile Azure Identity Library kullanıyor. Geliştiriciler, aramalarını doğrulamak için Visual Studio veya Visual Studio Code de kullanabilir. daha fazla bilgi için bkz. [Azure Identity istemci kitaplığı ile Istemci kimlik doğrulaması](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

1. `login` komutunu çalıştırın.

    ```azurecli-interactive
    az login
    ```

    CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

    Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin.

2. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

### <a name="install-the-packages"></a>Paketleri yükler

1. Bir Terminal veya komut isteminde uygun bir proje klasörü oluşturun ve ardından [Python sanal ortamlarını kullanma](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)bölümünde açıklandığı gibi bir Python sanal ortamı oluşturun ve etkinleştirin.

1. Azure Active Directory Identity Library 'yi yükler:

    ```terminal
    pip install azure.identity
    ```


1. Key Vault anahtarı istemci kitaplığını yükler:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Kaynak grubu ve Anahtar Kasası oluşturma

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Anahtar kasanıza erişim izni verin

Anahtar kasanız için Kullanıcı hesabınıza gizli izin veren bir erişim ilkesi oluşturun.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Örnek kodu oluşturma

Python için Azure Key Vault anahtar istemci kitaplığı, şifreleme anahtarlarını yönetmenizi sağlar. Aşağıdaki kod örneği, bir istemci oluşturma, anahtar ayarlama, anahtar alma ve bir anahtarı silme işlemlerinin nasıl yapılacağını gösterir.

Bu kodu içeren *kv_keys. Kopyala* adlı bir dosya oluşturun.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kodu çalıştırma

Önceki bölümdeki kodun *kv_keys. Kopyala* adlı bir dosyada bulunduğundan emin olun. Ardından aşağıdaki komutla kodu çalıştırın:

```terminal
python kv_keys.py
```

- İzin hatalarıyla karşılaşırsanız, [ `az keyvault set-policy` komutunu](#grant-access-to-your-key-vault)çalıştırdığınızdan emin olun.
- Kodu aynı anahtar adıyla yeniden çalıştırmak, "(çakışma) anahtarı <name> Şu anda silinmiş ancak kurtarılabilir durumda." hatasını verebilir. Farklı bir anahtar adı kullanın.

## <a name="code-details"></a>Kod ayrıntıları

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, kimlik sağlamak için farklı seçeneklere sahip farklı ortamlarda aynı kodun kullanılmasına izin veren  [' DefaultAzureCredential () '](/python/api/azure-identity/azure.identity.defaultazurecredential) sınıfını kullanıyor. Daha fazla bilgi için bkz. [varsayılan Azure kimlik bilgisi kimlik doğrulaması](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Anahtar kaydetme

Anahtar Kasası için istemci nesnesini aldıktan sonra, [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) yöntemini kullanarak bir anahtar saklayabilirsiniz: 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

[Create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) veya [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-)de kullanabilirsiniz.

Bir yöntemi çağırmak, `create` Anahtar Kasası Için Azure REST API bir çağrı oluşturur.

Azure, isteği gerçekleştirirken arayanın kimliğini (hizmet sorumlusu) istemciye verdiğiniz kimlik bilgisi nesnesini kullanarak doğrular.

## <a name="retrieve-a-key"></a>Anahtar alma

Key Vault bir anahtarı okumak için [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) yöntemi kullanın:

```python
retrieved_key = client.get_key(keyName)
 ```

Anahtarın, [az keykasakey Show](/cli/azure/keyvault/key?#az_keyvault_key_show)Azure CLI komutuyla ayarlandığını da doğrulayabilirsiniz.

### <a name="delete-a-key"></a>Bir anahtarı silme

Bir anahtarı silmek için [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) yöntemi kullanın:

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key`Yöntemi zaman uyumsuzdur ve bir Poller nesnesi döndürür. Poller 'ın metodunu çağırma işleminin `result` tamamlanmasını bekler.

Anahtarın Azure CLı komutuyla silindiğini, [az keykasakey Show](/cli/azure/keyvault/key?#az_keyvault_key_show)komutunu doğrulayabilirsiniz.

Silinen bir anahtar, bir süredir silinmiş ancak kurtarılabilir durumunda kalır. Kodu yeniden çalıştırırsanız, farklı bir anahtar adı kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ayrıca, [Sertifikalar](../certificates/quick-create-python.md) ve [gizli](../secrets/quick-create-python.md)dizileri de denemek istiyorsanız, bu makalede oluşturulan Key Vault yeniden kullanabilirsiniz.

Aksi takdirde, bu makalede oluşturulan kaynaklarla işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Anahtar Kasası'na Genel Bakış](../general/overview.md)
- [Anahtar kasasına erişimin güvenliğini sağlama](../general/security-features.md)
- [Geliştirici Kılavuzu Azure Key Vault](../general/developers-guide.md)
- [Key Vault güvenliğe genel bakış](../general/security-features.md)
- [Key Vault ile kimlik doğrulama](../general/authentication.md)

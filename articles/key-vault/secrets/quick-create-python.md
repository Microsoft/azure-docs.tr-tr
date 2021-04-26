---
title: Hızlı başlangıç – Azure Key Vault Python istemci kitaplığı – gizli dizileri yönetme
description: Python istemci kitaplığını kullanarak bir Azure Anahtar Kasası 'nda gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: d7b5144264b31e62eac43513d6a851d09a09c60f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814055"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Hızlı başlangıç: Python için Azure Key Vault gizli istemci kitaplığı

Python için Azure Key Vault gizli istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere aşağıdaki adımları izleyin. Gizli dizileri depolamak için Key Vault kullanarak, kodunuzun içinde gizli dizileri saklamaktan kaçının ve bu, uygulamanızın güvenliğini artırır.

[API başvuru belgeleri](/python/api/overview/azure/keyvault-secrets-readme)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Paket (Python paket dizini)](https://pypi.org/project/azure-keyvault-secrets/)

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
    pip install azure-identity
    ```


1. Key Vault gizli dizileri kitaplığı 'nı yükler:

    ```terminal
    pip install azure-keyvault-secrets
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

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Örnek kodu oluşturma

Python için Azure Key Vault gizli istemci kitaplığı, gizli dizileri yönetmenizi sağlar. Aşağıdaki kod örneği, bir istemci oluşturma, gizli anahtar ayarlama, gizli anahtar alma ve gizli dizi silme işlemlerinin nasıl yapılacağını gösterir.

Bu kodu içeren *kv_secrets. Kopyala* adlı bir dosya oluşturun.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kodu çalıştırma

Önceki bölümdeki kodun *kv_secrets. Kopyala* adlı bir dosyada bulunduğundan emin olun. Ardından aşağıdaki komutla kodu çalıştırın:

```terminal
python kv_secrets.py
```

- İzin hatalarıyla karşılaşırsanız, [ `az keyvault set-policy` komutunu](#grant-access-to-your-key-vault)çalıştırdığınızdan emin olun.
- Aynı secrete adlı kodu yeniden çalıştırmak, "(çakışma) gizli anahtarı <name> Şu anda silinmiş ancak kurtarılabilir durumda." hatasını verebilir. Farklı bir gizli dizi adı kullanın.

## <a name="code-details"></a>Kod ayrıntıları

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, kimlik sağlamak için farklı seçeneklere sahip farklı ortamlarda aynı kodun kullanılmasına izin veren  [' DefaultAzureCredential () '](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) sınıfını kullanıyor. Daha fazla bilgi için bkz. [varsayılan Azure kimlik bilgisi kimlik doğrulaması](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Gizli dizi Kaydet

Anahtar Kasası için istemci nesnesini aldıktan sonra [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) yöntemi kullanarak bir gizli dizi saklayabilirsiniz: 

```python
client.set_secret(secretName, secretValue)
```

Çağırma `set_secret` , Anahtar Kasası Için Azure REST API bir çağrı oluşturur.

Azure, isteği gerçekleştirirken arayanın kimliğini (hizmet sorumlusu) istemciye verdiğiniz kimlik bilgisi nesnesini kullanarak doğrular.

### <a name="retrieve-a-secret"></a>Gizli dizi alma

Key Vault bir gizli dizi okumak için [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) metodunu kullanın:

```python
retrieved_secret = client.get_secret(secretName)
 ```

Gizli anahtar değeri içinde bulunur `retrieved_secret.value` .

Azure CLı komutu [az keykasa gizli göster](/cli/azure/keyvault/secret?#az_keyvault_secret_show)komutuyla da bir gizli dizi alabilirsiniz.

### <a name="delete-a-secret"></a>Gizli anahtarı silme

Bir parolayı silmek için [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) yöntemi kullanın:

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret`Yöntemi zaman uyumsuzdur ve bir Poller nesnesi döndürür. Poller 'ın metodunu çağırma işleminin `result` tamamlanmasını bekler.

Azure CLı komutu [az keykasa gizli göster](/cli/azure/keyvault/secret?#az_keyvault_secret_show)komutuyla parolanın kaldırıldığını doğrulayabilirsiniz.

Silindikten sonra gizli anahtar, bir zaman için silinmiş ancak kurtarılabilir durumda kalır. Kodu yeniden çalıştırırsanız, farklı bir gizli dizi adı kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Ayrıca, [Sertifikalar](../certificates/quick-create-python.md) ve [anahtarlarla](../keys/quick-create-python.md)denemeler yapmak istiyorsanız, bu makalede oluşturulan Key Vault yeniden kullanabilirsiniz.

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

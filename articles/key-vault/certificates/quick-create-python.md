---
title: Hızlı başlangıç – Azure Key Vault Python istemci kitaplığı – sertifikaları yönetme
description: Python istemci kitaplığını kullanarak bir Azure anahtar kasasından sertifika oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b980a74ebd4fd072c5e04089a737cb387d968f74
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814433"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Hızlı başlangıç: Python için sertifika istemci kitaplığı Azure Key Vault

Python için Azure Key Vault sertifika istemci kitaplığı ile çalışmaya başlayın. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere aşağıdaki adımları izleyin. Sertifikaları depolamak için Key Vault kullanarak, kodunuzun içinde sertifikaların depolanmasını önleyin, bu da uygulamanızın güvenliğini artırır.

[API başvuru belgeleri](/python/api/overview/azure/keyvault-certificates-readme)  |  [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Paket (Python paket dizini)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + veya 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Bu hızlı başlangıçta, [Azure CLI](/cli/azure/install-azure-cli) 'Yi bir Linux Terminal penceresinde çalıştırdığınız varsayılır.

## <a name="set-up-your-local-environment"></a>Yerel ortamınızı ayarlama

Bu hızlı başlangıç, Azure 'da kullanıcının kimliğini doğrulamak için Azure CLı ile Azure Identity Library kullanıyor. Geliştiriciler, çağrılarının kimliğini doğrulamak için Visual Studio veya Visual Studio Code de kullanabilir. daha fazla bilgi için bkz [. Azure Identity istemci kitaplığı ile Istemci kimlik doğrulaması](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

1. `login` komutunu çalıştırın.

    ```azurecli-interactive
    az login
    ```

    CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

    Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin.

2. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

### <a name="install-the-packages"></a>Paketleri yükler

1. Bir Terminal veya komut isteminde uygun bir proje klasörü oluşturun ve ardından [Python sanal ortamlarını kullanma](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) bölümünde açıklandığı gibi bir Python sanal ortamı oluşturun ve etkinleştirin

1. Azure Active Directory Identity Library 'yi yükler:

    ```terminal
    pip install azure.identity
    ```


1. Key Vault sertifikası istemci kitaplığını yükler:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Kaynak grubu ve Anahtar Kasası oluşturma

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Anahtar kasanıza erişim izni verin

Anahtar kasanız için Kullanıcı hesabınıza Sertifika izni veren bir erişim ilkesi oluşturun

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
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

Python için Azure Key Vault sertifikası istemci kitaplığı, sertifikaları yönetmenizi sağlar. Aşağıdaki kod örneği, bir istemci oluşturma, sertifika ayarlama, sertifika alma ve sertifikayı silme işlemlerinin nasıl yapılacağını gösterir.

Bu kodu içeren *kv_certificates. Kopyala* adlı bir dosya oluşturun.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kodu çalıştırma

Önceki bölümdeki kodun *kv_certificates. Kopyala* adlı bir dosyada bulunduğundan emin olun. Ardından aşağıdaki komutla kodu çalıştırın:

```terminal
python kv_certificates.py
```

- İzin hatalarıyla karşılaşırsanız, [ `az keyvault set-policy` komutunu](#grant-access-to-your-key-vault)çalıştırdığınızdan emin olun.
- Kodu aynı anahtar adıyla yeniden çalıştırmak, "(çakışma) sertifikası <name> Şu anda silinmiş ancak kurtarılabilir durumda." hatasını verebilir. Farklı bir anahtar adı kullanın.

## <a name="code-details"></a>Kod ayrıntıları

### <a name="authenticate-and-create-a-client"></a>İstemci kimliğini doğrulama ve oluşturma

Bu hızlı başlangıçta oturum açan kullanıcı, yerel geliştirme için tercih edilen yöntem olan Anahtar Kasası kimlik doğrulaması için kullanılır. Azure 'a dağıtılan uygulamalar için, yönetilen kimlik App Service veya sanal makineye atanmalıdır, daha fazla bilgi için bkz. [yönetilen kimliğe genel bakış](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Aşağıdaki örnekte, anahtar kasanızın adı, "https://. vault.azure.net" biçiminde Anahtar Kasası URI 'sine genişletilir \<your-key-vault-name\> . Bu örnek, kimlik sağlamak için farklı seçeneklere sahip farklı ortamlarda aynı kodun kullanılmasına izin veren  [' DefaultAzureCredential () '](/python/api/azure-identity/azure.identity.defaultazurecredential) sınıfını kullanıyor. Daha fazla bilgi için bkz. [varsayılan Azure kimlik bilgisi kimlik doğrulaması](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Sertifika kaydetme

Anahtar Kasası için istemci nesnesini aldıktan sonra, [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) yöntemini kullanarak bir sertifika oluşturabilirsiniz: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Burada sertifika, [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) yöntemiyle elde edilen bir ilke gerektirir.

Bir yöntemi çağırmak, `begin_create_certificate` Anahtar Kasası Için Azure REST API zaman uyumsuz bir çağrı oluşturur. Zaman uyumsuz çağrı bir Poller nesnesi döndürür. İşlemin sonucunu beklemek için, Poller 'in `result` yöntemini çağırın.

Azure, isteği gerçekleştirirken arayanın kimliğini (hizmet sorumlusu) istemciye verdiğiniz kimlik bilgisi nesnesini kullanarak doğrular.


### <a name="retrieve-a-certificate"></a>Sertifika alma

Key Vault bir sertifikayı okumak için [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) yöntemi kullanın:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Sertifikanın, [az keykasa sertifikası Show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show)Azure CLI komutuyla ayarlandığını da doğrulayabilirsiniz.

### <a name="delete-a-certificate"></a>Sertifikayı silme

Bir sertifikayı silmek için [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) yöntemi kullanın:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate`Yöntemi zaman uyumsuzdur ve bir Poller nesnesi döndürür. Poller 'ın metodunu çağırma işleminin `result` tamamlanmasını bekler.

Sertifikanın, [az keykasa sertifikası Show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show)Azure CLI komutuyla silindiğini doğrulayabilirsiniz.

Silinen bir sertifika, bir süredir silinmiş ancak kurtarılabilir durumda kalır. Kodu yeniden çalıştırırsanız, farklı bir sertifika adı kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[Gizli](../secrets/quick-create-python.md) dizileri ve [anahtarlarla](../keys/quick-create-python.md)de denemek istiyorsanız, bu makalede oluşturulan Key Vault yeniden kullanabilirsiniz.

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

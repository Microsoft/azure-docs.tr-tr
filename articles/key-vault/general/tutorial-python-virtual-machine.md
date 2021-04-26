---
title: Öğretici-Python 'da bir sanal makine ile Azure Key Vault kullanma | Microsoft Docs
description: Bu öğreticide, anahtar kasaınızdan gizli dizi okumak için bir sanal makineyi bir Python uygulaması yapılandırırsınız.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 25182105db831724565c6bf3dbbbb79832b677f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772069"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Öğretici: Python 'da bir sanal makine ile Azure Key Vault kullanma

Azure Key Vault, API anahtarları ve veritabanı bağlantı dizeleri gibi anahtarları, sırları ve sertifikaları korumanıza yardımcı olur.

Bu öğreticide, Azure kaynakları için Yönetilen kimlikler kullanarak Azure Key Vault bilgi okumak üzere bir Python uygulaması ayarlarsınız. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Anahtar kasası oluşturma
> * Key Vault bir gizli dizi depolayın
> * Azure Linux sanal makinesi oluşturma
> * Sanal makine için [yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md) etkinleştir
> * Key Vault verileri okumak için konsol uygulaması için gerekli izinleri verme
> * Key Vault bir gizli dizi alma

Başlamadan önce [temel kavramları Key Vault](basic-concepts.md)okuyun. 

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Windows, Mac ve Linux için:
  * [Git](https://git-scm.com/downloads)
  * Bu öğretici, Azure CLı 'yı yerel olarak çalıştırmanızı gerektirir. Azure CLı sürüm 2.0.4 veya daha yeni bir sürümün yüklü olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. CLI’yı yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 2.0’ı yükleme](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Azure CLI'yi kullanarak Azure'da oturum açmak için, şunları girin:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Kaynak grubu ve Anahtar Kasası oluşturma

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Anahtar kasanızı gizli dizi ile doldurma

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Aşağıdaki yöntemlerden birini kullanarak **myvm** ADLı bir VM oluşturun:

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure portalı](../../virtual-machines/linux/quick-create-portal.md) | [Azure portal](../../virtual-machines/windows/quick-create-portal.md) |

Azure CLı kullanarak bir Linux VM oluşturmak için [az VM Create](/cli/azure/vm) komutunu kullanın.  Aşağıdaki örnek, *azureuser* adlı bir kullanıcı hesabı ekler. `--generate-ssh-keys`Parametresi, otomatik olarak BIR SSH anahtarı oluşturmak ve varsayılan anahtar konumuna (*~/PST*) koymak için kullanılır. 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Çıktıda değerini aklınızda edin `publicIpAddress` .

## <a name="assign-an-identity-to-the-vm"></a>VM 'ye bir kimlik atama

Azure CLı [az VM Identity Assign](/cli/azure/vm/identity#az_vm_identity_assign) komutunu kullanarak sanal makine için sistem tarafından atanan bir kimlik oluşturun:

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Aşağıdaki kodda görüntülenen sistem tarafından atanan kimliği unutmayın. Yukarıdaki komutun çıkışı şöyle olacaktır: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM kimliğine izin atama

Artık aşağıdaki komutu çalıştırarak, önceden oluşturulmuş kimlik izinlerini anahtar kasanıza atayabilirsiniz:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM 'de oturum açma

Sanal makinede oturum açmak için, Bağlan bölümündeki yönergeleri izleyin [ve Linux çalıştıran bir Azure sanal makinesinde oturum açın](../../virtual-machines/linux/login-using-aad.md) veya [Windows çalıştıran bir Azure sanal makinesine bağlanın ve oturum açın](../../virtual-machines/windows/connect-logon.md).


Bir Linux VM 'de oturum açmak için SSH komutunu, <publicIpAddress> [sanal makine oluşturma](#create-a-virtual-machine) adımında verilen "" ile kullanabilirsiniz:

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>SANAL makineye Python kitaplıklarını yükler

Sanal makinede, Python betiğimizde kullanılacak iki Python kitaplığını (ve) yüklemelisiniz `azure-keyvault-secrets` `azure.identity` .  

Örneğin, bir Linux sanal makinesinde şunları kullanarak yükleyebilirsiniz `pip3` :

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Örnek Python betiği oluşturma ve düzenleme

Sanal makinede, **Sample.py** adlı bir Python dosyası oluşturun. Dosyayı aşağıdaki kodu içerecek şekilde düzenleyin: "<-Unique-keykasa-adı>" değerini anahtar kasanızın adıyla değiştirin:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Örnek Python uygulamasını çalıştırma

Son olarak, **Sample.py** çalıştırın. Hepsi iyi gittiğinden, gizli anahtar değerini döndürmelidir:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, sanal makineyi ve anahtar kasanızı silin.  Bu, yalnızca ait oldukları kaynak grubunu silerek hızlı bir şekilde yapabilirsiniz:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Key Vault REST API](/rest/api/keyvault/)
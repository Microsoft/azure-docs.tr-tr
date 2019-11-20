---
title: Azure CLı ile Windows VM oluşturma ve şifreleme
description: Bu hızlı başlangıçta, Azure CLı kullanarak bir Windows sanal makinesi oluşturma ve şifreleme hakkında bilgi edineceksiniz
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 9bbe74bd2f3137443b4e239201c604d9de52582e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246070"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Hızlı başlangıç: Azure CLı ile Windows VM oluşturma ve şifreleme

Azure CLı, komut satırından veya betiklerden Azure kaynakları oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, bir Windows Server 2016 sanal makinesi (VM) oluşturmak ve şifrelemek için Azure CLı 'nin nasıl kullanılacağı gösterilmektedir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.30 veya üstünü çalıştırıyor olmalıdır. Sürümü bulmak için `az --version` ' yı çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[Az Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) komutuyla bir kaynak grubu oluşturun. Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[Az VM Create](/cli/azure/vm?view=azure-cli-latest#az-vm-create)Ile bir VM oluşturun. Aşağıdaki örnek, *myvm*ADLı bir VM oluşturur. Bu örnek, bir Yönetici Kullanıcı adı ve parola olarak *myPassword12* için *azureuser* kullanır. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

VM ve destekleyici kaynakların oluşturulması birkaç dakika sürer. Aşağıdaki örnek çıktı, VM oluşturma işleminin başarılı olduğunu gösterir.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Şifreleme anahtarları için yapılandırılmış bir Key Vault oluşturma

Azure disk şifrelemesi, şifreleme anahtarını bir Azure Key Vault depolar. [Az keykasacreate](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)ile bir Key Vault oluşturun. Şifreleme anahtarlarını depolamak için Key Vault etkinleştirmek üzere--Enabled-for-disk-ENCRYPTION parametresini kullanın.
> [!Important]
> Her Key Vault benzersiz bir adı olmalıdır. Aşağıdaki örnek *Mykv*adlı bir Key Vault oluşturur, ancak sizinkilerle farklı bir ad vermelisiniz.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Sanal makineyi şifreleyin

VM 'nizi [az VM Encryption](/cli/azure/vm/encryption?view=azure-cli-latest)ile şifreleyin ve bu,--disk-Encryption-keykasa parametresine benzersiz Key Vault adı sağlar.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

[Az VM Show](/cli/azure/vm/encryption#az-vm-encryption-show) komutuyla VM 'niz üzerinde şifrelemenin etkin olduğunu doğrulayabilirsiniz

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Döndürülen çıktıda şunları göreceksiniz:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Kaynakları Temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group) komutunu kullanarak kaynak grubunu, VM 'yi ve Key Vault kaldırabilirsiniz. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sanal makine oluşturdunuz, şifreleme anahtarları için etkin bir Key Vault oluşturdunuz ve VM 'yi şifrelemiş olursunuz.  IaaS VM 'Leri için Azure disk şifrelemesi önkoşulları hakkında daha fazla bilgi edinmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)


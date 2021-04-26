---
title: Azure CLI ile Linux VM oluşturma ve şifreleme
description: Bu hızlı başlangıçta, bir Linux sanal makinesi oluşturmak ve şifrelemek için Azure CLı 'yi nasıl kullanacağınızı öğrenirsiniz.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5b98fde5e15a3c57b56ecc8aea60023ffb8c22a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774314"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Hızlı başlangıç: Azure CLı ile Linux VM oluşturma ve şifreleme

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, bir Linux sanal makinesi (VM) oluşturmak ve şifrelemek için Azure CLı 'nın nasıl kullanılacağı gösterilmektedir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Azure CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu hızlı başlangıç, Azure CLı sürüm 2.0.30 veya üstünü çalıştırıyor olmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm#az_vm_create) ile bir VM oluşturun. Aşağıdaki örnek, *myvm* ADLı bir VM oluşturur.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer. Aşağıdaki örnekte VM oluşturma işleminin başarılı olduğu gösterilmektedir.

```
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

Azure disk şifrelemesi, şifreleme anahtarını bir Azure Key Vault depolar. [Az keykasacreate](/cli/azure/keyvault#az_keyvault_create)ile bir Key Vault oluşturun. Şifreleme anahtarlarını depolamak için Key Vault etkinleştirmek üzere--Enabled-for-disk-ENCRYPTION parametresini kullanın.

> [!Important]
> Her anahtar kasasının Azure genelinde benzersiz bir adı olmalıdır. Aşağıdaki örneklerde-Unique-keykasa-adı> <seçtiğiniz adla değiştirin.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Sanal makineyi şifreleyin

VM 'nizi [az VM Encryption](/cli/azure/vm/encryption)ile şifreleyin ve bu,--disk-Encryption-keykasa parametresine benzersiz Key Vault adı sağlar.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

İşlemin döndürdüğü bir süre sonra, "şifreleme isteği kabul edildi. İlerlemeyi izlemek için lütfen ' göster ' komutunu kullanın. ". "Göster" komutu [az VM Show](/cli/azure/vm/encryption#az_vm_encryption_show)' dir.

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Şifreleme etkin olduğunda, döndürülen çıktıda aşağıdaki işlemi görürsünüz:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group) komutunu kullanarak kaynak grubunu, VM 'yi ve Key Vault kaldırabilirsiniz. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sanal makine oluşturdunuz, şifreleme anahtarları için etkin bir Key Vault oluşturdunuz ve VM 'yi şifrelemiş olursunuz.  Linux VM 'Ler için daha fazla Azure disk şifrelemesi hakkında daha fazla bilgi edinmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)

---
title: Azure CLI ile Windows VM oluşturma ve şifreleme
description: Bu hızlı başlangıçta, Azure CLı kullanarak bir Windows sanal makinesi oluşturma ve şifreleme hakkında bilgi edineceksiniz
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e81d29922bee53ba9021c9c26816258f7922a59c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759748"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Hızlı başlangıç: Azure CLı ile Windows VM oluşturma ve şifreleme

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, bir Windows Server 2016 sanal makinesi (VM) oluşturmak ve şifrelemek için Azure CLı 'nin nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.30 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm#az_vm_create) ile bir VM oluşturun. Aşağıdaki örnek, *myvm* ADLı bir VM oluşturur. Bu örnekte yönetici kullanıcı için *azureuser* kullanıcı adı ve *myPassword12* parolası kullanılır.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer. Aşağıdaki örnekte VM oluşturma işleminin başarılı olduğu gösterilmektedir.

```console
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
> Her Key Vault benzersiz bir adı olmalıdır. Aşağıdaki örnek *Mykv* adlı bir Key Vault oluşturur, ancak sizinkilerle farklı bir ad vermelisiniz.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Sanal makineyi şifreleyin

VM 'nizi [az VM Encryption](/cli/azure/vm/encryption)ile şifreleyin ve bu,--disk-Encryption-keykasa parametresine benzersiz Key Vault adı sağlar.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

[Az VM Show](/cli/azure/vm/encryption#az_vm_encryption_show) komutuyla VM 'niz üzerinde şifrelemenin etkin olduğunu doğrulayabilirsiniz

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

Döndürülen çıktıda şunları göreceksiniz:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group) komutunu kullanarak kaynak grubunu, VM 'yi ve Key Vault kaldırabilirsiniz.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sanal makine oluşturdunuz, şifreleme anahtarları için etkin bir Key Vault oluşturdunuz ve VM 'yi şifrelemiş olursunuz.  IaaS VM'leri için Azure Disk Şifrelemesi önkoşulları hakkında daha fazla bilgi edinmek üzere bir sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)

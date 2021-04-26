---
title: Sanal Makine Ölçek Kümesi Yönetimi için Azure CLı örneği
description: Bu örnek, bir sanal makine ölçek kümesine disklerin nasıl ekleneceğini gösterir. Diskleri yükseltebilir ve sanal makinelerinizi Azure AD kimlik doğrulamasına ekleyebilirsiniz.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672597"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi oluşturma ve yönetme

Azure CLı kullanarak bir sanal makine ölçek kümesini prototip için bu örnek komutları kullanın.

Bu örnek komutlar aşağıdaki işlemleri gösterir:

* Sanal makine ölçek kümesi oluşturun.
* Yeni veya mevcut diskleri bir ölçek kümesine veya kümenin örneğine ekleyin ve yükseltin.
* Azure Active Directory (Azure AD) kimlik doğrulamasına ölçek kümesi ekleyin.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Örnek komutlar

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Yeni bir veri diski ekledikten sonra diski biçimlendirin ve bağlayın. Windows sanal makineleri için, bkz. [Azure Portal kullanarak bir WINDOWS VM 'ye yönetilen veri diski iliştirme](../../virtual-machines/windows/attach-managed-disk-portal.md). Linux sanal makineleri için bkz. [LINUX VM 'ye disk ekleme](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Genişletilmiş diski kullanmak için, temel alınan bölümü genişletin. Daha fazla bilgi için bkz. [disk bölümünü ve dosya sistemini genişletme](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).

Bu örnek bir veri diski yeniden boyutlandırıldı. Bu yordamı, bir işletim sistemi diskini güncelleştirmek için kullanabilirsiniz. Bir Windows sanal makinesi hakkında daha fazla bilgi için bkz. [bir sanal makinenin işletim sistemi sürücüsünü genişletme](../../virtual-machines/windows/expand-os-disk.md). Linux sanal makineleri hakkında daha fazla bilgi için bkz. [Azure CLI Ile LINUX VM 'de sanal sabit diskleri genişletme](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu komutları kullandıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Bu makalede kullanılan Azure CLı başvuruları

* [az disk Delete](/cli/azure/disk#az_disk_delete)
* [az disk List](/cli/azure/disk#az_disk_list)
* [az disk Update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az sanal makine ölçek kümesi serbest bırakma](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az VMSS disk ayırma](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az VMSS Identity Assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az VMSS List-örnekleri](/cli/azure/vmss#az_vmss_list_instances)
* [az VMSS restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)
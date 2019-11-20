---
title: Azure CLı ile bölge ile bir Linux VM oluşturma
description: Azure CLı ile bir kullanılabilirlik bölgesinde Linux VM oluşturma
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 1cb8222b5e7bf2efee36673f9b80738500345754
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036555"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Azure CLı ile bir kullanılabilirlik alanında Linux sanal makinesi oluşturma

Bu makalede, Azure CLı bölgesinde bir Linux sanal makinesi oluşturmak için Azure CLı kullanma adımları sağlanır. [Kullanılabilirlik alanı](../../availability-zones/az-overview.md), bir Azure bölgesinde fiziksel olarak ayrılmış bir alandır. Uygulamalarınızı beklenmeyen hatalardan veya tüm veri merkezinin kaybedilmesinden korumak için kullanılabilirlik alanlarından yararlanın.

Kullanılabilirlik alanı kullanmak için, [desteklenen bir Azure bölgesinde](../../availability-zones/az-overview.md#services-support-by-region) sanal makinenizi oluşturun.

En son [Azure CLI](/cli/azure/install-az-cli2) 'yi yüklediğinizden ve [az Login](/cli/azure/reference-index)ile bir Azure hesabında oturum açtığınızdan emin olun.


## <a name="check-vm-sku-availability"></a>VM SKU kullanılabilirliğini denetleme
VM boyutları veya SKU'ların kullanılabilirliği, bölge ve alanlara göre farklılık gösterebilir. Kullanılabilirlik Alanları kullanımını planlamanıza yardımcı olmak üzere, kullanılabilir VM SKU'larını Azure bölgesine ve alana göre listeleyebilirsiniz. Bu özellik, uygun bir VM boyutu seçmenizi ve alanlar arasında istenen dayanıklılığı elde etmenizi sağlar. Farklı VM türleri ve boyutları hakkında daha fazla bilgi için bkz. [VM Boyutlarına genel bakış](sizes.md).

Kullanılabilir VM SKU 'Larını [az VM List-SKU](/cli/azure/vm) komutuyla görüntüleyebilirsiniz. Aşağıdaki örnekte kullanılabilir VM SKU'ları *eastus2* bölgesinde içinde listelenmiştir:

```azurecli
az vm list-skus --location eastus2 --output table
```

Çıktı, her VM boyutunun kullanılabilir olduğu Kullanılabilir Alanlarını gösteren aşağıdaki sıkıştırılmış örneğe benzer:

```azurecli
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun.  

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bir sanal makineden önce bir kaynak grubu oluşturulmalıdır. Bu örnekte, *eastus2* bölgesinde *Myresourcegroupvm* adlı bir kaynak grubu oluşturulur. Doğu ABD 2, kullanılabilirlik alanlarını destekleyen Azure bölgelerinden biridir.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Kaynak grubu, bu makale boyunca görülemeyen bir VM oluştururken veya değiştirilirken belirtilir.

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm) komutuyla bir sanal makine oluşturun. 

Bir sanal makine oluştururken, işletim sistemi görüntüsü, disk boyutlandırma ve yönetici kimlik bilgileri gibi çeşitli seçenekler bulunur. Bu örnekte, Ubuntu Server çalıştıran *myVM* adlı bir sanal makine oluşturulmuştur. VM, kullanılabilirlik bölge *1*' de oluşturulur. Varsayılan olarak, sanal makine *Standard_DS1_v2* boyutunda oluşturulur.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

VM’nin oluşturulması birkaç dakika sürebilir. VM oluşturulduktan sonra, Azure CLI VM hakkında bilgi çıkışı sağlar. VM 'nin çalıştığı kullanılabilirlik bölgesini belirten `zones` değerini bir yere göz atın. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Yönetilen disk ve IP adresi için bölgeyi Onayla

VM bir kullanılabilirlik alanında dağıtıldığında, sanal makine için yönetilen bir disk aynı Kullanılabilirlik bölgesinde oluşturulur. Varsayılan olarak, bu bölgede genel bir IP adresi de oluşturulur. Aşağıdaki örneklerde bu kaynaklarla ilgili bilgiler yer alır.

VM 'nin yönetilen diskinin kullanılabilirlik bölgesinde olduğunu doğrulamak için [az VM Show](/cli/azure/vm) komutunu kullanarak disk kimliğini döndürün. Bu örnekte, disk KIMLIĞI sonraki bir adımda kullanılan bir değişkende depolanır. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Artık yönetilen disk hakkında bilgi edinebilirsiniz:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Çıktı, yönetilen diskin VM ile aynı kullanılabilirlik alanında olduğunu gösterir:

```azurecli
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

*Myvm*'de genel IP adresi kaynağının adını döndürmek için [az VM List-ip-addresses](/cli/azure/vm) komutunu kullanın. Bu örnekte, ad sonraki bir adımda kullanılan bir değişkende depolanır.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Artık IP adresi hakkında bilgi edinebilirsiniz:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Çıktı, IP adresinin VM ile aynı Kullanılabilirlik bölgesinde olduğunu gösterir:

```azurecli
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kullanılabilirlik alanında nasıl sanal makine oluşturulacağını öğrendiniz. Azure VM 'Leri için [kullanılabilirlik](availability.md) hakkında daha fazla bilgi edinin.





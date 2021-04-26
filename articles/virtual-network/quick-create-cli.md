---
title: Sanal ağ oluşturma-hızlı başlangıç-Azure CLı
titlesuffix: Azure Virtual Network
description: Bu hızlı başlangıçta, Azure CLı kullanarak bir sanal ağ oluşturmayı öğrenin. Bir sanal ağ, Azure kaynaklarının birbirleriyle ve internet ile iletişim kurmasına olanak tanır.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776762"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLI kullanarak sanal ağ oluşturma

Sanal ağ, sanal makineler (VM) gibi Azure kaynaklarının birbiriyle ve internet ile özel olarak iletişim kurmasına olanak sağlar. 

Bu hızlı başlangıçta, sanal ağ oluşturmayı öğreneceksiniz. Bir sanal ağ oluşturduktan sonra, sanal ağa iki sanal makine dağıtacaksınız. Daha sonra internet 'ten VM 'lere bağlanır ve yeni sanal ağ üzerinden özel olarak iletişim kurabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Kaynak grubu ve sanal ağ oluşturma

Bir sanal ağ oluşturabilmeniz için önce sanal ağı barındırmak üzere bir kaynak grubu oluşturmanız gerekir. [az group create](/cli/azure/group#az_group_create) ile bir kaynak grubu oluşturun. Bu örnek **Eastus** konumunda **Createvnetqs-RG** adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) komutu ile bir sanal ağ oluşturun. Bu örnek, **varsayılan** adlı bir alt ağa sahip **myvnet** adlı varsayılan bir sanal ağ oluşturur:

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sanal ağ üzerinde iki sanal makine oluşturun.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

[az vm create](/cli/azure/vm#az_vm_create) ile bir VM oluşturun. 

SSH anahtarları varsayılan anahtar konumunda zaten mevcut değilse, komut bunları oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın. 

`--no-wait`Seçeneği, sanal makineyi arka planda oluşturur. Sonraki adıma devam edebilirsiniz. 

Bu örnek, **myVM1** adlı bir sanal makine oluşturur:

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

`--no-wait`Önceki adımda seçeneğini kullandınız. Devam edebilir ve **myVM2** adlı ikinci VM 'yi oluşturabilirsiniz.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Azure CLı çıkış iletisi

Sanal makinelerin oluşturulması birkaç dakika sürebilir. Azure VM 'Leri oluşturduktan sonra, Azure CLı şunun gibi bir çıktı döndürür:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>VM genel IP 'si

**MyVM2** genel IP adresini almak için [az Network public-IP Show](/cli/azure/network/public-ip#az_network_public_ip_show)kullanın:

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>İnternet'ten bir sanal makineye bağlanma

Bu komutta, `<publicIpAddress>` **myVM2** sanal makinenizin genel IP adresiyle değiştirin:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Sanal makineler arasında iletişim

**MyVM2** ve **myVM1** VM 'ler arasındaki özel iletişimi doğrulamak için şu komutu girin:

```bash
ping myVM1 -c 4
```

*10.0.0.4* adresinden dört yanıt alacaksınız.

**MyVM2** VM ile SSH oturumundan çıkın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group#az_group_delete) ' i kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta: 

* Varsayılan bir sanal ağ ve iki VM oluşturdunuz. 
* İnternet 'ten bir sanal makineye bağlanırsınız ve iki VM arasında özel olarak iletişim kuracaksınız.

Sanal ağ içinde VM 'Ler arasındaki özel iletişim Kısıtlanmamış değildir. 

Farklı türlerde VM ağı iletişimleri yapılandırma hakkında daha fazla bilgi edinmek için sonraki makaleye ilerleyin:
> [!div class="nextstepaction"]
> [Ağ trafiğini filtreleme](tutorial-filter-network-traffic.md)

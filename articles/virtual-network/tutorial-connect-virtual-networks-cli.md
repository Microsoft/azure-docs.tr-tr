---
title: Sanal ağları VNet eşlemesi ile bağlama-Azure CLı
description: Bu makalede, Azure CLı kullanarak sanal ağ eşlemesi ile sanal ağları bağlamayı öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: df1b90a638052de7b56854060badfb8e3483d4c2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065438"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Azure CLı kullanarak sanal ağ eşlemesi ile sanal ağları bağlama

Sanal ağ eşlemesi ile sanal ağları birbirine bağlayabilirsiniz. Sanal ağlar eşlendikten sonra, kaynaklar aynı sanal ağ üzerindeymiş gibi, aynı gecikme süresi ve bant genişliği ile her iki sanal ağdaki kaynaklar birbiriyle iletişim kurabilir. Bu makalede şunları öğreneceksiniz:

* İki sanal ağ oluşturma
* Sanal ağ eşlemesi iki sanal ağı bağlama
* Her sanal ağa sanal makine (VM) dağıtma
* Sanal makineler arasında iletişim

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-virtual-networks"></a>Sanal ağlar oluşturma

Bir sanal ağ oluşturmadan önce, sanal ağ ve bu makalede oluşturulan tüm diğer kaynaklar için bir kaynak grubu oluşturmanız gerekir. [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet) komutu ile bir sanal ağ oluşturun. Aşağıdaki örnek, *10.0.0.0/16* adres ön ekine sahip *myVirtualNetwork1* adlı bir sanal ağ oluşturur.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

*10.1.0.0/16* adres ön ekine sahip *myVirtualNetwork2* adlı bir sanal ağ oluşturun:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Sanal ağları eşleme

Eşlemeler sanal ağ kimlikleri arasında oluşturulur. bu nedenle, [az Network VNET Show](/cli/azure/network/vnet) ile kimliği bir değişkende depolayan her bir sanal ağın kimliğini almanız gerekir.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

[Az Network VNET eşleme Create](/cli/azure/network/vnet/peering)ile *myVirtualNetwork1* ile *myVirtualNetwork2* arasında bir eşleme oluşturun. `--allow-vnet-access`Parametre belirtilmemişse, bir eşleme oluşturulur, ancak hiçbir iletişim üzerinden akabilir.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Önceki komut yürütüldükten sonra döndürülen çıktıda, **Peeringstate** öğesinin *başlatıldığını* görürsünüz. Eşleme, *myVirtualNetwork2* ile *myVirtualNetwork1* arasında eşleme oluşturulana kadar *başlatılmış* durumda kalır. *MyVirtualNetwork2* ile *myVirtualNetwork1* arasında bir eşleme oluşturun. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Önceki komut yürütüldükten sonra döndürülen çıktıda, **Peeringstate** ' in *bağlı* olduğunu görürsünüz. Azure ayrıca *myVirtualNetwork1-myVirtualNetwork2* eşlemesinin eşleme durumunu *bağlı* olarak değiştirdi. *MyVirtualNetwork1-myVirtualNetwork2* eşlemesinin eşleme durumunun [az Network VNET eşleme Show](/cli/azure/network/vnet/peering)ile *bağlantılı* olarak değiştirildiğini doğrulayın.

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Bir sanal ağdaki kaynaklar, her iki sanal ağ içindeki eşler için **Peeringstate** *bağlı* olana kadar diğer sanal ağdaki kaynaklarla iletişim kuramaz. 

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sonraki bir adımda aralarında iletişim kurabilmeniz için her sanal ağ üzerinde bir sanal makine oluşturun.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

[az vm create](/cli/azure/vm) ile bir VM oluşturun. Aşağıdaki örnek, *myVirtualNetwork1* sanal ağında *MYVM1* adlı bir VM oluşturur. SSH anahtarları, varsayılan anahtar konumunda zaten mevcut değilse komut bunları oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın. Bu `--no-wait` seçenek, bir sonraki adıma devam edebilmeniz için sanal makineyi arka planda oluşturur.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

*MyVirtualNetwork2* sanal AĞıNDA bir VM oluşturun.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Sanal makinenin oluşturulması birkaç dakika sürer. VM oluşturulduktan sonra Azure CLı, aşağıdaki örneğe benzer bilgiler gösterir: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress** değerini not alın. Bu adres, sonraki bir adımda sanal makineye internet 'ten erişmek için kullanılır.

## <a name="communicate-between-vms"></a>Sanal makineler arasında iletişim

*MyVm2* VM Ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. `<publicIpAddress>`Sanal makinenizin genel IP adresi ile değiştirin. Önceki örnekte, genel IP adresi *13.90.242.231*' dir.

```bash
ssh <publicIpAddress>
```

*MyVirtualNetwork1* içinde VM 'ye ping gönderin.

```bash
ping 10.0.0.4 -c 4
```

Dört yanıt alırsınız. 

*MyVm2* VM 'ye SSH oturumunu kapatın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group) ' i kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırın.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, sanal ağ eşlemesi ile aynı Azure bölgesindeki iki ağı bağlamayı öğrendiniz. Farklı [desteklenen bölgelerde](virtual-network-manage-peering.md#cross-region) ve [farklı Azure aboneliklerinde](create-peering-different-subscriptions.md#cli) sanal ağları eşleyebilir ve eşleme ile [hub ve bağlı bileşen ağ tasarımları](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) oluşturabilirsiniz. Sanal ağ eşlemesi hakkında daha fazla bilgi için bkz. [Sanal ağ eşlemesine genel bakış](virtual-network-peering-overview.md) ve [Sanal ağ eşlemelerini yönetme](virtual-network-manage-peering.md).

Bir VPN aracılığıyla [kendi bilgisayarınızı bir sanal ağa bağlayabilirsiniz](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve bir sanal ağdaki kaynaklarla veya eşlenmiş sanal ağlarda etkileşim kurabilirsiniz. Sanal ağ makalelerinde kapsanan birçok görevi tamamlaması için yeniden kullanılabilir betikler için bkz. [betik örnekleri](cli-samples.md).

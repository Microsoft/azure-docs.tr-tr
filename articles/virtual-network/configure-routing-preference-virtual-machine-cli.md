---
title: VM için yönlendirme tercihini Yapılandırma-Azure CLı
description: Azure komut satırı arabirimi 'ni (CLı) kullanarak yönlendirme tercihi seçimiyle genel IP adresi ile bir VM oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9155c3114d5a5a1b8729351dc189bc1e5c22369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764486"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Azure CLı kullanarak bir VM için yönlendirme tercihini yapılandırma

Bu makalede, bir sanal makine için yönlendirme tercihini yapılandırma gösterilmektedir. Internet 'e ait trafiği yönlendirme tercih seçeneği olarak **Internet** ' i SEÇTIĞINIZDE, ISS ağı aracılığıyla yönlendirilecektir. Varsayılan yönlendirme, Microsoft Global ağı aracılığıyla yapılır.

Bu makalede, Azure CLı kullanarak genel İnternet üzerinden trafiği yönlendirmek üzere ayarlanmış bir genel IP ile sanal makine oluşturma işlemi gösterilmektedir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
1. Cloud Shell kullanılıyorsa adım 2 ' ye atlayın. Bir komut oturumu açın ve ile Azure 'da oturum açın `az login` .
2. [az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, Doğu ABD Azure bölgesinde bir kaynak grubu oluşturur:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma
Sanal makinelerinize Internet 'ten erişmek için genel bir IP adresi oluşturmanız gerekir. [az network public-ip create](/cli/azure/network/public-ip) komutu ile bir genel IP adresi oluşturun. Aşağıdaki örnek, *Doğu ABD* bölgesinde *Internet* yönlendirme tercihi türü olan genel bir IP oluşturur:

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

Bir VM 'yi dağıtmadan önce, destekleyici ağ kaynakları (ağ güvenlik grubu, sanal ağ ve sanal NIC) oluşturmanız gerekir.

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[Az Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create) ile VNET 'iniz içindeki gelen ve giden iletişimi yönetecek kurallar için bir ağ güvenlik grubu oluşturun

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) komutu ile bir sanal ağ oluşturun. Aşağıdaki örnek, alt ağ ile *Myvnet* adlı bir sanal ağ oluşturur *mysubnet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>NIC oluşturma

[Az Network NIC Create](/cli/azure/network/nic#az_network_nic_create)komutuyla VM için BIR sanal NIC oluşturun. Aşağıdaki örnek, VM 'ye eklenecek bir sanal NIC oluşturur.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm#az_vm_create) ile bir VM oluşturun. Aşağıdaki örnekte, zaten mevcut değilse, bir Windows Server 2019 VM ve gerekli sanal ağ bileşenleri oluşturulur.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

- [Genel IP adreslerinde yönlendirme tercihi](routing-preference-overview.md)hakkında daha fazla bilgi edinin.
- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin.
- [Genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address)hakkında daha fazla bilgi edinin.

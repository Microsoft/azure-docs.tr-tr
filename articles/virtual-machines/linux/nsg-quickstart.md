---
title: Azure CLı ile bir Linux sanal makinesine bağlantı noktaları açma
description: Azure Resource Manager dağıtım modelini ve Azure CLı 'yı kullanarak Linux VM 'nize bir bağlantı noktası açma/bitiş noktası oluşturma hakkında bilgi edinin
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 424dfc1dac21f227869f23e7401a083b06cef1d9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035561"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Azure CLı ile bir Linux VM 'ye bağlantı noktalarını ve uç noktaları açma

Bir alt ağ veya VM ağı arabiriminde ağ filtresi oluşturarak Azure 'da bir bağlantı noktası Açarsınız veya bir uç nokta oluşturursunuz. Hem gelen hem de giden trafiği denetleyen, trafiği alan kaynağa bağlı bir ağ güvenlik grubundaki bu filtreleri yerleştirebilirsiniz. Bağlantı noktası 80 ' de yaygın bir Web trafiği örneği kullanalım. Bu makalede, Azure CLı ile bir VM 'ye bağlantı noktası açma gösterilmektedir. 


Bir ağ güvenlik grubu ve kuralları oluşturmak için, en son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü ve [az oturum açma](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açmış olmanız gerekir.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. Örnek parametre adları *Myresourcegroup*, *mynetworksecuritygroup*ve *myvnet*' i içerir.


## <a name="quickly-open-a-port-for-a-vm"></a>Bir VM için hızlı bir şekilde bağlantı noktası açma
Bir geliştirme/test senaryosunda bir sanal makinenin bağlantı noktasını hızlıca açmanız gerekiyorsa, [az VM Open-Port](/cli/azure/vm) komutunu kullanabilirsiniz. Bu komut bir ağ güvenlik grubu oluşturur, bir kural ekler ve bir VM veya alt ağa uygular. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *MYVM* adlı VM 'de *80* numaralı bağlantı noktasını açar.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Bir kaynak IP adresi aralığı tanımlama gibi kurallar üzerinde daha fazla denetim için, bu makaledeki ek adımlarla devam edin.


## <a name="create-a-network-security-group-and-rules"></a>Ağ güvenlik grubu ve kuralları oluşturma
[Az Network NSG Create](/cli/azure/network/nsg)komutuyla ağ güvenlik grubunu oluşturun. Aşağıdaki örnek *eastus* konumunda *Mynetworksecuritygroup* adlı bir ağ güvenlik grubu oluşturur:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Web sunucunuza HTTP trafiğine izin vermek için [az Network NSG Rule Create](/cli/azure/network/nsg/rule) ile bir kural ekleyin (veya SSH erişimi veya veritabanı bağlantısı gibi kendi senaryonuzu ayarlayın). Aşağıdaki örnek, 80 numaralı bağlantı noktasında TCP trafiğine izin vermek için *Mynetworksecuritygrouprule* adlı bir kural oluşturur:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>VM 'ye ağ güvenlik grubu uygulama
Ağ güvenlik grubunu, [az Network NIC Update](/cli/azure/network/nic)ile sanal makinenizin ağ ARABIRIMI (NIC) ile ilişkilendirin. Aşağıdaki örnek, *MYNIC* adlı mevcut bir NIC 'Yi *Mynetworksecuritygroup*adlı ağ güvenlik grubuyla ilişkilendirir:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternatif olarak, ağ güvenlik grubunuzu yalnızca tek bir VM 'deki ağ arabirimine değil [az Network VNET subnet Update](/cli/azure/network/vnet/subnet) ile bir sanal ağ alt ağı ile ilişkilendirebilirsiniz. Aşağıdaki örnek, *Myvnet* sanal ağındaki *mysubnet* adlı mevcut bir alt ağı *Mynetworksecuritygroup*adlı ağ güvenlik grubuyla ilişkilendirir:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Ağ güvenlik grupları hakkında daha fazla bilgi
Buradaki hızlı komutlar, sanal makinenize akan trafikle çalışmaya başlamanızı sağlar. Ağ güvenlik grupları, kaynaklarınıza erişimi denetlemek için çok sayıda harika özellik ve ayrıntı düzeyi sağlar. [Burada ağ güvenlik grubu ve ACL kuralları oluşturma](tutorial-virtual-network.md#secure-network-traffic)hakkında daha fazla bilgi edinebilirsiniz.

Yüksek oranda kullanılabilir Web uygulamaları için sanal makinelerinizi bir Azure Load Balancer arkasına yerleştirmeniz gerekir. Yük dengeleyici, trafiği, trafik filtrelemesi sağlayan bir ağ güvenlik grubuyla sanal makinelere dağıtır. Daha fazla bilgi için bkz. [Azure 'Da Linux sanal makinelerinin yükünü dengelemek, yüksek oranda kullanılabilir bir uygulama oluşturmak için](tutorial-load-balancer.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekte, HTTP trafiğine izin vermek için basit bir kural oluşturdunuz. Aşağıdaki makalelerde daha ayrıntılı ortamlar oluşturma hakkında bilgi edinebilirsiniz:

* [Azure Resource Manager’a genel bakış](../../azure-resource-manager/resource-group-overview.md)
* [Ağ Güvenlik Grubu (NSG) Nedir?](../../virtual-network/security-overview.md)

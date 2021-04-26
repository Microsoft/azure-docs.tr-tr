---
title: Azure sanal ağındaki bir IPv4 uygulamasına IPv6 ekleme-Azure CLı
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure CLı kullanarak Azure sanal ağı 'nda var olan bir uygulamaya IPv6 adreslerinin nasıl dağıtılacağı gösterilmektedir.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 5835ea4d80f9c4111b76672facc4a0250ae0079a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769868"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Azure sanal ağındaki bir IPv4 uygulamasına IPv6 ekleme-Azure CLı

Bu makalede, Azure CLı kullanarak bir Standart Load Balancer için Azure sanal ağında IPv4 Genel IP adresi kullanan bir uygulamaya IPv6 adresleri ekleme işlemi gösterilmektedir. Yerinde yükseltme, bir sanal ağ ve alt ağ, IPv4 + ıPV6 ön uç yapılandırmalarına sahip bir Standart Load Balancer, bir IPv4 + IPv6 yapılandırması, ağ güvenlik grubu ve genel IP 'leri olan NIC 'Ler içeren VM 'Ler içerir.

## <a name="prerequisites"></a>Önkoşullar

- Bu makalede [hızlı başlangıç: bir standart Load Balancer oluşturma-Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md)bölümünde açıklandığı gibi standart Load Balancer dağıttığınız varsayılmaktadır.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-ipv6-addresses"></a>IPv6 adresleri oluştur

Standart Load Balancer için [az Network public-ip Create](/cli/azure/network/public-ip) Ile genel IPv6 adresi oluşturun. Aşağıdaki örnek, *Myresourcegroupslb* kaynak grubunda *PublicIP_v6* ADLı bir IPv6 genel IP adresi oluşturur:

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>IPv6 yük dengeleyici ön uç 'yi yapılandırma

[Az Network lb ön uç-IP Create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) ' i kullanarak yük dengeleyiciyi yenı IPv6 IP adresiyle yapılandırın:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6 yük dengeleyici arka uç havuzunu yapılandırma

[Az Network lb Address-Pool Create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) kullanılarak IPv6 adresleriyle NIC 'ler için arka uç havuzunu aşağıdaki gibi oluşturun:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6 yük dengeleyici kurallarını yapılandırma

[Az Network lb Rule Create](/cli/azure/network/lb/rule#az_network_lb_rule_create)komutuyla IPv6 yük dengeleyici kuralları oluşturun.

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6 adres aralıklarını Ekle

IPv6 adres aralıklarını sanal ağa ve yük dengeleyiciyi barındıran alt ağa aşağıdaki şekilde ekleyin:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>NIC 'lere IPv6 yapılandırması ekleme

[Az Network Nic IP-Config Create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) komutunu kullanarak VM NIC 'Leri bir IPv6 adresi ile yapılandırın:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal 'de IPv6 çift yığın sanal ağını görüntüleme

IPv6 çift yığın sanal ağını Azure portal içinde aşağıdaki gibi görüntüleyebilirsiniz:
1. Portalın arama çubuğunda *Myvnet*' i girin.
2. Arama sonuçlarında **Myvnet** göründüğünde seçin. Bu, *Myvnet* adlı çift yığın sanal ağının **genel bakış** sayfasını başlatır. Çift yığın sanal ağı, *Mysubnet* adlı çift yığın alt ağında bulunan IPv4 ve IPv6 yapılandırmalarına sahip üç NIC 'yi gösterir.

  ![Azure 'da IPv6 çift yığın sanal ağı](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu, VM 'yi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir IPv4 ön uç IP yapılandırması olan mevcut bir Standart Load Balancer ikili yığın (IPv4 ve IPv6) yapılandırmasına güncelleştirmiş olursunuz. Ayrıca, arka uç havuzundaki sanal makinelerin NIC 'lerine IPv6 yapılandırması eklediniz. Azure sanal ağlarında IPv6 desteği hakkında daha fazla bilgi edinmek için bkz. [Azure sanal ağ Için IPv6 nedir?](ipv6-overview.md)

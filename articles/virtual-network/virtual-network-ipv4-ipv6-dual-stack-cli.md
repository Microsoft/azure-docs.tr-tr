---
title: IPv6 ikili yığın uygulaması dağıtma-temel Load Balancer-CLı
titlesuffix: Azure Virtual Network
description: Azure CLı kullanarak temel Load Balancer bir çift yığın (IPv4 + IPv6) uygulaması dağıtmayı öğrenin.
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
ms.openlocfilehash: e6205b4c5428f03459bc75c6fbb2a40458f0f898
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773216"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Temel Load Balancer CLı kullanarak IPv6 ikili yığın uygulaması dağıtma

Bu makalede, çift yığın alt ağına sahip çift yığın sanal ağını, Çift (IPv4 + IPv6) ön uç yapılandırmalarına sahip bir temel Load Balancer, çift IP yapılandırması, çift ağ güvenlik grubu kuralları ve ikili genel IP 'Ler içeren NIC 'Ler içeren VM 'Ler içeren bir çift yığın (IPv4 + IPv6) Load Balancer uygulamasının nasıl dağıtılacağı gösterilmektedir.

Standart Load Balancer kullanarak bir çift yığın (ıPV4 + IPv6) uygulaması dağıtmak için bkz. [Azure CLI kullanarak standart Load Balancer Ile IPv6 ikili yığın uygulaması dağıtma](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.49 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Çift yığın Sanal ağınızı oluşturabilmeniz için [az Group Create](/cli/azure/group)ile bir kaynak grubu oluşturmanız gerekir. Aşağıdaki örnek *eastus* konumunda *DsResourceGroup01* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Yük Dengeleyici için IPv4 ve IPv6 genel IP adresleri oluşturma
Internet 'teki IPv4 ve IPv6 uç noktalarınıza erişmek için yük dengeleyici için IPv4 ve IPv6 genel IP adresleri gerekir. [az network public-ip create](/cli/azure/network/public-ip) komutu ile bir genel IP adresi oluşturun. Aşağıdaki örnek, *DsResourceGroup01* kaynak grubunda *dsPublicIP_v4* ve *dsPublicIP_v6* adlı IPv4 ve IPv6 genel IP adresi oluşturur:

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>VM 'Ler için genel IP adresleri oluşturma

İnternet 'teki sanal makinelerinize uzaktan erişmek için VM 'Ler için IPv4 Genel IP adreslerine ihtiyacınız vardır. [az network public-ip create](/cli/azure/network/public-ip) komutu ile bir genel IP adresi oluşturun.

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Temel Yük Dengeleyici Oluşturma

Bu bölümde, yük dengeleyici için çift ön uç IP (IPv4 ve IPv6) ve arka uç adres havuzunu yapılandırır ve ardından temel bir Load Balancer oluşturursunuz.

### <a name="create-load-balancer"></a>Yük dengeleyici oluşturma

Önceki adımda oluşturduğunuz **DsPublicIP_v4** ıPV4 genel IP adresi ile ilişkili **dsLbBackEndPool_v4** adlı bir arka uç havuzu içeren, [az network lb Create](/cli/azure/network/lb) adlı **dsdsLbFrontEnd_v4 lb** adlı temel Load Balancer oluşturun.  

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>IPv6 ön ucu oluşturma

[Az Network lb ön uç-IP Create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create)komutuyla bir IPv6 ön uç IP 'si oluşturun. Aşağıdaki örnek, *dsLbFrontEnd_v6* adlı bir ön uç IP yapılandırması oluşturur ve *dsPublicIP_v6* adresini iliştirir:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6 arka uç adres havuzunu yapılandırma

[Az Network lb Address-Pool Create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create)komutuyla bir IPv6 arka uç adres havuzu oluşturun. Aşağıdaki örnek, IPv6 NIC yapılandırmalarına sahip VM 'Leri dahil etmek için *dsLbBackEndPool_v6*  adlı arka uç adres havuzunu oluşturur:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Durum araştırması oluşturma
Sanal makinelerin durumunu izlemek için [az network lb probe create](/cli/azure/network/lb/probe) ile bir durum araştırması oluşturun. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Trafiğin VM’lere dağıtımını tanımlamak için bir yük dengeleyici kuralı kullanılır. Gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırması ve trafiği almak için arka uç IP havuzu tanımlamanız gerekir. 

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) komutuyla bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek, *dsLBrule_v4* ve *dsLBrule_v6* adlı yük dengeleyici kuralları oluşturur ve *TCP* bağlantı noktası *80* ' deki trafiği IPv4 ve IPv6 ön uç IP yapılandırmalarına dengeler:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma
Bazı VM 'Leri dağıtmadan önce destekleyici ağ kaynakları (kullanılabilirlik kümesi, ağ güvenlik grubu, sanal ağ ve sanal NIC 'Ler) oluşturmanız gerekir. 
### <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma
Uygulamanızın kullanılabilirliğini artırmak için VM 'lerinizi bir kullanılabilirlik kümesine yerleştirin.

[az vm availability-set create](/cli/azure/vm/availability-set) komutunu kullanarak bir kullanılabilirlik kümesi oluşturun. Aşağıdaki örnek, *Dsavset* adlı bir kullanılabilirlik kümesi oluşturur:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Ağ güvenlik grubu oluşturma

VNET 'iniz içindeki gelen ve giden iletişimi yönetecek kurallar için bir ağ güvenlik grubu oluşturun.

#### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[Az Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create) komutuyla bir ağ güvenlik grubu oluşturun


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Gelen ve giden bağlantılar için bir ağ güvenlik grubu kuralı oluşturma

Bağlantı noktası 3389 üzerinden RDP bağlantılarına izin vermek için bir ağ güvenlik grubu kuralı oluşturun, bağlantı noktası 80 üzerinden internet bağlantısı ve [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)ile giden bağlantılar için.

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) komutu ile bir sanal ağ oluşturun. Aşağıdaki örnek, alt ağlar *dsSubNET_v4* ve *DsSubNET_v6* ile *dsvnet* adlı bir sanal ağ oluşturur:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>NIC’leri oluşturma

[Az Network Nic Create](/cli/azure/network/nic#az_network_nic_create)Ile her VM Için sanal NIC 'ler oluşturun. Aşağıdaki örnek her VM için bir sanal NIC oluşturur. Her NIC 'de iki IP yapılandırması vardır (1 IPv4 yapılandırması, 1 IPv6 yapılandırması). IPV6 yapılandırmasını [az Network Nic IP-Config Create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create)ile oluşturursunuz.

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

[Az VM Create](/cli/azure/vm#az_vm_create)Ile VM 'ler oluşturun. Aşağıdaki örnekte, zaten mevcut değilse iki VM ve gerekli sanal ağ bileşenleri oluşturulur. 

*DsVM0* sanal makinesini aşağıdaki şekilde oluşturun:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

*DsVM1* sanal makinesini aşağıdaki şekilde oluşturun:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Azure portal 'de IPv6 çift yığın sanal ağını görüntüleme
IPv6 çift yığın sanal ağını Azure portal içinde aşağıdaki gibi görüntüleyebilirsiniz:
1. Portalın arama çubuğunda *Dsvnet*' i girin.
2. Arama sonuçlarında **myVirtualNetwork** göründüğünde seçin. Bu, *Dsvnet* adlı çift yığın sanal ağının **genel bakış** sayfasını başlatır. Çift yığın sanal ağı, hem IPv4 hem de IPv6 yapılandırmalarına sahip ve *Dssubnet* adlı çift yığın alt ağında bulunan iki NIC 'yi gösterir.

  ![Azure 'da IPv6 çift yığın sanal ağı](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, VM’yi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir temel Load Balancer bir çift ön uç IP yapılandırması (IPv4 ve IPv6) oluşturdunuz. Ayrıca, yük dengeleyicinin arka uç havuzuna eklenen çift IP yapılandırmalarına (ıPV4 + IPv6) sahip NIC 'Leri dahil eden iki sanal makine oluşturmuş olursunuz. Azure sanal ağlarında IPv6 desteği hakkında daha fazla bilgi edinmek için bkz. [Azure sanal ağ Için IPv6 nedir?](ipv6-overview.md)

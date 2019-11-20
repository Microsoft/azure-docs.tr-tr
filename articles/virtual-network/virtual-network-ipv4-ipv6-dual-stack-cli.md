---
title: Azure-CLı 'de temel Load Balancer kullanarak bir IPv6 ikili yığın uygulaması dağıtma
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure CLı kullanarak Azure sanal ağ 'da IPv6 ikili yığın uygulamasının nasıl dağıtılacağı gösterilmektedir.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: d4ca26606eb8be5b9092f40b70b57b9d5d85385c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804009"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli-preview"></a>Temel Load Balancer-CLı (Önizleme) kullanarak bir IPv6 çift yığın uygulaması dağıtma

Bu makalede, çift yığın alt ağına sahip bir çift yığın sanal ağı, ikili (IPv4 + IPv6) ön uç yapılandırmalarına sahip temel bir Load Balancer ve NIC 'leri olan VM 'Ler içeren bir çift yığın (IPv4 + IPv6) Load Balancer uygulamasının nasıl dağıtılacağı gösterilmektedir. Bu, çift bir IP yapılandırmasına, çift ağ güvenlik grubu kuralına ve ikili genel IP 'Lere sahiptir.

Standart Load Balancer kullanarak bir çift yığın (ıPV4 + IPv6) uygulaması dağıtmak için bkz. [Azure CLI kullanarak standart Load Balancer Ile IPv6 ikili yığın uygulaması dağıtma](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

> [!Important]
> Azure sanal ağ için IPv6 ikili yığını şu anda genel önizlemededir. Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure aboneliğiniz yoksa şimdi [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bunun yerine Azure CLı 'yı yüklemek ve kullanmak isterseniz, bu hızlı başlangıç, Azure CLı sürüm 2.0.49 veya sonraki bir sürümünü kullanmanızı gerektirir. Yüklü sürümünüzü bulmak için `az --version` ' ı çalıştırın. Bkz. Install veya Upgrade Info for [Azure CLI](/cli/azure/install-azure-cli) .

## <a name="prerequisites"></a>Önkoşullar
Azure sanal ağ için IPv6 özelliğini kullanmak üzere aboneliğinizi Azure CLı kullanarak aşağıdaki şekilde yapılandırmanız gerekir:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Özellik kaydının tamamlanabilmesi 30 dakika kadar sürer. Aşağıdaki Azure CLı komutunu çalıştırarak kayıt durumunuzu kontrol edebilirsiniz:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Kayıt tamamlandıktan sonra aşağıdaki komutu çalıştırın:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Çift yığın Sanal ağınızı oluşturabilmeniz için [az Group Create](/cli/azure/group)ile bir kaynak grubu oluşturmanız gerekir. Aşağıdaki örnek *eastus* konumunda *DsResourceGroup01* adlı bir kaynak grubu oluşturur:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Yük Dengeleyici için IPv4 ve IPv6 genel IP adresleri oluşturma
Internet 'teki IPv4 ve IPv6 uç noktalarınıza erişmek için yük dengeleyici için IPv4 ve IPv6 genel IP adresleri gerekir. [az network public-ip create](/cli/azure/network/public-ip) komutu ile bir genel IP adresi oluşturun. Aşağıdaki örnek *DsResourceGroup01* kaynak grubunda *dsPublicIP_v4* ve *dsPublicIP_v6* adlı IPv4 ve IPv6 genel IP adresi oluşturur:

```azurecli
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

```azurecli
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

**DsLbFrontEnd_v4**adlı bir ön uç havuzu içeren, [az Network lb Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) adlı **dslb** ile temel Load Balancer oluşturun, bu, IPv4 Genel IP adresiyle **ilişkili dsLbBackEndPool_v4 adlı bir arka uç havuzu içerir** önceki adımda oluşturduğunuz dsPublicIP_v4. 

```azurecli
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

[Az Network lb ön uç-IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)komutuyla bir IPv6 ön uç IP 'si oluşturun. Aşağıdaki örnek *dsLbFrontEnd_v6* adlı bir ön uç IP yapılandırması oluşturur ve *dsPublicIP_v6* adresini iliştirir:

```azurecli
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>IPv6 arka uç adres havuzunu yapılandırma

[Az Network lb Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)komutuyla bir IPv6 arka uç adres havuzu oluşturun. Aşağıdaki örnek, IPv6 NIC yapılandırmalarına sahip VM 'Leri eklemek için *dsLbBackEndPool_v6* adlı arka uç adres havuzunu oluşturur:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Trafiğin VM’lere dağıtımını tanımlamak için bir yük dengeleyici kuralı kullanılır. Gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırması ve trafiği almak için arka uç IP havuzu tanımlamanız gerekir. 

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) komutuyla bir yük dengeleyici kuralı oluşturun. Aşağıdaki örnek, *dsLBrule_v4* ve *dsLBrule_v6* adlı yük dengeleyici kuralları oluşturur ve *TCP* bağlantı noktası *80* üzerindeki trafiği IPv4 ve IPv6 ön uç IP yapılandırmalarına dengeler:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma
Bazı VM 'Leri dağıtmadan önce destekleyici ağ kaynakları (kullanılabilirlik kümesi, ağ güvenlik grubu, sanal ağ ve sanal NIC 'Ler) oluşturmanız gerekir. 
### <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma
Uygulamanızın kullanılabilirliğini artırmak için VM 'lerinizi bir kullanılabilirlik kümesine yerleştirin.

[az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) komutunu kullanarak bir kullanılabilirlik kümesi oluşturun. Aşağıdaki örnek, *Dsavset*adlı bir kullanılabilirlik kümesi oluşturur:

```azurecli
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

[Az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) komutuyla bir ağ güvenlik grubu oluşturun


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Gelen ve giden bağlantılar için bir ağ güvenlik grubu kuralı oluşturma

Bağlantı noktası 3389 üzerinden RDP bağlantılarına izin vermek için bir ağ güvenlik grubu kuralı oluşturun, bağlantı noktası 80 üzerinden internet bağlantısı ve [az Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)ile giden bağlantılar için.

```azurecli
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


### <a name="create-a-virtual-network"></a>Sanal ağ oluşturun

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) komutu ile bir sanal ağ oluşturun. Aşağıdaki örnek, *dsSubNET_v4* ve *dsSubNET_v6*alt ağları ile *dsvnet* adlı bir sanal ağ oluşturur:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>NIC’leri oluşturma

[Az Network Nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)Ile her VM Için sanal NIC 'ler oluşturun. Aşağıdaki örnek her VM için bir sanal NIC oluşturur. Her NIC 'de iki IP yapılandırması vardır (1 IPv4 yapılandırması, 1 IPv6 yapılandırması). IPV6 yapılandırmasını [az Network Nic IP-Config Create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)ile oluşturursunuz.
 
```azurecli
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

[Az VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)Ile VM 'ler oluşturun. Aşağıdaki örnekte, zaten mevcut değilse iki VM ve gerekli sanal ağ bileşenleri oluşturulur. 

*DsVM0* sanal makinesini aşağıdaki şekilde oluşturun:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```
*DsVM1* sanal makinesini aşağıdaki şekilde oluşturun:

```azurecli
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
2. Arama sonuçlarında **myVirtualNetwork** göründüğünde seçin. Bu, *Dsvnet*adlı çift yığın sanal ağının **genel bakış** sayfasını başlatır. Çift yığın sanal ağı, hem IPv4 hem de IPv6 yapılandırmalarına sahip ve *Dssubnet*adlı çift yığın alt ağında bulunan iki NIC 'yi gösterir.

  ![Azure 'da IPv6 çift yığın sanal ağı](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Azure sanal ağ için IPv6, bu önizleme sürümünde salt okunurdur Azure portal kullanılabilir.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az-group-delete) komutunu kullanarak kaynak grubunu, VM’yi ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir temel Load Balancer bir çift ön uç IP yapılandırması (IPv4 ve IPv6) oluşturdunuz. Ayrıca, yük dengeleyicinin arka uç havuzuna eklenen çift IP yapılandırmalarına (ıPV4 + IPv6) sahip NIC 'Leri dahil eden iki sanal makine oluşturmuş olursunuz. Azure sanal ağlarında IPv6 desteği hakkında daha fazla bilgi edinmek için bkz. [Azure sanal ağ Için IPv6 nedir?](ipv6-overview.md)

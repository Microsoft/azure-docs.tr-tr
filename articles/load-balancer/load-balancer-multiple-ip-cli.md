---
title: Azure CLı kullanarak birden çok IP yapılandırmasında Yük Dengeleme
titlesuffix: Azure Load Balancer
description: Azure CLı kullanarak bir sanal makineye birden çok IP adresi atamayı öğrenin.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: c1606f14650843ea42cfe55381b5f23bf2742a58
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274713"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Azure CLı kullanarak birden çok IP yapılandırmasında Yük Dengeleme

Bu makalede, bir ikincil ağ arabirimi (NIC) üzerinde birden çok IP adresi ile Azure Load Balancer nasıl kullanılacağı açıklanır. Bu senaryo için, her biri birincil ve ikincil NIC olan Windows çalıştıran iki sanal makine vardır. İkincil NIC 'lerin her birinin iki IP yapılandırması vardır. Her VM, hem contoso.com hem de fabrikam.com Web sitelerini barındırır. Her Web sitesi, ikincil NIC 'deki IP yapılandırmalarından birine bağlıdır. Her Web sitesi için bir tane olmak üzere, bir Web sitesi için ilgili IP yapılandırmasına trafik dağıtmak üzere iki ön uç IP adresini kullanıma sunmak için Azure Load Balancer kullanırız. Bu senaryo, hem ön uç havuzu IP adresleri hem de ön uçlarda aynı bağlantı noktası numarasını kullanır.

![LB senaryo resmi](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Birden çok IP yapılandırmasında yük dengelemeye yönelik adımlar

Bu makalede özetlenen senaryoya ulaşmak için aşağıdaki adımları izleyin:

1. Bağlantılı makaledeki adımları izleyerek [Azure CLI 'Yı yükleyip yapılandırın](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ve Azure hesabınızda oturum açın.
2. *Contosofabrikam* adlı [bir kaynak grubunu](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) aşağıdaki gibi oluşturun:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. İki VM için için [bir kullanılabilirlik kümesi oluşturun](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) . Bu senaryo için aşağıdaki komutu kullanın:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. *Myvnet* adlı [bir sanal ağ](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) ve *Mysubnet*adlı bir alt ağ oluşturun:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. *Mylb*adlı [Yük dengeleyiciyi oluşturun](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) :

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Yük dengeleyicinizin ön uç IP yapılandırması için iki dinamik genel IP adresi oluşturun:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Sırasıyla *contosofe* ve *fabrikamfe* olmak üzere iki ön uç IP yapılandırması oluşturun:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Arka uç adres havuzlarınızı oluşturun *-contosopool* ve *fabrikampool*, bir [araştırma](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *http*ve yük dengeleme kurallarınızı- *httpc* ve *httpf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Aşağıdaki komutu çalıştırarak [Yük dengeleyicinizin](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) doğru şekilde oluşturulduğunu doğrulamak için çıktıyı denetleyin:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. *Mystorageaccont1* ilk sanal makineniz için [bir genel IP](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *mypublicıp*ve [depolama hesabı](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)oluşturun, VM1 aşağıdaki gibi:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. VM1 için [ağ arabirimlerini oluşturun](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) ve ıkıncı bir IP yapılandırması, *VM1-ipconfig2*ekleyin ve [VM 'yi](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) şu şekilde oluşturun:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. İkinci VM 'niz için 10-11 adımlarını yineleyin:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Son olarak, DNS kaynak kayıtlarını Load Balancer ilgili ön uç IP adresini gösterecek şekilde yapılandırmanız gerekir. Etki alanlarınızı Azure DNS barındırabilirsiniz. Load Balancer ile Azure DNS kullanma hakkında daha fazla bilgi için bkz. [diğer Azure hizmetleriyle Azure DNS kullanma](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Sonraki adımlar
- Azure 'da yük [Dengeleme hizmetlerini kullanarak](../traffic-manager/traffic-manager-load-balancing-azure.md)Yük Dengeleme hizmetlerini Azure 'da birleştirme hakkında daha fazla bilgi edinin.
- [Azure Load Balancer Için Log Analytics](../load-balancer/load-balancer-monitor-log.md)'te yük dengeleyiciyi yönetmek ve sorunlarını gidermek için Azure 'da farklı türlerdeki günlükleri nasıl kullanabileceğinizi öğrenin.

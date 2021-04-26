---
title: Bir bölgedeki VM 'Leri yük dengelemesi-Azure CLı
description: Bu Azure CLI betik örneğinde, belirli bir kullanılabilirlik alanındaki VM'lerde trafik yük dengelemesi gösterilmektedir
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a8ce3f52005b349c266381da599ebe09c866fce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788736"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-within-a-specific-availability-zone"></a>Azure CLı betiği örneği: belirli bir kullanılabilirlik alanı içindeki VM 'lere Yük Dengeleme trafiği

Bu Azure CLI betik örneği, belirli bir kullanılabilirlik alanında yüksek oranda kullanılabilir ve yük dengeli bir yapılandırmada yapılandırılmış birkaç Ubuntu sanal makinesini çalıştırmak için gereken her şeyi oluşturur. Betiği çalıştırdıktan sonra Azure Standart Load Balancer üzerinden erişilebilen bir bölgenin içinde tekli kullanılabilirlik alanlarında yer alan üç sanal makineniz olur. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

```azurecli-interactive
  #!/bin/bash

  # Create a resource group.
   az group create \
    --name myResourceGroup \
    --location westeurope

  # Create a virtual network.
   az network vnet create \
    --resource-group myResourceGroup \
    --location westeurope \
    --name myVnet \
    --subnet-name mySubnet

  # Create a zonal Standard public IP address.
   az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard
    --zone 1

  # Create an Azure Load Balancer.
   az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --sku Standard

  # Creates an LB probe on port 80.
   az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80

  # Creates an LB rule for port 80.
   az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRuleWeb \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe

  # Create three NAT rules for port 22.
   for i in `seq 1 3`; do
    az network lb inbound-nat-rule create \
     --resource-group myResourceGroup \
     --lb-name myLoadBalancer \
     --name myLoadBalancerRuleSSH$i \
     --protocol tcp \
     --frontend-port 422$i \
     --backend-port 22 \
     --frontend-ip-name myFrontEndPool
   done

  # Create a network security group
   az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup

  # Create a network security group rule for port 22.
   az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*'  \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 1000

  # Create a network security group rule for port 80.
    az network nsg rule create \
     --resource-group myResourceGroup \
     --nsg-name myNetworkSecurityGroup \
     --name myNetworkSecurityGroupRuleHTTP \
     --protocol tcp \
     --direction inbound \
     --source-address-prefix '*' \
     --source-port-range '*' \
     --destination-address-prefix '*' \
     --destination-port-range 80 \
     --access allow \
     --priority 2000

  # Create three virtual network cards and associate with public IP address and NSG.
  for i in `seq 1 3`; do
   az network nic create \
     --resource-group myResourceGroup \
     --name myNic$i \
     --vnet-name myVnet \
     --subnet mySubnet \
     --network-security-group myNetworkSecurityGroup \
     --lb-name myLoadBalancer \
     --lb-address-pools myBackEndPool \
     --lb-inbound-nat-rules myLoadBalancerRuleSSH$i
  done

# Create three virtual machines, this creates SSH keys if not present.
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroup \
    --name myVM$i \
    --zone 1 \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
done

```

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, sanal makine, kullanılabilirlik kümesi, yük dengeleyici ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Bir Azure sanal ağı ve alt ağ oluşturur. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Statik bir IP adresi ve ilişkili bir DNS adı ile bir genel IP adresi oluşturur. |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Azure yük dengeleyici oluşturur. |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | Yük dengeleyici araştırması oluşturur. Yük dengeleyici araştırması, yük dengeleyici kümesindeki her bir VM’yi izlemek için kullanılır. Herhangi bir VM erişilemez hale gelirse trafik VM’ye yönlendirilmez. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Yük dengeleyici kuralı oluşturur. Bu örnekte 80 numaralı bağlantı noktası için bir kural oluşturulur. HTTP trafiği yük dengeleyiciye ulaştığında, yük dengeleyici kümesindeki VM’lerden birinin 80 numaralı bağlantı noktasına yönlendirilir. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Yük dengeleyici Ağ Adresi Çevirisi (NAT) kuralı oluşturur.  NAT kuralları, bir yük dengeleyici bağlantı noktasını VM üzerindeki bir bağlantı noktasına eşler. Bu örnekte, yük dengeleyici kümesindeki her bir VM’ye giden SSH trafiği için bir NAT kuralı oluşturulur.  |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | İnternet ile sanal makine arasında güvenlik sınırı olan bir ağ güvenlik grubu (NSG) oluşturur. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Gelen trafiğe izin veren bir NSG kuralı oluşturur. Bu örnekte 22 numaralı bağlantı noktası SSH trafiğine açılır. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Sanal makine kartı oluşturur ve sanal ağa, alt ağa ve NSG’ye bağlar. |
| [az vm create](/cli/azure/vm#az_vm_create) | Sanal makine oluşturur ve ağ kartına, sanal ağa, alt ağa ve NSG’ye bağlar. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure Ağ CLI betiği örnekleri, [Azure Ağ belgelerinde](../cli-samples.md) bulunabilir.
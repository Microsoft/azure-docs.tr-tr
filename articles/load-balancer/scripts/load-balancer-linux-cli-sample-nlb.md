---
title: HA için VM 'lere trafik yükünü dengelemek-Azure CLı-Azure Load Balancer
description: Bu Azure CLI betik örneğinde, yüksek oranda kullanılabilirlik için VM’lerde trafik yük dengelemesi gösterilmektedir
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3aaa7246806a1a7f1352481d26df00091e52a440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790032"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure CLI betik örneği: Yüksek oranda kullanılabilirlik için VM’lerde trafik yük dengelemesi

Bu Azure CLI betik örneği, yüksek oranda kullanılabilir ve yük dengeli bir yapılandırmada yapılandırılmış birkaç Ubuntu sanal makinesini çalıştırmak için gereken her şeyi oluşturur. Betiği çalıştırdıktan sonra bir Azure Kullanılabilirlik Kümesine eklenmiş ve bir Azure Load Balancer üzerinden erişilebilen üç sanal makineniz olur. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

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
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Bir kullanılabilirlik kümesi oluşturur. Kullanılabilirlik kümeleri, hata oluşması durumunda tüm kümenin etkilenmemesi için sanal makineleri fiziksel kaynaklara yayarak uygulama çalışma süresi sağlar. |
| [az vm create](/cli/azure/vm#az_vm_create) | Sanal makine oluşturur ve ağ kartına, sanal ağa, alt ağa ve NSG’ye bağlar. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure Ağ CLI betiği örnekleri, [Azure Ağ belgelerinde](../cli-samples.md) bulunabilir.
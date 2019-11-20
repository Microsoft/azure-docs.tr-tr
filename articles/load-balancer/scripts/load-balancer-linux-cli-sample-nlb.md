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
ms.openlocfilehash: c0bfb5705989c99d3626d6e728b764e94f83fffe
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048922"
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
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Bir Azure sanal ağı ve alt ağ oluşturur. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | Statik bir IP adresi ve ilişkili bir DNS adı ile bir genel IP adresi oluşturur. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Azure yük dengeleyici oluşturur. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | Yük dengeleyici araştırması oluşturur. Yük dengeleyici araştırması, yük dengeleyici kümesindeki her bir VM’yi izlemek için kullanılır. Herhangi bir VM erişilemez hale gelirse trafik VM’ye yönlendirilmez. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Yük dengeleyici kuralı oluşturur. Bu örnekte 80 numaralı bağlantı noktası için bir kural oluşturulur. HTTP trafiği yük dengeleyiciye ulaştığında, yük dengeleyici kümesindeki VM’lerden birinin 80 numaralı bağlantı noktasına yönlendirilir. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Yük dengeleyici Ağ Adresi Çevirisi (NAT) kuralı oluşturur.  NAT kuralları, bir yük dengeleyici bağlantı noktasını VM üzerindeki bir bağlantı noktasına eşler. Bu örnekte, yük dengeleyici kümesindeki her bir VM’ye giden SSH trafiği için bir NAT kuralı oluşturulur.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az-network-nsg-create) | İnternet ile sanal makine arasında güvenlik sınırı olan bir ağ güvenlik grubu (NSG) oluşturur. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Gelen trafiğe izin veren bir NSG kuralı oluşturur. Bu örnekte 22 numaralı bağlantı noktası SSH trafiğine açılır. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | Sanal makine kartı oluşturur ve sanal ağa, alt ağa ve NSG’ye bağlar. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Bir kullanılabilirlik kümesi oluşturur. Kullanılabilirlik kümeleri, hata oluşması durumunda tüm kümenin etkilenmemesi için sanal makineleri fiziksel kaynaklara yayarak uygulama çalışma süresi sağlar. |
| [az vm create](/cli/azure/vm#az-vm-create) | Sanal makine oluşturur ve ağ kartına, sanal ağa, alt ağa ve NSG’ye bağlar. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek Azure Ağ CLI betiği örnekleri, [Azure Ağ belgelerinde](../cli-samples.md) bulunabilir.
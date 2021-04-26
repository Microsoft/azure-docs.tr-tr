---
title: Birden çok Web sitesi yük dengelemesi-Azure CLı-Azure Load Balancer
description: Bu Azure CLI betiği örneğinde, aynı sanal makinede birden fazla web sitesi için yük dengeleme gösterilmektedir
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77dfc42164419d86e174bc47297e2f596e757ffe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790104"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Azure CLI betik örneği: Birden çok web sitesinin yük dengelemesi

Bu Azure CLI betik örneği bir kullanılabilirlik kümesinin üyesi olan iki sanal makine (VM) ile bir sanal ağ oluşturur. Yük dengeleyici iki ayrı IP adresi için trafiği iki VM’ye yönlendirir. Betiği çalıştırdıktan sonra, web sunucusu yazılımını VM’lere dağıtabilir ve her biri kendi IP adresine sahip birden fazla web sitesi barındırabilirsiniz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, sanal ağ, yük dengeleyici ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Bir Azure sanal ağı ve alt ağ oluşturur. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Statik bir IP adresi ve ilişkili bir DNS adı ile bir genel IP adresi oluşturur. |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Azure Load Balancer oluşturur. |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | Yük dengeleyici araştırması oluşturur. Yük dengeleyici araştırması, yük dengeleyici kümesindeki her bir VM’yi izlemek için kullanılır. Herhangi bir VM erişilemez hale gelirse trafik VM’ye yönlendirilmez. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Yük dengeleyici kuralı oluşturur. Bu örnekte 80 numaralı bağlantı noktası için bir kural oluşturulur. HTTP trafiği yük dengeleyiciye ulaştığında, yük dengeleyici kümesindeki VM’lerden birinin 80 numaralı bağlantı noktasına yönlendirilir. |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Yük Dengeleyici için ön uç IP adresi oluşturur. |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Arka uç adres havuzu oluşturur. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Sanal makine kartı oluşturur ve sanal ağa ve alt ağa bağlar. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Bir kullanılabilirlik kümesi oluşturur. Kullanılabilirlik kümeleri, hata oluşması durumunda tüm kümenin etkilenmemesi için sanal makineleri fiziksel kaynaklara yayarak uygulama çalışma süresi sağlar. |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Bir IP yapılandırması oluşturur. Aboneliğiniz için Microsoft.Network/AllowMultipleIpConfigurationsPerNic özelliğini etkinleştirmeniz gerekir. Yalnızca bir yapılandırma, --make-primary flag kullanılarak her bir NIC için ana IP yapılandırması olarak atanabilir. |
| [az vm create](/cli/azure/vm/availability-set#az_vm_availability_set_create) | Sanal makine oluşturur ve ağ kartına, sanal ağa, alt ağa ve NSG’ye bağlar. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek ağ CLI betiği örnekleri, [Azure Ağına Genel Bakış belgeleri](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) içinde bulunabilir.
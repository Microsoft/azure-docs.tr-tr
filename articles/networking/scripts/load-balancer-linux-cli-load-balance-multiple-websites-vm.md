---
title: Azure CLı betik örneği-Azure CLı ile birden çok Web sitesinin yükünü dengeleme | Microsoft Docs
description: Azure CLı betik örneği-aynı sanal makineye birden çok Web sitesinin yükünü dengeleme
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: allensu
ms.openlocfilehash: 568934d6b5ecc2fbb3b6fc6588ecfd48c8306490
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889463"
---
# <a name="load-balance-multiple-websites"></a>Birden çok Web sitesinin yükünü dengelemek

Bu betik örneği, bir kullanılabilirlik kümesinin üyesi olan iki sanal makineye (VM) sahip bir sanal ağ oluşturur. Yük dengeleyici iki ayrı IP adresi için trafiği iki VM’ye yönlendirir. Betiği çalıştırdıktan sonra, web sunucusu yazılımını VM’lere dağıtabilir ve her biri kendi IP adresine sahip birden fazla web sitesi barındırabilirsiniz.

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
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Bir Azure sanal ağı ve alt ağ oluşturur. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Statik bir IP adresi ve ilişkili bir DNS adı ile bir genel IP adresi oluşturur. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Azure Load Balancer oluşturur. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Yük dengeleyici araştırması oluşturur. Yük dengeleyici araştırması, yük dengeleyici kümesindeki her bir VM’yi izlemek için kullanılır. Herhangi bir VM erişilemez hale gelirse trafik VM’ye yönlendirilmez. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Yük dengeleyici kuralı oluşturur. Bu örnekte 80 numaralı bağlantı noktası için bir kural oluşturulur. HTTP trafiği yük dengeleyiciye ulaştığında, yük dengeleyici kümesindeki VM’lerden birinin 80 numaralı bağlantı noktasına yönlendirilir. |
| [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) | Yük Dengeleyici için ön uç IP adresi oluşturur. |
| [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool) | Arka uç adres havuzu oluşturur. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Sanal makine kartı oluşturur ve sanal ağa ve alt ağa bağlar. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Bir kullanılabilirlik kümesi oluşturur. Kullanılabilirlik kümeleri, hata oluşması durumunda tüm kümenin etkilenmemesi için sanal makineleri fiziksel kaynaklara yayarak uygulama çalışma süresi sağlar. |
| [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config) | Bir IP yapılandırması oluşturur. Aboneliğiniz için Microsoft.Network/AllowMultipleIpConfigurationsPerNic özelliğini etkinleştirmeniz gerekir. Yalnızca bir yapılandırma, --make-primary flag kullanılarak her bir NIC için ana IP yapılandırması olarak atanabilir. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Sanal makine oluşturur ve ağ kartına, sanal ağa, alt ağa ve NSG’ye bağlar. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek ağ CLI betiği örnekleri, [Azure Ağına Genel Bakış belgeleri](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) içinde bulunabilir.

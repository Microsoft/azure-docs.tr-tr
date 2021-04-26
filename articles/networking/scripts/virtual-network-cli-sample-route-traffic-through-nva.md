---
title: Azure CLı betik örneği-ağ sanal gereci aracılığıyla trafiği yönlendirme
description: Azure CLI betiği örneği - Güvenlik duvarı ağ sanal gereci aracılığıyla trafiği yönlendirme.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7970ab4472000c53e23f7962a9cbf4ec05ea3465
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763190"
---
# <a name="use-an-azure-cli-script-to-route-traffic-through-a-network-virtual-appliance"></a>Ağ sanal gereci üzerinden trafiği yönlendirmek için bir Azure CLı betiği kullanma

Bu betik örneği, ön uç ve arka uç alt ağları ile sanal ağ oluşturur. Ayrıca iki alt ağ arasında trafiği yönlendirmek için IP iletme etkinleştirilmiş şekilde bir sanal makine de oluşturur. Betiği çalıştırdıktan sonra, sanal makineye güvenlik duvarı uygulaması gibi ağ yazılımı dağıtabilirsiniz.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Örnek betik


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir kaynak grubu, sanal ağ ve ağ güvenliği grupları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az network vnet create](/cli/azure/network/vnet) | Bir Azure sanal ağı ve ön uç alt ağı oluşturur. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Arka uç ve DMZ alt ağları oluşturur. |
| [az network public-ip create](/cli/azure/network/public-ip) | Internet 'ten sanal makineye erişmek için bir genel IP adresi oluşturur. |
| [az network nic create](/cli/azure/network/nic) | Bir sanal ağ arabirimi oluşturur ve bunun için IP iletimini etkinleştirir. |
| [az network nsg create](/cli/azure/network/nsg) | Ağ güvenlik grubu (NSG) oluşturur. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Sanal makineye gelen HTTP ve HTTPS bağlantı noktalarına izin veren NSG kuralları oluşturur. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| NSG’leri ve rota tablolarını alt ağlarla ilişkilendirir. |
| [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create)| Tüm rotalar için bir rota tablosu oluşturur. |
| [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)| VM aracılığıyla trafiği alt ağlar ve Internet arasında yönlendirmek için yollar oluşturur. |
| [az vm create](/cli/azure/vm) | Bir sanal makine oluşturur ve ona NIC’yi ekler. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir. |
| [az group delete](/cli/azure/group) | Bir kaynak grubunu ve içerdiği tüm kaynakları siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek ağ CLı betiği örnekleri, [Azure ağlarına genel bakış belgelerinde](../cli-samples.md) bulunabilir

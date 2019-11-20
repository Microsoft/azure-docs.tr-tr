---
title: Azure CLI betiği örneği - Ağ sanal gereci aracılığıyla trafiği yönlendirme | Microsoft Docs
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
ms.openlocfilehash: 55b9c9cba4a34fc9e209e03565861885ea192cc1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890232"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Ağ sanal gereci aracılığıyla trafiği yönlendirme

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
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Tüm rotalar için bir rota tablosu oluşturur. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| VM aracılığıyla trafiği alt ağlar ve Internet arasında yönlendirmek için yollar oluşturur. |
| [az vm create](/cli/azure/vm) | Bir sanal makine oluşturur ve ona NIC’yi ekler. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir. |
| [az group delete](/cli/azure/group) | Bir kaynak grubunu ve içerdiği tüm kaynakları siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek ağ CLı betiği örnekleri, [Azure ağlarına genel bakış belgelerinde](../cli-samples.md) bulunabilir

---
title: Azure CLI betiği örneği - Sanal makine ağ trafiğini filtreleme | Microsoft Docs
description: Azure CLI betiği örneği - Gelen ve giden sanal makine ağ trafiğini filtreleme.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: e91e59e8e8acbf76ed35cff6b2f654103bb763b5
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888565"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Gelen ve giden sanal makine ağ trafiğini filtreleme

Bu betik örneği, ön uç ve arka uç alt ağları ile sanal ağ oluşturur. Ön uç alt ağına gelen ağ trafiği, HTTP, HTTPS ve SSH ile sınırlandırılmıştır, ancak arka uç alt ağından Internet 'e giden trafiğe izin verilmez. Betiği çalıştırdıktan sonra iki NIC içeren bir sanal makineniz olacaktır. Her NIC, farklı bir alt ağa bağlanır.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

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
| [az network subnet create](/cli/azure/network/vnet/subnet) | Bir arka uç alt ağı oluşturur. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | NSG’leri alt ağlarla ilişkilendirir. |
| [az network public-ip create](/cli/azure/network/public-ip) | Internet 'ten sanal makineye erişmek için bir genel IP adresi oluşturur. |
| [az network nic create](/cli/azure/network/nic) | Sanal ağ arabirimleri oluşturur ve bunları sanal ağın ön uç ve arka uç alt ağlarına ekler. |
| [az network nsg create](/cli/azure/network/nsg) | Ön uç ve arka uç alt ağlarıyla ilişkilendirilmiş ağ güvenlik grupları (NSG) oluşturur. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Belirli alt ağlara yönelik belirli bağlantı noktalarına izin veren veya engelleyen NSG kuralları oluşturur. |
| [az vm create](/cli/azure/vm) | Sanal makineler oluşturur ve her sanal makineye bir NIC ekler. Bu komut ayrıca kullanılacak sanal makine görüntüsünü ve yönetici kimlik bilgilerini belirtir. |
| [az group delete](/cli/azure/group) | Bir kaynak grubunu ve içerdiği tüm kaynakları siler. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek ağ CLı betiği örnekleri, [Azure ağlarına genel bakış belgelerinde](../cli-samples.md) bulunabilir

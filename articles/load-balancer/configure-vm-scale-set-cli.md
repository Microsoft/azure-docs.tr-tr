---
title: Sanal makine ölçek kümesini mevcut bir Azure Load Balancer Yapılandırma-Azure CLı
description: Azure CLı kullanarak bir sanal makine ölçek kümesini mevcut bir Azure Load Balancer nasıl yapılandıracağınızı öğrenin.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518218"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Azure CLı kullanarak mevcut bir Azure Load Balancer bir sanal makine ölçek kümesi yapılandırma

Bu makalede, bir sanal makine ölçek kümesini mevcut bir Azure Load Balancer nasıl yapılandıracağınızı öğreneceksiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar 

- Abonelikte, sanal makine ölçek kümesinin dağıtılacağı mevcut bir standart SKU yük dengeleyicisine sahip olmanız gerekir.

- Sanal makine ölçek kümesi için bir Azure sanal ağı gerekir.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Mevcut yük dengeleyiciye sahip bir sanal makine ölçek kümesi dağıtma

Köşeli ayraçlar içindeki değerleri, yapılandırmanızda bulunan kaynakların adlarıyla değiştirin.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Aşağıdaki örnek, ile bir sanal makine ölçek kümesi dağıtır:

- **Myvmss** adlı sanal makine ölçek kümesi
- **Myloadbalancer** adlı Azure Load Balancer
- **Mybackendpool** adlı yük dengeleyici arka uç havuzu
- **Myvnet** adlı Azure sanal ağı
- **Mysubnet** adlı alt ağ
- **Myresourcegroup** adlı kaynak grubu
- Sanal makine ölçek kümesi için Ubuntu sunucu görüntüsü

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Ölçek kümesi oluşturulduktan sonra, yük dengeleyicinin bir sistem durumu araştırması tarafından kullanılan bir yük dengeleme kuralı için arka uç bağlantı noktası değiştirilemez. Bağlantı noktasını değiştirmek için Azure sanal makine ölçek kümesini güncelleştirerek sistem durumu araştırmasını kaldırabilir, bağlantı noktasını güncelleştirebilir ve ardından sistem durumu araştırmasını yeniden yapılandırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, mevcut bir Azure Load Balancer bir sanal makine ölçek kümesi dağıttınız.  Sanal Makine Ölçek Kümeleri ve yük dengeleyici hakkında daha fazla bilgi edinmek için bkz.:

- [Azure Load Balancer nedir?](load-balancer-overview.md)
- [Sanal makine ölçek kümeleri nedir?](../virtual-machine-scale-sets/overview.md)
                                
---
title: Azure CLı kullanarak genel IP adresi için yönlendirme tercihini yapılandırma
titlesuffix: Azure Virtual Network
description: Azure CLı kullanarak Internet trafiği yönlendirme tercihi ile genel IP oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 58b91c105ed48617b64356904942f5ab6b461cda
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776564"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Azure CLı kullanarak genel IP adresi için yönlendirme tercihini yapılandırma

Bu makalede, Azure CLı kullanarak genel bir IP adresi için ISS ağı (**Internet** seçeneği) aracılığıyla yönlendirme tercihini yapılandırma gösterilmektedir. Genel IP adresini oluşturduktan sonra, internet 'e gelen ve giden trafik için aşağıdaki Azure kaynaklarıyla ilişkilendirebilirsiniz:

* Sanal makine
* Sanal makine ölçek kümesi
* Azure Kubernetes Service (AKS)
* Internet 'e yönelik yük dengeleyici
* Application Gateway
* Azure Güvenlik Duvarı

Varsayılan olarak, genel IP adresi için yönlendirme tercihi tüm Azure hizmetleri için Microsoft Global Network 'e ayarlanır ve herhangi bir Azure hizmetiyle ilişkilendirilebilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.49 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
[az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, **Doğu ABD** Azure bölgesinde bir kaynak grubu oluşturur:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Aşağıda gösterildiği gibi, [az Network public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create)komutunu kullanarak **Internet** türü yönlendirme tercıhı Ile genel bir IP adresi oluşturun.

Aşağıdaki komut, **Doğu ABD** Azure bölgesinde **Internet** yönlendirme tercihi Ile yeni bir genel IP oluşturur.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Şu anda yönlendirme tercihi yalnızca ıPV4 genel IP adreslerini destekler.

Yukarıdaki oluşturulmuş genel IP adresini bir [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesiyle ilişkilendirebilirsiniz. Öğretici sayfasındaki CLı bölümünü kullanın: genel IP adresini VM 'niz ile ilişkilendirmek için bir [sanal makineyle genel IP adresi ilişkilendirin](associate-public-ip-address-vm.md#azure-cli) . Ayrıca, yük dengeleyici **ön uç** yapılandırmasına atayarak yukarıda oluşturulan genel IP adresini bir [Azure Load Balancer](../load-balancer/load-balancer-overview.md)ile ilişkilendirebilirsiniz. Genel IP adresi yükü dengelenmiş bir sanal IP adresi (VIP) olarak işlev görür.

## <a name="next-steps"></a>Sonraki adımlar

- [Genel IP adreslerinde yönlendirme tercihi](routing-preference-overview.md)hakkında daha fazla bilgi edinin. 
- [Azure CLI kullanarak BIR VM için yönlendirme tercihini yapılandırın](configure-routing-preference-virtual-machine-cli.md).

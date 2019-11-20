---
title: Linux sanal makineleri için yakınlık yerleştirme gruplarını kullanma
description: Azure 'da Linux sanal makineleri için yakınlık yerleşimi grupları oluşturma ve kullanma hakkında bilgi edinin.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171044"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Azure CLı kullanarak yakınlık yerleşimi gruplarına VM 'Leri dağıtma

Olası en düşük gecikme süresini elde etmek üzere VM 'Leri olabildiğince yakın bir şekilde almak için, bunları bir [yakınlık yerleşimi grubu](co-location.md#proximity-placement-groups)içinde dağıtmanız gerekir.

Yakınlık yerleşimi grubu, Azure işlem kaynaklarının fiziksel olarak birbirlerine yakın bir yerde bulunduğundan emin olmak için kullanılan mantıksal bir gruplandırmadır. Yakınlık yerleşimi grupları, düşük gecikme süresinin gereksinim olduğu iş yükleri için faydalıdır.


## <a name="create-the-proximity-placement-group"></a>Yakınlık yerleşimi grubunu oluşturma
[`az ppg create`](/cli/azure/ppg#az-ppg-create)kullanarak bir yakınlık yerleşimi grubu oluşturun. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Yakınlık yerleşimi gruplarını Listele

[Az PPG List](/cli/azure/ppg#az-ppg-list)kullanarak tüm yakınlık yerleşimi gruplarınızı listeleyebilirsiniz.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>VM oluşturma

[Yeni az VM](/cli/azure/vm#az-vm-create)kullanarak yakınlık yerleşimi grubu IÇINDE bir VM oluşturun.

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

VM 'yi, [az PPG Show](/cli/azure/ppg#az-ppg-show)kullanarak yakınlık yerleşimi grubunda görebilirsiniz.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Kullanılabilirlik Kümeleri
Ayrıca, yakınlık yerleşimi grubunuzda bir kullanılabilirlik kümesi oluşturabilirsiniz. Kullanılabilirlik kümesi oluşturmak için [az VM AVAILABILITY-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) ile aynı `--ppg` parametresini kullanın ve kullanılabilirlik kümesindeki tüm VM 'lerin aynı yakınlık yerleşimi grubunda de oluşturulması gerekir.

## <a name="scale-sets"></a>Ölçek kümeleri

Ayrıca, yakınlık yerleştirme grubunuzda bir ölçek kümesi de oluşturabilirsiniz. Bir ölçek kümesi oluşturmak için [az VMSS Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) ile aynı `--ppg` parametresini kullanın ve tüm örnekler aynı yakınlık yerleşimi grubunda oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

Yakınlık yerleşimi grupları için [Azure CLI](/cli/azure/ppg) komutları hakkında daha fazla bilgi edinin.

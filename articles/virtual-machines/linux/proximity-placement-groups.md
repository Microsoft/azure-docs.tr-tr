---
title: Azure CLı kullanarak bir yakınlık yerleşimi grubu oluşturma
description: Azure 'da sanal makineler için yakınlık yerleşimi grupları oluşturma ve kullanma hakkında bilgi edinin.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: e4f91afa86a0d99b4ce42e96295bf2ae1f9fcd9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771452"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Azure CLI'yı kullanarak sanal makineleri yakın yerleştirilen gruplara dağıtma

Olası en düşük gecikme süresini elde etmek üzere VM 'Leri olabildiğince yakın bir şekilde almak için, bunları bir [yakınlık yerleşimi grubu](../co-location.md#proximity-placement-groups)içinde dağıtmanız gerekir.

Yakınlık yerleşimi grubu, Azure işlem kaynaklarının fiziksel olarak birbirlerine yakın bir yerde bulunduğundan emin olmak için kullanılan mantıksal bir gruplandırmadır. Yakınlık yerleşimi grupları, düşük gecikme süresinin gereksinim olduğu iş yükleri için faydalıdır.


## <a name="create-the-proximity-placement-group"></a>Yakınlık yerleşimi grubunu oluşturma
Kullanarak bir yakınlık yerleşimi grubu oluşturun [`az ppg create`](/cli/azure/ppg#az_ppg_create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Yakınlık yerleşimi gruplarını Listele

[Az PPG List](/cli/azure/ppg#az_ppg_list)kullanarak tüm yakınlık yerleşimi gruplarınızı listeleyebilirsiniz.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>VM oluşturma

[Yeni az VM](/cli/azure/vm#az_vm_create)kullanarak yakınlık yerleşimi grubu IÇINDE bir VM oluşturun.

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

VM 'yi, [az PPG Show](/cli/azure/ppg#az_ppg_show)kullanarak yakınlık yerleşimi grubunda görebilirsiniz.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Kullanılabilirlik Kümeleri
Ayrıca, yakınlık yerleşimi grubunuzda bir kullanılabilirlik kümesi oluşturabilirsiniz. `--ppg`Kullanılabilirlik kümesi oluşturmak için [az VM AVAILABILITY-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) ile aynı parametreyi kullanın ve kullanılabilirlik kümesindeki tüm VM 'ler aynı yakınlık yerleşimi grubunda de oluşturulur.

## <a name="scale-sets"></a>Ölçek kümeleri

Ayrıca, yakınlık yerleştirme grubunuzda bir ölçek kümesi de oluşturabilirsiniz. `--ppg`Bir ölçek kümesi oluşturmak için [az VMSS Create](/cli/azure/vmss#az_vmss_create) ile aynı parametreyi kullanın ve tüm örnekler aynı yakınlık yerleşimi grubunda oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

Yakınlık yerleşimi grupları için [Azure CLI](/cli/azure/ppg) komutları hakkında daha fazla bilgi edinin.
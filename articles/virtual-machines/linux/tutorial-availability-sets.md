---
title: Azure CLı kullanarak bir kullanılabilirlik kümesindeki VM 'Leri dağıtma
description: Kullanılabilirlik kümelerinde yüksek oranda kullanılabilir sanal makineleri dağıtmak için Azure CLı 'yı nasıl kullanacağınızı öğrenin
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 21f7d3e6f624c3a8e550a33293b46284bf5c5815
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816305"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Azure CLı kullanarak bir kullanılabilirlik kümesinde sanal makine oluşturma ve dağıtma

Bu öğreticide, Kullanılabilirlik Kümeleri adlı bir özellik kullanarak Azure’da Sanal Makine çözümlerinizin kullanılabilirlik ve güvenilirliğini nasıl artıracağınızı öğreneceksiniz. Kullanılabilirlik kümeleri, Azure’da dağıttığınız VM’lerin birden fazla yalıtılmış donanım kümesi arasında dağıtılmasını sağlar. Böylece, Azure’da bir donanım veya yazılım hatası oluşursa yalnızca sanal makinelerinizin bir alt kümesinin etkilenmesi ve genel çözümünüzün kullanılabilir ve çalışır durumda kalması sağlanır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kullanılabilirlik kümesi oluşturma
> * Kullanılabilirlik kümesinde sanal makine oluşturma
> * Kullanılabilir sanal makine boyutlarını denetleme

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](../../cloud-shell/overview.md)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma

[az vm availability-set create](/cli/azure/vm/availability-set) komutunu kullanarak bir kullanılabilirlik kümesi oluşturabilirsiniz. Bu örnekte, *myResourceGroupAvailability* kaynak grubundaki *myAvailabilitySet* adlı kullanılabilirlik kümesi için güncelleştirme ve hata etki alanları sayısı *2* olarak ayarlanmıştır.

İlk olarak, [az group create](/cli/azure/group#az_group_create) ile bir kaynak grubu oluşturun ve sonra kullanılabilirlik kümesini oluşturun:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Kullanılabilirlik Kümeleri, hata etki alanları ve güncelleştirme etki alanları arasında kaynakları yalıtmanıza olanak sağlar. **Hata etki alanı**, yalıtılmış bir sunucu + ağ + depolama kaynakları koleksiyonunu temsil eder. Önceki örnekte, sanal makineleri dağıtıldığında kullanılabilirlik kümesi en az iki hata etki alanı arasında dağıtılmıştır. Kullanılabilirlik kümesi de iki **güncelleme etki alanına** dağıtılmıştır. İki güncelleştirme etki alanı, Azure yazılım güncelleştirmeleri gerçekleştirdiğinde sanal makine kaynaklarının yalıtılmasını sağlayarak sanal makinede çalışan tüm yazılımların aynı anda güncelleştirilmesini önler.


## <a name="create-vms-inside-an-availability-set"></a>Kullanılabilirlik kümesi içinde sanal makineler oluşturma

Donanım arasında doğru şekilde dağıtıldığından emin olmak için sanal makinelerin kullanılabilirlik kümesi içinde oluşturulması gerekir. Mevcut VM, oluşturulduktan sonra kullanılabilirlik kümesine eklenemez.

[az vm create](/cli/azure/vm) ile yeni bir VM oluşturulduğunda, kullanılabilirlik kümesinin adını belirtmek için `--availability-set` parametresini kullanın.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Artık kullanılabilirlik kümesi içinde iki sanal makine vardır. Aynı kullanılabilirlik kümesinde olduklarından Azure, sanal makinelerin ve tüm kaynaklarının (veri diskleri dahil), yalıtılmış fiziksel donanım arasında dağıtılmasını sağlar. Bu dağıtım, genel sanal makine çözümünün çok daha yüksek düzeyde kullanılabilir olmasını sağlar.

Kullanılabilirlik kümesi dağıtımı, Kaynak Grupları > myResourceGroupAvailability > myAvailabilitySet öğesine gidilerek portaldan görüntülenebilir. VM’ler aşağıdaki örnekte gösterildiği gibi iki hata ve güncelleme etki alanında dağıtılmıştır:

![Portaldaki kullanılabilirlik kümesi](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kullanılabilir sanal makine boyutlarını denetleme

Ek VM’ler sonradan VM boyutlarının donanımda bulunduğu kullanılabilirlik kümesine eklenebilir. Kullanılabilirlik kümesi için donanım kümesindeki tüm kullanılabilir boyutları listelemek için [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) komutunu kullanın:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Kullanılabilirlik kümesi oluşturma
> * Kullanılabilirlik kümesinde sanal makine oluşturma
> * Kullanılabilir sanal makine boyutlarını denetleme

Sanal makine ölçek kümeleri hakkında daha fazla bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sanal makine ölçek kümesi oluşturma](tutorial-create-vmss.md)

* Kullanılabilirlik bölgeleri hakkında daha fazla bilgi edinmek için  [kullanılabilirlik alanları belgelerini](../../availability-zones/az-overview.md)ziyaret edin.
* Hem kullanılabilirlik kümeleri hem de kullanılabilirlik bölgeleri hakkında daha fazla belgeye de [ulaşabilirsiniz.](../availability.md)
* Kullanılabilirlik bölgelerini denemek için [Azure CLI ile bir kullanılabilirlik alanında Linux sanal makinesi oluşturma](./create-cli-availability-zone.md) sayfasını ziyaret edin
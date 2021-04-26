---
title: Linux sanal makine ölçek kümeleri için Azure Hibrit Avantajı
description: Azure 'da çalışan Linux sanal makinelerinizde tasarruf etmenize yardımcı olmak üzere Azure Hibrit Avantajı sanal makine ölçek kümesine nasıl uygulayabileceğinizi öğrenin.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: a714434c39a0c40c2e908f2d0c424f02851921a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933687"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Linux sanal makine ölçek kümesi için Azure Hibrit Avantajı (Genel Önizleme)

**Linux sanal makine ölçek kümesi için Azure hibrit avantajı Şu anda genel önizlemede**. AHB avantajı, RHEL ve SLES [sanal makine ölçek kümelerinizi](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)çalıştırmanın maliyetini azaltmanıza yardımcı olabilir.

Bu avantajdan yalnızca ölçek kümesinin altyapı maliyeti için ödeme yaparsınız. Bu avantaj, tüm RHEL ve SLES Market (PAYG) görüntüleri için kullanılabilir.


>[!NOTE]
> Bu makalede, Linux VMSS için Azure Hibrit Avantajı açıklanmaktadır. [Linux sanal makineleri için](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux), Kasım 2020 ' den beri Azure müşterilerine zaten sunulan ayrı bir [Makale mevcuttur.

## <a name="benefit-description"></a>Avantaj açıklaması
Azure hibrit, var olan bulut erişim lisanslarını Red hat veya SUSE 'ten kullanmanıza ve sanal makine ölçek kümesi örneklerini kendi aboneliklerinizi getir (KCG) faturalandırmaya dönüştürmenize olanak tanır. 

PAYG marketi görüntülerinden dağıtılan sanal makine ölçek kümesi, hem altyapı hem de yazılım ücretini ücretlendirir. AHB ile, PAYG örnekleri bir KCG faturalandırma modeline yeniden dağıtım olmadan dönüştürülebilir.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Linux VM 'lerinde Azure Hibrit Avantajı maliyet görselleştirmesi.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Linux için Azure Hibrit Avantajı uygunluk kapsamı
Azure Hibrit Avantajı Azure Marketi 'ndeki tüm RHEL ve SLES PAYG görüntüleri için kullanılabilir. Avantaj, Azure Marketi 'nden RHEL veya SLES BYOS görüntüleri veya özel görüntüleri için henüz kullanılamamaktadır.

Linux VM 'lerle avantajını zaten kullanıyorsanız, Azure adanmış konak örnekleri ve SQL hibrit avantajları Azure Hibrit Avantajı için uygun değildir.

## <a name="get-started"></a>başlarken

### <a name="red-hat-customers"></a>Red Hat müşterileri

RHEL için Azure Hibrit Avantajı, bu ölçütlerin her ikisini de karşılayan Red Hat müşterileri tarafından kullanılabilir:

- Azure 'da kullanıma uygun etkin veya kullanılmayan RHEL abonelikleri var
- [Red Hat bulut erişimi](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programıyla Azure 'da kullanılmak üzere Bu aboneliklerden bir veya daha fazlasını etkinleştirdi

> [!IMPORTANT]
> [Bulut erişimi](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programında doğru aboneliğin etkinleştirildiğinden emin olun.

Red Hat avantajını kullanmaya başlamak için:

1. [Red Hat bulut erişimi müşteri arabirimini](https://access.redhat.com/management/cloud)kullanarak Azure 'da uygun RHEL aboneliklerinizi etkinleştirin.

   Red Hat bulut erişimi etkinleştirme işlemi sırasında sağladığınız Azure aboneliklerinin Azure Hibrit Avantajı özelliğini kullanmasına izin verilir.
1. Mevcut ve yeni RHEL PAYG sanal makine ölçek kümelerinden herhangi birine Azure Hibrit Avantajı uygulayın. Avantajı etkinleştirmek için Azure portal veya Azure CLı kullanabilirsiniz.
1. RHEL VM 'leriniz için güncelleştirme kaynaklarını yapılandırma ve RHEL abonelik uyumluluk yönergeleri için önerilen [sonraki adımları](https://access.redhat.com/articles/5419341) izleyin.


### <a name="suse-customers"></a>SUSE müşterileri

SUSE avantajı 'nı kullanmaya başlamak için:

1. SUSE genel bulut programı ile kaydolun.
1. Yeni oluşturduğunuz veya var olan sanal makine ölçek kümesine Azure portal veya Azure CLı aracılığıyla avantajını uygulayın.
1. Avantajlarınızı alan VM 'lerinizi ayrı bir güncelleştirme kaynağıyla kaydedin.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Azure portalında avantajı etkinleştirme ve devre dışı bırakma 
Sanal makine ölçek kümesinde AHB 'yi etkinleştirme ve devre dışı bırakma için Portal deneyimi **Şu anda kullanılamıyor**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Azure CLı kullanarak avantajı etkinleştirme ve devre dışı bırakma

`az vmss update`Mevcut VM 'leri güncelleştirmek için komutunu kullanabilirsiniz. RHEL VM 'Leri için komutunu `--license-type` parametresiyle çalıştırın `RHEL_BYOS` . SLES sanal makineleri için komutunu `--license-type` parametresiyle çalıştırın `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Avantajı sağlamak için CLı örneği
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Avantajı devre dışı bırakmak için CLı örneği
Avantajı devre dışı bırakmak için, bir `--license-type` değeri kullanın `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Ölçek kümelerinde, sanal makinelerin en son ölçek kümesi modeliyle güncel hale getirilme biçimini belirleyen bir ["yükseltme ilkesi"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) bulunur. Bu nedenle, VMSS 'niz ' otomatik ' yükseltme ilkesi varsa, VM örnekleri güncelleştirilerek, AHB avantajı otomatik olarak uygulanır. VMSS 'nin zamanlanan güncelleştirmelere bağlı olarak ' sıralı ' yükseltme ilkesi varsa, AHB uygulanır.
' El Ile ' yükseltme ilkesi söz konusu olduğunda, var olan her sanal makinenin "el ile yükseltme" gerçekleştirmeniz gerekecektir.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>"El Ile yükseltme" ilkesi durumunda sanal makine ölçek kümesi örneklerini yükseltmek için CLı örneği 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Azure Hibrit Avantajı sanal makine ölçek kümesi oluşturma zamanına Uygula 
Mevcut Kullandıkça Öde sanal makine ölçek kümesine Azure Hibrit Avantajı uygulamanın yanı sıra, sanal makine ölçek kümesi oluşturma sırasında çağırabilirsiniz. Bunu yapmanın avantajları şunlardır:
- Aynı görüntüyü ve işlemi kullanarak hem PAYG hem de KCG sanal makine ölçek kümesi örnekleri sağlayabilirsiniz.
- Daha sonra, yalnızca bir KCG görüntüsü ile kullanılamayan bir şey daha ileride lisanslama modu değişikliklerine izin vermez.
- Sanal makine ölçek kümesi örnekleri, güncel ve güvenli kalmasını sağlamak için varsayılan olarak Red Hat güncelleştirme altyapısına (RHUı) bağlanır. Dağıtım sonrasında güncelleştirilmiş mekanizmayı dilediğiniz zaman değiştirebilirsiniz.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>AHB avantajı ile sanal makine ölçek kümesi oluşturmak için CLı örneği
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Sonraki adımlar
* [Azure CLı kullanarak VM oluşturma ve güncelleştirme ve Azure Hibrit Avantajı için lisans türleri (RHEL_BYOS SLES_BYOS) ekleme hakkında bilgi edinin](/cli/azure/vmss)

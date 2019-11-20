---
title: Kullanılabilirlik Alanları kullanan bir Azure ölçek kümesi oluşturma | Microsoft Docs
description: Kesintilere karşı daha fazla artıklık için Kullanılabilirlik Alanları kullanan Azure sanal makine ölçek kümeleri oluşturmayı öğrenin
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 0a31ed174c7a5986594f7c07b7ce00b1649413c8
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907973"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Kullanılabilirlik Alanları kullanan bir sanal makine ölçek kümesi oluşturma

Sanal makine ölçek kümelerinizi veri merkezi düzeyindeki hatalardan korumak için Kullanılabilirlik Alanları arasında bir ölçek kümesi oluşturabilirsiniz. Kullanılabilirlik Alanları destekleyen Azure bölgelerinin, her biri kendi bağımsız güç kaynağına, ağına ve soğutmasına sahip olan en az üç ayrı bölge vardır. Daha fazla bilgi için bkz. [kullanılabilirlik alanları genel bakış](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Kullanılabilirlik konusunda dikkat edilmesi gerekenler

Bir ölçek kümesini API sürüm *2017-12-01*itibariyle bir veya daha fazla bölgeye dağıttığınızda, "en büyük yayma" veya "statik 5 hata etki alanı yayma" ile dağıtım seçeneğiniz vardır. Maksimum yayılmaları ile, ölçek kümesi sanal makinelerinizi her bölge içinde mümkün olduğunca çok hata etki alanı arasında yayar. Bu yayma, bölge başına beş veya daha fazla hata etki alanı üzerinde olabilir. "Statik 5 hata etki alanı yayma" ile, ölçek kümesi sanal makinelerinizi bölge başına tam olarak beş hata etki alanına yayar. Ölçek kümesi, ayırma isteğini karşılamak için bölge başına beş ayrı hata etki alanı bulamazsa, istek başarısız olur.

Bu yaklaşım çoğu durumda en iyi yayılmasını sağladığından, **çoğu iş yükü için en fazla yayılmaya karşı dağıtım yapmanızı öneririz**. Çoğaltmalarının ayrı donanım yalıtımı birimlerine yayılması gerekiyorsa, Kullanılabilirlik Alanları arasında yayılmasını ve her bölgede en fazla yayılmasını öneririz.

Maksimum yayılmaya sahip olarak, sanal makinelerin kaç hata etki alanından yayıldığına bakılmaksızın yalnızca bir hata etki alanını ölçek kümesi VM örneği görünümünde ve örnek meta verilerinde görürsünüz. Her bölge içindeki yayma örtük bir şekilde yapılır.

En büyük yayılmayı kullanmak için *Platformfaultdomaincount* değerini *1*olarak ayarlayın. Statik beş hata etki alanı yayma kullanmak için *Platformfaultdomaincount* değerini *5*olarak ayarlayın. API sürüm *2017-12-01*' de *platformfaultdomaincount* , tek bölge ve çapraz bölge ölçek kümeleri için varsayılan olarak *1* ' dir. Şu anda bölgesel (ZGen olmayan) ölçek kümeleri için yalnızca statik beş hata etki alanı yayma desteklenir.

### <a name="placement-groups"></a>Yerleştirme grupları

Bir ölçek kümesi dağıttığınızda, kullanılabilirlik alanı başına tek bir [yerleştirme grubu](./virtual-machine-scale-sets-placement-groups.md) veya bölge başına birden çok dağıtım seçeneği de vardır. Bölgesel (ZGen olmayan) ölçek kümeleri için, tercih edilen bölgede tek bir yerleştirme grubu olması veya bölgede birden fazla olması gerekir. Çoğu iş yükü için, daha fazla ölçeğe izin veren birden çok yerleştirme grubu önerilir. API sürüm *2017-12-01*' de, ölçek kümeleri varsayılan olarak tek bölge ve çapraz bölge ölçek kümeleri için birden çok yerleştirme grubuna ayarlanır, ancak bölgesel (ZGen olmayan) ölçek kümeleri için varsayılan olarak tek bir yerleştirme grubu vardır.

> [!NOTE]
> Maksimum yayma kullanırsanız, birden çok yerleştirme grubu kullanmanız gerekir.

### <a name="zone-balancing"></a>Bölge Dengeleme

Son olarak, birden çok bölgede dağıtılan ölçek kümelerinde, "en iyi efor bölge bakiyesi" veya "katı bölge bakiyesi" seçme seçeneğiniz de vardır. Ölçek kümesi için her bölgede aynı VM 'ler veya +\\-1 VM varsa, ölçek kümesi "dengeli" olarak değerlendirilir. Örneğin:

- Bölge 1 ' de 2 VM, bölge 2 ' deki 3 VM ve bölge 3 ' te 3 VM içeren bir ölçek kümesi dengeli kabul edilir. Farklı bir VM sayısı olan tek bir bölge vardır ve bu yalnızca diğer bölgelerden yalnızca 1 küçüktür. 
- Bölge 1 ' de 1 VM, bölge 2 ' deki 3 VM ve bölge 3 ' te 3 VM içeren bir ölçek kümesi dengesiz olarak değerlendirilir. Bölge 1 2 ve 3 bölge 2 ' den daha az VM 'ye sahiptir.

Ölçek kümesindeki VM 'Lerin başarıyla oluşturulması olasıdır, ancak bu VM 'lerdeki uzantılar dağıtılamaz. Uzantı hatalarıyla birlikte bu VM 'Ler, bir ölçek kümesinin dengelenmesi belirlenirken hala sayılır. Örneğin, bölge 1 ' de 3 VM içeren bir ölçek kümesi, bölge 2 ' de 3 VM ve bölge 3 ' teki 3 VM 'Ler, bölge 1 ' de tüm uzantılar başarısız olsa ve 2 ve 3. bölgelerde tüm uzantılar başarılı olsa bile dengeli olarak değerlendirilir.

En iyi efor bölge bakiyesiyle, ölçek kümesi dengeyi sürdürirken ölçeği ölçeklendirmeye ve dışarı yüklemeye çalışır. Ancak, bazı nedenlerle bu mümkün değilse (örneğin, bir bölge kapalıysa, ölçek kümesi bu bölgede yeni bir sanal makine oluşturamaz), ölçek kümesi geçici dengesizliğine başarıyla ölçeklenebilmesini sağlar. Sonraki genişleme denemelerinde ölçek kümesi, ölçek kümesinin dengelenmesi için daha fazla VM gerektiren bölgelere VM 'Ler ekler. Benzer şekilde, sonraki ölçeğe karşı, ölçek kümesi, ölçek kümesinin dengelenmesi için daha az VM gerektiren bölgelerdeki VM 'Leri kaldırır. "Katı bölge bakiyesi" sayesinde, ölçek kümesi, ölçeği, büyük/dışarı dengelemeye neden olur.

En iyi efor bölge bakiyesini kullanmak için, bölge *bakiyesini* *yanlış*olarak ayarlayın. Bu ayar API sürüm *2017-12-01*' de varsayılandır. Katı bölge bakiyesini kullanmak için, *bölge bakiyesini* *doğru*olarak ayarlayın.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Tek bölge ve bölgesel olarak yedekli ölçek kümeleri

Bir sanal makine ölçek kümesi dağıttığınızda, bir bölgede veya birden çok bölgede tek bir kullanılabilirlik alanı kullanmayı seçebilirsiniz.

Tek bir bölgede bir ölçek kümesi oluşturduğunuzda, bu sanal makine örneklerinin tümünün hangi bölgede çalışacağını ve ölçek kümesinin yönetilip otomatik ölçeklenmesi gerektiğini kontrol edersiniz. Bölgesel olarak yedekli ölçek kümesi, birden fazla bölgeye yayılan tek bir ölçek kümesi oluşturmanıza olanak sağlar. VM örnekleri oluşturulurken, varsayılan olarak bölgeler arasında eşit dengelenebilir. Bölgelerden birinde kesinti oluşması durumunda, bir ölçek kümesi kapasiteyi artırmak için otomatik olarak ölçeklendirmez. En iyi yöntem, CPU veya bellek kullanımına göre otomatik ölçeklendirme kurallarını yapılandırmak olacaktır. Otomatik ölçeklendirme kuralları, ölçek kümesinin, kalan işletimsel bölgelerdeki yeni örnekleri ölçeklendirerek bir bölgedeki sanal makine örneklerinin kaybına yanıt vermesini sağlar.

Kullanılabilirlik Alanları kullanmak için, ölçek kümesinin [desteklenen bir Azure bölgesinde](../availability-zones/az-overview.md#services-support-by-region)oluşturulması gerekir. Aşağıdaki yöntemlerden biriyle Kullanılabilirlik Alanları kullanan bir ölçek kümesi oluşturabilirsiniz:

- [Azure portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager şablonları](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Azure portalı kullanma

Bir kullanılabilirlik alanı kullanan bir ölçek kümesi oluşturma işlemi Başlarken [makalesinde](quick-create-portal.md)ayrıntılı olarak aynıdır. Desteklenen bir Azure bölgesi seçtiğinizde, aşağıdaki örnekte gösterildiği gibi bir veya daha fazla kullanılabilir bölgede ölçek kümesi oluşturabilirsiniz:

![Tek bir kullanılabilirlik bölgesinde ölçek kümesi oluşturma](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Azure yük dengeleyici ve genel IP adresi gibi ölçek kümesi ve destekleyici kaynaklar, belirttiğiniz tek bölgede oluşturulur.

## <a name="use-the-azure-cli"></a>Azure CLI kullanma

Bir kullanılabilirlik alanı kullanan bir ölçek kümesi oluşturma işlemi Başlarken [makalesinde](quick-create-cli.md)ayrıntılı olarak aynıdır. Kullanılabilirlik Alanları kullanmak için, ölçek kümesini desteklenen bir Azure bölgesinde oluşturmanız gerekir.

[](/cli/azure/vmss) Parametresini az VMSS Create komutuna ekleyin ve hangi bölgenin kullanılacağını (bölge 1, 2 veya 3) belirtin. `--zones` Aşağıdaki örnek, bölge *1*' de *myScaleSet* adlı tek bölgeli bir ölçek kümesi oluşturur:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Tek bölgeli ölçek kümesi ve ağ kaynaklarının tam bir örneği için, bkz. [Bu örnek CLI betiği](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Bölgesel olarak yedekli ölçek kümesi

Bölgesel olarak yedekli ölçek kümesi oluşturmak için *Standart* SKU genel IP adresi ve yük dengeleyici kullanın. Gelişmiş artıklık için *Standart* SKU, bölgesel olarak yedekli ağ kaynakları oluşturur. Daha fazla bilgi için bkz. [Azure Load Balancer standart genel bakış](../load-balancer/load-balancer-standard-overview.md) ve [Standart Load Balancer ve kullanılabilirlik alanları](../load-balancer/load-balancer-standard-availability-zones.md).

Bölgesel olarak yedekli ölçek kümesi oluşturmak için, `--zones` parametresiyle birden çok bölge belirtin. Aşağıdaki örnek, bölge *1, 2, 3*üzerinde *myScaleSet* adlı bölgesel olarak yedekli bir ölçek kümesi oluşturur:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Belirttiğiniz bölgelerde ölçek kümesi kaynaklarının ve VM 'Lerin tümünün oluşturulması ve yapılandırılması birkaç dakika sürer. Bölgesel olarak yedekli ölçek kümesi ve ağ kaynaklarının tam bir örneği için, bkz. [Bu örnek CLI betiği](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Azure PowerShell kullanma

Kullanılabilirlik Alanları kullanmak için, ölçek kümesini desteklenen bir Azure bölgesinde oluşturmanız gerekir. [New-azvmssconfig](/powershell/module/az.compute/new-azvmssconfig) komutuna parametresiniekleyinvehangibölgeninkullanılacağını(bölge1,2veya3)`-Zone` belirtin.

Aşağıdaki örnek, *Doğu ABD 2* bölge *1*' de *myScaleSet* adlı tek bölgeli ölçek kümesi oluşturur. Sanal ağ, genel IP adresi ve yük dengeleyici için Azure ağ kaynakları otomatik olarak oluşturulur. İstendiğinde, ölçek kümesindeki sanal makine örnekleri için kendi istediğiniz yönetici kimlik bilgilerini sağlayın:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Bölgesel olarak yedekli ölçek kümesi

Bölgesel olarak yedekli ölçek kümesi oluşturmak için, `-Zone` parametresiyle birden çok bölge belirtin. Aşağıdaki örnek, *Doğu ABD 2* bölge *1, 2, 3*üzerinde *myScaleSet* adlı bölgesel olarak yedekli bir ölçek kümesi oluşturur. Sanal ağ, genel IP adresi ve yük dengeleyici için bölge yedekli Azure ağ kaynakları otomatik olarak oluşturulur. İstendiğinde, ölçek kümesindeki sanal makine örnekleri için kendi istediğiniz yönetici kimlik bilgilerini sağlayın:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarını kullanma

Kullanılabilirlik alanı kullanan bir ölçek kümesi oluşturma işlemi, [Linux](quick-create-template-linux.md) veya [Windows](quick-create-template-windows.md)için Başlarken makalesinde ayrıntılı olarak aynıdır. Kullanılabilirlik Alanları kullanmak için, ölçek kümesini desteklenen bir Azure bölgesinde oluşturmanız gerekir. `zones` Şablonunuzda Microsoft. COMPUTE/virtualMachineScaleSets kaynak türüne özelliği ekleyin ve hangi bölgenin kullanılacağını (bölge 1, 2 veya *3*) belirtin.

Aşağıdaki örnek, *Doğu ABD 2* bölge *1*' de *myScaleSet* adlı bir Linux tek bölge ölçek kümesi oluşturur:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Tek bölgeli ölçek kümesi ve ağ kaynaklarının tam bir örneği için, bkz. [Bu örnek kaynak yöneticisi şablonu](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Bölgesel olarak yedekli ölçek kümesi

Bölgesel olarak yedekli ölçek kümesi oluşturmak için, *Microsoft. COMPUTE/virtualMachineScaleSets* kaynak `zones` türü özelliğinde birden çok değer belirtin. Aşağıdaki örnek, *Doğu ABD 2* bölge *1, 2, 3*üzerinde *myScaleSet* adlı bölgesel olarak yedekli bir ölçek kümesi oluşturur:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Genel bir IP adresi veya yük dengeleyici oluşturursanız, *"SKU": {"Name" seçeneğini belirtin: Bölge yedekli ağ kaynakları oluşturmak* için "standart"} "özelliği. Ayrıca, herhangi bir trafiğe izin vermek için bir ağ güvenlik grubu ve kurallar oluşturmanız gerekir. Daha fazla bilgi için bkz. [Azure Load Balancer standart genel bakış](../load-balancer/load-balancer-standard-overview.md) ve [Standart Load Balancer ve kullanılabilirlik alanları](../load-balancer/load-balancer-standard-availability-zones.md).

Bölgesel olarak yedekli ölçek kümesi ve ağ kaynaklarının tam bir örneği için, bkz. [Bu örnek kaynak yöneticisi şablonu](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Sonraki adımlar

Artık bir kullanılabilirlik alanında ölçek kümesi oluşturduğunuza [göre, sanal makine ölçek kümelerinde uygulama dağıtmayı](tutorial-install-apps-cli.md) veya [Sanal Makine Ölçek Kümeleri Ile otomatik ölçeklendirmeyi kullanmayı](tutorial-autoscale-cli.md)öğrenebilirsiniz.

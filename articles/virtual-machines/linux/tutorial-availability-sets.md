---
title: Öğretici-Azure 'da Linux sanal makineleri için yüksek kullanılabilirlik
description: Bu öğreticide, Kullanılabilirlik Kümelerinde yüksek oranda kullanılabilir sanal makineler dağıtmak için Azure CLI kullanmayı öğreneceksiniz
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cd0366a0029ccc4816308e280ac93b7c724bb82a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034620"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Öğretici: Azure CLI ile yüksek oranda kullanılabilir sanal makineler oluşturma ve dağıtma

Bu öğreticide, Kullanılabilirlik Kümeleri adlı bir yetenek kullanarak Azure’da Sanal Makine çözümlerinizin kullanılabilirlik ve güvenilirliğini nasıl artıracağınızı öğreneceksiniz. Kullanılabilirlik kümeleri, Azure’da dağıttığınız VM’lerin birden fazla yalıtılmış donanım kümesi arasında dağıtılmasını sağlar. Böylece, Azure’da bir donanım veya yazılım hatası oluşursa yalnızca sanal makinelerinizin bir alt kümesinin etkilenmesi ve genel çözümünüzün kullanılabilir ve çalışır durumda kalması sağlanır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Kullanılabilirlik kümesi oluşturma
> * Kullanılabilirlik kümesinde sanal makine oluşturma
> * Kullanılabilir sanal makine boyutlarını denetleme

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="high-availability-in-azure-overview"></a>Azure 'da yüksek kullanılabilirlik genel bakış
Azure 'da yüksek kullanılabilirlik, birçok farklı şekilde oluşturulabilir. Kullanılabilirlik kümeleri ve kullanılabilirlik bölgeleri olan iki seçenek vardır. Kullanılabilirlik kümelerini kullanarak, VM 'niz bir veri merkezinde oluşabilecek hatalardan korunacaktır. Bu, donanım arızalarını ve Azure yazılım başarısızlıklarını içerir. Kullanılabilirlik bölgelerini kullanarak VM 'niz, paylaşılan kaynak olmadan fiziksel olarak ayrı altyapıya yerleştirilir ve bu nedenle tüm veri merkezi hatalarından korunacaktır.

Azure 'da güvenilir VM tabanlı çözümler dağıtmak istediğinizde kullanılabilirlik kümelerini veya Kullanılabilirlik Alanları kullanın.

### <a name="availability-set-overview"></a>Kullanılabilirlik kümesine genel bakış

Kullanılabilirlik Kümesi, içine yerleştirdiğiniz sanal makine kaynaklarının, Azure veri merkezinde dağıtıldığında birbirinden yalıtılmasını sağlamak için Azure’da kullanabileceğiniz bir mantıksal gruplama yeteneğidir. Azure, bir Kullanılabilirlik Kümesi içine yerleştirdiğiniz sanal makinelerin birden fazla fiziksel sunucuda, bilgi işlem rafında, depolama biriminde ve ağ anahtarında çalışmasını sağlar. Bir donanım veya Azure yazılım hatası oluşursa, yalnızca sanal makinelerinizin bir alt kümesi etkilenir ve genel uygulama güncel kalıp müşterilerinizin kullanımına sunulmaya devam eder. Kullanılabilirlik Kümesi, güvenilir bulut çözümleri oluşturmak istediğinizde gerekli olan temel bir yetenektir.

Dört adet ön uç web sunucusuna sahip olabileceğiniz ve bir veritabanını barındıran iki adet arka uç sanal makine kullandığınız tipik bir sanal makine tabanlı çözümü düşünelim. Azure ile, sanal makinelerinizi dağıtmadan önce iki kullanılabilirlik kümesi tanımlamak istersiniz: bir kullanılabilirlik kümesi, "web" katmanı için ve bir kullanılabilirlik kümesi de "veritabanı" katmanı için. Yeni bir sanal makine oluşturduğunuzda, az vm create komutuna parametre olarak kullanılabilirlik kümesini belirtebilirsiniz ve Azure da otomatik olarak kullanılabilirlik kümesi içinde oluşturduğunuz sanal makinelerin birden fazla fiziksel donanım kaynağı arasında yalıtılmasını sağlar. Web Sunucusu veya Veritabanı Sunucusu Sanal Makinelerinizden birinin çalıştığı fiziksel donanımda sorun varsa, Web Sunucunuzun ve Veritabanı Sanal Makinelerinizin diğer örneklerinin farklı donanımlarda bulunması nedeniyle çalışmaya devam edeceğini bilirsiniz.

### <a name="availability-zone-overview"></a>Kullanılabilirlik bölgesine genel bakış

Kullanılabilirlik Alanları, uygulamalarınızı ve verilerinizi veri merkezi hatalarından koruyan yüksek kullanılabilirliğe sahip bir tekliftir. Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır. Bir bölgedeki Kullanılabilirlik Alanları fiziksel ayrımı, uygulamaları ve verileri veri merkezi hatalarından korur. Bölgesel olarak yedekli hizmetler, uygulamalarınızı ve verilerinizi Kullanılabilirlik Alanları arasında çoğaltarak hata noktalarından koruyun. Azure, Kullanılabilirlik Alanları, sektörde en iyi% 99,99 VM çalışma süresi SLA 'Sı sunmaktadır.

Kullanılabilirlik kümelerine benzer şekilde, dört adet ön uç Web sunucusuna sahip olabileceğiniz ve bir veritabanını barındıran iki arka uç VM 'yi kullanabileceğiniz tipik bir VM tabanlı çözümü ele alalım. Kullanılabilirlik kümelerine benzer şekilde, sanal makinelerinizi iki ayrı kullanılabilirlik bölgesinde da dağıtmak isteyeceksiniz: "Web" katmanı için bir kullanılabilirlik bölgesi ve "veritabanı" katmanı için bir kullanılabilirlik alanı. Yeni bir VM oluşturduğunuzda ve az VM Create komutuna bir parametre olarak kullanılabilirlik alanını belirttiğinizde, Azure otomatik olarak oluşturduğunuz VM 'Lerin tamamen farklı kullanılabilirlik bölgelerinde yalıtılmasını sağlar. Web sunucunuz veya veritabanı sunucusu VM 'lerinden birinin üzerinde çalıştığı tüm veri merkezinde bir sorun varsa, Web sunucunuzun ve veritabanı sanal makinelerinizin diğer örneklerinin tamamen ayrı veri merkezlerinde çalıştığı için çalışmaya devam ettiğinden emin olmalısınız.

## <a name="create-an-availability-set"></a>Kullanılabilirlik kümesi oluşturma

[az vm availability-set create](/cli/azure/vm/availability-set) komutunu kullanarak bir kullanılabilirlik kümesi oluşturabilirsiniz. Bu örnekte, *myResourceGroupAvailability* kaynak grubundaki *myAvailabilitySet* adlı kullanılabilirlik kümesi için güncelleştirme ve hata etki alanları sayısı *2* olarak ayarlanmıştır.

İlk olarak, [az group create](/cli/azure/group#az-group-create) ile bir kaynak grubu oluşturun ve sonra kullanılabilirlik kümesini oluşturun:

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

Ek VM’ler sonradan VM boyutlarının donanımda bulunduğu kullanılabilirlik kümesine eklenebilir. Kullanılabilirlik kümesi için donanım kümesindeki tüm kullanılabilir boyutları listelemek için [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) komutunu kullanın:

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

* Kullanılabilirlik bölgeleri hakkında daha fazla bilgi edinmek için [kullanılabilirlik alanları belgelerini](../../availability-zones/az-overview.md)ziyaret edin.
* Hem kullanılabilirlik kümeleri hem de kullanılabilirlik bölgeleri hakkında daha fazla belgeye de [ulaşabilirsiniz.](./manage-availability.md)
* Kullanılabilirlik bölgelerini denemek için [Azure CLI ile bir kullanılabilirlik alanında Linux sanal makinesi oluşturma](./create-cli-availability-zone.md) sayfasını ziyaret edin

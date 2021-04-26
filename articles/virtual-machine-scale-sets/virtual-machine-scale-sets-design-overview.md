---
title: Azure sanal makine ölçek kümeleri için tasarım konuları
description: Azure sanal makine ölçek kümeleriniz için tasarım konuları hakkında bilgi edinin. Ölçek Kümeleri özelliklerini VM özellikleriyle karşılaştırın.
keywords: Linux sanal makinesi, sanal makine ölçek kümeleri
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 04446243ed827cca4972a4b606c4930e74a2c704
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774975"
---
# <a name="design-considerations-for-scale-sets"></a>Ölçek Kümeleri Için tasarım konuları
Bu makalede, sanal makine ölçek kümelerine ilişkin tasarım konuları ele alınmaktadır. Sanal makine ölçek kümelerinin ne olduğu hakkında bilgi için [sanal makine ölçek kümelerine genel bakış](./overview.md)bölümüne bakın.

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Sanal makineler yerine ölçek kümeleri ne zaman kullanılır?
Genellikle, ölçek kümeleri, bir makine kümesinin benzer yapılandırmasına sahip olduğu yüksek oranda kullanılabilir altyapıyı dağıtmak için yararlıdır. Ancak, bazı özellikler yalnızca sanal makinelerde kullanılabilir durumdayken ölçek kümelerinde kullanılabilir. Her teknolojinin ne zaman kullanılacağı konusunda bilinçli bir karar vermek için, öncelikle VM 'Ler değil, ölçek kümelerinde kullanılabilen yaygın olarak kullanılan özelliklerden bazılarını göz atalım:

### <a name="scale-set-specific-features"></a>Ölçek kümesine özgü özellikler

- Ölçek kümesi yapılandırmasını belirttikten sonra, paralel olarak daha fazla VM dağıtmak için *Kapasite* özelliğini güncelleştirebilirsiniz. Bu işlem, birçok ayrı VM 'yi paralel olarak dağıtmak için bir komut dosyası yazmadan daha iyidir.
- Azure otomatik [ölçeklendirme özelliğini kullanarak bir ölçek kümesini otomatik olarak ölçeklendirin](./virtual-machine-scale-sets-autoscale-overview.md) , tek tek VM 'leri kullanamazsınız.
- [Ölçek kümesi sanal makinelerini yeniden görüntü](/rest/api/compute/virtualmachinescalesets/reimage) oluşturabilir, ancak [tek tek sanal](/rest/api/compute/virtualmachines)makinelere sahip olabilirsiniz.
- Daha fazla güvenilirlik ve daha hızlı dağıtım süreleri için ölçek kümesi sanal makinelerini [fazla temin](#overprovisioning) edebilirsiniz. Bu eylemi gerçekleştirmek için özel kod yazmadığınız müddetçe tek tek VM 'Leri fazla sağlayamazsınız.
- Ölçek kümesindeki VM 'lerde yükseltmeleri kullanıma almayı kolaylaştırmak için bir [yükseltme ilkesi](./virtual-machine-scale-sets-upgrade-scale-set.md) belirtebilirsiniz. Ayrı VM 'Ler ile güncelleştirmeleri kendiniz ayarlamalısınız.

### <a name="vm-specific-features"></a>VM 'ye özgü özellikler

Bazı özellikler Şu anda yalnızca VM 'lerde kullanılabilir:

- Bir ölçek kümesindeki bir VM 'den değil, tek bir VM 'den görüntü yakalayabilirsiniz.
- Tek bir VM 'yi yerel disklerden yönetilen disklere geçirebilirsiniz, ancak bir ölçek kümesindeki sanal makine örneklerini geçiremezsiniz.
- Tek bir VM sanal ağ arabirim kartına (NIC 'ler) IPv6 genel IP adresleri atayabilir, ancak bunu bir ölçek kümesindeki sanal makine örnekleri için gerçekleştirilemez. IPv6 genel IP adreslerini, tek tek VM 'lerin veya ölçek kümesi VM 'lerinin önünde yük dengeleyicileri atayabilirsiniz.

## <a name="storage"></a>Depolama

### <a name="scale-sets-with-azure-managed-disks"></a>Azure yönetilen diskler ile ölçek kümeleri
Ölçek Kümeleri, geleneksel Azure depolama hesapları yerine [Azure yönetilen disklerle](../virtual-machines/managed-disks-overview.md) oluşturulabilir. Yönetilen diskler aşağıdaki avantajları sağlar:
- Ölçek kümesi VM 'Leri için Azure Storage hesapları kümesini önceden oluşturmanız gerekmez.
- Ölçek kümesindeki VM 'Ler için [bağlı veri disklerini](virtual-machine-scale-sets-attached-disks.md) tanımlayabilirsiniz.
- Ölçek Kümeleri, [bir küme içinde en fazla 1.000 VM 'yi destekleyecek](virtual-machine-scale-sets-placement-groups.md)şekilde yapılandırılabilir. 

Mevcut bir şablonunuz varsa, [şablonu yönetilen diskleri kullanacak şekilde de güncelleştirebilirsiniz](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Kullanıcı tarafından yönetilen depolama
Azure yönetilen diskler ile tanımlanmayan bir ölçek kümesi, küme içindeki VM 'lerin işletim sistemi disklerini depolamak için Kullanıcı tarafından oluşturulan depolama hesaplarına bağımlıdır. Maksimum GÇ elde etmek ve ayrıca _aşırı sağlama_ özelliğinden yararlanmak için depolama hesabı başına 20 VM veya daha az bir oran kullanılması önerilir (aşağıya bakın). Ayrıca, depolama hesabı adlarının başlangıç karakterlerini alfabede yaymış olmanız önerilir. Bunun yapılması, farklı iç sistemlerde yükün yüklenmesine yardımcı olur. 


## <a name="overprovisioning"></a>Fazla sağlama
Ölçek Kümeleri Şu anda varsayılan "fazla sağlama" sanal makinelerinize sahiptir. Aşırı sağlama açık olduğunda, ölçek kümesi, sizin isteenden daha fazla VM 'yi alır, ardından istenen VM sayısı başarıyla sağlandığında ek VM 'leri siler. Aşırı sağlama, sağlama başarı oranlarını artırır ve dağıtım süresini azaltır. Ek VM 'Ler için faturalandırılmaz ve kota sınırlarınıza doğru sayılmaz.

Aşırı sağlama, sağlama başarı oranlarını iyileştirirken, ek VM 'Leri işlemek ve sonra geri dönmek için tasarlanmamış bir uygulama için kafa karıştırıcı davranışa neden olabilir. Fazla sağlamayı devre dışı bırakmak için şablonunuzda aşağıdaki dizeye sahip olduğunuzdan emin olun: `"overprovision": "false"` . Daha fazla ayrıntı, [Ölçek kümesi REST API belgelerinde](/rest/api/virtualmachinescalesets/create-or-update-a-set)bulunabilir.

Ölçek ayarlandıysa Kullanıcı tarafından yönetilen depolama kullanılıyorsa ve aşırı sağlamayı kapatırsanız, depolama hesabı başına 20 ' den fazla VM 'niz olabilir, ancak GÇ performans nedenleriyle 40 ' ün üzerine gitmeniz önerilmez. 

## <a name="limits"></a>Sınırlar
Market görüntüsünde oluşturulan bir ölçek kümesi (Platform görüntüsü olarak da bilinir) ve Azure yönetilen diskleri kullanmak için yapılandırılmışsa 1.000 sanal makine kapasitesi desteklenir. Ölçek kümesini 100 ' den fazla VM 'yi destekleyecek şekilde yapılandırırsanız, tüm senaryolar aynı şekilde çalışmaz (örneğin, Yük Dengeleme). Daha fazla bilgi için bkz. [büyük sanal makine ölçek kümeleriyle çalışma](virtual-machine-scale-sets-placement-groups.md). 

Kullanıcı tarafından yönetilen depolama hesaplarıyla yapılandırılmış bir ölçek kümesi şu anda 100 VM ile sınırlıdır (ve bu ölçek için 5 depolama hesabı önerilir).

Özel bir görüntüde oluşturulan ölçek kümesi (sizin tarafınızdan oluşturulan), Azure yönetilen disklerle yapılandırıldığında 600 sanal makineye kadar kapasiteye sahip olabilir. Ölçek kümesi Kullanıcı tarafından yönetilen depolama hesaplarıyla yapılandırıldıysa, tek bir depolama hesabında tüm işletim sistemi diski VHD 'lerini oluşturması gerekir. Sonuç olarak, özel bir görüntü ve Kullanıcı tarafından yönetilen depolama üzerinde oluşturulan bir ölçek kümesindeki en fazla önerilen sanal makine sayısı 20 ' dir. Aşırı sağlamayı kapatırsanız 40 'e kadar gidebilirsiniz.

Bu limitlerin izin verenden daha fazla VM için, [Bu şablonda](https://azure.microsoft.com/resources/templates/301-custom-images-at-scale/)gösterildiği gibi birden çok ölçek kümesi dağıtmanız gerekir.

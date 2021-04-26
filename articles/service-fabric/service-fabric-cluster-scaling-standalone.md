---
title: Azure Service Fabric tek başına kümesi ölçeklendirme
description: Service Fabric tek başına kümeleri ve yukarı veya aşağı ölçekleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843040"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Tek başına kümeler Service Fabric ölçeklendirme
Service Fabric küme, mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Bir kümenin parçası olan makine veya VM, düğüm olarak adlandırılır. Kümeler potansiyel binlerce düğüm içerebilir. Service Fabric kümesi oluşturduktan sonra, kümeyi yatay olarak ölçeklendirebilirsiniz (düğüm sayısını değiştirebilir) veya dikey (düğümlerin kaynaklarını değiştirebilirsiniz).  Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz.  Küme ölçeklenirken uygulamalarınız da otomatik olarak ölçeklendirilir.

Küme neden ölçeklendirmeliyim? Uygulama taleplerine zaman içinde değişiklik yapılır.  Daha fazla uygulama iş yükünü veya ağ trafiğini karşılamak için küme kaynaklarını artırmanız veya talep düştüğünde küme kaynaklarını azaltmanız gerekebilir.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Ölçek Genişletme ve genişletme veya yatay ölçeklendirme
Kümedeki düğümlerin sayısını değiştirir.  Yeni düğümler kümeye katılıyorsa, [küme kaynak yöneticisi](service-fabric-cluster-resource-manager-introduction.md) Hizmetleri bunlara, var olan düğümlerde yükü azaltacak şekilde gider.  Kümenin kaynakları verimli bir şekilde kullanılmıyorsa düğüm sayısını da azaltabilirsiniz.  Düğüm kümeden ayrıldığında, hizmetler bu düğümleri kapatır ve kalan düğümlerde yük artar.  Azure 'da çalışan bir kümedeki düğümlerin sayısını azaltmak, bu VM 'lerde iş yükünü değil kullandığınız VM sayısı için ödeme yaptığınız için tasarruf etmenizi sağlayabilir.  

- Avantajlar: sonsuz ölçek, teorik olarak.  Uygulamanız ölçeklenebilirlik için tasarlandıysa daha fazla düğüm ekleyerek sınırsız büyümeyi etkinleştirebilirsiniz.  Bulut ortamlarındaki araçlar, düğüm eklemeyi veya kaldırmayı kolaylaştırır, böylece kapasiteyi kolayca ayarlayabilir ve yalnızca kullandığınız kaynaklar için ödeme yaparsınız.  
- Dezavantajları: uygulamalar [ölçeklenebilirlik için tasarlanmalıdır](service-fabric-concepts-scalability.md).  Uygulama veritabanları ve kalıcılık, daha fazla mimari çalışmanın de ölçeğini gerektirebilir.  Service Fabric durum bilgisi olan hizmetlerde [güvenilir koleksiyonlar](service-fabric-reliable-services-reliable-collections.md) , uygulama verilerinizi ölçeklendirmenizi çok daha kolay hale getirir.

Tek başına kümeler, şirket içinde veya istediğiniz bulut sağlayıcısında Service Fabric kümesi dağıtmanıza olanak tanır.  Düğüm türleri, dağıtımınıza bağlı olarak fiziksel makinelerden veya sanal makinelerden oluşur. Azure 'da çalışan kümelerle karşılaştırıldığında, tek başına kümeyi ölçekleme işlemi biraz daha karmaşıktır.  Kümedeki düğümlerin sayısını el ile değiştirmeniz ve sonra bir küme yapılandırma yükseltmesi çalıştırmanız gerekir.

Düğümlerin kaldırılması, birden çok yükseltme başlatabilir. Bazı düğümler `IsSeedNode=”true”` etiketiyle işaretlenir ve [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest)kullanılarak küme bildirimi sorgulanarak belirlenebilir. Çekirdek düğümlerin bu tür senaryolarda taşınması gerektiğinden, bu tür düğümlerin kaldırılması diğerlerinden daha uzun sürebilir. Küme, en az üç birincil düğüm türü düğümü korumalıdır.

> [!WARNING]
> Düğüm sayısını kümenin [güvenilirlik katmanının küme boyutunun](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) altında düşürmenizi öneririz. Bu, Service Fabric sistem hizmetlerinin küme genelinde çoğaltılmasına engel olur ve kümeyi kaldırır veya büyük olasılıkla yok eder.
>

Tek başına kümeyi ölçeklendirirken aşağıdaki yönergeleri göz önünde bulundurun:
- Birincil düğümlerin yerine geçen bir düğüm, ve sonra toplu işlemlere eklemek yerine bir düğüm daha yapılmalıdır.
- Düğüm türünü kaldırmadan önce düğüm türüne başvuran düğüm olup olmadığını kontrol edin. Karşılık gelen düğüm türünü kaldırmadan önce bu düğümleri kaldırın. Karşılık gelen tüm düğümler kaldırıldıktan sonra, küme yapılandırmasından NodeType 'yi kaldırabilir ve [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)kullanarak bir yapılandırma yükseltmesi başlatabilirsiniz.

Daha fazla bilgi için bkz. [tek başına kümeyi ölçekleme](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Ölçeği artırma ve azaltma ya da dikey ölçekleme 
Kümedeki düğümlerin kaynaklarını (CPU, bellek veya depolama) değiştirir.
- Avantajlar: yazılım ve uygulama mimarisi aynı kalır.
- Dezavantajları: bağımsız düğümlerde kaynakları ne kadar artırabileceğiniz için bir sınır olduğundan, sınırlı ölçek. Kapalı kalma süresi, kaynak eklemek veya kaldırmak için fiziksel veya sanal makineleri çevrimdışına almanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
* [Uygulama ölçeklenebilirliği](service-fabric-concepts-scalability.md)hakkında bilgi edinin.
* [Bir Azure kümesini içinde veya dışarı ölçeklendirin](service-fabric-tutorial-scale-cluster.md).
* Akıcı Azure işlem SDK 'sını kullanarak [bir Azure kümesini programlı bir şekilde ölçeklendirin](service-fabric-cluster-programmatic-scaling.md) .
* [Tek başına kümeyi içinde veya dışarı ölçeklendirin](service-fabric-cluster-windows-server-add-remove-nodes.md).


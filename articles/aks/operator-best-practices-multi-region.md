---
title: AKS iş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar
description: Azure Kubernetes Service (AKS) ' de yüksek kullanılabilirlik ve olağanüstü durum kurtarma için hazırlık olanağı sunan bir küme işlecinin en iyi uygulamalarını öğrenin.
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105094"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de iş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, uygulama çalışma süresi önemli hale gelir. Varsayılan olarak, AKS, [sanal makine ölçek kümesinde (VMSS)](../virtual-machine-scale-sets/overview.md)birden çok düğüm kullanarak yüksek kullanılabilirlik sağlar. Ancak bu birden çok düğüm, sisteminizi bir bölge hatasından korumaz. Çalışma alanınızı en üst düzeye çıkarmak için iş sürekliliği ve olağanüstü durum kurtarma için hazırlanma konusuna planlayın.

Bu makalede, AKS 'de iş sürekliliği ve olağanüstü durum kurtarmayı planlamaya odaklanılır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Birden çok bölgede AKS kümelerini planlayın.
> * Azure Traffic Manager kullanarak trafiği birden çok küme arasında yönlendirin.
> * Kapsayıcı görüntü kayıt defterleri için coğrafi çoğaltma kullanın.
> * Birden çok küme genelinde uygulama durumunu planlayın.
> * Depolama alanını birden çok bölgede çoğaltın.

## <a name="plan-for-multiregion-deployment"></a>Çok bölgeli dağıtımı için plan

> **En iyi yöntem**
>
> Birden çok AKS kümesi dağıttığınızda, AKS 'in kullanılabildiği bölgeleri seçin. Eşleştirilmiş bölgeleri kullanın.

AKS kümesi tek bir bölgeye dağıtılır. Sisteminizi bölge arızasına karşı korumak için, uygulamanızı farklı bölgelerde birden çok AKS kümesine dağıtın. AKS kümenizin nereye dağıtılacağını planlarken şunları göz önünde bulundurun:

* [**AKS bölgesi kullanılabilirliği**](./quotas-skus-regions.md#region-availability)
    * Kullanıcılarınıza yakın bölgeler ' i seçin. 
    * AKS sürekli yeni bölgelere genişletilir.
* [**Azure eşlenmiş bölgeler**](../best-practices-availability-paired-regions.md)
    * Coğrafi bölgenizde, birlikte eşleştirilmiş iki bölge seçin.
    * Eşleştirilmiş bölgeler platform güncelleştirmelerini koordine edin ve gerektiğinde kurtarma çalışmalarını önceliklendirin.
* **Hizmet kullanılabilirliği**
    * Eşleştirilmiş bölgelerin etkin/sıcak, sık/sıcak veya sık/soğuk olması gerektiğine karar verin.
    * Her iki bölgeyi de aynı anda çalıştırmak istiyor musunuz, tek bir bölge, trafiğe hizmet vermeye *başlamaya hazırlanıyor* mi? Veya
    * Trafiğe sunmaya hazırlanmak için bir bölgeye zaman vermek istiyor musunuz?

AKS bölgesi kullanılabilirliği ve eşleştirilmiş bölgeler, bir eklem noktadır. AKS kümelerinizi, bölge olağanüstü durum kurtarmayı birlikte yönetmek için tasarlanan eşleştirilmiş bölgelere dağıtın. Örneğin, AKS Doğu ABD ve Batı ABD kullanılabilir. Bu bölgeler eşlenilmiştir. AKS BC/DR stratejisi oluştururken bu iki bölgeyi seçin.

Uygulamanızı dağıtırken, bu çoklu AKS kümelerine dağıtmak için CI/CD işlem hattınızla başka bir adım ekleyin. Dağıtım işlem hatlarınızı güncelleştirmek, uygulamaların bölgelerinizden ve AKS kümelerinden yalnızca birine dağıtılmasını engeller. Bu senaryoda, bir ikincil bölgeye yöneltilen müşteri trafiği en son kod güncelleştirmelerini almaz.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Trafiği yönlendirmek için Azure Traffic Manager kullanma

> **En iyi yöntem**
>
> Azure Traffic Manager sizi en yakın AKS kümenize ve uygulama örneğine yönlendirebilir. En iyi performans ve artıklık için, tüm uygulama trafiğini AKS kümenize geçmeden önce Traffic Manager aracılığıyla doğrudan yönlendirin.

Farklı bölgelerde birden fazla AKS kümeniz varsa, her kümede çalışan uygulamalara trafik akışını denetlemek için Traffic Manager kullanın. [Azure Traffic Manager](../traffic-manager/index.yml) , bölgeler arasında ağ trafiği DAĞıTABILECEK DNS tabanlı bir trafik yük dengeleyicidir. Kullanıcıları küme yanıt süresine göre veya Coğrafya temelinde yönlendirmek için Traffic Manager kullanın.

![Traffic Manager ile AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Tek bir AKS kümeniz varsa, genellikle belirli bir uygulamanın hizmet IP 'si veya DNS adına bağlanırsınız. Birden çok küme dağıtımında, her bir AKS kümesindeki hizmetlere işaret eden bir Traffic Manager DNS adına bağlanmalısınız. Bu hizmetleri Traffic Manager uç noktaları kullanarak tanımlayın. Her uç nokta *hizmet yük DENGELEYICI IP*'dir. Ağ trafiğini bir bölgedeki Traffic Manager uç noktasından farklı bir bölgedeki uç noktaya yönlendirmek için bu yapılandırmayı kullanın.

![Traffic Manager aracılığıyla coğrafi yönlendirme](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager DNS aramalarını gerçekleştirir ve en uygun uç noktayı döndürür. İç içe profiller birincil bir konuma öncelik verebilir. Örneğin, en yakın coğrafi bölgelere bağlanmanız gerekir. Bu bölgede bir sorun varsa Traffic Manager bir ikincil bölgeye yönlendirir. Bu yaklaşım, en yakın coğrafi bölgeniz kullanılamaz olsa bile bir uygulama örneğine bağlanmanızı sağlar.

Uç noktaları ve yönlendirmeyi ayarlama hakkında daha fazla bilgi için, bkz. [Traffic Manager kullanarak coğrafi trafik yönlendirme yöntemini yapılandırma](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Azure ön kapı hizmeti ile uygulama yönlendirme

Bölünmüş TCP tabanlı her noktaya yayın protokolünü kullanarak, [Azure ön kapı hizmeti](../frontdoor/front-door-overview.md) son kullanıcılarınızı en yakın ön kapı açısına (varlık noktası) en iyi şekilde bağlar. Azure ön kapı hizmeti 'nin daha fazla özelliği:
* TLS sonlandırma
* Özel etki alanı
* Web uygulaması güvenlik duvarı
* URL Yeniden Yazma
* Oturum benzeşimi 

En uygun çözümü anlamak için uygulama trafiğinizin ihtiyaçlarını gözden geçirin.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Genel sanal ağ eşlemesi ile Interconnect bölgeleri

Kümeler arasında iletişimi etkinleştirmek için sanal [ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md) aracılığıyla her iki sanal ağı birbirine bağlayın. Sanal ağ eşleme, farklı coğrafi bölgelerde bile Microsoft 'un omurga ağı arasında yüksek bant genişliğine sahip sanal ağları birbirine bağlar.

AKS kümelerini çalıştıran sanal ağları eşlemeden önce AKS kümenizdeki standart Load Balancer kullanın. Bu önkoşul, Kubernetes hizmetlerini sanal ağ eşlemesi üzerinde erişilebilir hale getirir.

## <a name="enable-geo-replication-for-container-images"></a>Kapsayıcı görüntüleri için Coğrafi çoğaltmayı etkinleştirme

> **En iyi yöntem**
> 
> Kapsayıcı görüntülerinizi Azure Container Registry ve coğrafi olarak kayıt defterinde her bir AKS bölgesine depolayın.

Uygulamalarınızı AKS 'de dağıtmak ve çalıştırmak için kapsayıcı görüntülerini depolamak ve çekmek için bir yol gerekir. Container Registry, AKS ile tümleşir, bu nedenle kapsayıcı görüntülerinizi veya Held grafiklerini güvenli bir şekilde depolayabilirler. Container Registry, görüntülerinizi dünyanın dört bir yanındaki Azure bölgelerine otomatik olarak çoğaltmak için çok kaynaklı Coğrafi çoğaltmayı destekler. 

Performansı ve kullanılabilirliği artırmak için:
1. AKS kümeniz olan her bölgede bir kayıt defteri oluşturmak için coğrafi çoğaltma Container Registry kullanın. 
1. Her bir AKS kümesi daha sonra aynı bölgedeki yerel kapsayıcı kayıt defterinden kapsayıcı görüntülerini çeker:

![Kapsayıcı görüntüleri için coğrafi çoğaltma Container Registry](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Aynı bölgedeki görüntüleri çekmek için coğrafi çoğaltma Container Registry kullandığınızda sonuçlar şunlardır:

* **Daha hızlı**: aynı Azure bölgesindeki yüksek hızlı, düşük gecikmeli ağ bağlantılarından görüntü çekme.
* **Daha güvenilir**: bir bölge kullanılamıyorsa aks kümeniz görüntüleri kullanılabilir bir kapsayıcı kayıt defterinden çeker.
* **Kimaper**: veri merkezleri arasında ağ çıkış ücreti alınmaz.

Coğrafi çoğaltma, *Premium* SKU kapsayıcısı kayıt defteri özelliğidir. Coğrafi çoğaltmanın nasıl yapılandırılacağı hakkında bilgi için bkz. [Container Registry coğrafi çoğaltma](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Hizmet durumunu kapsayıcıların içinden kaldır

> **En iyi yöntem**
> 
> Hizmet durumunu kapsayıcının içinde saklamaktan kaçının. Bunun yerine, çok bölgeli çoğaltmayı destekleyen bir hizmet olarak Azure platformu (PaaS) kullanın.

*Hizmet durumu* , bir hizmetin çalışması için gereken bellek içi veya disk üzerindeki verilere başvurur. Durum, hizmetin okuduğu ve yazdığı veri yapılarını ve üye değişkenlerini içerir. Hizmetin nasıl mimari olduğuna bağlı olarak, durum, diskte depolanan dosyaları veya diğer kaynakları da içerebilir. Örneğin, durum, bir veritabanının veri ve işlem günlüklerini depolamak için kullandığı dosyaları içerebilir.

Durum externalized veya durumu işleyen kodla birlikte bulunabilir. Genellikle, bir veritabanı veya ağ üzerinden çalışan farklı makinelerde çalışan veya aynı makinede işlem dışında çalışan başka bir veri deposu kullanarak externalize.

Kapsayıcılar ve mikro hizmetler, içinde çalışan işlemlerin durumu korumaları durumunda en esnektir. Uygulamalar neredeyse her zaman bir durum içerdiğinden, bir PaaS çözümü kullanın, örneğin:
* Azure Cosmos DB
* PostgreSQL için Azure Veritabanı
* MySQL için Azure Veritabanı
* Azure SQL Veritabanı

Taşınabilir uygulamalar oluşturmak için aşağıdaki yönergelere bakın:

* [12 öğeli uygulama yöntemi](https://12factor.net/)
* [Bir Web uygulamasını birden çok Azure bölgesinde çalıştırma](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Depolama geçiş planı oluşturma

> **En iyi yöntem**
>
> Azure Storage kullanıyorsanız, depolama alanınızı birincil bölgeden yedekleme bölgesine nasıl geçirebileceğiniz hazırlayın ve test edin.

Uygulamalarınız, verileri için Azure Storage kullanıyor olabilirler. Bu durumda, uygulamalarınız farklı bölgelerde birden çok AKS kümesi arasında yayılır. Depolamayı eşitlenmiş tutmanız gerekir. Depolama çoğaltması için iki yaygın yol aşağıda verilmiştir:

* Altyapı tabanlı zaman uyumsuz çoğaltma
* Uygulama tabanlı zaman uyumsuz çoğaltma

### <a name="infrastructure-based-asynchronous-replication"></a>Altyapı tabanlı zaman uyumsuz çoğaltma

Bir pod silindikten sonra bile uygulamalarınız kalıcı depolama gerekebilir. Kubernetes 'de, veri depolamayı kalıcı hale getirmek için kalıcı birimleri kullanabilirsiniz. Kalıcı birimler bir düğüm sanal makinesine bağlanır ve sonra da pods 'leri kullanıma sunulur. Kalıcı birimler, düğüm aynı kümedeki farklı bir düğüme taşınsa bile, Pod 'ler izler.

Kullandığınız çoğaltma stratejisi, depolama çözümünüze bağlıdır. Aşağıdaki genel depolama çözümleri olağanüstü durum kurtarma ve çoğaltma hakkında kendi kılavuzunu sağlar:
* [Gluster Storage](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

Genellikle, uygulamaların verilerini yazdıkları ortak bir depolama noktası sağlarsınız. Bu veriler daha sonra bölgeler arasında çoğaltılır ve yerel olarak erişilir.

![Altyapı tabanlı zaman uyumsuz çoğaltma](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure yönetilen diskler kullanıyorsanız çoğaltma ve olağanüstü durum kurtarmayı işlemek için [Azure 'Da Velero][velero] ve [kasten][kasten] kullanabilirsiniz. Bu seçenekler, yerel olarak, Kubernetes tarafından desteklenmeyen çözümlerin yedeğini alınır.

### <a name="application-based-asynchronous-replication"></a>Uygulama tabanlı zaman uyumsuz çoğaltma

Kubernetes Şu anda uygulama tabanlı zaman uyumsuz çoğaltma için yerel uygulama sunmamaktadır. Kapsayıcılar ve Kubernetes gevşek olarak bağlanmış olduğundan, geleneksel uygulama veya dil yaklaşımının çalışması gerekir. Genellikle, uygulamalar depolama isteklerini çoğaltarak her kümenin temel alınan veri depolamaya yazılır.

![Uygulama tabanlı zaman uyumsuz çoğaltma](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümelerine yönelik iş sürekliliği ve olağanüstü durum kurtarma konuları ele alınmaktadır. AKS 'deki küme işlemleri hakkında daha fazla bilgi için en iyi uygulamalar hakkında aşağıdaki makalelere bakın:

* [Çok kiracılı ve küme yalıtımı][aks-best-practices-cluster-isolation]
* [Temel Kubernetes Zamanlayıcı özellikleri][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/
---
title: Azure Kubernetes Service (AKS) üzerinde yüksek kullanılabilirlik ve olağanüstü durum kurtarma
description: Azure Kubernetes Service (AKS) ' de yüksek kullanılabilirlik ve olağanüstü durum kurtarma için hazırlık olanağı sunan bir küme işlecinin en iyi uygulamalarını öğrenin.
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.openlocfilehash: 5a0a7e59e71e51a109af0f89cbb7ba580b2b97e6
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967197"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de iş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, uygulama çalışma süresi önemli hale gelir. AKS, bir kullanılabilirlik kümesinde birden çok düğüm kullanarak yüksek kullanılabilirlik sağlar. Ancak bu birden çok düğüm, sisteminizi bir bölge hatasından korumaz. Çalışma alanınızı en üst düzeye çıkarmak için iş sürekliliği ve olağanüstü durum kurtarma için hazırlanma konusuna planlayın.

Bu makalede, AKS 'de iş sürekliliği ve olağanüstü durum kurtarmayı planlamaya odaklanılır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Birden çok bölgede AKS kümelerini planlayın.
> * Azure Traffic Manager kullanarak trafiği birden çok küme arasında yönlendirin.
> * Kapsayıcı görüntü kayıt defterleri için coğrafi çoğaltma kullanın.
> * Birden çok küme genelinde uygulama durumunu planlayın.
> * Depolama alanını birden çok bölgede çoğaltın.

## <a name="plan-for-multiregion-deployment"></a>Çok bölgeli dağıtımı için plan

**En iyi uygulama**: Birden çok AKS kümesi dağıttığınızda, AKS 'in kullanılabildiği bölgeleri seçin ve eşleştirilmiş bölgeleri kullanın.

AKS kümesi tek bir bölgeye dağıtılır. Sisteminizi bölge arızasına karşı korumak için, uygulamanızı farklı bölgelerde birden çok AKS kümesine dağıtın. AKS kümenizi nereye dağıtacağınızı planlarken şunları göz önünde bulundurun:

* [**Aks bölgesi kullanılabilirliği**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Kullanıcılarınıza yakın bölgeler ' i seçin. AKS sürekli yeni bölgelere genişletilir.
* [**Azure eşlenmiş bölgeler**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Coğrafi bölgenizde, birbirleriyle eşleştirilmiş iki bölge seçin. Eşleştirilmiş bölgeler platform güncelleştirmelerini koordine edin ve gerektiğinde kurtarma çalışmalarını önceliklendirin.
* **Hizmet kullanılabilirliği**: Eşleştirilmiş bölgelerin etkin/sıcak, sık/sıcak veya sık/soğuk olması gerektiğine karar verin. Her iki bölgeyi de aynı anda çalıştırmak istiyor musunuz, tek bir bölge, trafiğe hizmet vermeye *başlamaya hazırlanıyor* mi? Ya da bir bölgenin, trafiği sunmaya hazırlamak için zaman almasını ister misiniz?

AKS bölgesi kullanılabilirliği ve eşleştirilmiş bölgeler, bir eklem noktadır. AKS kümelerinizi, bölge olağanüstü durum kurtarmayı birlikte yönetmek için tasarlanan eşleştirilmiş bölgelere dağıtın. Örneğin, AKS Doğu ABD ve Batı ABD kullanılabilir. Bu bölgeler eşlenilmiştir. AKS BC/DR stratejisi oluştururken bu iki bölgeyi seçin.

Uygulamanızı dağıtırken, bu çoklu AKS kümelerine dağıtmak için CI/CD işlem hattınızla başka bir adım ekleyin. Dağıtım işlem hatlarınızı güncelleştirmemeniz halinde uygulamalar, Bölgelerinizdeki ve AKS kümelerinden yalnızca birine dağıtılabilir. Bir ikincil bölgeye yöneltilen müşteri trafiği, en son kod güncelleştirmelerini almaz.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Trafiği yönlendirmek için Azure Traffic Manager kullanma

**En iyi uygulama**: Azure Traffic Manager, müşterileri en yakın AKS kümesine ve uygulama örneğine yönlendirebilir. En iyi performans ve artıklık için, tüm uygulama trafiğini AKS kümenize geçmeden önce Traffic Manager aracılığıyla doğrudan yönlendirin.

Farklı bölgelerde birden fazla AKS kümeniz varsa, trafiğin her kümede çalışan uygulamalara nasıl akacağını denetlemek için Traffic Manager kullanın. [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) , bölgeler arasında ağ trafiği DAĞıTABILECEK DNS tabanlı bir trafik yük dengeleyicidir. Kullanıcıları küme yanıt süresine göre veya Coğrafya temelinde yönlendirmek için Traffic Manager kullanın.

![Traffic Manager ile AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Tek bir AKS kümesi olan müşteriler tipik olarak belirli bir uygulamanın hizmet IP 'sini veya DNS adına bağlanır. Birden çok Luster dağıtımında, müşteriler her bir AKS kümesindeki hizmetlere işaret eden bir Traffic Manager DNS adına bağlanmalıdır. Bu hizmetleri Traffic Manager uç noktaları kullanarak tanımlayın. Her uç nokta *hizmet yük DENGELEYICI IP*'dir. Ağ trafiğini bir bölgedeki Traffic Manager uç noktasından farklı bir bölgedeki uç noktaya yönlendirmek için bu yapılandırmayı kullanın.

![Traffic Manager aracılığıyla coğrafi yönlendirme](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager DNS aramalarını gerçekleştirir ve kullanıcının en uygun uç noktasını döndürür. İç içe profiller birincil bir konuma öncelik verebilir. Örneğin, kullanıcılar genellikle en yakın coğrafi bölgelere bağlanmalıdır. Bu bölgede bir sorun varsa Traffic Manager bunun yerine kullanıcıları ikincil bir bölgeye yönlendirir. Bu yaklaşım, en yakın coğrafi bölgesi kullanılamaz olsa bile müşterilerin bir uygulama örneğine bağlanabilmelerini sağlar.

Uç noktaları ve yönlendirmeyi ayarlama hakkında daha fazla bilgi için, bkz. [Traffic Manager kullanarak coğrafi trafik yönlendirme yöntemini yapılandırma](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Azure ön kapı hizmeti ile katman 7 uygulama yönlendirme

Traffic Manager trafiği şekillendirmek için DNS (katman 3) kullanır. [Azure ön kapı hizmeti](https://docs.microsoft.com/azure/frontdoor/front-door-overview) bir http/https (katman 7) yönlendirme seçeneği sağlar. Azure ön kapı hizmeti 'nin ek özellikleri arasında SSL sonlandırma, özel etki alanı, Web uygulaması güvenlik duvarı, URL yeniden yazma ve oturum benzeşimi bulunur. En uygun çözümü anlamak için uygulama trafiğinizin ihtiyaçlarını gözden geçirin.

## <a name="enable-geo-replication-for-container-images"></a>Kapsayıcı görüntüleri için Coğrafi çoğaltmayı etkinleştirme

**En iyi uygulama**: Kapsayıcı görüntülerinizi Azure Container Registry ve coğrafi olarak kayıt defterinde her bir AKS bölgesine depolayın.

Uygulamalarınızı AKS 'de dağıtmak ve çalıştırmak için kapsayıcı görüntülerini depolamak ve çekmek için bir yol gerekir. Container Registry, AKS ile tümleşir, bu nedenle kapsayıcı görüntülerinizi veya Held grafiklerini güvenli bir şekilde depolayabilirler. Container Registry, görüntülerinizi dünyanın dört bir yanındaki Azure bölgelerine otomatik olarak çoğaltmak için çok kaynaklı Coğrafi çoğaltmayı destekler. 

Performansı ve kullanılabilirliği artırmak için, AKS kümeniz olan her bölgede bir kayıt defteri oluşturmak üzere coğrafi çoğaltma Container Registry kullanın. Her bir AKS kümesi daha sonra aynı bölgedeki yerel kapsayıcı kayıt defterinden kapsayıcı görüntülerini çeker:

![Kapsayıcı görüntüleri için coğrafi çoğaltma Container Registry](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Aynı bölgedeki görüntüleri çekmek için coğrafi çoğaltma Container Registry kullandığınızda sonuçlar şunlardır:

* **Daha hızlı**: Aynı Azure bölgesindeki yüksek hızlı, düşük gecikmeli ağ bağlantılarından görüntü çekebilirsiniz.
* **Daha güvenilir**: Bir bölge kullanılamıyorsa AKS kümeniz, görüntüleri kullanılabilir bir kapsayıcı kayıt defterinden çeker.
* **Kimaper**: Veri merkezleri arasında ağ çıkış ücreti yok.

Coğrafi çoğaltma, *Premium* SKU kapsayıcısı kayıt defterlerinin bir özelliğidir. Coğrafi çoğaltmanın nasıl yapılandırılacağı hakkında bilgi için bkz. [Container Registry coğrafi çoğaltma](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Hizmet durumunu kapsayıcıların içinden kaldır

**En iyi uygulama**: Mümkün olduğunda, hizmet durumunu kapsayıcının içinde depolamamayın. Bunun yerine, çok bölgeli çoğaltmasını destekleyen bir hizmet olarak Azure platformu (PaaS) kullanın.

*Hizmet durumu* , bir hizmetin çalışması için gereken bellek içi veya disk üzerindeki verilere başvurur. Durum, hizmetin okuduğu ve yazdığı veri yapılarını ve üye değişkenlerini içerir. Hizmetin nasıl mimari olduğuna bağlı olarak, durum, diskte depolanan dosyaları veya diğer kaynakları da içerebilir. Örneğin, durum, bir veritabanının veri ve işlem günlüklerini depolamak için kullandığı dosyaları içerebilir.

Durum externalized veya durumu işleyen kodla eklenebilir. Genellikle, bir veritabanı veya ağ üzerinden çalışan farklı makinelerde çalışan veya aynı makinede işlem dışında çalışan başka bir veri deposu kullanarak externalize.

Kapsayıcılar ve mikro hizmetler, içinde çalışan işlemlerin durumu korumaları durumunda en esnektir. Uygulamalar neredeyse her zaman bir durum içerdiğinden, MySQL için Azure veritabanı, PostgreSQL için Azure veritabanı veya Azure SQL veritabanı gibi bir PaaS çözümü kullanın.

Taşınabilir uygulamalar oluşturmak için aşağıdaki yönergelere bakın:

* [12 öğeli uygulama yöntemi](https://12factor.net/)
* [Bir Web uygulamasını birden çok Azure bölgesinde çalıştırma](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Depolama geçiş planı oluşturma

**En iyi uygulama**: Azure Storage kullanıyorsanız, depolama alanınızı birincil bölgeden yedekleme bölgesine nasıl geçirebileceğiniz hazırlayın ve test edin.

Uygulamalarınız, verileri için Azure Storage kullanıyor olabilirler. Uygulamalarınız farklı bölgelerdeki birden çok AKS kümesine yayıldığından, depolamayı eşitlenmiş tutmanız gerekir. Depolama çoğaltması için iki yaygın yol aşağıda verilmiştir:

* Altyapı tabanlı zaman uyumsuz çoğaltma
* Uygulama tabanlı zaman uyumsuz çoğaltma

### <a name="infrastructure-based-asynchronous-replication"></a>Altyapı tabanlı zaman uyumsuz çoğaltma

Bir pod silindikten sonra bile uygulamalarınız kalıcı depolama gerekebilir. Kubernetes 'de, veri depolamayı kalıcı hale getirmek için kalıcı birimleri kullanabilirsiniz. Kalıcı birimler bir düğüm sanal makinesine bağlanır ve sonra da pods 'leri kullanıma sunulur. Kalıcı birimler, düğüm aynı kümedeki farklı bir düğüme taşınsa bile, Pod 'ler izler.

Kullandığınız çoğaltma stratejisi, depolama çözümünüze bağlıdır. [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html)ve [portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) gibi yaygın depolama çözümleri olağanüstü durum kurtarma ve çoğaltma hakkında kendi kılavuzunu sağlar.

Tipik strateji, uygulamaların verilerini yazabilmesi için ortak bir depolama noktası sağlamaktır. Bu veriler daha sonra bölgeler arasında çoğaltılır ve sonra yerel olarak erişilir.

![Altyapı tabanlı zaman uyumsuz çoğaltma](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure yönetilen diskleri kullanıyorsanız, şu gibi çoğaltma ve DR çözümlerini seçebilirsiniz:

* [Azure 'da Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

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

---
title: Azure Kubernetes Service 'e (AKS) geçiş
description: Azure Kubernetes hizmetine (AKS) geçiş yapın.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/07/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 0c243d216e00adf49a6425e5b7be0d38caeef043
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929058"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 'e (AKS) geçiş

Bu makale, Azure Kubernetes Service (AKS) için başarılı bir geçiş planlayıp yürütmenize yardımcı olur. Bu kılavuz, önemli kararlar almanıza yardımcı olmak için, AKS için önerilen geçerli yapılandırmaya ilişkin ayrıntılar sağlar. Bu makale her senaryoyu kapsamamaktadır ve uygun yerlerde, makalenin başarılı bir geçiş planlaması için daha ayrıntılı bilgilere bağlantılar içerir.

Bu belge, aşağıdaki senaryoları desteklemeye yardımcı olmak için kullanılabilir:

* [Kullanılabilirlik kümeleri](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) tarafından desteklenen bir aks kümesini [sanal makine ölçek kümelerine](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) geçirme
* AKS kümesini [Standart SKU yük dengeleyici](https://docs.microsoft.com/azure/aks/load-balancer-standard) kullanmak üzere geçirme
* [Azure Container Service (ACS)-devre dışı bırakma 31 ocak 2020,](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) aks 'e geçiriliyor
* [Aks altyapısından](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) aks 'e geçiş
* Azure olmayan tabanlı Kubernetes kümelerinden AKS 'e geçiş

Geçiş yaparken, hedef Kubernetes sürümünüzün AKS için desteklenen pencere kapsamında olduğundan emin olun. Daha eski bir sürüm kullanıyorsanız, bu desteklenen aralıkta olmayabilir ve yükseltme sürümlerinin AKS tarafından desteklenmesi gerekir. Daha fazla bilgi için bkz. [aks desteklenen Kubernetes sürümleri](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) .

Kubernetes 'in daha yeni bir sürümüne geçiş yapıyorsanız, [Kubernetes sürümünü ve sürüm eğriltme destek ilkesini](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)gözden geçirin.

Çeşitli açık kaynaklı araçlar, senaryonuza bağlı olarak geçişinize yardımcı olabilir:

* [Velero](https://velero.io/) (Kubernetes 1.7 + gerektirir)
* [Azure Kuto CLı uzantısı](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

Bu makalede, için geçiş ayrıntılarını özetliyoruz:

> [!div class="checklist"]
> * Standart Load Balancer ve sanal makine ölçek kümeleri ile AKS 'ler
> * Mevcut bağlı Azure hizmetleri
> * Geçerli kotalar olduğundan emin olun
> * Yüksek kullanılabilirlik ve iş sürekliliği
> * Durum bilgisi olmayan uygulamalarla ilgili konular
> * Statefull uygulamalarına yönelik konular
> * Küme yapılandırmanızın dağıtımı

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>Standart Load Balancer ve sanal makine ölçek kümeleri ile AKS 'ler

AKS, daha düşük yönetim yüküyle benzersiz yetenekler sunan bir yönetilen hizmettir. Yönetilen bir hizmet olmanın bir sonucu olarak, AKS 'nin desteklediği bir [bölge](https://docs.microsoft.com/azure/aks/quotas-skus-regions) kümesinden seçim yapmanız gerekir. Mevcut kümenizdeki AKS 'e geçiş, AKS tarafından yönetilen denetim düzleminde sağlıklı kalması için mevcut uygulamalarınızın değiştirilmesini gerektirebilir.

[Birden çok düğüm havuzu](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), [KULLANıLABILIRLIK ALANLARı](https://docs.microsoft.com/azure/availability-zones/az-overview), [yetkilendirilmiş IP aralıkları](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), [küme otomatik Scaler](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [aks için Azure ilkesi](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)ve yayımlandıkları gibi diğer yeni özellikler gibi özellikleri almanızı sağlamak için [Sanal Makine Ölçek Kümeleri](https://docs.microsoft.com/azure/virtual-machine-scale-sets) ve [Azure Standart Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) tarafından desteklenen aks kümelerini kullanmanızı öneririz.   

[Sanal makine kullanılabilirlik kümeleri](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) tarafından desteklenen aks kümelerinde bu özelliklerin birçoğu için destek yok.

Aşağıdaki örnek, bir sanal makine ölçek kümesi tarafından desteklenen tek düğümlü havuz içeren bir AKS kümesi oluşturur. Standart yük dengeleyici kullanır. Ayrıca küme için düğüm havuzunda küme otomatik Scaler 'ı ve en az *1* ve en fazla *3* düğüm ayarlar:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Mevcut bağlı Azure hizmetleri

Kümeleri geçirirken dış Azure hizmetlerinizi bağlı kalabilirsiniz. Bunlar kaynak yeniden oluşturma gerektirmez, ancak işlevleri sürdürmek için önceki yeni kümelere bağlantıların güncelleştirilmesini gerektirecektir.

* Azure Container Kayıt Defteri
* Log Analytics
* Application Insights
* Traffic Manager
* Depolama Hesabı
* Dış veritabanları

## <a name="ensure-valid-quotas"></a>Geçerli kotalar olduğundan emin olun

Geçiş sırasında aboneliğinize daha fazla sanal makine dağıtılacağından, Kotalarınızın ve limitlerinizin bu kaynaklar için yeterli olduğunu doğrulamanız gerekir. [VCPU kotasında](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)artış istemeniz gerekebilir.

IP 'Leri tüketmemenizi sağlamak için [ağ kotaları](https://docs.microsoft.com/azure/azure-supportability/networking-quota-requests) artışı istemeniz gerekebilir. Ek bilgi için bkz. [AKS için ağ ve IP aralıkları](https://docs.microsoft.com/azure/aks/configure-kubenet) .

Daha fazla bilgi için bkz. [Azure aboneliği ve hizmet sınırları](https://docs.microsoft.com/azure/azure-subscription-service-limits). Geçerli kotalarınızı denetlemek için, Azure portal [abonelikler dikey penceresine](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)gidin, aboneliğinizi seçin ve ardından **kullanım + kotalar**' ı seçin.

## <a name="high-availability-and-business-continuity"></a>Yüksek kullanılabilirlik ve Iş sürekliliği

Uygulamanız kapalı kalma süresini işleyememesi durumunda yüksek kullanılabilirlik geçiş senaryoları için en iyi yöntemleri izlemeniz gerekir.  Karmaşık iş sürekliliği planlama, olağanüstü durum kurtarma ve en yüksek çalışma süresi, bu belgenin kapsamı dışındadır.  Daha fazla bilgi edinmek için [Azure Kubernetes Service 'te (AKS) iş sürekliliği ve olağanüstü durum kurtarma Için en iyi uygulamalar](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) hakkında daha fazla bilgi edinin.

Karmaşık uygulamalar için genellikle her seferinde değil zaman içinde geçiş yapabilirsiniz. Bu, eski ve yeni ortamların ağ üzerinden iletişim kurması gerekebilecek anlamına gelir. Daha önce iletişim kurmak için `ClusterIP` hizmetleri kullanan uygulamaların, tür `LoadBalancer` olarak sunulup güvenli bir şekilde sağlanması gerekebilir.

Geçişi gerçekleştirmek için, istemcileri AKS üzerinde çalışan yeni hizmetlere işaret etmek isteyeceksiniz. DNS 'yi, AKS kümenizin önünde bulunan Load Balancer işaret etmek üzere güncelleştirerek trafiği yeniden yönlendirmenizi öneririz.

[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) , müşterileri Istenen Kubernetes kümesine ve uygulama örneğine yönlendirebilir.  Traffic Manager, bölgeler arasında ağ trafiği dağıtabilecek DNS tabanlı bir trafik yük dengeleyicidir.  En iyi performans ve artıklık için, tüm uygulama trafiğini AKS kümenize geçmeden önce Traffic Manager aracılığıyla doğrudan yönlendirin.  Birden çok Luster dağıtımında, müşteriler her bir AKS kümesindeki hizmetlere işaret eden bir Traffic Manager DNS adına bağlanmalıdır. Bu hizmetleri Traffic Manager uç noktaları kullanarak tanımlayın. Her uç nokta *hizmet yük DENGELEYICI IP*'dir. Ağ trafiğini bir bölgedeki Traffic Manager uç noktasından farklı bir bölgedeki uç noktaya yönlendirmek için bu yapılandırmayı kullanın.

![Traffic Manager ile AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure ön kapı hizmeti](https://docs.microsoft.com/azure/frontdoor/front-door-overview) , aks kümelerine yönelik trafiği yönlendirme için başka bir seçenektir.  Azure Front Door Service, yüksek kullanılabilirliğe yönelik en iyi performans ve anında genel yük devretme için iyileştirerek, web trafiğinizin genel yönlendirmesini tanımlamanızı, yönetmenizi ve izlemenizi sağlar. 

### <a name="considerations-for-stateless-applications"></a>Durum bilgisi olmayan uygulamalarla ilgili konular 

Durum bilgisiz uygulama geçişi en kolay durumdur. Kaynak tanımlarınızı (YAML veya Held) yeni kümeye uygulayın, her şeyin beklendiği gibi çalıştığından emin olun ve yeni kümenizi etkinleştirmek için trafiği yeniden yönlendirin.

### <a name="considers-for-stateful-applications"></a>Durum bilgisi olan uygulamalar için dikkate alır

Veri kaybını veya beklenmedik kapalı kalma süresini önlemek için durum bilgisi olan uygulamaların geçişini dikkatle planlayın.

Azure dosyaları kullanıyorsanız, dosya payını yeni kümeye bir birim olarak bağlayabilirsiniz:
* [Statik Azure dosyalarını birim olarak bağlama](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Azure yönetilen diskler kullanıyorsanız, diski yalnızca herhangi bir VM 'ye bağlı değilse bağlayabilirsiniz:
* [Statik Azure diskini birim olarak bağlama](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Bu yaklaşımlardan hiçbiri işe çalışmadıysanız, yedekleme ve geri yükleme seçeneklerini kullanabilirsiniz:
* [Azure 'da Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure Dosyaları

Disklerden farklı olarak, Azure dosyaları aynı anda birden çok konağa bağlanabilir. AKS kümenizde, Azure ve Kubernetes, ACS kümenizin hala kullandığı bir pod oluşturmanızı engellemez. Veri kaybını ve beklenmedik davranışı engellemek için, kümelerin aynı anda aynı dosyalara yazmayın olduğundan emin olun.

Uygulamanız aynı dosya paylaşımının işaret eden birden çok kopyayı barındırabiliyorsanız, durum bilgisi olmayan geçiş adımlarını izleyin ve YAML tanımlarınızı yeni kümenize dağıtın. Aksi takdirde, olası bir geçiş yaklaşımı aşağıdaki adımları içerir:

* Uygulamanızın düzgün çalıştığını doğrulayın.
* Canlı trafiğinizi yeni AKS kümenize getirin.
* Eski kümenin bağlantısını kesin.

Boş bir paylaşımdan başlamak ve kaynak verilerin bir kopyasını oluşturmak istiyorsanız, [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) komutlarını kullanarak verilerinizi geçirebilirsiniz.


#### <a name="migrating-persistent-volumes"></a>Kalıcı birimleri geçirme

Mevcut kalıcı birimleri AKS 'e geçiriyorsanız, genellikle bu adımları takip edersiniz:

* Uygulamaya sessiz yazma yazma. (Bu adım isteğe bağlıdır ve kapalı kalma süresi gerektirir.)
* Disklerin anlık görüntülerini alın.
* Anlık görüntülerden yeni yönetilen diskler oluşturun.
* AKS 'de kalıcı birimler oluşturun.
* Pod belirtimlerini, Persistentvolumeclaim (statik sağlama) yerine [mevcut birimleri kullanacak](https://docs.microsoft.com/azure/aks/azure-disk-volume) şekilde güncelleştirin.
* Uygulamanızı AKS 'e dağıtın.
* Uygulamanızın düzgün çalıştığını doğrulayın.
* Canlı trafiğinizi yeni AKS kümenize getirin.

> [!IMPORTANT]
> Yazmaları sessiz değil ' i seçerseniz, verileri yeni dağıtıma çoğaltmanız gerekir. Aksi takdirde, disk anlık görüntülerini aldıktan sonra yazılmış verileri kaçırılırsınız.

Bazı açık kaynaklı araçlar, yönetilen diskler oluşturmanıza ve birimleri Kubernetes kümeleri arasında geçirmeye yardımcı olabilir:

* [Azure CLI disk kopyalama uzantısı](https://github.com/noelbundick/azure-cli-disk-copy-extension) , diskleri kaynak grupları ve Azure bölgeleri arasında kopyalar ve dönüştürür.
* [Azure KUTO CLI uzantısı](https://github.com/yaron2/azure-kube-cli) , ACS Kubernetes birimlerini numaralandırır ve bunları bir aks kümesine geçirir.


### <a name="deployment-of-your-cluster-configuration"></a>Küme yapılandırmanızın dağıtımı

AKS 'e bilinen iyi bir yapılandırma dağıtmak için mevcut sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattınızı kullanmanızı öneririz. [Uygulamalarınızı derlemek ve dağıtmak](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) için, mevcut dağıtım görevlerinizi klonlamak ve `kubeconfig` yeni aks kümesine işaret ettiğini garantilemek için Azure Pipelines kullanabilirsiniz.

Bu mümkün değilse, mevcut Kubernetes kümenizdeki kaynak tanımlarını dışarı aktarın ve ardından bunları AKS 'e uygulayın. Nesneleri dışarı aktarmak için `kubectl` kullanabilirsiniz.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Bu makalede, için geçiş ayrıntıları özetlenmektedir:

> [!div class="checklist"]
> * Standart Load Balancer ve sanal makine ölçek kümeleri ile AKS 'ler
> * Mevcut bağlı Azure hizmetleri
> * Geçerli kotalar olduğundan emin olun
> * Yüksek kullanılabilirlik ve iş sürekliliği
> * Durum bilgisi olmayan uygulamalarla ilgili konular
> * Statefull uygulamalarına yönelik konular
> * Küme yapılandırmanızın dağıtımı

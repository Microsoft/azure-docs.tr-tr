---
title: Azure Kubernetes Service 'e (AKS) geçiş
description: Azure Kubernetes hizmetine (AKS) geçiş yapın.
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 123f4e5c2442b913a53288602d1c56f199b131a6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872794"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 'e (AKS) geçiş

Azure Kubernetes Service (AKS) için başarılı bir geçiş planlayıp yürütmede yardımcı olması için bu kılavuzda, geçerli önerilen AKS yapılandırmasına ilişkin ayrıntılar sağlanmaktadır. Bu makale her senaryoyu kapsamıyorken, başarılı bir geçiş planlamak için daha ayrıntılı bilgi için bağlantılar içerir.

Bu belge aşağıdaki senaryoları desteklemeye yardımcı olur:

* Belirli uygulamaları kapsayıklaşın ve bunları [Azure geçişi](../migrate/migrate-services-overview.md)kullanarak aks 'e geçirme.
* [Kullanılabilirlik kümeleri](../virtual-machines/windows/tutorial-availability-sets.md) tarafından desteklenen bir aks kümesini [sanal makine ölçek kümelerine](../virtual-machine-scale-sets/overview.md)geçirme.
* Bir AKS kümesini [Standart SKU yük dengeleyici](./load-balancer-standard.md)kullanmak üzere geçirme.
* [Azure Container Service (ACS)-31 ocak 2020 '](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) den aks 'e geçiş.
* [Aks altyapısından](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) aks 'e geçiş.
* Azure olmayan tabanlı Kubernetes kümelerinden AKS 'e geçiş.
* Mevcut kaynaklar farklı bir bölgeye taşınıyor.

Geçiş yaparken, hedef Kubernetes sürümünüzün AKS için desteklenen pencere kapsamında olduğundan emin olun. Eski sürümler desteklenen aralık dahilinde olmayabilir ve bir sürüm yükseltmesinin AKS tarafından desteklenmesi gerekir. Daha fazla bilgi için bkz. [aks desteklenen Kubernetes sürümleri](./supported-kubernetes-versions.md).

Kubernetes 'in daha yeni bir sürümüne geçiş yapıyorsanız, [Kubernetes sürümünü ve sürüm eğriltme destek ilkesini](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)gözden geçirin.

Çeşitli açık kaynaklı araçlar, senaryonuza bağlı olarak geçişinize yardımcı olabilir:

* [Velero](https://velero.io/) (Kubernetes 1.7 + gerektirir)
* [Azure Kuto CLı uzantısı](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

Bu makalede, için geçiş ayrıntılarını özetliyoruz:

> [!div class="checklist"]
> * Azure geçişi ile uygulama Kapsayıcılı hale getirme 
> * Standart Load Balancer ve sanal makine ölçek kümeleri ile AKS 'ler
> * Mevcut bağlı Azure hizmetleri
> * Geçerli kotalar olduğundan emin olun
> * Yüksek kullanılabilirlik ve iş sürekliliği
> * Durum bilgisi olmayan uygulamalarla ilgili konular
> * Durum bilgisi olan uygulamalarla ilgili konular
> * Küme yapılandırmanızın dağıtımı

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Uygulamalarınızı AKS 'e geçirmek için Azure geçişi 'ni kullanma

Azure geçişi, Azure şirket içi sunucuları, altyapısı, uygulamaları ve verileri değerlendirmek ve bu uygulamalara geçiş yapmak için birleştirilmiş bir platform sunar. AKS için Azure geçişi 'ni aşağıdaki görevler için kullanabilirsiniz:

* [ASP.NET uygulamaları kapsayıklaın ve AKS 'e geçiş](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Java Web uygulamalarını containerleştirme ve AKS 'e geçirme](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>Standart Load Balancer ve sanal makine ölçek kümeleri ile AKS 'ler

AKS, daha düşük yönetim yüküyle benzersiz yetenekler sunan bir yönetilen hizmettir. AKS yönetilen bir hizmet olduğundan, AKS 'nin desteklediği bir [bölge](./quotas-skus-regions.md) kümesinden seçim yapmanız gerekir. Mevcut kümeinizden AKS 'e geçiş sırasında AKS tarafından yönetilen denetim düzlemine devam etmek için mevcut uygulamalarınızı değiştirmeniz gerekebilir.

[Sanal Makine Ölçek Kümeleri](../virtual-machine-scale-sets/index.yml) tarafından desteklenen aks kümelerini ve [Azure Standart Load Balancer](./load-balancer-standard.md) kullanmanızı öneririz; örneğin:
* [Birden çok düğüm havuzu](./use-multiple-node-pools.md),
* [Kullanılabilirlik alanları](../availability-zones/az-overview.md),
* [YETKILENDIRILMIŞ IP aralıkları](./api-server-authorized-ip-ranges.md),
* [Küme otomatik Scaler](./cluster-autoscaler.md),
* [AKS Için Azure ilkesi](../governance/policy/concepts/policy-for-kubernetes.md)ve
* Yayımlandıkları gibi diğer yeni özellikler.

[Sanal makine kullanılabilirlik kümeleri](../virtual-machines/availability.md#availability-sets) tarafından desteklenen aks kümelerinde bu özelliklerin birçoğu için destek yok.

Aşağıdaki örnek, bir sanal makine (VM) ölçek kümesi tarafından desteklenen tek düğümlü havuz içeren bir AKS kümesi oluşturur. Küme:
* Standart yük dengeleyici kullanır. 
* Küme için düğüm havuzunda küme otomatik Scaler öğesini sunar.
* En az *1* ve en fazla *3* düğüm ayarlar.

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

Kümeleri geçirirken, dış Azure hizmetleri 'ne bağlı olabilirsiniz. Aşağıdaki hizmetler kaynak yeniden oluşturma gerektirse de, işlevleri sürdürmek için önceki yeni kümelere bağlantıların güncelleştirilmesini gerektirecektir.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Depolama Hesabı
* Dış veritabanları

## <a name="ensure-valid-quotas"></a>Geçerli kotalar olduğundan emin olun

Geçiş sırasında diğer VM 'Ler aboneliğinize dağıtılabilmesi için, Kotalarınızın ve limitlerinizin bu kaynaklar için yeterli olduğunu doğrulamanız gerekir. Gerekirse, [vCPU kotasında](../azure-portal/supportability/per-vm-quota-requests.md)bir artış isteyin.

IP 'Leri tüketmemenizi sağlamak için [ağ kotaları](../azure-portal/supportability/networking-quota-requests.md) artışı istemeniz gerekebilir. Daha fazla bilgi için bkz. [AKS için ağ ve IP aralıkları](./configure-kubenet.md).

Daha fazla bilgi için bkz. [Azure aboneliği ve hizmet sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md). Geçerli kotalarınızı denetlemek için, Azure portal [abonelikler dikey penceresine](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)gidin, aboneliğinizi seçin ve ardından **kullanım + kotalar**' ı seçin.

## <a name="high-availability-and-business-continuity"></a>Yüksek kullanılabilirlik ve Iş sürekliliği

Uygulamanız kapalı kalma süresini işleyememesi durumunda yüksek kullanılabilirlik geçiş senaryoları için en iyi yöntemleri izlemeniz gerekir. [Karmaşık iş sürekliliği planlama, olağanüstü durum kurtarma ve Azure Kubernetes hizmetindeki (AKS) çalışma süresini en üst düzeye çıkarmak Için en iyi uygulamalar](./operator-best-practices-multi-region.md)hakkında daha fazla bilgi edinin.

Karmaşık uygulamalar için genellikle her seferinde değil zaman içinde geçiş yapmanız gerekir. Bu, eski ve yeni ortamların ağ üzerinden iletişim kurması gerekebileceği anlamına gelir. Daha önce `ClusterIP` iletişim kurmak için Hizmetleri kullanan uygulamaların tür olarak açığa çıkarılması `LoadBalancer` ve güvenli bir şekilde sağlanması gerekebilir.

Geçişi gerçekleştirmek için, istemcileri AKS üzerinde çalışan yeni hizmetlere işaret etmek isteyeceksiniz. DNS 'yi, AKS kümenizin önünde oturacak Load Balancer işaret etmek üzere güncelleştirerek trafiği yeniden yönlendirmenizi öneririz.

[Azure Traffic Manager](../traffic-manager/index.yml) , müşterileri Istenen Kubernetes kümesine ve uygulama örneğine yönlendirebilir. Traffic Manager, bölgeler arasında ağ trafiği dağıtabilecek DNS tabanlı bir trafik yük dengeleyicidir. En iyi performans ve artıklık için, tüm uygulama trafiğini AKS kümenize geçmeden önce Traffic Manager aracılığıyla doğrudan yönlendirin. 

Birden çok küme dağıtımında, müşteriler her bir AKS kümesindeki hizmetlere işaret eden bir Traffic Manager DNS adına bağlanmalıdır. Bu hizmetleri Traffic Manager uç noktaları kullanarak tanımlayın. Her uç nokta *hizmet yük DENGELEYICI IP*'dir. Ağ trafiğini bir bölgedeki Traffic Manager uç noktasından farklı bir bölgedeki uç noktaya yönlendirmek için bu yapılandırmayı kullanın.

![Traffic Manager ile AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure ön kapı hizmeti](../frontdoor/front-door-overview.md) , aks kümelerine yönelik trafiği yönlendirme için başka bir seçenektir. Azure ön kapı hizmeti sayesinde, yüksek kullanılabilirlik için en iyi performansı ve hızlı genel yük devretmeyi iyileştirerek Web trafiğiniz için küresel yönlendirmeyi tanımlayabilir, yönetebilir ve izleyebilirsiniz. 

### <a name="considerations-for-stateless-applications"></a>Durum bilgisi olmayan uygulamalarla ilgili konular

Durum bilgisiz uygulama geçişi en kolay durumdur:
1. Kaynak tanımlarınızı (YAML veya Held) yeni kümeye uygulayın.
1. Her şeyin beklendiği gibi çalıştığından emin olun.
1. Yeni kümenizi etkinleştirmek için trafiği yeniden yönlendirin.

### <a name="considerations-for-stateful-applications"></a>Durum bilgisi olan uygulamalarla ilgili konular

Veri kaybını veya beklenmedik kapalı kalma süresini önlemek için durum bilgisi olan uygulamaların geçişini dikkatle planlayın.

* Azure dosyaları kullanıyorsanız, dosya payını yeni kümeye bir birim olarak bağlayabilirsiniz. Bkz. [statik Azure dosyalarını birim olarak bağlama](./azure-files-volume.md#mount-file-share-as-an-persistent-volume).
* Azure yönetilen diskler kullanıyorsanız, diski yalnızca herhangi bir VM 'ye bağlanmadığından bağlayabilirsiniz. Bkz. [statik Azure diskini birim olarak bağlama](./azure-disk-volume.md#mount-disk-as-volume).
* Bu yaklaşımlardan hiçbiri işe çalışmadıysanız, yedekleme ve geri yükleme seçeneklerini kullanabilirsiniz. Bkz. [Velero on Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md).

#### <a name="azure-files"></a>Azure Dosyaları

Disklerden farklı olarak, Azure dosyaları aynı anda birden çok konağa bağlanabilir. AKS kümenizde, Azure ve Kubernetes, AKS kümenizin hala kullandığı bir pod oluşturmaya engel olmaz. Veri kaybını ve beklenmedik davranışı engellemek için kümelerin aynı anda aynı dosyalara yazmayın olduğundan emin olun.

Uygulamanız aynı dosya paylaşımının işaret eden birden çok kopyayı barındırabiliyorsanız, durum bilgisi olmayan geçiş adımlarını izleyin ve YAML tanımlarınızı yeni kümenize dağıtın. 

Aksi takdirde, olası bir geçiş yaklaşımı aşağıdaki adımları içerir:

1. Uygulamanızın düzgün çalıştığını doğrulayın.
1. Canlı trafiğinizi yeni AKS kümenize getirin.
1. Eski kümenin bağlantısını kesin.

Boş bir paylaşımdan başlamak ve kaynak verilerin bir kopyasını oluşturmak istiyorsanız, bu [`az storage file copy`](/cli/azure/storage/file/copy) komutları kullanarak verilerinizi geçirebilirsiniz.


#### <a name="migrating-persistent-volumes"></a>Kalıcı birimleri geçirme

Mevcut kalıcı birimleri AKS 'e geçiriyorsanız, genellikle bu adımları takip edersiniz:

1. Uygulamaya sessiz yazma yazma. 
    * Bu adım isteğe bağlıdır ve kapalı kalma süresi gerektirir.
1. Disklerin anlık görüntülerini alın.
1. Anlık görüntülerden yeni yönetilen diskler oluşturun.
1. AKS 'de kalıcı birimler oluşturun.
1. Pod belirtimlerini, Persistentvolumeclaim (statik sağlama) yerine [mevcut birimleri kullanacak](./azure-disk-volume.md) şekilde güncelleştirin.
1. Uygulamanızı AKS 'e dağıtın.
1. Uygulamanızın düzgün çalıştığını doğrulayın.
1. Canlı trafiğinizi yeni AKS kümenize getirin.

> [!IMPORTANT]
> Yazmaları sessiz değil ' i seçerseniz, verileri yeni dağıtıma çoğaltmanız gerekir. Aksi takdirde, disk anlık görüntülerini aldıktan sonra yazılmış verileri kaçırılırsınız.

Bazı açık kaynaklı araçlar, yönetilen diskler oluşturmanıza ve birimleri Kubernetes kümeleri arasında geçirmeye yardımcı olabilir:

* [Azure CLI disk kopyalama uzantısı](https://github.com/noelbundick/azure-cli-disk-copy-extension) , diskleri kaynak grupları ve Azure bölgeleri arasında kopyalar ve dönüştürür.
* [Azure KUTO CLI uzantısı](https://github.com/yaron2/azure-kube-cli) , ACS Kubernetes birimlerini numaralandırır ve bunları bir aks kümesine geçirir.


### <a name="deployment-of-your-cluster-configuration"></a>Küme yapılandırmanızın dağıtımı

AKS 'e bilinen iyi bir yapılandırma dağıtmak için mevcut sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattınızı kullanmanızı öneririz. [Uygulamalarınızı derlemek ve AKS 'e dağıtmak](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)için Azure Pipelines kullanabilirsiniz. Mevcut dağıtım görevlerinizi kopyalayın ve `kubeconfig` Yeni AKS kümesine işaret edin.

Bu mümkün değilse, mevcut Kubernetes kümenizdeki kaynak tanımlarını dışarı aktarın ve ardından bunları AKS 'e uygulayın. `kubectl`Nesneleri dışarı aktarmak için ' i kullanabilirsiniz.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Mevcut kaynakları başka bir bölgeye taşıma

AKS kümenizi [aks tarafından desteklenen farklı bir bölgeye][region-availability]taşımak isteyebilirsiniz. Diğer bölgede yeni bir küme oluşturmanızı ve ardından kaynaklarınızı ve uygulamalarınızı yeni kümenize dağıtmanızı öneririz. 

Ayrıca, AKS kümenizde çalışan [Azure dev Spaces][azure-dev-spaces] gibi bir hizmet varsa, bu hizmetleri yeni bölgedeki kümenize yüklemeniz ve yapılandırmanız gerekecektir.


Bu makalede, için geçiş ayrıntılarını özetliyoruz:

> [!div class="checklist"]
> * Standart Load Balancer ve sanal makine ölçek kümeleri ile AKS 'ler
> * Mevcut bağlı Azure hizmetleri
> * Geçerli kotalar olduğundan emin olun
> * Yüksek kullanılabilirlik ve iş sürekliliği
> * Durum bilgisi olmayan uygulamalarla ilgili konular
> * Durum bilgisi olan uygulamalarla ilgili konular
> * Küme yapılandırmanızın dağıtımı


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml

---
title: Azure Red Hat OpenShift hakkında sık sorulan sorular
description: Red Hat OpenShift Microsoft Azure hakkında sık sorulan soruların yanıtları aşağıda verilmiştir
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a3721083e48774963cd761178abdb552c93b15c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634354"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift SSS

Bu makalede, Microsoft Azure Red Hat OpenShift hakkında sık sorulan sorular (SSS) yanıtlanmaktadır.

## <a name="installation-and-upgrade"></a>Yükleme ve yükseltme

### <a name="which-azure-regions-are-supported"></a>Hangi Azure bölgeleri destekleniyor?

Azure Red Hat OpenShift 4. x için desteklenen bölgelerin listesi için bkz. [available Region](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all).

Azure Red Hat OpenShift 3,11 için desteklenen bölgelerin listesi için bkz. [bölgelere göre kullanılabilir ürünler](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Hangi sanal makine boyutlarını kullanabilirim?

Azure Red Hat OpenShift 4 için desteklenen sanal makine boyutlarının listesi için bkz. [Azure Red Hat OpenShift 4 Için desteklenen kaynaklar](support-policies-v4.md).

Azure Red Hat OpenShift 3,11 için desteklenen sanal makine boyutlarının listesi için bkz. [Azure Red Hat openshift 3,11 Için desteklenen kaynaklar](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Bir Azure Red Hat OpenShift kümesinde maksimum sayıda Pod nedir?  Azure Red Hat OpenShift 'teki düğüm başına en fazla düğüm sayısı nedir?

Desteklenen yığınların gerçek sayısı, bir uygulamanın belleğine, CPU 'ya ve depolama gereksinimlerine bağlıdır.

Azure Red Hat OpenShift 4. x, 250 adet Pod-node sınırına ve 100 işlem düğümü sınırına sahiptir. Bu, bir kümede desteklenen maksimum sayıda Pod sayısını 250 &times; 100 = 25.000 olarak aşıyor.

Azure Red Hat OpenShift 3,11, 50 Pod-node sınırına ve 20 işlem düğümü sınırına sahiptir. Bu, bir kümede desteklenen maksimum sayıda Pod sayısını 50 &times; 20 = 1.000 olarak aşıyor.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Bir kümenin birden fazla Azure bölgesinde işlem düğümleri olabilir mi?

Hayır. Bir Azure Red Hat OpenShift kümesindeki tüm düğümler aynı Azure bölgesinde olmalıdır.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Bir küme, birden çok kullanılabilirlik bölgesine dağıtılabilir mi?

Evet. Kümeniz, kullanılabilirlik bölgelerini destekleyen bir Azure bölgesine dağıtılmışsa bu otomatik olarak gerçekleşir. Daha fazla bilgi için bkz. [kullanılabilirlik alanları](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Denetim düzlemi düğümleri Azure Kubernetes hizmeti (AKS) ile olduklarından soyut mı?

Hayır. Küme ana düğümleri dahil olmak üzere tüm kaynaklar müşteri aboneliğinizde çalışır. Bu tür kaynaklar salt okunurdur bir kaynak grubuna yerleştirilir.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Küme bir müşteri aboneliğinde mı var? 

Azure yönetilen uygulaması, bir kilitli kaynak grubunda Müşteri aboneliğiyle birlikte bulunur. Müşteriler bu kaynak grubundaki nesneleri görüntüleyebilir, ancak değiştiremez.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Azure Red Hat OpenShift 'te diğer müşterilerle paylaşılan bir öğe var mı? Ya da her şey bağımsız mi?

Her bir Azure Red Hat OpenShift kümesi, belirli bir müşteriye ayrılmıştır ve müşterinin aboneliği dahilinde bulunur. 

### <a name="are-infrastructure-nodes-available"></a>Altyapı düğümleri kullanılabilir mi?

Azure Red Hat OpenShift 4. x kümelerinde altyapı düğümleri Şu anda kullanılamıyor.

Azure Red Hat OpenShift 3,11 kümelerinde altyapı düğümleri varsayılan olarak dahil edilir.

## <a name="how-do-i-handle-cluster-upgrades"></a>Nasıl yaparım? küme yükseltmeleri mi?

Yükseltmeler, bakım ve desteklenen sürümler hakkında daha fazla bilgi için bkz. [destek yaşam döngüsü kılavuzu](support-lifecycle.md).

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Ana bilgisayar işletim sistemi ve OpenShift yazılımı nasıl güncelleştirilir?

Ana bilgisayar işletim sistemleri ve OpenShift yazılımı Azure Red Hat Openshıft, yukarı akış OpenShift kapsayıcı platformundan küçük yayın sürümlerini ve düzeltme eklerini tüketir.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Güncelleştirilmiş düğümü yeniden başlatma işlemi nedir?

Düğümler bir yükseltmenin parçası olarak yeniden başlatılır.

## <a name="cluster-operations"></a>Küm işlemleri

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Uygulamalarımı izlemek için Prometheus kullanabilir miyim?

Prometheus, Azure Red Hat OpenShift 4. x kümeleri için önceden yüklenmiş ve yapılandırılmış olarak sunulur. [Küme izleme](https://docs.openshift.com/container-platform/4.6/operators/operator_sdk/osdk-monitoring-prometheus.html)hakkında daha fazla bilgi edinin.

Azure Red Hat OpenShift 3,11 kümelerinde, ad alanınız içinde Prometheus 'ları dağıtabilir ve ad uzayındaki uygulamaları izleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Red Hat OpenShift kümesinde Prometheus örneğini dağıtma](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Küme durumu ve kapasitesine ilişkin ölçümleri izlemek için Prometheus kullanabilir miyim?

Azure Red Hat OpenShift 4. x: Evet.

Azure Red Hat Openshıft 3,11: No.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Temel alınan VM 'lerin günlükleri bir müşteri günlüğü analiz sistemine teslim edilebilir mi?

Temel VM 'lerdeki Günlükler yönetilen hizmet tarafından işlenir ve müşterilere gösterilmez.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Bir müşteri, düğüm düzeyindeki CPU/bellek gibi ölçümlere nasıl erişim alabilir, bu da ölçeklendirme, hata ayıklama sorunları vb. için işlem gerçekleştirebilir? Azure Red Hat OpenShift kümesinde kubectl üst öğesini çalıştıramıyorum.

Azure Red Hat OpenShift 4. x kümelerinde, OpenShift Web Konsolu düğüm düzeyindeki tüm ölçümleri içerir. Daha fazla bilgi için bkz. [küme bilgilerini görüntülemek](https://docs.openshift.com/container-platform/4.6/web_console/using-dashboard-to-get-cluster-information.html)Için Red Hat belgeleri.

Müşteriler, Azure Red Hat OpenShift 3,11 kümelerinde, komutunu kullanarak `oc adm top nodes` veya `kubectl top nodes` Müşteri-yönetici kümesi rolüyle, düğüm düzeyindeki CPU/bellek ölçümlerine erişebilirler. Müşteriler, veya komutuyla ilgili CPU/bellek ölçümlerine de erişebilir `pods` `oc adm top pods` `kubectl top pods` .

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Dağıtımı ölçeklendirebiliyoruz, Azure hata etki alanları, bir hizmetin tüm yığınlarını tek bir hata etki alanındaki bir hata nedeniyle Alta almaz hale getirmek için pod yerleştirme ile nasıl eşlenir?

Azure 'da sanal makine ölçek kümeleri kullanılırken varsayılan beş hata etki alanı vardır. Ölçek kümesindeki her bir sanal makine örneği, bu hata etki alanlarından birine yerleştirilir. Bu, bir kümedeki işlem düğümlerine dağıtılan uygulamaların ayrı hata etki alanlarına yerleştirilmelerini sağlar.

Daha fazla bilgi için bkz. [sanal makine ölçek kümesi için hata etki alanlarının doğru sayısını seçme](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Pod yerleşimini yönetmenin bir yolu var mı?

Müşteriler, düğüm alma ve etiketleri müşteri yöneticisi olarak görüntüleme imkanına sahiptir. Bu, ölçek kümesindeki tüm VM 'leri hedeflemek için bir yol sağlar.

Belirli Etiketler kullanılırken dikkatli olunmalıdır:

- Ana bilgisayar adı kullanılmamalıdır. Ana bilgisayar adı yükseltmeler ve güncelleştirmeler ile sık döndürülür ve değişiklik garanti edilir.
- Müşterinin belirli Etiketler veya bir dağıtım stratejisi için bir isteği varsa, bu gerçekleştirilebilir ancak mühendislik çabalarına ihtiyaç duyar ve bugün desteklenmez.

Daha fazla bilgi için bkz. [Pod yerleşimini denetleme](https://docs.openshift.com/container-platform/4.6/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Görüntü kayıt defteri dışarıdan kullanılabilir, bu nedenle Jenkins gibi araçları kullanabilir miyim?

4. x kümelerinde, güvenli bir kayıt defteri oluşturmanız ve kimlik doğrulamasının yapılandırılması gerekir. Daha fazla bilgi için aşağıdaki Red Hat belgelerine bakın:

- [Kayıt defteri gösterme](https://docs.openshift.com/container-platform/4.6/registry/securing-exposing-registry.html)
- [Kayıt defterine erişme](https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html)

3,11 kümesi için Docker görüntü kayıt defteri kullanılabilir. Docker kayıt defteri ' nden kullanılabilir `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` . Azure Container Registry de kullanabilirsiniz.

## <a name="networking"></a>Ağ

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Bir kümeyi var olan bir sanal ağa dağıtabilir miyim?

4. x kümelerinde, var olan bir sanal ağa bir küme dağıtabilirsiniz.

3,11 kümelerinde, bir kümeyi mevcut bir VNet 'e dağıtamazsınız. Eşleme yoluyla bir Azure Red Hat OpenShift 3,11 kümesini mevcut bir VNet 'e bağlayabilirsiniz.

### <a name="is-cross-namespace-networking-supported"></a>Çapraz ad alanı ağı destekleniyor mu?

Müşteri ve bireysel proje yöneticileri, nesneleri kullanarak proje başına temelinde ad alanı ağını (reddetme dahil) özelleştirebilir `NetworkPolicy` .

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Farklı bir abonelikteki sanal bir ağa eşdüzey olarak erişmeye çalışıyorum ancak VNet CıDR hatası alamadı.

Sanal ağı olan abonelikte, `Microsoft.ContainerService` sağlayıcıyı aşağıdaki komutla kaydettiğinizden emin olun: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Özel VNet üzerinde dağıtım için IP aralıklarını belirtebilir, eşlendikten sonra diğer kurumsal VNET 'ler ile çakışıyor önleyebilirsiniz.

4. x kümelerinde kendi IP aralıklarını belirtebilirsiniz.

3,11 kümelerinde Azure Red Hat Openshıft, VNet eşlemesini destekler ve müşterinin, OpenShift ağının çalışacağı VNET 'e ve VNet 'e VNet 'e sahip bir VNET sağlamasına izin verir.

Azure Red Hat OpenShift tarafından oluşturulan VNet korunacaktır ve yapılandırma değişikliklerini kabul etmez. Eşlenen VNet müşteri tarafından denetlenir ve aboneliklerinde bulunur.

### <a name="is-the-software-defined-network-module-configurable"></a>Yazılım tanımlı ağ modülü yapılandırılabilir mi?

Yazılım tanımlı ağ, `openshift-ovs-networkpolicy` ve yapılandırılamaz.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Azure Red Hat OpenShift tarafından hangi Azure yük dengeleyici kullanılır?  BT standart veya temel midir ve yapılandırılabilir mi?

Azure Red Hat OpenShift standart Azure Load Balancer kullanır ve yapılandırılamaz.

## <a name="permissions"></a>İzinler

### <a name="can-an-admin-manage-users-and-quotas"></a>Yönetici, kullanıcıları ve kotaları yönetebilir mi?

Evet. Azure Red Hat OpenShift Yöneticisi, Kullanıcı tarafından oluşturulan tüm projelere erişilmesine ek olarak kullanıcıları ve kotaları yönetebilir.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Bir kümeyi yalnızca belirli Azure AD kullanıcılarıyla kısıtlayabilir miyim?

Evet. Azure AD uygulamasını yapılandırarak, hangi Azure AD kullanıcılarının bir kümede oturum açmasını kısıtlayabilirsiniz. Ayrıntılar için bkz. [nasıl yapılır: uygulamanızı bir Kullanıcı kümesiyle kısıtlama](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>Kullanıcıların proje oluşturmasını kısıtlayabilirim miyim?

Evet. Kümenizde yönetici olarak oturum açın ve şu komutu yürütün:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Daha fazla bilgi için bkz. küme sürümünüz için kendi kendine sağlamayı devre dışı bırakma hakkında OpenShift belgeleri:

- [4,6 kümelerinde kendi kendine sağlamayı devre dışı bırakma](https://docs.openshift.com/container-platform/4.6/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [3,11 kümelerinde kendi kendine sağlamayı devre dışı bırakma](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Ana uygulamalar/Infra/uygulama düğümleri için hangi UNIX hakları (IaaS 'de) kullanılabilir?

4. x kümelerinde, düğüm erişimi, küme yönetici rolü aracılığıyla kullanılabilir. Daha fazla bilgi için bkz. [Kubernetes RBAC 'e genel bakış](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

3,11 kümelerinde, düğüm erişimi yasaktır.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Hangi OCP haklarına sahip olduğumuz? Küme Yöneticisi? Proje-yönetici?

4. x kümelerinde, küme yönetici rolü kullanılabilir. Daha fazla bilgi için bkz. [Kubernetes RBAC 'e genel bakış](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html).

3,11 kümelerinde daha fazla ayrıntı için bkz. [küme yönetimine genel bakış](https://docs.openshift.com/aro/admin_guide/index.html) .

### <a name="which-identity-providers-are-available"></a>Hangi kimlik sağlayıcıları kullanılabilir?

4. x kümelerinde kendi kimlik sağlayıcınızı yapılandırırsınız. Daha fazla bilgi için bkz. [kimlik sağlayıcılarını yapılandırma](https://docs.openshift.com/container-platform/4.6/authentication/identity_providers/configuring-ldap-identity-provider.html)hakkında Red Hat belgeleri.

3,11 kümesi için Azure AD tümleştirmesini kullanabilirsiniz. 

## <a name="storage"></a>Depolama

### <a name="is-data-on-my-cluster-encrypted"></a>Kümemdeki veriler şifrelendi mi?

Varsayılan olarak, veriler Rest 'de şifrelenir. Azure Storage platformu, verileri kalıcı yapmadan otomatik olarak şifreler ve almadan önce verilerin şifresini çözer. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama hizmeti şifrelemesi](../storage/common/storage-service-encryption.md).

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 'te şifrelenen, etcd 'de depolanan veriler mi?

Azure Red Hat OpenShift 4 kümelerinde, veriler varsayılan olarak şifrelenmez, ancak şifrelemeyi etkinleştirme seçeneğiniz vardır. Daha fazla bilgi için, bkz. [BCD şifreleme](https://docs.openshift.com/container-platform/4.6/security/encrypting-etcd.html)Kılavuzu.

3,11 kümesi için, veriler etcd düzeyinde şifrelenmez. Şifrelemeyi açma seçeneği şu anda desteklenmiyor. Openshıft bu özelliği destekler, ancak mühendislik çabalarının yol eşlemesinde olması gerekir. Veriler disk düzeyinde şifrelenir. Daha fazla bilgi için [veri deposu katmanındaki verileri şifreleme](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) bölümüne bakın.

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>OCS gibi kalıcı depolama çözümlerini seçebilir miyim? 

4. x kümelerinde, Azure disk (Premium_LRS) varsayılan depolama sınıfı olarak yapılandırılır. Ek depolama sağlayıcıları ve yapılandırma ayrıntıları için (Azure dosyası dahil), [kalıcı depolamada](https://docs.openshift.com/container-platform/4.6/storage/understanding-persistent-storage.html)Red Hat belgelerine bakın.

3,11 kümesi için, varsayılan olarak iki depolama sınıfı sağlanır: bir Azure disk (Premium_LRS) ve bir Azure dosyası için.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>ARO, tüm müşteri verilerini kümenin bölgesi dışında depolar mi?

Hayır. Bir ARO kümesinde oluşturulan tüm veriler kümenin bölgesi içinde tutulur.

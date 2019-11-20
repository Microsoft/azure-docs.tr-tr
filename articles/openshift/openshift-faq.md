---
title: Azure Red Hat OpenShift için sık sorulan sorular | Microsoft Docs
description: Red Hat OpenShift Microsoft Azure hakkında sık sorulan soruların yanıtları aşağıda verilmiştir
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d8707e2edccf144cbe58a530bcfe2c176e656915
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582410"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift SSS

Bu makalede, Microsoft Azure Red Hat OpenShift hakkında sık sorulan sorular (SSS) ele alınmaktadır.

## <a name="which-azure-regions-are-supported"></a>Hangi Azure bölgeleri destekleniyor?

Azure Red Hat OpenShift 'in desteklendiği genel bölgelerin listesi için bkz. [desteklenen kaynaklar](supported-resources.md#azure-regions) .

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Bir kümeyi var olan bir sanal ağa dağıtabilir miyim?

Hayır. Ancak, eşleme yoluyla bir Azure Red Hat OpenShift kümesini mevcut bir VNET 'e bağlayabilirsiniz. Ayrıntılar için bkz. [bir kümenin sanal ağını mevcut bir sanal ağa bağlama](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="what-cluster-operations-are-available"></a>Hangi küme işlemleri kullanılabilir?

Yalnızca işlem düğümleri sayısını ölçeklendirebilir veya azaltabilirsiniz. Oluşturulduktan sonra `Microsoft.ContainerService/openShiftManagedClusters` kaynağına başka değişikliklere izin verilmez. En fazla işlem düğümü sayısı 20 ile sınırlıdır.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Hangi sanal makine boyutlarını kullanabilirim?

Azure Red Hat OpenShift kümesiyle kullanabileceğiniz sanal makine boyutlarının listesi için bkz. [Azure Red Hat OpenShift sanal makine boyutları](supported-resources.md#virtual-machine-sizes) .

## <a name="is-data-on-my-cluster-encrypted"></a>Kümemdeki veriler şifrelendi mi?

Varsayılan olarak, bekleyen şifreleme vardır. Azure Storage platformu, verileri kalıcı yapmadan otomatik olarak şifreler ve almadan önce verilerin şifresini çözer. Ayrıntılar için bkz. [bekleyen veri Için Azure depolama hizmeti şifrelemesi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) .

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Uygulamalarımı izlemek için Prometheus/Grafana kullanabilir miyim?

Evet, ad alanınız içinde Prometheus dağıtabilir ve ad uzayındaki uygulamaları izleyebilirsiniz.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Prometheus/Grafana kullanarak küme durumu ve kapasitesine ilişkin ölçümleri izleyebilir miyim?

Hayır, geçerli zamanda değil.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Jenkins gibi araçları kullanabilmeniz için Docker kayıt defteri dışarıdan kullanılabilir mi?

Docker kayıt defterinin `https://docker-registry.apps.<clustername>.<region>.azmosa.io/`, ancak güçlü bir depolama dayanıklılık garantisi sağlanmaz. [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)de kullanabilirsiniz.

## <a name="is-cross-namespace-networking-supported"></a>Çapraz ad alanı ağı destekleniyor mu?

Müşteri ve bireysel proje yöneticileri, `NetworkPolicy` nesneleri kullanarak her proje için çapraz ad alanı ağını (reddetme dahil) özelleştirebilir.

## <a name="can-an-admin-manage-users-and-quotas"></a>Yönetici, kullanıcıları ve kotaları yönetebilir mi?

Evet. Azure Red Hat OpenShift Yöneticisi, Kullanıcı tarafından oluşturulan tüm projelere erişilmesine ek olarak kullanıcıları ve kotaları yönetebilir.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Bir kümeyi yalnızca belirli Azure AD kullanıcılarıyla kısıtlayabilir miyim?

Evet. Azure AD uygulamasını yapılandırarak, hangi Azure AD kullanıcılarının bir kümede oturum açmasını kısıtlayabilirsiniz. Ayrıntılar için bkz [. nasıl yapılır: uygulamanızı bir Kullanıcı kümesiyle kısıtlama](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Bir kümenin birden fazla Azure bölgesinde işlem düğümleri olabilir mi?

Hayır. Bir Azure Red Hat OpenShift kümesindeki tüm düğümler aynı Azure bölgesinden kaynaklanmalıdır.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Ana ve altyapı düğümleri Azure Kubernetes hizmeti (AKS) ile çalıştıkları için soyut mı?

Hayır. Küme Yöneticisi de dahil olmak üzere tüm kaynaklar müşteri aboneliğinizde çalışır. Bu tür kaynaklar salt okunurdur bir kaynak grubuna yerleştirilir.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Azure için açık Hizmet Aracısı (OSBA) destekleniyor mu?

Evet. OSBA 'yi Azure Red Hat OpenShift ile birlikte kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure için hizmet Aracısı açma](https://github.com/Azure/open-service-broker-azure#openshift-project-template) .

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Farklı bir abonelikte bir sanal ağa eşdüzey ve `Failed to get vnet CIDR` hatası alma girişimi yaşıyorum.

Sanal ağı olan abonelikte `Microsoft.ContainerService` sağlayıcısını `az provider register -n Microsoft.ContainerService --wait` ile kaydettiğinizden emin olun 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Azure Red Hat OpenShift (ARO) bakım işlemi nedir?

ARO: yükseltmeler, etcd verilerinin yedeklenmesi ve geri yüklenmesi ve bulut sağlayıcısının başlattığı bakım için üç tür bakım vardır.

+ Yükseltmeler yazılım yükseltmeleri ve Cvileri içerir. CVE düzeltmesi, `yum update` çalıştırılarak ve anında risk azaltma için sağlanarak oluşur.  Paralel olarak, gelecekteki küme oluşturmak için yeni bir görüntü derlemesi oluşturulacaktır.

+ Etcd verilerinin yedeklenmesi ve yönetimi, eyleme bağlı olarak küme kapalı kalma süresi gerektirebilecek otomatikleştirilmiş bir işlemdir. Etcd veritabanı bir yedekten geri yüklenirse kapalı kalma süresi olacaktır. Etcd 'yi her saat yedekliyoruz ve son 6 saat yedeklemenin tutulması önerilir.

+ Bulut sağlayıcısı tarafından başlatılan bakımda ağ, depolama ve bölgesel kesintiler bulunur. Bakım, bulut sağlayıcısına bağımlıdır ve sağlayıcı tarafından sağlanan güncelleştirmelere dayanır.

## <a name="what-is-the-general-upgrade-process"></a>Genel yükseltme işlemi nedir?

Bir yükseltmenin çalıştırılması, çalıştırmak için güvenli bir işlem olmalıdır ve küme hizmetlerini kesintiye uğramamalıdır. SRE, yeni sürümler kullanılabilir olduğunda veya cele alırken yükseltme işlemini tetikleyebilirler.

Kullanılabilir güncelleştirmeler bir aşama ortamında test edilir ve ardından üretim kümelerine uygulanır. Uygulandığında, yeni bir düğüm geçici olarak eklenir ve düğümler çoğaltma sayısını koruyacak şekilde bir döndürme olarak güncelleştirilir. Aşağıdaki en iyi yöntemler, kesinti olmaması için en az düzeyde güvence sağlanmasına yardımcı

Bekleyen yükseltmenin veya güncelleştirmenin önem derecesine bağlı olarak, işlemin hizmetin bir CVE 'ın etkilenme olasılığını azaltmak için hızlı bir şekilde uygulanabileceğini fark edebilirsiniz. Yeni bir görüntü, zaman uyumsuz, test edilmiş ve küme yükseltmesi olarak kullanıma alınmış olarak oluşturulur. Bunun dışında, acil durum ve planlı bakım arasında fark yoktur. Planlı bakım, müşteriyle birlikte önceden kullanılamaz.

Müşteri iletişimi gerekliyse, bildirimler ICM ve e-posta aracılığıyla gönderilebilir.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Acil durum ile planlanmış bakım pencereleri hakkında ne olacak?

İki tür bakım arasında ayrım yapmıyoruz. Takımlarımız 24/7/365 kullanılabilir ve geleneksel olarak zamanlanan "saat dışı" bakım pencerelerini kullanmıyor.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>İşletim sistemi ve OpenShift yazılımı nasıl güncelleştirilir?

Ana bilgisayar işletim sistemi ve OpenShift yazılımı, genel yükseltme ve görüntü oluşturma sürecimiz aracılığıyla güncelleştirilir.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Güncelleştirilmiş düğümü yeniden başlatma işlemi nedir?

Bu, yükseltmenin bir parçası olarak işlenmelidir.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Veriler, ARO 'da şifreli olarak depolanmış mı?

Etcd düzeyinde şifrelenmez. Etkinleştirme seçeneği şu anda desteklenmiyor. Openshıft bu özelliği destekler, ancak mühendislik çabalarının yol eşlemesinde olması gerekir. Veriler disk düzeyinde şifrelenir. Daha fazla bilgi için [veri deposu katmanındaki verileri şifreleme](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) bölümüne bakın.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Temel alınan VM 'lerin günlükleri bir müşteri günlüğü analiz sistemine teslim edilebilir mi?

Syslog, Docker günlükleri, Journal ve dmesg, yönetilen hizmet tarafından işlenir ve müşterilere gösterilmez.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Bir müşteri, düğüm düzeyindeki CPU/bellek gibi ölçümlere nasıl erişim alabilir, bu da ölçeklendirme, hata ayıklama sorunları vb. için işlem gerçekleştirebilir. Bir ARO kümesinde `kubectl top` çalıştıramıyorum.

`kubectl top` Red Hat OpenShift üzerinde kullanılamaz. Bu, OpenShift izleme yığınına dahil olmayan, her ikisi de Heapster (kullanım dışı) veya ölçüm-sunucu (ınubating veya Alpha) için bir yedekleme ölçümleri kaynağı gerektirir.

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>ARO için varsayılan Pod Zamanlayıcı Yapılandırması nedir?

ARO, OpenShift 'te gelen varsayılan zamanlayıcıyı kullanır. ARO 'de desteklenmeyen birkaç ek mekanizma vardır. Daha fazla ayrıntı için [varsayılan Zamanlayıcı belgeleri](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) ve [ana Zamanlayıcı belgelerine](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) bakın.

Gelişmiş/özel zamanlama Şu anda desteklenmiyor. Daha fazla ayrıntı için [zamanlama belgelerine](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) bakın.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Dağıtımı ölçeklendirebiliyoruz, Azure hata etki alanları, bir hizmetin tüm yığınlarını tek bir hata etki alanındaki bir hata nedeniyle Alta almaz hale getirmek için pod yerleştirme ile nasıl eşlenir?

Azure 'da sanal makine ölçek kümeleri kullanılırken varsayılan beş hata etki alanı vardır. Ölçek kümesindeki her bir sanal makine örneği, bu hata etki alanlarından birine yerleştirilir. Bu, bir kümedeki işlem düğümlerine dağıtılan uygulamaların ayrı hata etki alanlarına yerleştirilmelerini sağlar.

Daha ayrıntılı bilgi için [sanal makine ölçek kümesi için hata etki alanlarının doğru sayısını seçme](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) bölümüne bakın.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Pod yerleşimini yönetmenin bir yolu var mı?

Yaklaşan müşteri yönetici güncelleştirmesiyle, müşteriler düğümleri alma ve etiketleri görüntüleme yeteneğine sahip olur.  Bu, ölçek kümesindeki tüm VM 'leri hedeflemek için bir yol sağlar.

Belirli Etiketler kullanılırken dikkatli olunmalıdır:

- Ana bilgisayar adı kullanılmamalıdır. Ana bilgisayar adı yükseltmeler ve güncelleştirmeler ile sık döndürülür ve değişiklik garanti edilir.

- Müşterinin belirli Etiketler veya bir dağıtım stratejisi için bir isteği varsa, bu gerçekleştirilebilir ancak mühendislik çabalarına ihtiyaç duyar ve bugün desteklenmez.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Bir Aro kümesindeki maksimum sayıda Pod nedir?  ARO 'daki düğüm başına en fazla düğüm sayısı nedir?

Daha fazla ayrıntı için [yukarı akış OpenShift docs](https://docs.openshift.com/container-platform/3.11/scaling_performance/cluster_limits.html#scaling-performance-current-cluster-limits) bölümüne bakın. Red Hat OpenShift 3,11 ' de 250-Pod/node sınırı vardır. bu sayede, Aro kümesinde desteklenen maksimum sayıda Pod 'nin 250 * 20 = 5000 [olması gerekir.](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Özel VNET üzerinde dağıtım için IP aralıklarını belirtebilir, eşlendikten sonra diğer kurumsal VNET 'ler ile çakışıyor önleyebilirsiniz.

Azure Red Hat OpenShift, VNET eşlemesini destekler ve müşterinin, OpenShift ağının çalışacağı VNET 'e ve sanal ağa VNET sağlamasına izin verir.

ARO tarafından oluşturulan VNET korunacaktır ve yapılandırma değişikliklerini kabul etmez. Eşlenen VNET müşteri tarafından denetlenir ve aboneliklerinde bulunur.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Küme bir müşteri aboneliğinde mı var? 

Azure yönetilen uygulaması, bir kilitli kaynak grubunda Müşteri aboneliğiyle birlikte bulunur. Müşteri, söz konusu RG 'de nesneleri görüntüleyebilir ancak değiştiremez.

## <a name="is-the-sdn-module-configurable"></a>SDN modülü yapılandırılabilir mi?

SDN, OpenShift-OVS-networkpolicy ve yapılandırılamaz.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Ana uygulamalar/Infra/uygulama düğümleri için hangi UNIX hakları (IaaS 'de) kullanılabilir?

Bu teklif için geçerli değildir. Düğüm erişimi yasak.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Hangi OCP haklarına sahip olduğumuz? Küme Yöneticisi? Proje-yönetici?

Ayrıntılar için bkz. Azure Red Hat OpenShift [küme yönetimine genel bakış](https://docs.openshift.com/aro/admin_guide/index.html).

## <a name="which-kind-of-federation-with-ldap"></a>LDAP ile hangi tür Federasyon?

Bu, Azure AD tümleştirmesi aracılığıyla elde edilir. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Diğer müşterilerle Paylaşılanlar içinde herhangi bir öğe var mı? Ya da her şey bağımsız mi?

Her bir Azure Red Hat OpenShift kümesi, belirli bir müşteriye ayrılmıştır ve müşterinin aboneliği dahilinde bulunur. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>OCS gibi kalıcı depolama çözümlerini seçebilir miyim? 

Seçebileceğiniz iki depolama sınıfı vardır: Azure disk ve Azure dosyası.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Küme nasıl güncelleştirilir (güvenlik açıklarına bağlı olarak Majors ve sömürmeyi amaçlama dahil)?

Bkz [. genel yükseltme işlemi nedir?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>ARO tarafından hangi Azure yük dengeleyici kullanılır?  BT standart veya temel midir ve yapılandırılabilir mi?

ARO standart Azure Load Balancer kullanır ve yapılandırılamaz.

## <a name="can-aro-use-netapp-based-storage"></a>ARO, NetApp tabanlı depolama kullanabilir mi?

Şu anda yalnızca Azure disk ve Azure dosya depolama sınıfları desteklenen depolama seçenekleridir. 



---
title: Azure Kubernetes hizmeti (AKS) için sık sorulan sorular
description: Azure Kubernetes hizmeti (AKS) ile ilgili bazı yaygın soruların yanıtlarını bulun.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: references_regions
ms.openlocfilehash: f13d7a33ce1dc04700932072fe0af80a901c681f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783206"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) hakkında sık sorulan sorular

Bu makalede, Azure Kubernetes hizmeti (AKS) hakkında sık sorulan sorular ele alınmaktadır.

## <a name="which-azure-regions-currently-provide-aks"></a>Hangi Azure bölgeleri Şu anda AKS sağlıyor?

Kullanılabilir bölgelerin tüm listesi için bkz. [aks bölgeleri ve kullanılabilirliği][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Bir AKS kümesini bölgeler arasında yayılabilir miyim?

Hayır. AKS kümeleri bölgesel kaynaklardır ve bölgelere yayılamaz. Birden çok bölge içeren bir mimari oluşturma hakkında yönergeler için bkz. [iş sürekliliği ve olağanüstü durum kurtarma için en iyi uygulamalar][bcdr-bestpractices] .

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Kullanılabilirlik alanları arasında bir AKS kümesini yayı yapabilir miyim?

Evet. Bir AKS kümesini, [bunları destekleyen bölgelerde][az-regions]bir veya daha fazla [kullanılabilirlik][availability-zones] alanında dağıtabilirsiniz.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kubernetes API sunucusuna kimlerin erişebileceğini sınırlayabilir miyim?

Evet. API sunucusuna erişimi kısıtlamak için iki seçenek vardır:

- API sunucusu için genel bir uç nokta sürdürmek istiyorsanız ancak güvenilen bir IP aralığı kümesine erişimi kısıtlamak istiyorsanız, [API sunucusu YETKILENDIRILMIŞ IP aralıklarını][api-server-authorized-ip-ranges] kullanın.
- API sunucusunu *yalnızca* sanal ağınızın içinden erişilebilir olacak şekilde sınırlamak istiyorsanız [özel bir küme][private-clusters] kullanın.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Tek bir kümede farklı VM boyutlarına sahip olabilir miyim?

Evet, [birden çok düğüm havuzu][multi-node-pools]oluşturarak aks kümenizde farklı sanal makine boyutları kullanabilirsiniz.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Güvenlik güncelleştirmeleri AKS aracı düğümlerine uygulandı mi?

Azure, bir gecelik zamanlamaya göre kümenizdeki Linux düğümlerine otomatik olarak güvenlik düzeltme ekleri uygular. Ancak, bu Linux düğümlerinin gerektiği şekilde yeniden başlatılmasını sağlamaya sorumlusunuz. Düğümleri yeniden başlatmak için çeşitli seçenekleriniz vardır:

- Azure portal veya Azure CLı aracılığıyla el ile.
- AKS kümenizi yükselterek. Küme yükseltmeleri, [düğümleri][cordon-drain] otomatik olarak kaldırır ve yeni bir düğümü en son Ubuntu görüntüsü ve yeni bir yama sürümü ya da bir Ikincil Kubernetes sürümü ile çevrimiçi duruma getirir. Daha fazla bilgi için bkz. [AKS kümesini yükseltme][aks-upgrade].
- [Node Image Upgrade](node-image-upgrade.md)kullanılarak.

### <a name="windows-server-nodes"></a>Windows Server düğümleri

Windows Server düğümleri için Windows Update otomatik olarak çalıştırılmaz ve en son güncelleştirmeleri uygulamaz. Windows Update yayın döngüsünün ve kendi doğrulama işleminizin etrafında düzenli bir zamanlamaya göre, küme üzerinde ve AKS kümenizdeki Windows Server düğüm havuzunda bir yükseltme gerçekleştirmeniz gerekir. Bu yükseltme işlemi, en son Windows Server görüntüsünü ve düzeltme eklerini çalıştıran düğümleri oluşturur, daha sonra eski düğümleri kaldırır. Bu işlemle ilgili daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS ile neden iki kaynak grubu oluşturulur?

AKS, sanal makine ölçek kümeleri, sanal ağlar ve yönetilen diskler dahil olmak üzere bir dizi Azure altyapı kaynağı üzerinde oluşturulur. Bu, AKS tarafından sunulan yönetilen Kubernetes ortamında Azure platformunun birçok temel özelliği avantajlarından yararlanmanızı sağlar. Örneğin, çoğu Azure sanal makine türü doğrudan AKS ile kullanılabilir ve Azure ayırmaları bu kaynaklara otomatik olarak iskontolar almak için kullanılabilir.

Bu mimariyi etkinleştirmek için, her bir AKS dağıtımı iki kaynak grubuna yaymıştır:

1. İlk kaynak grubunu oluşturursunuz. Bu grup yalnızca Kubernetes hizmet kaynağını içerir. AKS kaynak sağlayıcısı, dağıtım sırasında ikinci kaynak grubunu otomatik olarak oluşturur. İkinci kaynak grubuna bir örnek *MC_myResourceGroup_myAKSCluster_eastus*. Bu ikinci kaynak grubunun adını belirtme hakkında daha fazla bilgi için sonraki bölüme bakın.
1. *Düğüm kaynak grubu* olarak bilinen ikinci kaynak grubu, kümeyle ilişkili tüm altyapı kaynaklarını içerir. Bu kaynaklar, Kubernetes düğümü VM 'Leri, sanal ağ ve depolamayı içerir. Varsayılan olarak, düğüm kaynak grubunun *MC_myResourceGroup_myAKSCluster_eastus* gibi bir adı vardır. AKS, küme her silindiğinde düğüm kaynağını otomatik olarak siler, bu nedenle yalnızca kümenin yaşam döngüsünü paylaşan kaynaklar için kullanılmalıdır.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>AKS düğümü kaynak grubu için kendi adını verebilir miyim?

Evet. Varsayılan olarak, AKS, düğüm kaynak grubunu *MC_resourcegroupname_clustername_location* olarak adlandırın, ancak kendi adınızı de sağlayabilirsiniz.

Kendi kaynak grubu adınızı belirtmek için, [aks-Preview][aks-preview-cli] Azure CLI uzantısı sürüm *0.3.2* veya üstünü yüklemelisiniz. [Az aks Create][az-aks-create] komutunu kullanarak bir aks kümesi oluşturduğunuzda, `--node-resource-group` parametresini kullanın ve kaynak grubu için bir ad belirtin. AKS kümesi dağıtmak için [bir Azure Resource Manager şablonu kullanırsanız][aks-rm-template] , *Noderesourcegroup* özelliğini kullanarak kaynak grubu adını tanımlayabilirsiniz.

* İkincil kaynak grubu, kendi aboneliğinizde Azure Kaynak sağlayıcısı tarafından otomatik olarak oluşturulur.
* Yalnızca kümeyi oluştururken özel bir kaynak grubu adı belirtebilirsiniz.

Düğüm kaynak grubuyla çalışırken şunları yapamazsınız:

* Düğüm kaynak grubu için mevcut bir kaynak grubu belirtin.
* Düğüm kaynak grubu için farklı bir abonelik belirtin.
* Küme oluşturulduktan sonra düğüm kaynak grubu adını değiştirin.
* Düğüm kaynak grubu içindeki yönetilen kaynakların adlarını belirtin.
* Düğüm kaynak grubu içinde yönetilen kaynakların Azure tarafından oluşturulan etiketlerini değiştirin veya silin. (Sonraki bölümde daha fazla bilgi için bkz.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Düğüm kaynak grubundaki AKS kaynaklarının etiketlerini ve diğer özelliklerini değiştirebilir miyim?

Düğüm kaynak grubundaki Azure tarafından oluşturulan etiketleri ve diğer kaynak özelliklerini değiştirir veya silerseniz, hataları ölçekleme ve yükseltme gibi beklenmedik sonuçlara ulaşabilirsiniz. AKS, son kullanıcılar tarafından oluşturulan özel etiketler oluşturup değiştirmenize olanak sağlar ve [düğüm havuzu oluştururken](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)bu etiketleri ekleyebilirsiniz. Örneğin, bir iş birimi veya maliyet merkezi atamak için özel etiketler oluşturmak veya değiştirmek isteyebilirsiniz. Bu Ayrıca, yönetilen kaynak grubundaki bir kapsamla Azure Ilkeleri oluşturularak elde edilebilir.

Ancak, AKS kümesindeki düğüm kaynak grubu altındaki kaynaklarda bulunan **Azure tarafından oluşturulan etiketlerin** değiştirilmesi, hizmet düzeyi HEDEFINI (SLO) kesen desteklenmeyen bir eylemdir. Daha fazla bilgi için bkz. [AKS bir hizmet düzeyi sözleşmesi sunuyor mu?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Kubernetes giriş denetleyicileri AKS 'i destekliyor? Giriş denetleyicileri eklenebilir mi, kaldırılabilir mi?

AKS aşağıdaki [giriş denetleyicilerini][admission-controllers]destekler:

- *Namespaceyaşam döngüsü*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *Defaulttoleranationseconds*
- *Değiştirici Tingadmissionweb kancası*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *Pod Nodeselector*
- *Pod Toleranationrestriction*
- *Extendedresourcetoleranation*

Şu anda AKS 'de giriş denetleyicileri listesini değiştiremezsiniz.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>AKS 'de giriş denetleyicisi Web kancalarını kullanabilir miyim?

Evet, AKS üzerinde giriş denetleyicisi Web kancalarını kullanabilirsiniz. **Denetim düzlemi etiketiyle** işaretlenen iç aks ad alanlarını dışlayamazsınız önerilir. Örneğin, aşağıdaki Web kancası yapılandırmasına ekleyerek:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

Erişim Denetleyicisi Web kancalarınızın küme içinden erişilebilir olması için, API sunucusu çıkışı ile güvenlik duvarlarını AKS.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Erişim Denetleyicisi Web kancaları, kuin sistemi ve iç AKS ad alanlarını etkileyebilir mi?

Sistemin kararlılığını korumak ve özel giriş denetleyicilerinin kuin sisteminde iç Hizmetleri etkilemeleri önlemesini engellemek için, ad alanı AKS 'leri, kuin sistemi ve AKS iç ad alanlarını otomatik olarak dışlayan bir **Admissions Enforcer** içerir. Bu hizmet, özel giriş denetleyicilerinin kuin sisteminde çalışan hizmetleri etkilememesini sağlar.

Özel giriş Web kancasının kapsamında olmasını istediğiniz bir şeyin Kuto sistemine dağıtılması için kritik bir kullanım örneğine sahipseniz, Admissions Enforcer bunu yok sayabilmesi için aşağıdaki etiketi veya ek açıklamayı ekleyebilirsiniz.

Etiket: ```"admissions.enforcer/disabled": "true"``` veya ek açıklama: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS ile tümleşik Azure Key Vault mı?

AKS Şu anda Azure Key Vault ile yerel olarak tümleştirilmiştir. Ancak, [CSI Gizli dizileri için Azure Key Vault sağlayıcısı][csi-driver] , Kubernetes Pod 'den Key Vault gizli anahtarlara doğrudan tümleştirmeyi sağlar.

## <a name="can-i-run-windows-server-containers-on-aks"></a>AKS üzerinde Windows Server kapsayıcıları çalıştırabilir miyim?

Evet, Windows Server kapsayıcıları AKS 'de kullanılabilir. AKS 'de Windows Server kapsayıcıları çalıştırmak için, Konuk işletim sistemi olarak Windows Server çalıştıran bir düğüm havuzu oluşturursunuz. Windows Server kapsayıcıları yalnızca Windows Server 2019 kullanabilir. Başlamak için bkz. [Windows Server düğüm havuzu Ile AKS kümesi oluşturma][aks-windows-cli].

Düğüm havuzu için Windows Server desteği, Kubernetes projesinde yukarı akış Windows Server 'ın bir parçası olan bazı sınırlamalar içerir. Bu sınırlamalar hakkında daha fazla bilgi için bkz. [AKS kısıtlamalarında Windows Server kapsayıcıları][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS bir hizmet düzeyi sözleşmesi sunuyor mu?

AKS, [çalışma süresi SLA 'sı][uptime-sla]ile isteğe bağlı bir eklenti ÖZELLIĞI olarak SLA garantisi sağlar. 

Varsayılan olarak sunulan ücretsiz SKU, ilişkili bir hizmet düzeyi *anlaşmasına* sahip değildir, ancak% 99,5 hizmet düzeyi *amacını* içerir. Geçici bağlantı sorunlarının, yükseltmeler, sağlıksız olmayan düğümler, platform bakımı, uygulama API sunucusunu isteklerle, vb. olarak ortaya çıkmasına neden olabilir. İş yükünüz API sunucusu yeniden başlatmalarının kabul etmezse, çalışma süresi SLA 'sını kullanmayı öneririz.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>AKS aracı düğümlerine Azure rezervasyon indirimleri uygulayabilir miyim?

AKS aracı düğümleri standart Azure sanal makineleri olarak faturalandırılır, bu nedenle AKS 'de kullandığınız VM boyutu için [Azure ayırmaları][reservation-discounts] satın aldıysanız, bu indirimler otomatik olarak uygulanır.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kümemi Azure kiracılar arasında taşıyabilir/geçirebilir miyim?

AKS kümenizi kiracılar arasında taşımak Şu anda desteklenmiyor.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Abonelikler arasında kümemi taşıyabilir/geçirebilir miyim?

Abonelikler arasında küme hareketi şu anda desteklenmiyor.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>AKS kümelerimi geçerli Azure aboneliğinden diğerine taşıyabilir miyim?

AKS kümenizi ve ilişkili kaynaklarını Azure abonelikleri arasında taşıma desteklenmez.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>AKS kümemi veya AKS altyapı kaynaklarından diğer kaynak gruplarına taşıyabilir veya onları yeniden adlandırabilir miyim?

AKS kümenizi ve ilişkili kaynaklarını taşıma veya yeniden adlandırma desteklenmez.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Kümemin neden bu kadar uzun sürüyor?

Çoğu küme Kullanıcı isteğiyle silinir; Bazı durumlarda, özellikle de müşteriler kendi kaynak grubunu getiriyor ya da faaliyetsiz görevler silme işlemleri ek zaman alabilir veya başarısız olabilir. Silme işlemiyle ilgili bir sorununuz varsa, RG üzerinde kilit olmadığından, RG 'nin her türlü kaynağın bir RG ile ilişkisi olduğunu ve bu şekilde devam edip etmez.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>' Nodekaybedildi ' veya ' bilinmiyor ' durumunda Pod/dağıtımlar varsa, kümemi yükseltebilirim?

Ancak, bu, AKS bunun için önerilmez. Kümenin durumu bilindiğinde ve sağlıklı olduğunda yükseltmeler gerçekleştirilmelidir.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Sağlıksız bir durumda bir veya daha fazla düğümü olan bir kümeniz varsa veya bilgisayarı kapatırsanız, bir yükseltme gerçekleştirebilir miyim?

Hayır, başarısız durumundaki düğümleri silin veya yükseltmeden önce kümeden kaldırın.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Bir küme silme çalıştırdım, ancak hataya bakın `[Errno 11001] getaddrinfo failed`

En yaygın olarak, bunun nedeni bir veya daha fazla ağ güvenlik grubu (NSG) hala kullanımda ve kümeyle ilişkili olan kullanıcılardır.  Bunları kaldırın ve silmeyi yeniden deneyin.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Bir yükseltme çalıştırdım, ancak şu anda My Pod çökme döngülerinde ve hazırlık araştırmaları başarısız oldu mu?

Hizmet sorumlunun süresinin dolmadığından emin olun.  Bkz: [aks hizmet sorumlusu](./kubernetes-service-principal.md) ve [aks güncelleştirme kimlik bilgileri](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Kümem çalışıyor, ancak aniden LoadBalancers, bağlama PVC 'leri vb. sağlayamaz.

Hizmet sorumlunun süresinin dolmadığından emin olun.  Bkz: [aks hizmet sorumlusu](./kubernetes-service-principal.md)  ve [aks güncelleştirme kimlik bilgileri](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>AKS kümemi sıfıra ölçeklendirebiliyor miyim?
[Çalışan BIR AKS kümesini tümüyle durdurabilir](start-stop-cluster.md)ve ilgili işlem maliyetlerine kaydedebilirsiniz. Ayrıca, yalnızca gerekli küme yapılandırmasını korumak üzere [tüm veya belirli `User` düğüm havuzlarını ölçeklendirebilir veya otomatik ölçeklendirme](scale-cluster.md#scale-user-node-pools-to-0) seçeneğini de belirleyebilirsiniz.
[Sistem düğüm havuzlarını](use-system-pools.md) doğrudan sıfıra ölçeklendirebilirsiniz.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Sanal makine ölçek kümesi API 'Lerini el ile ölçeklendirmek için kullanabilir miyim?

Hayır, sanal makine ölçek kümesi API 'Lerini kullanarak ölçeklendirme işlemleri desteklenmez. AKS API 'Lerini ( `az aks scale` ) kullanın.

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Sanal makine ölçek kümelerini, sıfır düğümlere el ile ölçeklendirmek için kullanabilir miyim?

Hayır, sanal makine ölçek kümesi API 'Lerini kullanarak ölçeklendirme işlemleri desteklenmez. AKS API 'sini kullanarak, sistem dışı düğüm havuzlarını sıfıra ölçeklendirebilir veya bunun yerine [kümenizi durdurabilirsiniz](start-stop-cluster.md) .

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Tüm VM 'lerimi durdurabilir veya serbest bırakabilirsiniz miyim?

AKS, bu tür bir yapılandırmaya ve kurtarmaya yönelik esnekliği mekanizmalarına sahip olsa da, bu desteklenen bir yapılandırma değildir. Bunun yerine [kümenizi durdurun](start-stop-cluster.md) .

## <a name="can-i-use-custom-vm-extensions"></a>Özel VM uzantılarını kullanabilir miyim?

Log Analytics Aracısı Microsoft tarafından yönetilen bir uzantı olduğundan desteklenir. Aksi takdirde, AKS yönetilen bir hizmettir ve IaaS kaynaklarını düzenleme desteklenmez. Özel bileşenleri yüklemek için Kubernetes API 'Lerini ve mekanizmalarını kullanın. Örneğin, gerekli bileşenleri yüklemek için DaemonSets kullanın.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>AKS, tüm müşteri verilerini kümenin bölgesi dışında depolıyor mu?

Müşteri verilerinin tek bir bölgede depolanmasını etkinleştirme özelliği şu anda yalnızca Asya Pasifik coğrafi ve Brezilya Güney (Sao Paulo Eyaleti) Brezilya coğrafi bölge bölgesinin Güneydoğu Asya bölgesinde (Singapur) kullanılabilir. Diğer tüm bölgeler için müşteri verileri coğrafi olarak depolanır.

## <a name="are-aks-images-required-to-run-as-root"></a>AKS görüntüleri kök olarak çalıştırmak için gerekli mi?

Aşağıdaki iki görüntü dışında, AKS görüntülerinin kök olarak çalıştırılması gerekmez:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Azure CNı saydam modu ile Köprü modu nedir?

V 1.2.0 Azure CNı 'den, tek kiracılı Linux CNı dağıtımları için varsayılan olarak saydam modu olacaktır. Saydam mod Köprü modunu değiştiriyor. Bu bölümde, her iki mod hakkındaki farklar ve Azure CNı 'de saydam modu kullanmanın avantajları/sınırlamaları hakkında daha fazla bilgi vereceğiz.

### <a name="bridge-mode"></a>Köprü modu

Adından da anlaşılacağı gibi, "tam zamanında" bir şekilde köprü modu Azure CNı, "azure0" adlı bir L2 köprü oluşturacak. Tüm konak tarafı Pod `veth` çifti arabirimleri bu köprüye bağlanacak. Bu nedenle VM içi iletişim Pod-Pod ve kalan trafik bu köprüden geçer. Söz konusu köprü, bir veya daha fazla gerçek cihazı bir veya daha fazla gerçek cihaz bağlamadığınız takdirde hiçbir şey alamıyor veya iletemediği bir katman 2 sanal aygıtıdır. Bu nedenle, Linux VM 'nin eth0 "azure0" köprüsüne bir alt öğesine dönüştürülmesi gerekir. Bu, Linux sanal makinesi içinde karmaşık bir ağ topolojisi oluşturur ve bir belirti olarak, DNS sunucusu güncelleştirmesi gibi diğer ağ işlevlerinin ilgilenmek zorunda kalmaktadır.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Köprü modu topolojisi":::

Aşağıda, IP yolu kurulumunun köprü modundaki gibi nasıl göründüğü hakkında bir örnek verilmiştir. Düğümde kaç tane düğüm olduğuna bakılmaksızın, yalnızca iki yol olur. İlk olarak, azure0 üzerinde yerel olan tüm trafik, IP 'si "src 10.240.0.4" (düğüm birincil IP) olan arabirim aracılığıyla alt ağın varsayılan ağ geçidine gider ve ikinci bir "10,20. x. x" Pod alanını çekirdek için çekirdek için çekirdekine söyler.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Saydam mod
Saydam modu, Linux ağını ayarlamaya yönelik düz bir yaklaşımlar alır. Bu modda, Azure CNı, Linux sanal makinesinde eth0 arabirimi özelliklerinin hiçbirini değiştirmez. Linux ağ özelliklerinin değiştirilmesine yönelik bu en az yaklaşım, kümelerin köprü moduyla karşılaştığı karmaşık köşe örnek sorunlarını azaltmaya yardımcı olur. Saydam modda Azure CNı, ana bilgisayar ağına eklenecek konak tarafı Pod çifti arabirimlerini oluşturur ve ekler `veth` . VM 'nin Pod-Pod arası iletişimi, CNı 'nin ekleneceği IP yollardır. Temelde Pod-Pod iletişimi katman 3 ' ten ve pod trafiğinden, L3 yönlendirme kuralları tarafından yönlendirilir.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Saydam mod topolojisi":::

Aşağıda, saydam moddan oluşan örnek bir IP yolu kurulumu verilmiştir. her Pod 'ın arabirimi, Pod olarak hedef IP 'si olan trafik doğrudan Pod 'ın ana bilgisayar tarafı çiftinin arabirimine gönderilmek üzere bir statik rota iliştirilir `veth` .

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Saydam modunun avantajları

- `conntrack`, Düğüm yerel DNS 'yi ayarlama gereksinimi olmadan DNS paralel yarış durumu ve 5 San DNS gecikme sorunlarının engelleme için risk azaltma sağlar (performans nedenleriyle düğüm yerel DNS 'i kullanmaya devam edebilirsiniz).
- İlk 5 sn DNS gecikme süresi CNı köprü modunun, "tam zamanında" köprü kurulumu nedeniyle bugün tanıtılmakta olduğunu ortadan kaldırır.
- Köprü modundaki köşe çalışmalarından biri, Azure CNı 'nin kullanıcıların VNET veya NIC 'ye ekleyen özel DNS sunucusu listelerini güncellemesidir. Bu, CNı 'nin yalnızca DNS sunucusu listesinin yalnızca ilk örneğini çekilmesine neden olur. CNı, eth0 özelliklerini değiştirmediğinden saydam modda çözüldü. Daha fazla bilgi için [buraya](https://github.com/Azure/azure-container-networking/issues/713)bakın.
- ARP zaman aşımına uğrarsa UDP trafiğinin daha iyi işlenmesini ve UDP taşma süresini hafifletme sağlar. Köprü oluşturma modunda, köprü, VM 'de Pod-Pod arası iletişimde bir MAC adresi bilmez, tasarıma göre, bu, paketin tüm bağlantı noktalarına fırtınası ile sonuçlanır. Yolda L2 cihaz olmadığından, saydam modda çözüldü. Daha fazla bilgi için [buraya](https://github.com/Azure/azure-container-networking/issues/704)bakın.
- Saydam modu, köprü oluşturma modu ile karşılaştırıldığında aktarım hızı ve gecikme süresi bakımından sanal makine Pod-Pod iletişimi için daha iyi performans sağlar.

## <a name="how-to-avoid-permission-ownership-setting-slow-issues-when-the-volume-has-a-lot-of-files"></a>Birimde çok fazla dosya olduğunda izin sahipliğine engel olma sorunu ortadan kaldırılsın mı?

Geleneksel olarak, Pod 'niz kök olmayan bir Kullanıcı (yapmanız gerekir) olarak çalışıyorsa, `fsGroup` birimin Pod tarafından okunabilir ve yazılabilir olması için pod 'ın güvenlik bağlamı içinde bir belirtmeniz gerekir. Bu gereksinim, [burada](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)daha ayrıntılı bir şekilde ele alınmıştır.

Ancak, tek bir yan etkisi, `fsGroup` bir birimin her takılışında Kubernetes 'in, `chown()` `chmod()` aşağıda belirtilen birkaç özel durum ile birim içindeki dosya ve dizinlerin her zaman özyinelemeli ve tüm dosyalar ve dizinler olması gerekir. Bu durum, birimin Grup sahipliği zaten istenen ile uyuşsa `fsGroup` ve çok sayıda küçük dosya içeren daha büyük birimler için oldukça pahalı olabildiğinde, Pod başlatmasının uzun sürmesine neden olabilir. Bu senaryoda, v 1,20 'den önce bilinen bir sorun oluştu ve geçici çözüm Pod farklı çalıştır kökünü ayarmıştır:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

Sorun Kubernetes v 1,20 tarafından çözümlendi, daha fazla ayrıntı için [kubernetes 1,20: birim Izin değişikliklerinin ayrıntılı denetimi](https://kubernetes.io/blog/2020/12/14/kubernetes-release-1.20-fsgroupchangepolicy-fsgrouppolicy/) bölümüne bakın.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/

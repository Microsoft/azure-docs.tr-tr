---
title: Yaygın Azure Kubernetes hizmet sorunlarını giderme
description: Azure Kubernetes Service (AKS) kullanırken karşılaşılan yaygın sorunları giderme ve çözme hakkında bilgi edinin
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 1d3dff19bd75bfa4e7564eb4b188ffe68d605025
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952040"
---
# <a name="aks-troubleshooting"></a>AKS sorunlarını giderme

Azure Kubernetes hizmeti (AKS) kümeleri oluştururken veya yönetirken, zaman içinde sorunlar ortaya çıkabilir. Bu makalede bazı yaygın sorunlar ve sorun giderme adımları ayrıntılı olarak anlatılmaktadır.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Genel olarak, Kubernetes sorunlarını ayıklama hakkındaki bilgileri nerede bulabilirim?

[Kubernetes kümelerinde sorun gidermeye yönelik resmi kılavuzunu](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)deneyin.
Ayrıca, pods, düğümler, kümeler ve diğer özelliklerle ilgili sorunları gidermeye yönelik bir Microsoft mühendis tarafından yayımlanan bir [sorun giderme kılavuzu](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)vardır.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>`quota exceeded`Oluşturma veya yükseltme sırasında hata alıyorum. Ne yapmalıyım? 

 [Daha fazla çekirdek isteyin](../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>`insufficientSubnetSize`Gelişmiş ağlarla BIR AKS kümesi dağıtma sırasında hata alıyorum. Ne yapmalıyım?

Bu hata, bir küme için kullanımda olan bir alt ağın, başarılı bir kaynak ataması için CıDR içindeki IP 'Leri artık kullanmadığını gösterir. Kubenet kümeleri için, gereksinim kümedeki her düğüm için yeterli bir IP alanıdır. Azure CNı kümeleri için, gereksinim her düğüm ve kümedeki pod için yeterli IP alanı olur.
[Azure CNI tasarımı](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)hakkında daha fazla bilgi edinmek için IP 'lere IP atama.

Bu hatalar Ayrıca, yetersiz alt ağ boyutu gibi sorunları proaktif olarak sunan [aks tanılamasında](concepts-diagnostics.md)de ortaya çıkmış olur.

Aşağıdaki üç (3) durum, yetersiz alt ağ boyutu hatasına neden olur:

1. AKS ölçeği veya AKS düğüm havuzu ölçeği
   1. Kubenet kullanılıyorsa, ' `number of free IPs in the subnet` **den küçük** olduğunda `number of new nodes requested` .
   1. Azure CNı kullanıyorsanız, ' `number of free IPs in the subnet` **den az** olduğunda `number of nodes requested times (*) the node pool's --max-pod value` .

1. AKS yükseltme veya AKS düğüm havuzu yükseltmesi
   1. Kubenet kullanılıyorsa, ' `number of free IPs in the subnet` **den küçük** olduğunda `number of buffer nodes needed to upgrade` .
   1. Azure CNı kullanıyorsanız, ' `number of free IPs in the subnet` **den az** olduğunda `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Varsayılan olarak AKS kümeleri bir (1) bir en fazla dalgalanma (yükseltme arabelleği) değeri ayarladı, ancak bu yükseltme davranışı bir düğüm havuzunun [en fazla aşırı gerilim değeri ayarlanarak özelleştirilebilir, bu da bir yükseltmeyi tamamlaması gereken kullanılabilir IP sayısını artırır.

1. AKS düğüm havuzu ekleme veya AKS düğümü oluşturma
   1. Kubenet kullanılıyorsa, ' `number of free IPs in the subnet` **den küçük** olduğunda `number of nodes requested for the node pool` .
   1. Azure CNı kullanıyorsanız, ' `number of free IPs in the subnet` **den az** olduğunda `number of nodes requested times (*) the node pool's --max-pod value` .

Yeni alt ağlar oluşturularak aşağıdaki hafifletme uygulanabilir. Var olan bir alt ağın CıDR aralığını güncelleştirememesi nedeniyle, risk azaltma için yeni bir alt ağ oluşturma izni gerekir.

1. İşlem hedefleri için yeterince daha büyük bir CıDR aralığıyla yeni bir alt ağ oluşturun:
   1. Yeni bir istenen çakışmayan aralığa sahip yeni bir alt ağ oluşturun.
   1. Yeni alt ağda yeni bir düğüm havuzu oluşturun.
   1. Yenisiyle değiştirilmesini sağlamak için eski alt ağda bulunan eski düğüm havuzundan Pod 'yi boşaltın.
   1. Eski alt ağı ve eski düğüm havuzunu silin.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Pod My CrashLoopBackOff modunda takılmış. Ne yapmalıyım?

Pod 'un bu modda takılmasının çeşitli nedenleri olabilir. Şöyle görünebilir:

* Kullanarak Pod kendisini `kubectl describe pod <pod-name>` .
* Kullanılarak Günlükler `kubectl logs <pod-name>` .

Pod sorunlarını giderme hakkında daha fazla bilgi için bkz. [uygulamalarda hata ayıklama](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>`TCP timeouts` `kubectl` API sunucusuna bağlanırken veya diğer üçüncü taraf araçları kullanırken alıyorum
AKS 'in hizmet düzeyi hedeflerini (SLOs) ve hizmet düzeyi sözleşmelerini (SLA 'Lar) sağlamak için çekirdek sayısına göre dikey ölçeklendirilen HA denetim düzlemleri vardır. Bağlantı zaman aşımına uğradıysanız, aşağıdakileri denetleyin:

- **Tüm API komutlarınız sürekli olarak mı yoksa yalnızca birkaç tane mi çalışıyor?** Yalnızca birkaç tane ise, `tunnelfront` `aks-link` düğüm > denetim düzlemi iletişiminden sorumlu olan Pod veya Pod 'niz çalışır durumda olmayabilir. Bu Pod 'un barındırıldığı düğümlerin aşırı veya stres altında olmadığından emin olun. Bunları kendi [ `system` düğüm havuzlarına](use-system-pools.md)taşımayı göz önünde bulundurun.
- **[Aıks çıkış trafiği](limit-egress-traffic.md)belgeleriyle belirtilen tüm gerekli bağlantı noktalarını, FQDN 'Leri ve IP 'leri açtığınızdan emin misiniz?** Aksi takdirde birkaç komut çağrısı başarısız olabilir.
- **[API IP yetkili aralıklarının](api-server-authorized-ip-ranges.md)geçerli IP 'si kapsanıyor mi?** Bu özelliği kullanıyorsanız ve IP 'niz aralığa dahil edilmemişse, çağrılarınız engellenir. 
- **API sunucusuna sızan bir istemciniz ya da uygulamanız var mı?** Sık sık çağrı yapmak yerine izlemeler kullandığınızdan ve üçüncü taraf uygulamalarınızın bu tür çağrıları sızdırmadığından emin olun. Örneğin, yeni bir gizli dizi her okurken, Istio Mixer 'teki bir hata yeni bir API sunucusu izleme bağlantısının oluşturulmasına neden olur. Bu davranış düzenli bir aralıkta olduğundan, izleme bağlantıları hızlı bir şekilde birikir ve sonuç olarak, ölçeklendirme deseninin ne olduğuna bakılmaksızın API sunucusunun aşırı yüklenmiş olmasına neden olur. https://github.com/istio/istio/issues/19481
- **Held dağıtımlarınızda çok sayıda yayın var mı?** Bu senaryo, her iki Tiller de düğümlerde çok fazla bellek kullanmasına neden olabilir ve bu da büyük miktarda `configmaps` , API sunucusunda gereksiz ani artışlar oluşmasına neden olabilir. `--history-max`' De yapılandırmayı düşünün `helm init` ve yeni helb 3 ' ü kullanın. Aşağıdaki sorunlar hakkında daha fazla bilgi: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[Düğümler arasında iç trafik engellensin mi?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>`TCP timeouts`Şunu alıyorum`dial tcp <Node_IP>:10250: i/o timeout`

Bu zaman aşımları, engellenen düğümler arasındaki iç trafikle ilgili olabilir. Bu trafiğin, kümenizin düğümleri için alt ağdaki [ağ güvenlik grupları](concepts-security.md#azure-network-security-groups) gibi engellenmediğinden emin olun.

## <a name="im-trying-to-enable-kubernetes-role-based-access-control-kubernetes-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Var olan bir kümede Kubernetes rol tabanlı erişim denetimini (Kubernetes RBAC) etkinleştirmeye çalışıyorum. Bunu nasıl yapabilirim?

Mevcut kümeler üzerinde Kubernetes rol tabanlı erişim denetimini (Kubernetes RBAC) etkinleştirmek Şu anda desteklenmiyor, yeni kümeler oluşturulurken ayarlanmalıdır. Kubernetes RBAC, CLı, portal veya daha sonraki bir API sürümü kullanılırken varsayılan olarak etkindir `2020-03-01` .

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Kubectl günlüklerini kullanarak günlükleri alamıyor veya API sunucusuna bağlanamıyorum. "Sunucudan hata: arka uç ararken hata: TCP ara..." hatasını alıyorum. Ne yapmalıyım?

API sunucusuna bağlanmak için 22, 9000 ve 1194 bağlantı noktalarının açık olduğundan emin olun. `tunnelfront` `aks-link` Komutunu kullanarak *Kuto-System* ad alanında veya Pod 'ın çalışıp çalışmadığını denetleyin `kubectl get pods --namespace kube-system` . Değilse, Pod 'ın silinmesini zorla ve yeniden başlatılır.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>`"tls: client offered only unsupported versions"`AKS API 'sine bağlanırken istemcimde alıyorum. Ne yapmalıyım?

AKS 'de desteklenen en düşük TLS sürümü TLS 1,2 ' dir.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Yükseltmeye veya ölçeklendirmeye çalışıyorum ve bir `"Changing property 'imageReference' is not allowed"` hata alıyorum. Nasıl yaparım? bu sorun düzeltilsin mi?

AKS kümesi içindeki aracı düğümlerinde bulunan etiketleri değiştirdiğiniz için bu hatayı alabilirsiniz. MC_ * kaynak grubundaki kaynakların etiketlerini ve diğer özelliklerini değiştirme veya silme, beklenmeyen sonuçlara neden olabilir. AKS kümesindeki MC_ * grubundaki kaynakları değiştirmek, hizmet düzeyi hedefini (SLO) keser.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Kümemin başarısız durumunda olduğunu ve yükseltme ya da ölçeklendirmeyi düzeltilinceye kadar çalışmayacak

*Bu sorun giderme yardımı şuradan yönlendirilir https://aka.ms/aks-cluster-failed*

Bu hata, kümeler birden çok nedenden dolayı başarısız bir durum girerken oluşur. Daha önce başarısız olan işlemi yeniden denemeden önce kümenizin başarısız durumunu çözümlemek için aşağıdaki adımları izleyin:

1. Kümenin `failed` durumu olmadığından `upgrade` ve `scale` işlemler başarılı olmayacaktır. Ortak kök sorunları ve çözümleri şunları içerir:
    * **Yetersiz işlem (CRP) kotasıyla** ölçekleme. Çözümlemek için, önce kümenizi kotanın içindeki kararlı bir hedef durumuna ölçeklendirin. Ardından, ilk kota limitlerinin ötesinde daha fazla ölçeklendirmeyi denemeden önce [bir işlem kotası artışı istemek için bu adımları](../azure-portal/supportability/resource-manager-core-quotas-request.md) izleyin.
    * Gelişmiş ağ ve **yetersiz alt ağ (ağ) kaynaklarıyla** bir kümeyi ölçeklendirin. Çözümlemek için, önce kümenizi kotanın içindeki kararlı bir hedef durumuna ölçeklendirin. Ardından ilk kota limitlerinin ötesinde daha fazla ölçeklendirmeyi denemeden önce [bir kaynak kotası artışı istemek için bu adımları](../azure-resource-manager/templates/error-resource-quota.md#solution) izleyin.
2. Yükseltme hatasının temeldeki nedeni çözümlendikten sonra, kümenizin başarılı bir durumda olması gerekir. Başarılı bir durum doğrulandıktan sonra, özgün işlemi yeniden deneyin.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Bu durumun yükseltilme veya ölçeklendirilmesi sırasında hata alıyorum veya yükseltme başarısız oldu

*Bu sorun giderme yardımı şuradan yönlendirilir https://aka.ms/aks-pending-upgrade*

 Aynı anda yükseltme ve ölçeklendirme için bir küme veya düğüm havuzunuz olamaz. Bunun yerine, her işlem türünün aynı kaynaktaki bir sonraki istekten önce hedef kaynakta tamamlaması gerekir. Sonuç olarak, etkin yükseltme veya ölçeklendirme işlemleri gerçekleşirken veya denendiğinde işlemler sınırlıdır. 

`az aks show -g myResourceGroup -n myAKSCluster -o table`Kümenizde ayrıntılı durum almak için sorunu tanılamaya yardımcı olmak için. Sonuca göre:

* Küme etkin bir şekilde yükseltildiğinde, işlem bitene kadar bekleyin. Başarılı olduysa, daha önce başarısız olan işlemi yeniden deneyin.
* Kümede yükseltme başarısız olursa, önceki bölümde açıklanan adımları izleyin.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kümemi farklı bir aboneliğe veya kümeme yeni bir kiracıya sahip aboneliğime taşıyabilir miyim?

AKS kümenizi farklı bir aboneliğe veya kümenin aboneliğini yeni bir kiracıya taşıdıysanız, küme kimliği izinleri eksik olduğundan küme çalışmaz. Aks, bu kısıtlama nedeniyle **kümelerin abonelikler veya kiracılar arasında taşınmasını desteklemez** .

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri gerektiren özellikleri kullanmaya çalışırken hata alıyorum

*Bu sorun giderme yardımı aka.ms/aks-vmss-enablement 'ten yönlendirilir*

Aşağıdaki örnekte olduğu gibi, AKS kümenizi bir sanal makine ölçek kümesi üzerinde olmadığını belirten hatalar alabilirsiniz:

**AgentPool `<agentpoolname>` Otomatik ölçeklendirmeyi etkin olarak ayarladı, ancak sanal makine ölçek kümelerinde değil**

Küme otomatik Scaler veya birden çok düğüm havuzu gibi özellikler, sanal makine ölçek kümelerini olarak gerektirir `vm-set-type` .

Bir AKS kümesini doğru şekilde oluşturmak için uygun belge içindeki *başlamadan önce* ' i izleyin:

* [Küme otomatik Scaler 'ı kullanma](cluster-autoscaler.md)
* [Birden çok düğüm havuzu oluşturma ve kullanma](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS kaynakları ve parametreleri için hangi adlandırma kısıtlamaları zorlanır?

*Bu sorun giderme yardımı aka.ms/aks-naming-rules 'ten yönlendirilir*

Adlandırma kısıtlamaları hem Azure platformu hem de AKS tarafından uygulanır. Bir kaynak adı veya parametresi bu kısıtlamaların birini keserse, farklı bir giriş sağlamanızı isteyen bir hata döndürülür. Aşağıdaki ortak adlandırma yönergeleri geçerlidir:

* Küme adları 1-63 karakter olmalıdır. Yalnızca harfler, rakamlar, tireler ve alt çizgi olmak üzere izin verilen karakterler. İlk ve son karakter bir harf veya sayı olmalıdır.
* AKS düğümü/*Mc_* kaynak grubu adı, kaynak grubu adını ve kaynak adını birleştirir. Otomatik olarak oluşturulan sözdiziminin `MC_resourceGroupName_resourceName_AzureRegion` 80 karakterden büyük olmaması gerekir. Gerekirse, kaynak grubu adınızın veya AKS kümesi adınızın uzunluğunu azaltın. Ayrıca, [düğüm kaynağı grubu adınızı özelleştirebilirsiniz](cluster-configuration.md#custom-resource-group-name)
* *Dnspredüzeltmesini* alfasayısal değerlerle başlamalı ve bitmeli ve 1-54 karakter arasında olmalıdır. Geçerli karakterler alfasayısal değerleri ve kısa çizgileri (-) içerir. *Dnspredüzeltmesini* nokta (.) gibi özel karakterler içeremez.
* AKS düğüm havuzu adları küçük harfle yazılmalıdır ve Linux düğüm havuzları için 1-11 karakter ve Windows düğüm havuzları için 1-6 karakter olmalıdır. Ad bir harf ile başlamalı ve yalnızca harf ve rakam olmak üzere yalnızca izin verilen karakterler olmalıdır.
* Linux düğümleri için yönetici kullanıcı adını ayarlayan *Yönetici-Kullanıcı adı*, bir harfle başlamalıdır, yalnızca harf, sayı, kısa çizgi ve alt çizgi içerebilir ve en fazla 64 karakter uzunluğunda olabilir.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Küme oluşturmaya, güncelleştirmeye, ölçeklendirmeye, silmeye veya yükseltmeye çalışırken hata alıyorum, devam eden başka bir işlem olduğundan bu işleme izin verilmiyor.

*Bu sorun giderme yardımı aka.ms/aks-pending-operation 'ten yönlendirilir*

Önceki bir işlem devam ederken küme işlemleri sınırlıdır. Kümenizin ayrıntılı durumunu almak için `az aks show -g myResourceGroup -n myAKSCluster -o table` komutunu kullanın. Gerektiğinde kendi kaynak grubunuzu ve AKS küme adınızı kullanın.

Küme durumunun çıkışına göre:

* Küme, *başarılı* veya *başarısız* dışında bir sağlama durumundaysa, Işlem (*yükseltme/güncelleştirme/oluşturma/ölçeklendirme/silme/geçirme*) bitene kadar bekleyin. Önceki işlem tamamlandığında, en son küme işleminizi yeniden deneyin.

* Kümenin başarısız bir yükseltmesi varsa, ana belirtilen adımları izleyerek [Kümemin başarısız durumunda olduğunu ve yükseltme ya da ölçeklendirmeyi düzeltilene kadar çalışmayacaktır](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Yeni bir küme oluşturmaya çalıştığımda, hizmet sorumlumun bulunamadığını veya geçersiz olduğunu belirten bir hata alındı.

Bir AKS kümesi oluştururken, sizin adınıza kaynak oluşturmak için bir hizmet sorumlusu veya yönetilen kimlik gerektirir. AKS, küme oluşturma sırasında otomatik olarak yeni bir hizmet sorumlusu oluşturabilir veya var olan bir hizmet sorumlusunu alabilir. Otomatik olarak oluşturulan bir kullanıldığında, oluşturma işleminin başarılı olması için Azure Active Directory her bölgeye yayması gerekir. Yayma çok uzun sürerse, küme, kullanılabilir bir hizmet sorumlusu bulamadığı için doğrulama doğrulaması başarısız olur. 

Bu sorun için aşağıdaki geçici çözümleri kullanın:
* Zaten bölgelere yayılan ve küme oluşturma zamanında AKS 'e geçirilecek mevcut bir hizmet sorumlusunu kullanın.
* Otomasyon betikleri kullanıyorsanız, hizmet sorumlusu oluşturma ve AKS kümesi oluşturma arasında zaman gecikmeleri ekleyin.
* Azure portal kullanıyorsanız, oluşturma sırasında küme ayarlarına dönün ve birkaç dakika sonra doğrulama sayfasını yeniden deneyin.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>`"AADSTS7000215: Invalid client secret is provided."`AKS API 'sini kullanırken alıyorum. Ne yapmalıyım?

Bu sorun, hizmet sorumlusu kimlik bilgilerinin süresinin dolması nedeniyle oluşur. [AKS kümesinin kimlik bilgilerini güncelleştirin.](update-credentials.md)

## <a name="i-cant-access-my-cluster-api-from-my-automationdev-machinetooling-when-using-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>API sunucusu yetkilendirilmiş IP aralıklarını kullanırken Automation/dev makinem/araçları 'ndan küme API 'me erişemiyorum. Nasıl yaparım? bu sorun düzeltilsin mi?

Bu sorunu çözmek için, `--api-server-authorized-ip-ranges` kullanılan Otomasyon/geliştirme/araç sistemi SISTEMLERININ IP (lar) ya da IP aralıklarını içerdiğinden emin olun. [Yetkılı IP adresi aralıklarını kullanarak API sunucusuna güvenli erişim](api-server-authorized-ip-ranges.md)bölümünde ' IP 'yi bulma ' bölümüne bakın.

## <a name="im-unable-to-view-resources-in-kubernetes-resource-viewer-in-azure-portal-for-my-cluster-configured-with-api-server-authorized-ip-ranges-how-do-i-fix-this-problem"></a>API sunucusu yetkilendirilmiş IP aralıkları ile yapılandırılmış Kümem için Azure portal, Kubernetes kaynak görüntüleyicisinde kaynakları görüntüleyemiyorum. Nasıl yaparım? bu sorun düzeltilsin mi?

[Kubernetes kaynak görüntüleyicisinin](kubernetes-portal.md) `--api-server-authorized-ip-ranges` Yerel ISTEMCI bilgisayar veya IP adresi aralığına (portala gözatılırken) erişim içermesi gerekir. [Yetkılı IP adresi aralıklarını kullanarak API sunucusuna güvenli erişim](api-server-authorized-ip-ranges.md)bölümünde ' IP 'yi bulma ' bölümüne bakın.

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Çıkış trafiğini kısıtladıktan sonra hata alıyorum

AKS kümesinden çıkış trafiği kısıtlandığında, [gerekli ve isteğe bağlı olarak önerilen](limit-egress-traffic.md) giden bağlantı noktaları/ağ kuralları ve aks için FQDN/uygulama kuralları vardır. Ayarlarınız bu kurallardan herhangi biriyle çakışıyorsa, bazı `kubectl` Komutlar doğru çalışmaz. AKS kümesi oluştururken de hata görebilirsiniz.

Ayarlarınızın gerekli veya isteğe bağlı önerilen giden bağlantı noktaları/ağ kuralları ve FQDN/uygulama kurallarından hiçbiriyle çakışmadığını doğrulayın.

## <a name="im-receiving-429---too-many-requests-errors"></a>"429-çok fazla Istek" hatası alıyorum

Azure 'daki bir Kubernetes kümesi (AKS veya No) sıklıkla bir ölçek artırma/azaltma veya küme otomatik olarak (CA) kullandığında, bu işlemler, atanan abonelik kotasının önde gelen hata olarak aşıldığı çok sayıda HTTP çağrısı oluşmasına neden olabilir. Hatalar şöyle görünür

```
Service returned an error. Status=429 Code=\"OperationNotAllowed\" Message=\"The server rejected the request because too many requests have been received for this subscription.\" Details=[{\"code\":\"TooManyRequests\",\"message\":\"{\\\"operationGroup\\\":\\\"HighCostGetVMScaleSet30Min\\\",\\\"startTime\\\":\\\"2020-09-20T07:13:55.2177346+00:00\\\",\\\"endTime\\\":\\\"2020-09-20T07:28:55.2177346+00:00\\\",\\\"allowedRequestCount\\\":1800,\\\"measuredRequestCount\\\":2208}\",\"target\":\"HighCostGetVMScaleSet30Min\"}] InnerError={\"internalErrorCode\":\"TooManyRequestsReceived\"}"}
```

Bu azaltma hataları [burada](../azure-resource-manager/management/request-limits-and-throttling.md) ve [burada](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors) ayrıntılı olarak açıklanmaktadır

AKS mühendislik ekibinin önerisi, çok sayıda geliştirme içeren en az 1.18. x sürümünü çalıştırıyor olmanızı sağlamaktır. Bu geliştirmelerin [burada](https://github.com/Azure/AKS/issues/1413) ve [burada](https://github.com/kubernetes-sigs/cloud-provider-azure/issues/247)daha fazla ayrıntıya ulaşabilirsiniz.

Bu azaltma hataları, abonelik düzeyinde ölçülerek, şu durumlarda yine de gerçekleşmeyebilirsiniz:
- GET istekleri oluşturan üçüncü taraf uygulamalar vardır (örneğin, uygulamaları izleme vb.). Öneri, bu çağrıların sıklığını azaltmaktır.
- Sanal makine ölçek kümelerini kullanan çok sayıda AKS kümesi/düğüm havuzu vardır. Küme sayısını farklı aboneliklere bölmek için, özellikle de çok etkin (örneğin, etkin bir küme otomatik Scaler) veya birden çok istemcisi (örneğin, Ranma, terraform vb.) olmasını beklemeniz gerekir.

## <a name="my-clusters-provisioning-status-changed-from-ready-to-failed-with-or-without-me-performing-an-operation-what-should-i-do"></a>Kümenizin sağlama durumu, bir işlem gerçekleştirmeme veya bir işlem gerçekleştirmeksizin başarısız olarak değiştirildi. Ne yapmalıyım?

Kümenizin sağlama durumu, herhangi bir işlem gerçekleştirmeksizin veya ile *başarısız* *olarak değişirse* , ancak kümenizdeki uygulamalar çalışmaya devam edersek, bu sorun hizmet tarafından otomatik olarak çözülebilir ve uygulamalarınız etkilenmemelidir.

Kümenizin sağlama durumu *başarısız* olarak kalırsa veya kümenizdeki uygulamalar çalışmayı durdurduktan sonra [bir destek isteği gönderebilirsiniz](https://azure.microsoft.com/support/options/#submit).

## <a name="my-watch-is-stale-or-azure-ad-pod-identity-nmi-is-returning-status-500"></a>My Watch eski veya Azure AD Pod Identity NMI, durum 500 döndürüyor

Bu [örnekte](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)olduğu gibi Azure Güvenlik Duvarı 'nı kullanıyorsanız, uygulama kurallarını kullanan güvenlik duvarı aracılığıyla uzun süreli TCP bağlantılarında, güvenlik duvarında go 'nun sonlandırmasına neden olan bir hata (Q1CY21 ' de çözümlenebileceği) vardır `keepalives` . Bu sorun çözülene kadar, AKS API Server IP 'sine bir ağ kuralı (uygulama kuralı yerine) ekleyerek azaltabilirsiniz.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure depolama ve AKS sorunlarını giderme

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Uid ayarlanırken ve `GID` Azure disk Için mountOptions 'da hata oluştu

Azure disk, varsayılan olarak Ext4, XFS FileSystem ve UID = x, GID = x gibi bir bağlama zamanında ayarlanamaz. Örneğin, mountOptions uid = 999, GID = 999 ' u ayarlamaya çalıştıysanız, şöyle bir hata görür:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Seçeneklerden birini yaparak sorunu azaltabilirsiniz:

* FsGroup 'ta runAsUser ve GID ' de uid ' i ayarlayarak [pod için güvenlik bağlamını yapılandırın](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) . Örneğin, aşağıdaki ayar Pod Run as kökünü ayarlar ve herhangi bir dosya için erişilebilir hale getirir:

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

  >[!NOTE]
  > GID ve uid, varsayılan olarak kök veya 0 olarak bağlandığından. GID veya Uid, kök olmayan olarak ayarlandıysa, örneğin 1000, Kubernetes `chown` Bu disk altındaki tüm dizinleri ve dosyaları değiştirmek için kullanılır. Bu işlem zaman alabilir ve diski bağlama işlemi çok yavaş olabilir.

* `chown`Ve ayarlamak Için initContainers içinde `GID` kullanın `UID` . Örnek:

```yaml
initContainers:
- name: volume-mount
  image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Olası yarış durumu sorununa ve geçersiz veri diski listesine önde gelen Azure disk ayırma hatası

Bir Azure diski ayrılmazsa, üstel geri kapatmayı kullanarak diski ayırmak en fazla altı kez yeniden dener. Ayrıca, yaklaşık 3 dakika boyunca veri diski listesinde düğüm düzeyinde bir kilit da tutar. Disk listesi bu süre içinde el ile güncelleştirilirse, düğüm düzeyi kilidi tarafından tutulan disk listesinin kullanımdan kalkmasına ve düğüm üzerinde kararsızlığa neden olur.

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
|--|:--:|
| 1.12 | 1.12.9 veya üzeri |
| 1.13 | 1.13.6 veya üzeri |
| 1,14 | 1.14.2 veya üzeri |
| 1,15 ve üzeri | Yok |

Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız ve düğümünüz eski bir disk listesine sahipse, mevcut olmayan tüm diskleri VM 'den toplu bir işlem olarak ayırarak azaltabilirsiniz. **Mevcut olmayan diskleri tek tek ayırmak başarısız olabilir.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Çok sayıda Azure diski yavaş iliştirme/ayır oluşmasına neden oluyor

Tek düğümlü VM 'yi hedefleyen Azure disk iliştirme/ayır işlemleri sayısı 10 ' dan büyük veya tek sanal makine ölçek kümesi havuzunu hedeflerken 3 ' ten büyükse, ardışık olarak yapıldığından beklenenden daha yavaş olabilir. Bu sorun bilinen bir kısıtlamadır ve şu anda geçici çözüm yoktur. [Sayının dışında paralel iliştirme/ayır desteklemek Için kullanıcı sesli öğesi.](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for).

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Başarısız durumundaki olası düğüm VM 'sine yönelik Azure disk ayırma hatası

Bazı Edge durumlarında, Azure disk ayırma kısmen başarısız olabilir ve düğüm VM 'sini başarısız bir durumda bırakabilir.

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
|--|:--:|
| 1.12 | 1.12.10 veya üzeri |
| 1.13 | 1.13.8 veya üzeri |
| 1,14 | 1.14.4 veya üzeri |
| 1,15 ve üzeri | Yok |

Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız ve düğümünüz hatalı durumdaysa, aşağıdakilerden birini kullanarak VM durumunu el ile güncelleştirerek azaltabilirsiniz:

* Kullanılabilirlik kümesi tabanlı küme için:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Bir VMSS tabanlı küme için:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure dosyaları ve AKS sorunlarını giderme

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Azure dosyaları için, Kubernetes 'in önerilen kararlı sürümleri nelerdir?
 
| Kubernetes sürümü | Önerilen sürüm |
|--|:--:|
| 1.12 | 1.12.6 veya üzeri |
| 1.13 | 1.13.4 veya üzeri |
| 1,14 | 1.14.0 veya üzeri |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Azure dosyaları kullanılırken varsayılan mountOptions nedir?

Önerilen Ayarlar:

| Kubernetes sürümü | fileMode ve dirMode değeri|
|--|:--:|
| 1.12.0-1.12.1 | 0755 |
| 1.12.2 ve üzeri | 0777 |

Bağlama seçenekleri, depolama sınıfı nesnesinde belirtilebilir. Aşağıdaki örnek *0777* olarak ayarlanır:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Bazı ek kullanışlı *Mountoptions* ayarları:

* `mfsymlinks` Azure dosyaları bağlama (CIFS) sembolik bağlantıları destekliyor olur
* `nobrl` , bayt aralığı kilit isteklerinin sunucuya gönderilmesini engeller. Bu ayar, CIFS stili zorunlu bayt aralığı kilitleriyle kesen belirli uygulamalar için gereklidir. Çoğu CIFS sunucusu henüz danışmanlık bayt aralığı kilitlerini istemeyi desteklemez. *Nobrl* kullanmıyorsanız, CIFS stili zorunlu bayt aralığı kilitleri ile kesen uygulamalar aşağıdakine benzer hata iletilerine neden olabilir:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Azure dosyaları kullanılırken "izinler değiştirilemedi" hatası

Azure dosyaları eklentisinde PostgreSQL çalıştırırken şuna benzer bir hata görebilirsiniz:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Bu hata, CIFS/SMB protokolü kullanılarak Azure dosyaları eklentisi nedeniyle oluşur. CIFS/SMB protokolünü kullanırken, dosya ve dizin izinleri bağlama işleminden sonra değiştirilemez.

Bu sorunu çözmek için `subPath` Azure disk eklentisi ile birlikte kullanın. 

> [!NOTE] 
> Ext3/4 disk türü için disk biçimlendirildikten sonra kayıp + bulunan bir dizin bulunur.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure dosyaları, birçok küçük dosyayı işlerken Azure diskine kıyasla yüksek gecikme süresine sahiptir

Birçok küçük dosyayı işleme gibi bazı durumlarda, Azure disk ile karşılaştırıldığında Azure dosyalarını kullanırken yüksek gecikme süresine karşılaşabilirsiniz.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Depolama hesabı 'ndaki "seçili ağdan erişime izin ver" ayarı etkinleştirilirken hata oluştu

AKS 'de dinamik sağlama için kullanılan bir depolama hesabındaki *Seçili ağdan erişime izin ver* ' i etkinleştirirseniz, aks 'ler bir dosya paylaşma oluşturduğunda bir hata alırsınız:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Bu hata, *Seçili ağdan erişime izin ver* ayarı yapılırken, Kubernetes *persistentvolume-Controller tarafından* seçilen ağ üzerinde olmayan bir hatadır.

[Azure dosyaları ile statik sağlamayı](azure-files-volume.md)kullanarak sorunu azaltabilirsiniz.

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure dosyaları Windows Pod 'da yeniden bağlama yapamıyor

Azure dosyaları bağlaması olan bir Windows Pod silinirse ve aynı düğümde yeniden oluşturulmak üzere zamanlanırsa, bağlama başarısız olur. Bu hata, `New-SmbGlobalMapping` Azure dosyaları bağlama, düğüme zaten bağlı olduğundan komutun başarısız olmasından kaynaklanır.

Örneğin şuna benzer bir hata görebilirsiniz:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
|--|:--:|
| 1.12 | 1.12.6 veya üzeri |
| 1.13 | 1.13.4 veya üzeri |
| 1,14 ve üzeri | Yok |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Azure dosyaları bağlama, depolama hesabı anahtarı değiştiği için başarısız oluyor

Depolama hesabı anahtarınız değiştiyse Azure dosyaları bağlama hatalarıyla karşılaşabilirsiniz.

`azurestorageaccountkey`Base64 ile kodlanmış depolama hesabı anahtarınızla Azure dosya gizli anahtarındaki alanı el ile güncelleştirerek azaltabilirsiniz.

Depolama hesabı anahtarınızı Base64 olarak kodlamak için kullanabilirsiniz `base64` . Örnek:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Azure gizli dosyanızı güncelleştirmek için kullanın `kubectl edit secret` . Örnek:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Birkaç dakika sonra, aracı düğümü güncelleştirilmiş depolama anahtarıyla Azure dosya takasını yeniden dener.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Küme otomatik algılama işlemi, düğüm grubu boyutlarını düzeltemedi hata vererek ölçeklendirme yapamıyor

Kümenizin otomatik olarak ölçeği artırma/azaltma yoksa, [küme otomatik Scaler günlüklerinde][view-master-logs]aşağıdaki gibi bir hata görürsünüz.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Bu hata, bir yukarı akış kümesi otomatik Scaler yarış durumu nedeniyle oluşur. Böyle bir durumda, Cluster otomatik Scaler, aslında kümede olandan farklı bir değerle sona erer. Bu durumdan yararlanmak için [küme otomatik Scaler][cluster-autoscaler]'ı devre dışı bırakıp yeniden etkinleştirin.

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Yavaş disk eki, `GetAzureDiskLun` 10 ila 15 dakika sürer ve bir hata alırsınız

**1.15.0 ' den eski** Kubernetes sürümlerinde, **hata waitforattach for disk için LUN bulunamıyor** gibi bir hata alabilirsiniz.  Bu sorunun geçici çözümü yaklaşık 15 dakika bekleyip yeniden denenecektir.


### <a name="why-do-upgrades-to-kubernetes-116-fail-when-using-node-labels-with-a-kubernetesio-prefix"></a>Kubernetes 1,16 yükseltmesi, kubernetes.io ön ekiyle düğüm etiketleri kullanılırken başarısız olur

Kubernetes [1,16](https://v1-16.docs.kubernetes.io/docs/setup/release/notes/) itibariyle, [Kubernetes.io ön ekine sahip etiketlerin yalnızca tanımlı bir alt kümesi](https://v1-18.docs.kubernetes.io/docs/concepts/overview/working-with-objects/labels/) , kubelet 'in düğümlere uygulanabilir. AKS, etkilenen iş yükleri için kapalı kalma süresine neden olabileceğinden, sizin adınıza etkin etiketleri kaldıramaz.

Sonuç olarak, bu sorunu azaltmak için şunları yapabilirsiniz:

1. Küme denetim düzlemi 'ni 1,16 veya üzeri bir sürüme yükseltin
2. Desteklenmeyen kubernetes.io etiketleri olmadan 1,16 veya üzeri yeni bir nodepoool ekleyin
3. Eski düğüm havuzunu Sil

AKS, bu hafifletme kalitesini artırmak için düğüm havuzundaki etkin etiketlerin muyalarını araştırmaktadır.



<!-- LINKS - internal -->
[view-master-logs]: ./view-control-plane-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
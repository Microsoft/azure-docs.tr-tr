---
title: Yaygın Azure Kubernetes hizmet sorunlarını giderme
description: Azure Kubernetes Service (AKS) kullanırken karşılaşılan yaygın sorunları giderme ve çözme hakkında bilgi edinin
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 5ae97f18bb15b5ab2fe092a1e3b857ea3ef0aed0
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012980"
---
# <a name="aks-troubleshooting"></a>AKS sorunlarını giderme

Azure Kubernetes hizmeti (AKS) kümeleri oluştururken veya yönetirken, zaman zaman sorunlarla karşılaşabilirsiniz. Bu makalede bazı yaygın sorunlar ve sorun giderme adımları ayrıntılı olarak anlatılmaktadır.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Genel olarak, Kubernetes sorunlarını ayıklama hakkındaki bilgileri nerede bulabilirim?

[Kubernetes kümelerinde sorun gidermeye yönelik resmi kılavuzunu](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)deneyin.
Ayrıca, pods, düğümler, kümeler ve diğer özelliklerle ilgili sorunları gidermeye yönelik bir Microsoft mühendis tarafından yayımlanan bir [sorun giderme kılavuzu](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)vardır.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Oluşturma veya yükseltme sırasında "Kota aşıldı" hatası alıyorum. Ne yapmalıyım? 

[Çekirdek istemeniz](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)gerekir.

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>AKS için düğüm başına en fazla düğüm sayısı ayarı nedir?

Azure portal bir AKS kümesi dağıtırsanız, düğüm başına en fazla düğüm sayısı ayarı varsayılan olarak 30 ' dur.
Azure CLı 'de bir AKS kümesi dağıtırsanız, düğüm başına en fazla düğüm ayarı varsayılan olarak 110 ' dir. (Azure CLı 'nın en son sürümünü kullandığınızdan emin olun). Bu varsayılan ayar, `az aks create` komutunda `–-max-pods` bayrağı kullanılarak değiştirilebilir.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Gelişmiş ağlarla AKS kümesi dağıtma sırasında insufficientSubnetSize hatası alıyorum. Ne yapmalıyım?

Azure CNı (Gelişmiş ağ) kullanılıyorsa, AKS, yapılandırılmış düğüm başına "en yüksek pods" temelinde IP adresleri ayırır. Düğüm başına yapılandırılan maksimum düğüm sayısına bağlı olarak, alt ağ boyutu düğüm sayısının ve düğüm başına en fazla Pod 'ın ürünüyle daha büyük olmalıdır. Aşağıdaki denklem şunları özetler:

Alt ağ boyutu > kümedeki düğümlerin sayısı (gelecekteki ölçekleme gereksinimlerinin dikkate alınması) * düğüm başına en fazla düğüm kümesi.

Daha fazla bilgi için bkz. [kümeniz IÇIN IP adresleme planlaması](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Pod My CrashLoopBackOff modunda takılmış. Ne yapmalıyım?

Pod 'un bu modda takılmasının çeşitli nedenleri olabilir. Şöyle görünebilir:

* `kubectl describe pod <pod-name>`kullanarak Pod kendisini kullanın.
* `kubectl log <pod-name>`kullanarak Günlükler.

Pod sorunlarını giderme hakkında daha fazla bilgi için bkz. [uygulamalarda hata ayıklama](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Mevcut bir kümede RBAC 'yi etkinleştirmeye çalışıyorum. Bunu nasıl yapabilirim?

Ne yazık ki, mevcut kümelerde rol tabanlı erişim denetimi 'ni (RBAC) etkinleştirmek Şu anda desteklenmiyor. Açıkça yeni kümeler oluşturmanız gerekir. CLı kullanıyorsanız, RBAC varsayılan olarak etkindir. AKS portalını kullanıyorsanız, oluşturma iş akışında RBAC 'yi etkinleştirmek için iki durumlu bir düğme bulunur.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Azure CLı 'yi varsayılan olarak veya Azure portal kullanarak ve şimdi Kubernetes panosunda çok sayıda uyarı görmem için RBAC ile etkinleştirilen bir küme oluşturdum. Herhangi bir uyarı olmadan çalışmak için kullanılan Pano. Ne yapmalıyım?

Panodaki uyarıların nedeni, kümenin RBAC ile etkin hale gelir ve erişim varsayılan olarak devre dışı bırakılmıştır. Genel olarak bu yaklaşım iyi bir uygulamadır çünkü panonun tüm kullanıcıları için varsayılan olarak pozlaması güvenlik tehditlerine neden olabilir. Panoyu hala etkinleştirmek istiyorsanız, [Bu blog gönderisine](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)ilişkin adımları izleyin.

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Panoya bağlanamıyorum. Ne yapmalıyım?

Bir küme dışında hizmetinize erişmenin en kolay yolu, `kubectl proxy`çalıştırmak için, localhost bağlantı noktası 8001 ' e, Kubernetes API sunucusuna gönderilen isteklerin proxy 'sidir. Buradan, API sunucusu hizmetinize proxy gönderebilir: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Kubernetes panosunu görmüyorsanız, `kube-proxy` Pod 'un `kube-system` ad alanında çalışıp çalışmadığını denetleyin. Çalışır durumda değilse, Pod 'yi silin ve yeniden başlatılır.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Kubectl günlüklerini kullanarak günlükleri alamıyor veya API sunucusuna bağlanamıyorum. "Sunucudan hata: arka uç ararken hata: TCP ara..." hatasını alıyorum. Ne yapmalıyım?

Varsayılan ağ güvenlik grubunun değiştirilmediğinden ve API sunucusuyla bağlantı için 22 ve 9000 bağlantı noktasının açık olduğundan emin olun. `tunnelfront` Pod 'ın, `kubectl get pods --namespace kube-system` komutunu kullanarak *kuin-System* ad alanında çalışıp çalışmadığını denetleyin. Değilse, Pod 'ın silinmesini zorla ve yeniden başlatılır.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>"ImageReference" özelliğinin değiştirilmesine izin verilmez ve "bir ileti alıyorum" hatası alıyorum. Nasıl yaparım? bu sorun düzeltilsin mi?

AKS kümesi içindeki aracı düğümlerinde bulunan etiketleri değiştirdiğiniz için bu hatayı alabilirsiniz. MC_ * kaynak grubundaki kaynakların ve diğer özelliklerinin değiştirilmesi ve silinmesi, beklenmeyen sonuçlara neden olabilir. AKS kümesindeki MC_ * grubu altındaki kaynakları değiştirmek, hizmet düzeyi hedefini (SLO) keser.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Kümemin başarısız durumunda olduğunu ve yükseltme ya da ölçeklendirmeyi düzeltilinceye kadar çalışmayacak

*Bu sorun giderme yardımı https://aka.ms/aks-cluster-failed yönlendirilir*

Bu hata, kümeler birden çok nedenden dolayı başarısız bir durum girerken oluşur. Daha önce başarısız olan işlemi yeniden denemeden önce kümenizin başarısız durumunu çözümlemek için aşağıdaki adımları izleyin:

1. Küme `failed` durum dışına çıkana kadar `upgrade` ve `scale` işlemleri başarılı olmayacaktır. Ortak kök sorunları ve çözümleri şunları içerir:
    * **Yetersiz işlem (CRP) kotasıyla**ölçekleme. Çözümlemek için, önce kümenizi kotanın içindeki kararlı bir hedef durumuna ölçeklendirin. Ardından, ilk kota limitlerinin ötesinde daha fazla ölçeklendirmeyi denemeden önce [bir işlem kotası artışı istemek için bu adımları](../azure-supportability/resource-manager-core-quotas-request.md) izleyin.
    * Gelişmiş ağ ve **yetersiz alt ağ (ağ) kaynaklarıyla**bir kümeyi ölçeklendirin. Çözümlemek için, önce kümenizi kotanın içindeki kararlı bir hedef durumuna ölçeklendirin. Ardından ilk kota limitlerinin ötesinde daha fazla ölçeklendirmeyi denemeden önce [bir kaynak kotası artışı istemek için bu adımları](../azure-resource-manager/resource-manager-quota-errors.md#solution) izleyin.
2. Yükseltme hatasının temeldeki nedeni çözümlendikten sonra, kümenizin başarılı bir durumda olması gerekir. Başarılı bir durum doğrulandıktan sonra, özgün işlemi yeniden deneyin.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Bu durumun yükseltilme veya ölçeklendirilmesi sırasında hata alıyorum, bu durum kümemdeki Şu anda yükseltilmekte veya yükseltme başarısız oldu

*Bu sorun giderme yardımı https://aka.ms/aks-pending-upgrade yönlendirilir*

Tek düğümlü havuz veya [birden çok düğüm](use-multiple-node-pools.md) havuzu içeren bir küme ile bir kümede yükseltme ve ölçeklendirme işlemleri birbirini dışlıyor. Aynı anda yükseltme ve ölçeklendirme için bir küme veya düğüm havuzunuz olamaz. Bunun yerine, her işlem türünün aynı kaynaktaki bir sonraki istekten önce hedef kaynakta tamamlaması gerekir. Sonuç olarak, etkin yükseltme veya ölçeklendirme işlemleri gerçekleşirken veya denendiğinde ve daha sonra başarısız olduğunda işlemler sınırlıdır. 

`az aks show -g myResourceGroup -n myAKSCluster -o table` çalıştırma sorunu tanılamaya yardımcı olmak için kümenizde ayrıntılı durum alma. Sonuca göre:

* Küme etkin bir şekilde yükseltildiğinde, işlem sonlanana kadar bekleyin. Başarılı olduysa, daha önce başarısız olan işlemi yeniden deneyin.
* Kümede yükseltme başarısız olursa, önceki bölümde açıklanan adımları izleyin.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kümemi farklı bir aboneliğe veya kümeme yeni bir kiracıya sahip aboneliğime taşıyabilir miyim?

AKS kümenizi farklı bir aboneliğe veya kümeye sahip olan aboneliğe yeni bir kiracıya taşıdıysanız, rol atamalarının ve hizmet sorumlusu haklarının kaybolması nedeniyle küme işlevselliği kaybeder. Aks 'ler, bu kısıtlama nedeniyle **kümelerin abonelikler veya kiracılar arasında taşınmasını desteklemez** .

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri gerektiren özellikleri kullanmaya çalışırken hata alıyorum

*Bu sorun giderme yardımı aka.ms/aks-vmss-enablement 'ten yönlendirilir*

AKS kümenizin, aşağıdaki örnekte olduğu gibi bir sanal makine ölçek kümesi üzerinde olmadığını belirten hatalar alabilirsiniz:

**AgentPool ' agentpool ' otomatik ölçeklendirmeyi etkin olarak ayarladı, ancak sanal makine ölçek kümelerinde değil**

Küme otomatik veya birden çok düğüm havuzu gibi özellikleri kullanmak için, sanal makine ölçek kümelerini kullanan AKS kümelerinin oluşturulması gerekir. Sanal makine ölçek kümelerine bağımlı olan özellikleri kullanmayı denerseniz ve normal, sanal olmayan bir makine ölçek kümesi AKS kümesini hedeflerseniz hatalar döndürülür.

Bir AKS kümesini doğru şekilde oluşturmak için uygun belge içindeki *başlamadan önce* ' i izleyin:

* [Küme otomatik Scaler 'ı kullanma](cluster-autoscaler.md)
* [Birden çok düğüm havuzu oluşturma ve kullanma](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS kaynakları ve parametreleri için hangi adlandırma kısıtlamaları zorlanır?

*Bu sorun giderme yardımı aka.ms/aks-naming-rules 'ten yönlendirilir*

Adlandırma kısıtlamaları hem Azure platformu hem de AKS tarafından uygulanır. Bir kaynak adı veya parametresi bu kısıtlamaların birini keserse, farklı bir giriş sağlamanızı isteyen bir hata döndürülür. Aşağıdaki ortak adlandırma yönergeleri geçerlidir:

* Küme adları 1-63 karakter olmalıdır. Yalnızca harf, sayı, kısa çizgi ve alt çizgi olan karakterler izin verilir. İlk ve son karakter bir harf veya sayı olmalıdır.
* AKS *Mc_* kaynak grubu adı, kaynak grubu adını ve kaynak adını birleştirir. `MC_resourceGroupName_resourceName_AzureRegion` otomatik olarak oluşturulan sözdizimi 80 karakterden büyük olmamalıdır. Gerekirse, kaynak grubu adınızın veya AKS kümesi adınızın uzunluğunu azaltın.
* *Dnspredüzeltmesinin* alfasayısal değerlerle başlaması ve bitmesi gerekir. Geçerli karakterler alfasayısal değerleri ve kısa çizgileri (-) içerir. *Dnspredüzeltmesini* nokta (.) gibi özel karakterler içeremez.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Küme oluşturmaya, güncelleştirmeye, ölçeklendirmeye, silmeye veya yükseltmeye çalışırken hata alıyorum, devam eden başka bir işlem olduğundan bu işleme izin verilmiyor.

*Bu sorun giderme yardımı aka.ms/aks-pending-operation 'ten yönlendirilir*

Önceki bir işlem devam ederken küme işlemleri sınırlıdır. Kümenizin ayrıntılı durumunu almak için `az aks show -g myResourceGroup -n myAKSCluster -o table` komutunu kullanın. Gerektiğinde kendi kaynak grubunuzu ve AKS küme adınızı kullanın.

Küme durumunun çıkışına göre:

* Küme, *başarılı* veya *başarısız*dışında bir sağlama durumundaysa, Işlem (*yükseltme/güncelleştirme/oluşturma/ölçeklendirme/silme/geçirme*) bitene kadar bekleyin. Önceki işlem tamamlandığında, en son küme işleminizi yeniden deneyin.

* Kümenin başarısız bir yükseltmesi varsa, ana belirtilen adımları izleyerek [Kümemin başarısız durumunda olduğunu ve yükseltme ya da ölçeklendirmeyi düzeltilene kadar çalışmayacaktır](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Mevcut bir kümeyi geçirmeden yeni bir küme oluşturmaya çalıştığımda hizmet sorumlumun bulunamadığını belirten hatalar alıyorum.

Bir AKS kümesi oluştururken sizin adınıza kaynak oluşturmak için hizmet sorumlusu gerekir. AKS, küme oluşturma sırasında yeni bir tane oluşturma özelliği sunar, ancak bu, kümenin oluşturma işleminin başarılı olması için yeni hizmet sorumlusunu uygun bir süre içinde tam olarak yaymak için Azure Active Directory gerektirir. Bu yayma çok uzun sürerse, küme, kullanılabilir bir hizmet sorumlusu bulamadığından, oluşturulması için doğrulama başarısız olur. 

Bunun için aşağıdaki geçici çözümleri kullanın:
1. Bölgelere zaten yayılan ve küme oluşturma zamanında AKS 'e geçirilecek mevcut bir hizmet sorumlusunu kullanın.
2. Otomasyon betikleri kullanıyorsanız, hizmet sorumlusu oluşturma ve AKS kümesi oluşturma arasında zaman gecikmeleri ekleyin.
3. Azure portal kullanıyorsanız, oluşturma sırasında küme ayarlarına dönün ve birkaç dakika sonra doğrulama sayfasını yeniden deneyin.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Çıkış trafiğinizi kısıtladıktan sonra hata alıyorum

AKS kümesinden çıkış trafiği kısıtlandığında, [gerekli ve isteğe bağlı olarak önerilen](limit-egress-traffic.md) giden bağlantı noktaları/ağ kuralları ve aks için FQDN/uygulama kuralları vardır. Ayarlarınız bu kuralların herhangi biriyle çakışıyorsa, bazı `kubectl` komutlarını çalıştıramayabilir. AKS kümesi oluştururken de hata görebilirsiniz.

Ayarlarınızın gerekli veya isteğe bağlı önerilen giden bağlantı noktaları/ağ kuralları ve FQDN/uygulama kuralları ile çakışmadığından emin olun.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure depolama ve AKS sorunlarını giderme

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Azure disk için, Kubernetes 'in önerilen kararlı sürümleri nelerdir? 

| Kubernetes sürümü | Önerilen sürüm |
| -- | :--: |
| 1,12 | 1.12.9 veya üzeri |
| 1,13 | 1.13.6 veya üzeri |
| 1,14 | 1.14.2 veya üzeri |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Kubernetes 'in hangi sürümleri, Sovereign bulutu üzerinde Azure disk desteğine sahip?

| Kubernetes sürümü | Önerilen sürüm |
| -- | :--: |
| 1,12 | 1.12.0 veya üzeri |
| 1,13 | 1.13.0 veya üzeri |
| 1,14 | 1.14.0 veya üzeri |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure disk için WaitForAttach başarısız oldu: "/dev/disk/Azure/scsi1/lun1" Ayrıştırılıyor: geçersiz sözdizimi

Kubernetes sürüm 1,10 ' de, Bağlamabirimi. WaitForAttach, Azure disk uzaktan bağlantısı ile başarısız olabilir.

Linux 'ta yanlış bir DevicePath biçim hatası görebilirsiniz. Örneğin:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

Windows 'ta yanlış bir DevicePath (LUN) numarası hatası görebilirsiniz. Örneğin:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
| -- | :--: |
| 1,10 | 1.10.2 veya üzeri |
| 1,11 | 1.11.0 veya üzeri |
| 1,12 ve üzeri | Yok |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Azure diski için mountOptions 'da uid ve GID ayarlanırken hata oluştu

Azure disk, varsayılan olarak Ext4, XFS FileSystem ve UID = x, GID = x gibi bir bağlama zamanında ayarlanamaz. Örneğin, mountOptions uid = 999, GID = 999 ' u ayarlamaya çalıştıysanız, şöyle bir hata görür:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Aşağıdakilerden birini yaparak sorunu azaltabilirsiniz:

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
  > GID ve uid, varsayılan olarak kök veya 0 olarak bağlandığından. GID veya Uid, kök olmayan olarak ayarlandıysa, örneğin 1000, Kubernetes, bu disk altındaki tüm dizinleri ve dosyaları değiştirmek için `chown` kullanacaktır. Bu işlem zaman alabilir ve diski bağlama işlemi çok yavaş olabilir.

* GID ve uid ayarlamak için ınitcontainers içinde `chown` kullanın. Örneğin:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Pod tarafından kullanılan Azure disk PersistentVolumeClaim silinirken hata oluştu

Pod tarafından kullanılmakta olan bir Azure disk PersistentVolumeClaim 'yi silmeye çalışırsanız bir hata görebilirsiniz. Örneğin:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

Kubernetes sürüm 1,10 ve üzeri sürümlerde, bu hatayı engellemek için varsayılan olarak etkinleştirilen bir PersistentVolumeClaim koruma özelliği vardır. Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız, PersistentVolumeClaim 'yi silmeden önce PersistentVolumeClaim kullanarak Pod 'u silerek bu sorunu azaltabilirsiniz.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Bir düğüme disk eklenirken "disk için LUN bulunamıyor" hatası

Bir düğüme bir disk iliştirirken, şu hatayı görebilirsiniz:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
| -- | :--: |
| 1,10 | 1.10.10 veya üzeri |
| 1,11 | 1.11.5 veya üzeri |
| 1,12 | 1.12.3 veya üzeri |
| 1,13 | 1.13.0 veya üzeri |
| 1,14 ve üzeri | Yok |

Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız, birkaç dakika bekleyip yeniden denemeden sorunu azaltabilirsiniz.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Çoklu iliştirme/ayırma işlemleri sırasında Azure disk iliştirme/ayırma hatası, bağlama sorunları veya g/ç hataları

Kubernetes sürüm 1.9.2 'dan başlayarak, paralel olarak birden çok iliştirme/ayır işlemi çalıştırırken, bir kirli VM önbelleği nedeniyle aşağıdaki disk sorunlarını görebilirsiniz:

* Disk iliştirme/ayırma sorunları
* Disk g/ç hataları
* VM 'den beklenmeyen disk kesilmesi
* Mevcut olmayan disk iliştirilirken VM, başarısız durumunda çalışıyor

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
| -- | :--: |
| 1,10 | 1.10.12 veya üzeri |
| 1,11 | 1.11.6 veya üzeri |
| 1,12 | 1.12.4 veya üzeri |
| 1,13 | 1.13.0 veya üzeri |
| 1,14 ve üzeri | Yok |

Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız, aşağıdaki adımları deneyerek sorunu azaltabilirsiniz:

* Bir disk uzun bir süre ayrılmayı bekliyorsa, diski el ile ayırmayı deneyin

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure diski sonsuza kadar ayrılmayı bekliyor

Bazı durumlarda, ilk denemede bir Azure disk ayırma işlemi başarısız olursa, ayırma işlemini yeniden denemez ve özgün düğüm sanal makinesine bağlı olarak kalır. Bu hata, bir diski bir düğümden diğerine taşırken ortaya çıkabilir. Örneğin:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
| -- | :--: |
| 1,11 | 1.11.9 veya üzeri |
| 1,12 | 1.12.7 veya üzeri |
| 1,13 | 1.13.4 veya üzeri |
| 1,14 ve üzeri | Yok |

Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız, diski el ile ayırarak sorunu azaltabilirsiniz.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Olası yarış durumu sorununa ve geçersiz veri diski listesine önde gelen Azure disk ayırma hatası

Bir Azure diski ayrılmazsa, üstel geri kapatmayı kullanarak diski ayırmak en fazla altı kez yeniden dener. Ayrıca, yaklaşık 3 dakika boyunca veri diski listesinde düğüm düzeyinde bir kilit da tutar. Bu süre boyunca bir el ile iliştirme veya ayırma işlemi gibi disk listesi el ile güncelleniyorsa, bu, düğüm düzeyi kilidi tarafından tutulan disk listesinin kullanımdan kalkmasına ve düğüm sanal makinesinde kararsızlığa neden olur.

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
| -- | :--: |
| 1,12 | 1.12.9 veya üzeri |
| 1,13 | 1.13.6 veya üzeri |
| 1,14 | 1.14.2 veya üzeri |
| 1,15 ve üzeri | Yok |

Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız ve düğüm sanal makinenizin kullanım dışı bir disk listesi varsa, mevcut olmayan tüm diskleri VM 'den tek bir toplu işlem olarak ayırarak sorunu azaltabilirsiniz. **Mevcut olmayan diskleri tek tek ayırmak başarısız olabilir.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Çok sayıda Azure diski yavaş iliştirme/ayır oluşmasına neden oluyor

Bir düğüm sanal makinesine bağlı Azure disk sayısı 10 ' dan büyükse, Attach ve Detach işlemleri yavaş olabilir. Bu sorun bilinen bir sorundur ve şu anda geçici çözüm bulunmamaktadır.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Başarısız durumundaki olası düğüm VM 'sine yönelik Azure disk ayırma hatası

Bazı Edge durumlarında, Azure disk ayırma kısmen başarısız olabilir ve düğüm VM 'sini başarısız bir durumda bırakabilir.

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
| -- | :--: |
| 1,12 | 1.12.10 veya üzeri |
| 1,13 | 1.13.8 veya üzeri |
| 1,14 | 1.14.4 veya üzeri |
| 1,15 ve üzeri | Yok |

Bu sorun için düzeltilmesi olmayan bir Kubernetes sürümü kullanıyorsanız ve Node VM 'niz başarısız durumdaysa, aşağıdakilerden birini kullanarak VM durumunu el ile güncelleştirerek sorunu azaltabilirsiniz:

* Kullanılabilirlik kümesi tabanlı küme için:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Bir VMSS tabanlı küme için:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure dosyaları ve AKS sorunlarını giderme

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Azure dosyaları için, Kubernetes 'in önerilen kararlı sürümleri nelerdir?
 
| Kubernetes sürümü | Önerilen sürüm |
| -- | :--: |
| 1,12 | 1.12.6 veya üzeri |
| 1,13 | 1.13.4 veya üzeri |
| 1,14 | 1.14.0 veya üzeri |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Kubernetes 'in hangi sürümleri, Sovereign bulutu üzerinde Azure dosyaları destekliyor?

| Kubernetes sürümü | Önerilen sürüm |
| -- | :--: |
| 1,12 | 1.12.0 veya üzeri |
| 1,13 | 1.13.0 veya üzeri |
| 1,14 | 1.14.0 veya üzeri |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Azure dosyaları kullanılırken varsayılan mountOptions nedir?

Önerilen Ayarlar:

| Kubernetes sürümü | fileMode ve dirMode değeri|
| -- | :--: |
| 1.12.0-1.12.1 | 0755 |
| 1.12.2 ve üzeri | 0777 |

Kuberetes sürüm 1.8.5 veya üzerini içeren bir küme kullanıyorsanız ve kalıcı birimi bir depolama sınıfıyla dinamik olarak oluşturursanız, depolama sınıfı nesnesinde bağlama seçenekleri belirtilebilir. Aşağıdaki örnek *0777*olarak ayarlanır:

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

* *mfsymlinks* , Azure dosyaları bağlama (CIFS) sembolik bağlantıları destekliyor
* *nobrl* , bayt aralığı kilit isteklerinin sunucuya gönderilmesini engelleyecek. Bu ayar, CIFS stili zorunlu bayt aralığı kilitleriyle kesen belirli uygulamalar için gereklidir. Çoğu CIFS sunucusu henüz danışmanlık bayt aralığı kilitlerini istemeyi desteklemez. *Nobrl*kullanmıyorsanız, CIFS stili zorunlu bayt aralığı kilitleri ile kesen uygulamalar aşağıdakine benzer hata iletilerine neden olabilir:
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

Bu sorunu çözmek için, Azure disk eklentisi ile birlikte alt *yol* kullanın. 

> [!NOTE] 
> Ext3/4 disk türü için disk biçimlendirildikten sonra kayıp + bulunan bir dizin bulunur.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure dosyaları, birçok küçük dosyayı işlerken Azure diskine kıyasla yüksek gecikme süresine sahiptir

Birçok küçük dosyayı işleme gibi bazı durumlarda, Azure disk ile karşılaştırıldığında Azure dosyalarını kullanırken yüksek gecikme süresine karşılaşabilirsiniz.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Depolama hesabı 'ndaki "seçili ağdan erişime izin ver" ayarı etkinleştirilirken hata oluştu

AKS 'de dinamik sağlama için kullanılan bir depolama hesabındaki *Seçili ağdan erişime izin ver* ' i etkinleştirirseniz, aks 'ler bir dosya paylaşma oluşturduğunda bir hata alırsınız:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Bu hata, *Seçili ağdan erişime izin ver*ayarı yapılırken, Kubernetes *persistentvolume-Controller tarafından* seçilen ağ üzerinde olmayan bir hatadır.

[Azure dosyaları ile statik sağlamayı](azure-files-volume.md)kullanarak sorunu azaltabilirsiniz.

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure dosyaları Windows Pod 'da yeniden bağlama yapamıyor

Azure dosyaları bağlaması olan bir Windows Pod silinirse ve aynı düğümde yeniden oluşturulmak üzere zamanlanırsa, bağlama başarısız olur. Bu hata, Azure dosyaları bağlama, düğüme zaten bağlı olduğundan `New-SmbGlobalMapping` komutunun başarısız olmasından kaynaklanır.

Örneğin şuna benzer bir hata görebilirsiniz:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Bu sorun aşağıdaki Kubernetes sürümlerinde düzeltildi:

| Kubernetes sürümü | Sabit sürüm |
| -- | :--: |
| 1,12 | 1.12.6 veya üzeri |
| 1,13 | 1.13.4 veya üzeri |
| 1,14 ve üzeri | Yok |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Azure dosyaları bağlama, depolama hesabı anahtarı değiştiği için başarısız oluyor

Depolama hesabı anahtarınız değiştiyse Azure dosyaları bağlama hatalarıyla karşılaşabilirsiniz.

*Azurestokgeaccountkey* alanını Azure dosya gizli dizisi ' nde Base64 kodlamalı depolama hesabı anahtarınızla el ile güncelleştirerek sorunu azaltabilirsiniz.

Depolama hesabı anahtarınızı Base64 olarak kodlamak için `base64`kullanabilirsiniz. Örneğin:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Azure gizli dosyanızı güncelleştirmek için `kubectl edit secret`kullanın. Örneğin:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Birkaç dakika sonra, aracı düğümü güncelleştirilmiş depolama anahtarıyla Azure dosya takasını yeniden dener.

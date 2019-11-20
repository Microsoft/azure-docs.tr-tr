---
title: İşleç en iyi uygulamalar - Azure Kubernetes Hizmetleri (AKS) küme güvenliği
description: Küme güvenliği ve yükseltmeleri Azure Kubernetes Service (AKS) nasıl yönetileceği küme işleci en iyi uygulamaları öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 46e44804ddbabd8bf5620ad9516f1ca2d5017bfa
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019303"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Küme güvenliği ve yükseltmeler Azure Kubernetes Service (AKS) için en iyi uygulamalar

Azure Kubernetes Service (AKS) kümeleri yönetme gibi iş yüklerinde ve veri güvenliğini önemli bir noktadır. Özellikle, mantıksal yalıtım kullanarak çok kiracılı kümeleri çalıştırdığınızda, kaynaklar ve iş yüklerini güvenli erişim gerekir. Saldırı riskini en aza indirmek için Ayrıca düğümün işletim sistemi güvenlik güncelleştirmelerini ve en son Kubernetes geçerli olduğundan emin olmamız gerekir.

Bu makalede, AKS kümenizin güvenliğini sağlama konusunda odaklanır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * API sunucusu erişimi güvenli hale getirmek için Azure Active Directory ve rol tabanlı erişim denetimleri kullanma
> * Kapsayıcı düğümü kaynaklarına güvenli erişim
> * Bir AKS kümesi son Kubernetes sürümüne yükseltmek için
> * Düğümleri güncelleştirme güncel tutulmasını ve güvenlik düzeltme eklerini otomatik olarak Uygula

Ayrıca, [kapsayıcı görüntüsü yönetimi][best-practices-container-image-management] ve [Pod güvenliği][best-practices-pod-security]için en iyi yöntemleri okuyabilirsiniz.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>API sunucu ile küme düğümleri erişimin güvenliğini sağlama

**En iyi uygulama kılavuzunu** -Kubernetes API sunucuya erişim güvenliğini sağlama, kümenizin güvenliğini sağlamak için yapabileceğiniz en önemli şeylerden biridir. Kubernetes rol tabanlı erişim denetimi (RBAC) API sunucusuna erişimi denetlemek için Azure Active Directory ile tümleştirin. Bu denetimler, AKS Azure aboneliklerinize erişimi güvenli şekilde güvenli hale getirmenize olanak tanır.

Kubernetes API sunucusuna bir küme içindeki eylemleri gerçekleştirmek istekleri için bir tek bağlantı noktası sağlar. Güvenli ve API sunucusuna erişimi denetlemek için erişimi sınırlamak ve gereken en az ayrıcalıklı erişim izinleri sağlar. Bu yaklaşım, Kubernetes için benzersiz değildir, ancak çok kiracılı kullanılmak üzere mantıksal olarak yalıtılmış bir AKS kümesi olduğunda özellikle önemlidir.

Azure Active Directory (AD), AKS kümeleri ile tümleşen bir kurumsal kullanıma hazır kimlik yönetimi çözümü sağlar. Kubernetes, bir kimlik yönetimi çözümü sağlamayan gibi Aksi takdirde API sunucusuna erişimi kısıtlamak için ayrıntılı bir şekilde sağlamak zor olabilir. Azure AD ile tümleşik kümeleriyle aks'deki, API sunucusu için kullanıcıların kimliğini doğrulamak için mevcut kullanıcı ve grup hesapları kullanırsınız.

![AKS kümeleri için Azure Active Directory Tümleştirmesi](media/operator-best-practices-cluster-security/aad-integration.png)

Kubernetes RBAC ve Azure API sunucusunun güvenliğini sağlama ve az sağlamak için AD tümleştirme tek bir ad alanı gibi kaynaklar kapsamlı bir dizi gerekli izinler sayısı. Farklı kullanıcılar veya gruplar Azure AD'de farklı RBAC rollerini verilebilir. Bu ayrıntılı izinler API sunucusuna erişimi kısıtlamak ve gerçekleştirilen eylemlerin bir açık denetim izi sağlamak belirlemenizi sağlar.

Önerilen en iyi uygulama grupları dosyalara ve klasörlere ayrı kimlik ve erişim sağlamak için Azure AD kullanma kullanmaktır *grubu* kullanıcıları tek tek yerine RBAC rollerini bağlamak için üyelik *kullanıcılar*. Bir kullanıcının grup üyeliği değişiklikleri olarak bunların erişim izinlerini AKS kümesinde uygun şekilde değiştirirsiniz. Bir role kullanıcı bağlarsanız, kendi iş işlevi değiştirebilir. Azure AD grup üyeliklerini güncelleştirmeniz gerekir, ancak izinleri AKS kümesi, yansıtmıyor. Bu senaryoda, kullanıcı, bir kullanıcı gerektirir. daha fazla izin verilmeden sona erer.

Azure AD tümleştirmesi ve RBAC hakkında daha fazla bilgi için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Kapsayıcı kaynaklarına güvenli erişim

**En iyi uygulama kılavuzunu** -kapsayıcıları gerçekleştirebileceği eylemleri erişimi. En az sağlayan izinleri sayısı ve kök kullanmaktan kaçının / ayrıcalıklı yükseltme.

En az kullanıcılar veya gruplar vermelisiniz aynı şekilde ayrıcalıkları sayısı gerekli, kapsayıcılar da eylemleri ve ihtiyaç duydukları işlemleri yalnızca sınırlı olmalıdır. Saldırı riskini en aza indirmek için uygulamaları ve ilerletilen ayrıcalıkları gerektiren veya kök erişim kapsayıcıları yapılandırmayın. Örneğin, `allowPrivilegeEscalation: false` pod katıştırır. Bunlar *güvenlik kapsamları pod* Kubernetes ve kullanıcı veya grup gibi ek izinler olarak çalıştırmak için tanımlamanıza imkan tanır yerleşiktir ya da Linux özellikleri göstermek için. Daha iyi uygulamalar için bkz. [kaynaklara güvenli Pod erişimi][pod-security-contexts].

Kapsayıcı işlemlerin daha ayrıntılı denetim için yerleşik Linux güvenlik özellikleri gibi kullanabilirsiniz *AppArmor* ve *seccomp*. Bu özellikler düğümü düzeyinde tanımlanan ve ardından bir pod bildirimi aracılığıyla uygulanır. Yerleşik Linux güvenlik özellikleri yalnızca Linux düğümlerinde ve yığınların üzerinde kullanılabilir.

> [!NOTE]
> Kubernetes ortamları, AKS veya başka bir yerde, çok kiracılı Kullanıcı kullanımı için tamamen güvenli değildir. Düğümler için *AppArmor*, *seccomp*, *Pod güvenlik ilkeleri*veya daha AYRıNTıLı rol tabanlı erişim denetimleri (RBAC) gibi ek güvenlik özellikleri, güvenli hale getirme daha zordur. Ancak, çok kiracılı çoklu kiracı iş yüklerini çalıştırırken doğru güvenlik için bir hiper yönetici, güvenmeniz gereken tek güvenlik düzeyidir. Kubernetes güvenlik etki alanı, tek bir düğüm değil, tüm küme haline gelir. Bu tür çok kiracılı iş yükleri için, fiziksel olarak yalıtılmış kümeler kullanmanız gerekir.

### <a name="app-armor"></a>Uygulama Armor

Kapsayıcıların gerçekleştirebileceği eylemleri sınırlandırmak için [AppArmor][k8s-apparmor] Linux çekirdek güvenlik modülünü kullanabilirsiniz. AppArmor kullanılabilir işletim sistemi, temel alınan AKS düğümü bir parçası olarak ve varsayılan olarak etkindir. AppArmor eylemler gibi kısıtlama profillerini okuma, yazma veya yürütme ya da dosya sistemleri bağlama gibi sistem işlevlerini oluşturursunuz. Varsayılan AppArmor profilleri çeşitli erişimi kısıtlamak `/proc` ve `/sys` konumları ve temel alınan düğümünden kapsayıcılar mantıksal olarak ayırmak için bir yol sağlar. Yalnızca Kubernetes pod'larını Linux üzerinde çalışan herhangi bir uygulama için AppArmor çalışır.

![Kapsayıcı eylemlerine sınırlamak için bir AKS kümesi kullanımda AppArmor profilleri](media/operator-best-practices-container-security/apparmor.png)

AppArmor nasıl çalıştığını görmek için aşağıdaki örnek dosyalara yazma engelleyen bir profili oluşturur. [SSH][aks-ssh] 'YI BIR aks düğümüne, ardından *deny-Write. Profile* adlı bir dosya oluşturun ve aşağıdaki içeriği yapıştırın:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor profilleri kullanılarak eklenir `apparmor_parser` komutu. Profili eklemek için AppArmor ve önceki adımda oluşturulan profilin adını belirtin:

```console
sudo apparmor_parser deny-write.profile
```

Profilin doğru bir şekilde ayrıştırılır ve AppArmor için uygulanan olursa çıktı yok. Komut istemine geri dönersiniz.

Yerel makinenizden artık adlı pod bildirim oluşturmak *aks apparmor.yaml* ve aşağıdaki içeriği yapıştırın. Bu bildirim için bir ek açıklama tanımlar `container.apparmor.security.beta.kubernetes` başvurular ekleme *Reddet yazma* önceki adımlarda oluşturulan profil:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak örnek Pod 'u dağıtın:

```console
kubectl apply -f aks-apparmor.yaml
```

Pod ile dağıtılan bir dosyaya yazmak için [kubectl exec][kubectl-exec] komutunu kullanın. Aşağıdaki örnek çıktıda gösterildiği gibi komutu yürütülemiyor:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

AppArmor hakkında daha fazla bilgi için bkz. [Kubernetes 'Te AppArmor profilleri][k8s-apparmor].

### <a name="secure-computing"></a>Bilgi işlem güvenliğini sağlama

AppArmor tüm Linux uygulamaları için çalışırken, [seccomp (*San*) işlem][seccomp] düzeyinde çalışmaktadır. Seccomp aynı zamanda bir Linux çekirdek güvenlik modülü olan ve AKS düğümleri tarafından kullanılan Docker çalışma zamanı tarafından yerel olarak desteklenir. Kapsayıcıları gerçekleştirebilirsiniz işlem çağrıları seccomp ile sınırlıdır. İzin verme veya reddetme eylemleri tanımlayın. filtre oluşturma ve seccomp filtresiyle ilişkilendirilebilmesi için ek açıklamalar içindeki bir pod YAML bildirimi'i kullanın. Bu, yalnızca kapsayıcı çalıştırmak için gereken en az düzeyde izinleri verme ve artık en iyi uygulama hizalar.

Seccomp iş başında görmek için değiştirme engelleyen bir filtre oluşturun. bir dosya üzerindeki izinleri. Bir AKS düğümüne [SSH][aks-ssh] ekleyin, ardından */var/lib/kubelet/seccomp/prevent-chmod* adlı bir seccomp filtresi oluşturun ve aşağıdaki içeriği yapıştırın:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Yerel makinenizden artık adlı pod bildirim oluşturmak *aks seccomp.yaml* ve aşağıdaki içeriği yapıştırın. Bu bildirim için bir ek açıklama tanımlar `seccomp.security.alpha.kubernetes.io` ve başvurur *önlemek chmod* önceki adımda oluşturduğunuz Filtresi:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak örnek Pod 'u dağıtın:

```console
kubectl apply -f ./aks-seccomp.yaml
```

[Kubectl Get Pod][kubectl-get] komutunu kullanarak Pod 'nin durumunu görüntüleyin. Pod bir hata bildirir. `chmod` Komut çalışması engellenir seccomp filtre tarafından aşağıdaki örnek çıktıda gösterildiği gibi:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Kullanılabilir filtreler hakkında daha fazla bilgi için bkz. [Docker Için Seccomp güvenlik profilleri][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Düzenli olarak kubernetes en son sürüme güncelleştirme

**En iyi uygulama kılavuzunu** - yeni özellikler ve hata düzeltmeleri, AKS kümenizde Kubernetes sürümüne yükseltmek düzenli olarak güncel kalın.

Kubernetes, yeni özellikleri daha geleneksel altyapı platformları daha hızlı bir hızda serbest bırakır. Kubernetes güncelleştirmeleri, yeni özellikler ve hata veya güvenlik düzeltmeleri içerir. Yeni özellikler genellikle taşıma yoluyla bir *alfa* ardından *beta* haline gelmeden önce durumu *kararlı* ve genel olarak kullanılabilir ve üretim kullanımı için önerilen. Bu sürüm döngüsü Kubernetes düzenli olarak yeni değişiklikler karşılaşıldığında veya dağıtımları ve şablonları ayarlama olmadan güncelleştirmenize izin vermelidir.

AKS, dört alt Kubernetes sürümlerini destekler. Bu, yeni bir ikincil düzeltme eki sürümü eklendiğinde, desteklenen en eski ikincil sürüm ve yama sürümler kullanımdan anlamına gelir. Kubernetes için küçük güncelleştirmeler düzenli aralıklarla gerçekleşecek. Denetleyin ve destek kapsamı dışında kalan yoksa için gereken şekilde yükseltmek için idare işlemi olduğundan emin olun. Daha fazla bilgi için bkz. [desteklenen Kubernetes sürümleri AKS][aks-supported-versions]

Kümeniz için kullanılabilen sürümleri denetlemek için, aşağıdaki örnekte gösterildiği gibi [az aks Get-yükseltmeler][az-aks-get-upgrades] komutunu kullanın:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Daha sonra, [az aks Upgrade][az-aks-upgrade] komutunu kullanarak aks kümenizi yükseltebilirsiniz. Yükseltme işlemi güvenli bir şekilde cordons ve aynı anda bir düğümü boşaltır, pod'ların Kalan düğümlerde zamanlar ve sonra en son işletim sistemi ve Kubernetes sürümlerini çalıştıran yeni bir düğüm dağıtır.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

AKS 'teki yükseltmeler hakkında daha fazla bilgi için bkz. [aks 'de desteklenen Kubernetes sürümleri][aks-supported-versions] ve [aks kümesini yükseltme][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux düğüm güncelleştirmelerini işleme ve kured kullanarak yeniden başlatmalar

**En iyi Yöntem Kılavuzu** -aks her bir Linux düğümünde güvenlik düzeltmelerini otomatik olarak indirir ve yükler, ancak gerektiğinde otomatik olarak yeniden başlatılır. Kullanım `kured` izlemesi yeniden başlatmalar, ardından güvenli bir şekilde kordon altına alma ve düğümü yeniden başlatmak izin vermek için düğüm boşaltma için güncelleştirmelerini ve işletim sistemi ile ilgili mümkün olduğu kadar güvenli. Windows Server düğümleri için (Şu anda AKS 'de önizlemededir), büyük bir şekilde bir AKS yükseltme işlemi gerçekleştirip, büyük/veya dışarı ve güncelleştirilmiş düğümleri dağıtır.

AKS içindeki her akşam Linux düğümü, kendi sahip oldukları güncelleştirme kanalıyla sunulan güvenlik düzeltme eklerini alır. Bu davranış, bir AKS kümesinde düğümlere dağıtılmış olarak otomatik olarak yapılandırılır. Kesintisi ve çalışan iş yükleri için olası etkisini en aza indirmek için düğümleri otomatik olarak bir güvenlik düzeltme eki, yeniden başlatılır değil veya çekirdek güncelleştirme gerektiriyor.

Açık kaynaklı [kured (KUbernetes önyükleme cini)][kured] projesi, bekleyen düğüm yeniden başlatmaları için izler. Bir Linux düğümü, yeniden başlatma gerektiren güncelleştirmeler uygularsa, düğüm, kümedeki diğer düğümlerde bulunan düğümleri taşımak ve zamanlamak için güvenli bir şekilde donmış ve taşınabilir. Düğüm yeniden başlatıldıktan sonra Kubernetes sürdürür üzerindeki pod'ları zamanlama ve küme yeniden içine eklenir. Uğramasını azaltmak için aynı anda yalnızca tek bir düğüm tarafından başlatılması izin `kured`.

![Kured kullanarak AKS düğümü yeniden başlatma işlemi](media/operator-best-practices-cluster-security/node-reboot-process.png)

Ne zaman yeniden başlatma gerçekleşir, ince detaylı denetim istiyorsanız `kured` diğer bakım olayları veya devam eden küme sorunları varsa, yeniden başlatma işlemlerini önlemek için Prometheus ile tümleştirebilirsiniz. Bu tümleştirme ek zorluklar diğer sorunlarını giderme etkin durumdayken düğümlerin yeniden başlatılması en aza indirir.

Düğüm yeniden başlatmaların nasıl işleneceği hakkında daha fazla bilgi için bkz. [AKS 'deki düğümlere güvenlik ve çekirdek güncelleştirmelerini uygulama][aks-kured].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümenizin güvenliğini sağlama konusunda odaklanır. Bu alanlardan bazıları uygulamak için aşağıdaki makalelere bakın:

* [Azure Active Directory AKS ile tümleştirme][aks-aad]
* [Bir AKS kümesini Kubernetes 'in en son sürümüne yükseltme][aks-upgrade]
* [Kured ile güvenlik güncelleştirmelerini ve düğüm yeniden başlatmaları işleme][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md

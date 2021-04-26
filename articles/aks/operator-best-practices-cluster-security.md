---
title: Küme güvenliği için en iyi uygulamalar
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ' de küme güvenliğini ve yükseltmelerini yönetme için en iyi küme işletmeni uygulamalarını öğrenin
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 5cb103d843aafbb7f72c03d65b45fe3a84f8d1cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782990"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) üzerinde küme güvenliği ve yükseltmeleri için en iyi uygulamalar

Azure Kubernetes Service (AKS) içindeki kümeleri yönetirken, iş yükü ve veri güvenliği önemli bir konudur. Mantıksal yalıtım kullanarak çok kiracılı kümeler çalıştırdığınızda, özellikle kaynak ve iş yükü erişimini güvenli hale getirmeniz gerekir. En son Kubernetes ve düğüm işletim sistemi güvenlik güncelleştirmelerini uygulayarak saldırı riskini en aza indirin.

Bu makalede AKS kümenizin güvenliğini sağlama konusuna odaklanılmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * API sunucusu erişimini güvenli hale getirmek için Azure Active Directory ve Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) kullanın.
> * Düğüm kaynaklarına güvenli kapsayıcı erişimi.
> * Bir AKS kümesini en son Kubernetes sürümüne yükseltin.
> * Düğümleri güncel tutun ve güvenlik düzeltme eklerini otomatik olarak uygulayın.

Ayrıca, [kapsayıcı görüntüsü yönetimi][best-practices-container-image-management] ve [Pod güvenliği][best-practices-pod-security]için en iyi yöntemleri okuyabilirsiniz.

Tehditleri algılamaya ve AKS kümelerinizi korumaya yönelik önerileri görüntülemenize yardımcı olması için [Güvenlik Merkezi Ile Azure Kubernetes Hizmetleri tümleştirmesini][security-center-aks] da kullanabilirsiniz.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>API sunucusuna ve küme düğümlerine güvenli erişim

> **En iyi yöntemler kılavuzu** 
>
> Kümenizi güvenli hale getirmeye yönelik en önemli yollarla, Kubernetes API sunucusuna erişimin güvenli hale getirilmesine izin verilir. API sunucusuna erişimi denetlemek için Kubernetes RBAC 'i Azure Active Directory (Azure AD) ile tümleştirin. Bu denetimlerle, Azure aboneliklerinize erişiminizi güvenli hale getirmenin yanı da aynı şekilde güvenliği sağlayabilirsiniz.

Kubernetes API sunucusu, bir küme içinde eylemleri gerçekleştirmek için istekler için tek bir bağlantı noktası sağlar. API sunucusuna erişimi güvenli hale getirmek ve denetlemek için erişimi sınırlayın ve mümkün olan en düşük izin düzeylerini sağlayın. Bu yaklaşım Kubernetes 'e özgü olmasa da, AKS kümenizi çok kiracılı kullanım için mantıksal olarak yalıtıldığınızda özellikle önemlidir.

Azure AD, AKS kümeleriyle tümleşen kurumsal özellikli bir kimlik yönetimi çözümü sağlar. Kubernetes bir kimlik yönetimi çözümü sağlamadığından, API sunucusuna erişimi çok fazla kısıtlamak için, sesli olarak basılmış yapabilirsiniz. AKS 'deki Azure AD ile tümleşik kümeler sayesinde, API sunucusuna kullanıcıların kimliğini doğrulamak için mevcut kullanıcı ve grup hesaplarınızı kullanırsınız.

![AKS kümeleri için Azure Active Directory tümleştirme](media/operator-best-practices-cluster-security/aad-integration.png)

Kubernetes RBAC ve Azure AD-tümleştirme kullanarak, API sunucusunu güvenli hale getirebilirsiniz ve tek bir ad alanı gibi kapsamlı bir kaynak kümesi için gereken en düşük izinleri sağlayabilirsiniz. Farklı Azure AD kullanıcılarına veya farklı Kubernetes rollerine farklı gruplara izin verebilirsiniz. Ayrıntılı izinlerle, API sunucusuna erişimi kısıtlayabilir ve gerçekleştirilen eylemlerin net bir denetim izini sağlayabilirsiniz.

Önerilen en iyi yöntem, tek kimlikler yerine dosya ve klasörlere erişim sağlamak için *grupları* kullanmaktır. Örneğin, kullanıcıları bireysel *Kullanıcılar* yerine Kubernetes rollerine bağlamak Için BIR Azure AD *Grup* üyeliği kullanın. Bir kullanıcının grup üyeliği değiştikçe, AKS kümesindeki erişim izinleri buna göre değişir. 

Bu arada, bireysel kullanıcıyı doğrudan bir role ve onların iş işlevi değişikliklerine bağladığınızı varsayalım. Azure AD grup üyelikleri güncelleştirirken, AKS kümesinde izinleri olmaz. Bu senaryoda, Kullanıcı gerektirdiğinden daha fazla izinle sona erer.

Azure AD tümleştirmesi, Kubernetes RBAC ve Azure RBAC hakkında daha fazla bilgi için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Kaynaklara güvenli kapsayıcı erişimi

> **En iyi yöntemler kılavuzu** 
> 
> Kapsayıcıların gerçekleştirebileceği eylemlere erişimi sınırlayın. En az sayıda izin sağlayın ve kök erişimi veya ayrıcalıklı yükseltme kullanmaktan kaçının.

Kullanıcılara veya gruplara gereken en düşük ayrıcalıkları vermeniz gerektiği şekilde, kapsayıcıları yalnızca gerekli eylemler ve işlemlerle sınırlandırmanız gerekir. Saldırı riskini en aza indirmek için, ilerletilen ayrıcalıklar veya kök erişim gerektiren uygulamaları ve kapsayıcıları yapılandırmaktan kaçının. 

Örneğin, `allowPrivilegeEscalation: false` Pod bildiriminde ayarlayın. Bu yerleşik Kubernetes *Pod güvenlik bağlamları* , çalıştırılacak Kullanıcı veya grup veya kullanıma sunulacak Linux özellikleri gibi ek izinler tanımlamanızı sağlar. Daha iyi uygulamalar için bkz. [kaynaklara güvenli Pod erişimi][pod-security-contexts].

Kapsayıcı eylemlerine daha ayrıntılı denetim için, *AppArmor* ve *seccomp* gibi yerleşik Linux güvenlik özelliklerini de kullanabilirsiniz. 
1. Düğüm düzeyinde Linux güvenlik özelliklerini tanımlayın.
1. Pod bildirimi aracılığıyla özellikleri uygulayın. 

Yerleşik Linux güvenlik özellikleri yalnızca Linux düğümlerinde ve yığınların üzerinde kullanılabilir.

> [!NOTE]
> Şu anda, Kubernetes ortamları, çok kiracılı Kullanıcı kullanımı için tamamen güvenli değildir. Düğümler için *AppArmor*, *seccomp*,*Pod güvenlik Ilkeleri* veya Kubernetes RBAC gibi ek güvenlik özellikleri, etkin olarak güvenlik açıklarını engeller. 
>
>Saldırgan çok kiracılı iş yüklerini çalıştırırken doğru güvenlik için yalnızca bir hiper yöneticiye güvenin. Kubernetes güvenlik etki alanı, tek bir düğüm değil, tüm küme haline gelir. 
>
> Bu tür çok kiracılı iş yükleri için, fiziksel olarak yalıtılmış kümeler kullanmanız gerekir.

### <a name="app-armor"></a>Uygulama koruma sağlamak

Kapsayıcı eylemlerini sınırlandırmak için [AppArmor][k8s-apparmor] Linux çekirdek güvenlik modülünü kullanabilirsiniz. AppArmor, temel alınan AKS düğüm işletim sisteminin bir parçası olarak kullanılabilir ve varsayılan olarak etkindir. Okuma, yazma veya yürütme eylemlerini kısıtlayan AppArmor profilleri veya filesystems bağlama gibi sistem işlevlerini oluşturursunuz. Varsayılan AppArmor profilleri `/proc` , çeşitli ve konumlarına erişimi kısıtlar `/sys` ve temel alınan düğümden kapsayıcıları mantıksal olarak yalıtmak için bir yol sağlar. AppArmor, yalnızca Kubernetes pods değil, Linux üzerinde çalışan tüm uygulamalar için çalışır.

![Bir AKS kümesinde kullanılan AppArmor profilleri kapsayıcı eylemlerini sınırlandırmaya](media/operator-best-practices-container-security/apparmor.png)

AppArmor 'i görmek için aşağıdaki örnek, dosyalara yazmayı önleyen bir profil oluşturur. 
1. AKS düğümüne [SSH][aks-ssh] .
1. *Reddetme-yazma. Profile* adlı bir dosya oluşturun.
1. Aşağıdaki içeriği yapıştırın:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

AppArmor profilleri komutu kullanılarak eklenir `apparmor_parser` . 
1. Profili AppArmor öğesine ekleyin.
1. Önceki adımda oluşturulan profilin adını belirtin:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Profil doğru şekilde ayrıştırılıp AppArmor 'e uygulanmışsa, herhangi bir çıktı görmezsiniz ve komut istemine geri dönersiniz.

1. Yerel makinenizden, *aks-AppArmor. YAML* adlı bir pod bildirimi oluşturun. Bu bildirim:
    * İçin bir ek açıklama tanımlar `container.apparmor.security.beta.kubernetes` .
    * Önceki adımlarda oluşturulan *reddetme yazma* profiline başvurur.

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
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. Pod 'un dağıtıldığı ile, *Hello-AppArmor* Pod 'ın *Engellenen* şekilde göründüğünü doğrulama ' yı kullanın:

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

AppArmor hakkında daha fazla bilgi için bkz. [Kubernetes 'Te AppArmor profilleri][k8s-apparmor].

### <a name="secure-computing"></a>Güvenli bilgi işlem

AppArmor tüm Linux uygulamaları için çalışırken, [seccomp (*San*) işlem][seccomp] düzeyinde çalışmaktadır. Seccomp Ayrıca bir Linux çekirdek güvenlik modülüdür ve AKS düğümleri tarafından kullanılan Docker çalışma zamanı tarafından yerel olarak desteklenir. Seccomp ile kapsayıcı işlem çağrılarını sınırlayabilirsiniz. Kapsayıcının en düşük iznini yalnızca tarafından çalışacak şekilde verme konusunda en iyi uygulamaya hizalayın:
* Filtre ile tanımlama izin verilecek veya reddedilecek işlemler.
* Seccomp filtresi ile ilişkilendirilecek bir pod YAML bildirimi içine açıklama ekleme. 

Seccomp eylemini görmek için, bir dosyada izinleri değiştirmeyi önleyen bir filtre oluşturun. 
1. AKS düğümüne [SSH][aks-ssh] .
1. */Var/lib/kubelet/seccomp/prevent-chmod* adlı bir seccomp filtresi oluşturun.
1. Aşağıdaki içeriği yapıştırın:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    Sürüm 1,19 ve üzeri sürümlerde aşağıdakileri yapılandırmanız gerekir:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. Yerel makinenizden, *aks-seccomp. YAML* adlı bir pod bildirimi oluşturun ve aşağıdaki içeriği yapıştırın. Bu bildirim:
    * İçin bir ek açıklama tanımlar `seccomp.security.alpha.kubernetes.io` .
    * Önceki adımda oluşturulan *engelleme-chmod* filtresine başvurur.

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
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    Sürüm 1,19 ve üzeri sürümlerde aşağıdakileri yapılandırmanız gerekir:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. [Kubectl Apply][kubectl-apply] komutunu kullanarak örnek Pod 'u dağıtın:

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. [Kubectl Get Pod][kubectl-get] komutunu kullanarak Pod durumunu görüntüleyin. 
    * Pod bir hata bildiriyor. 
    * `chmod`Aşağıdaki örnek çıktıda gösterildiği gibi komutun seccomp filtresi tarafından çalışması engellenir:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Kullanılabilir filtreler hakkında daha fazla bilgi için bkz. [Docker Için Seccomp güvenlik profilleri][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>En son Kubernetes sürümüne düzenli olarak güncelleştir

> **En iyi yöntemler kılavuzu** 
> 
> Yeni özellikler ve hata düzeltmeleri üzerinde güncel kalmak için, AKS kümenizdeki Kubernetes sürümünü düzenli olarak yükseltin.

Kubernetes, daha geleneksel altyapı platformlarından daha hızlı bir şekilde yeni özellikler yayımlar. Kubernetes güncelleştirmeleri şunları içerir:
* Yeni özellikler
* Hata veya güvenlik düzeltmeleri 

Yeni özellikler tipik olarak *Alfa* ve *Beta* durumunu *kararlı* hale gelmeden önce taşır. Kararlı olduktan sonra, genel olarak kullanılabilir ve üretim kullanımı için önerilir. Kubernetes yeni özellik yayınlama çevrimi, Kubernetes 'i düzenli olarak bozmadan veya dağıtımlarınızı ve şablonlarınızı ayarlamadan güncelleştirmenize olanak tanır.

AKS, Kubernetes 'in üç alt sürümünü destekler. Yeni bir ikincil yama sürümü kullanıma sunulduktan sonra, desteklenen en eski ikincil sürüm ve düzeltme eki sürümleri kullanımdan kalkmıştır. İkincil Kubernetes güncelleştirmeleri düzenli aralıklarla gerçekleşir. Destek kapsamında kalmak için, gerekli yükseltmeleri denetlemek üzere bir idare işlemi kullandığınızdan emin olun. Daha fazla bilgi için bkz. [desteklenen Kubernetes sürümleri AKS][aks-supported-versions].

Kümeniz için kullanılabilen sürümleri denetlemek için, aşağıdaki örnekte gösterildiği gibi [az aks Get-yükseltmeler][az-aks-get-upgrades] komutunu kullanın:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Daha sonra, [az aks Upgrade][az-aks-upgrade] komutunu kullanarak aks kümenizi yükseltebilirsiniz. Yükseltme işlemi güvenli:
* Tek seferde bir düğümü korbağlar ve boşaltır.
* Kalan düğümlerde Pod zamanlar.
* En son işletim sistemi ve Kubernetes sürümlerini çalıştıran yeni bir düğüm dağıtır.

>[!IMPORTANT]
> Yeni küçük sürümleri bir geliştirme testi ortamında test edin ve yeni Kubernetes sürümüyle iş yükünüzün sağlıklı olduğunu doğrulayın. 
>
> Kubernetes, iş yüklerinizin kullandığı API 'Leri (sürüm 1,16 ' de olduğu gibi) kullanımdan kaldırır. Yeni sürümler üretime getirilirken, [ayrı sürümlerde birden çok düğüm havuzu](use-multiple-node-pools.md) kullanmayı ve tek tek havuzları tek seferde yükseltmeyi göz önünde bulundurun. Birden çok küme çalıştırıyorsanız, etki veya değişiklik için tek seferde bir kümeyi aşamalı olarak izlemeye yükseltin.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

AKS 'teki yükseltmeler hakkında daha fazla bilgi için bkz. [aks 'de desteklenen Kubernetes sürümleri][aks-supported-versions] ve [aks kümesini yükseltme][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux düğüm güncelleştirmelerini işleme ve kured kullanarak yeniden başlatmalar

> **En iyi yöntemler kılavuzu** 
> 
> AKS, her Linux düğümünde güvenlik düzeltmelerini otomatik olarak indirip yüklese de otomatik olarak yeniden başlatılır. 
> 1. `kured`Bekleyen yeniden başlatmalar için izlemek için kullanın.
> 1. Düğümün yeniden başlatılmasını sağlamak için düğümü güvenle kapatıp boşaltın.
> 1. Güncelleştirmeleri uygulayın.
> 1. İşletim sistemine göre olabildiğince güvenli olun. 

Windows Server düğümleri için düzenli olarak bir AKS yükseltme işlemi gerçekleştirip, büyük/dışarı boşaltma ve güncelleştirilmiş düğümleri dağıtma işlemleri yapılır.

AKS içindeki her akşam Linux düğümü, kendi sahip oldukları güncelleştirme kanalıyla güvenlik düzeltme ekleri alır. Bu davranış, düğümler bir AKS kümesinde dağıtıldığında otomatik olarak yapılandırılır. Çalışma yüklerini çalıştırmaya yönelik kesintiyi ve olası etkiyi en aza indirmek için, bir güvenlik düzeltme eki veya çekirdek güncelleştirmesi gerektiriyorsa düğümler otomatik olarak yeniden başlatılır.

Açık kaynaklı [kured (KUbernetes önyükleme cini)][kured] projesi, bekleyen düğüm yeniden başlatmaları için izler. Bir Linux düğümü, yeniden başlatma gerektiren güncelleştirmeler uygularsa, düğüm, kümedeki diğer düğümlerde bulunan düğümleri taşımak ve zamanlamak için güvenli bir şekilde donmış ve taşınabilir. Düğüm yeniden başlatıldıktan sonra, kümeye geri eklenir ve Kubernetes Pod zamanlamaya devam eder. Kesintiyi en aza indirmek için, tek seferde yalnızca bir düğümün tarafından yeniden başlatılması izin verilir `kured` .

![Kured kullanarak AKS düğümü yeniden başlatma işlemi](media/operator-best-practices-cluster-security/node-reboot-process.png)

Yeniden başlatmalar üzerinde daha yakından denetim istiyorsanız, `kured` devam eden başka bakım olayları ya da küme sorunları varsa, yeniden başlatmaları engellemek Için Prometheus ile tümleştirilebilir. Bu tümleştirme, diğer sorunları etkin bir şekilde giderirken düğümleri yeniden başlatarak karmaşıkmayı azaltır.

Düğüm yeniden başlatmaların nasıl işleneceği hakkında daha fazla bilgi için bkz. [AKS 'deki düğümlere güvenlik ve çekirdek güncelleştirmelerini uygulama][aks-kured].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümenizin güvenliğini sağlama konusunda odaklanılmıştır. Bu alanlardan bazılarını uygulamak için aşağıdaki makalelere bakın:

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
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md

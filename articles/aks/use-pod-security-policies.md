---
title: Azure Kubernetes Service (AKS) içinde Pod güvenlik ilkeleri kullanma
description: Azure Kubernetes Service (aks) içinde Pod SecurityPolicy kullanarak Pod sayede denetimini nasıl denetleyeceğinizi öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: 131a71e27bba1c37b6d50b718b8eac788109a59f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933759"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Önizleme-Azure Kubernetes Service (AKS) ' de Pod güvenlik ilkelerini kullanarak kümenizin güvenliğini sağlama

AKS kümenizin güvenliğini artırmak için, hangi yığınların zamanlanabileceği ile sınırlı olabilirsiniz. İzin vermeyenleri isteyen kaynaklar AKS kümesinde çalıştırılamaz. Bu erişimi Pod güvenlik ilkelerini kullanarak tanımlarsınız. Bu makalede, AKS 'deki yığınların dağıtımını sınırlamak için pod güvenlik ilkelerinin nasıl kullanılacağı gösterilmektedir.

> [!IMPORTANT]
> AKS Önizleme özellikleri self servis kabul etme sürecindedir. Önizlemeler, "olduğu gibi" ve "kullanılabilir olarak" verilmiştir ve hizmet düzeyi sözleşmelerinden ve sınırlı garantiden çıkarılır. AKS önizlemeleri, müşteri desteğinin en iyi çaba temelinde kısmen ele alınmıştır. Bu nedenle, bu özellikler üretim kullanımı için tasarlanmamıştır. Ek bilgi için lütfen aşağıdaki destek makalelerine bakın:
>
> * [AKS destek Ilkeleri][aks-support-policies]
> * [Azure desteği SSS][aks-faq]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Azure CLı sürüm 2.0.61 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Aks-Preview CLı uzantısını yükler

Pod güvenlik ilkelerini kullanmak için, *aks-Preview* CLI uzantısının sürüm 0.4.1 veya üzeri olması gerekir. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını yükledikten sonra [az Extension Update][az-extension-update] komutunu kullanarak kullanılabilir güncelleştirmeleri denetleyin:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod güvenlik ilkesi Özellik sağlayıcısını Kaydet

Pod güvenlik ilkelerini kullanmak üzere bir AKS kümesi oluşturmak veya güncelleştirmek için öncelikle aboneliğinizde bir özellik bayrağını etkinleştirin. *Pod Securitypolicypreview* Özellik bayrağını kaydetmek için, aşağıdaki örnekte gösterildiği gibi [az Feature Register][az-feature-register] komutunu kullanın:

> [!CAUTION]
> Bir abonelik üzerinde bir özelliği kaydettiğinizde, o özelliği şu anda kaydedemezsiniz. Bazı Önizleme özelliklerini etkinleştirdikten sonra, daha sonra abonelikte oluşturulan tüm AKS kümeleri için varsayılanlar kullanılabilir. Üretim aboneliklerinde Önizleme özelliklerini etkinleştirmeyin. Önizleme özelliklerini test etmek ve geri bildirim toplamak için ayrı bir abonelik kullanın.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Durumun *kayıtlı*gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu denetleyebilirsiniz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod güvenlik ilkelerine genel bakış

Bir Kubernetes kümesinde, bir kaynak oluşturulduğunda API sunucusuna yapılan istekleri ele almak için bir giriş denetleyicisi kullanılır. Giriş denetleyicisi daha sonra kaynak isteğini bir dizi kurala karşı *doğrulayabilir* *veya kaynağı* dağıtım parametrelerini değiştirecek şekilde alabilir.

*Pod SecurityPolicy* , Pod belirtiminin tanımlı gereksinimlerinizi karşıladığından emin olan bir giriş denetleyicisidir. Bu gereksinimler, ayrıcalıklı kapsayıcıların kullanımını, belirli depolama türlerine erişimi veya kapsayıcının çalışacağı kullanıcı veya grubu sınırlandırabilir. Pod belirtimlerinin Pod güvenlik ilkesinde özetlenen gereksinimleri karşılamadığında bir kaynak dağıtmaya çalıştığınızda, istek reddedilir. AKS kümesinde hangi yığınların zamanlanabileceği, bazı olası güvenlik açıklarını veya ayrıcalık yürüyen şeyleri engeller.

Bir AKS kümesinde Pod güvenlik ilkesini etkinleştirdiğinizde, bazı varsayılan ilkeler uygulanır. Bu varsayılan ilkeler, neyin planlanabileceği hakkında bilgi edinmek için kullanıma hazır bir deneyim sağlar. Ancak, küme kullanıcıları kendi ilkelerinizi tanımladıktan sonra Pod 'yi dağıtmaya yönelik sorunlar yaşayabilirsiniz. Önerilen yaklaşım şunlardır:

* AKS kümesi oluşturma
* Kendi Pod güvenlik ilkelerinizi tanımlama
* Pod güvenlik ilkesi özelliğini etkinleştirme

Varsayılan ilkelerin Pod dağıtımlarını nasıl sınırlayamayacağını göstermek için, bu makalede öncelikle Pod güvenlik ilkeleri özelliğini etkinleştirip özel bir ilke oluşturmanız gerekir.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS kümesinde Pod güvenlik ilkesini etkinleştirme

[Az aks Update][az-aks-update] komutunu kullanarak Pod güvenlik ilkesini etkinleştirebilir veya devre dışı bırakabilirsiniz. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *Myakscluster* küme adı üzerinde pod güvenlik ilkesini mümkün bir şekilde sunar.

> [!NOTE]
> Gerçek dünyada kullanım için, kendi özel ilkelerinizi tanımlana kadar Pod güvenlik ilkesini etkinleştirmeyin. Bu makalede, varsayılan ilkelerin Pod dağıtımlarını nasıl sınırlayaöğrenmek için pod güvenlik ilkesini ilk adım olarak etkinleştirirsiniz.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Varsayılan AKS ilkeleri

Pod güvenlik ilkesini etkinleştirdiğinizde AKS, *ayrıcalıklı*adlı bir varsayılan ilke oluşturur. Varsayılan ilkeyi düzenleme veya kaldırma. Bunun yerine, denetlemek istediğiniz ayarları tanımlayan kendi ilkelerinizi oluşturun. İlk olarak, bu varsayılan ilkelerin Pod dağıtımlarını nasıl etkileyeceğini göz atalım.

Kullanılabilir ilkeleri görüntülemek için, aşağıdaki örnekte gösterildiği gibi [kubectl Get PSP][kubectl-get] komutunu kullanın

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*Ayrıcalıklı* Pod güvenlik ilkesi, aks kümesindeki tüm kimliği doğrulanmış kullanıcılara uygulanır. Bu atama ClusterRoles ve ClusterRoleBindings tarafından denetlenir. [Kubectl Get clusterrolebindings][kubectl-get] komutunu kullanın ve *Varsayılan: ayrıcalıklı:* bağlama:

```console
kubectl get clusterrolebindings default:privileged -o yaml
```

Aşağıdaki sıkıştırılmış Çıktıda gösterildiği gibi, *PSP: kısıtlanmış* kümerolü herhangi bir *Sistem: kimliği doğrulanmış* kullanıcılara atanır. Bu özellik, kendi ilkelerinizin tanımlanmamaları gerekmeden temel bir kısıtlama düzeyi sağlar.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Bu varsayılan ilkelerin, kendi Pod güvenlik ilkelerinizi oluşturmaya başlamadan önce pod 'yi zamanlamaya yönelik Kullanıcı istekleriyle nasıl etkileşime gireceğini anlamak önemlidir. Sonraki birkaç bölümde, bu varsayılan ilkeleri eylemde görmek için bazı Pod zamanlamam.

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS kümesinde test kullanıcısı oluşturma

Varsayılan olarak, [az aks Get-Credentials][az-aks-get-credentials] komutunu kullandığınızda, aks kümesi için *yönetici* kimlik bilgileri `kubectl` config 'nize eklenir. Yönetici Kullanıcı, Pod güvenlik ilkelerinin zorlanmasını atlar. AKS kümeleriniz için Azure Active Directory tümleştirme kullanırsanız, ilkelerin eylemde uygulanmasını görmek için yönetici olmayan bir kullanıcının kimlik bilgileriyle oturum açın. Bu makalede, AKS kümesinde kullanabileceğiniz bir sınama Kullanıcı hesabı oluşturalım.

[Kubectl Create Namespace][kubectl-create] komutunu kullanarak test kaynakları için *PSP-aks* adlı bir örnek ad alanı oluşturun. Ardından, [kubectl Create ServiceAccount][kubectl-create] komutunu kullanarak *yönetici olmayan-User* adlı bir hizmet hesabı oluşturun:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Sonra, [kubectl Create rolebinding][kubectl-create] komutunu kullanarak *yönetici olmayan kullanıcılar* için bir rolebinding oluşturun ve ad alanında temel eylemleri gerçekleştirin:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Yönetici ve yönetici olmayan kullanıcı için diğer ad komutları oluşturma

`kubectl` kullanırken normal yönetici kullanıcı arasındaki farkı vurgulamak için, önceki adımlarda oluşturulan yönetici olmayan kullanıcı ile iki komut satırı diğer adı oluşturun:

* **Kubectl-admin** diğer adı normal yönetici kullanıcı içindir ve bu, *PSP-aks* ad alanı kapsamına alınır.
* **Kubectl-nonadminuser** diğer adı, önceki adımda oluşturulan *yönetici olmayan kullanıcılar* içindir ve bu, *PSP-aks* ad alanı kapsamına alınır.

Aşağıdaki komutlarda gösterildiği gibi bu iki diğer adı oluşturun:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Ayrıcalıklı Pod oluşturmayı test etme

`privileged: true`güvenlik bağlamı ile pod zamanladığınızda, ilk test edelim. Bu güvenlik bağlamı Pod 'un ayrıcalıklarını ilerletir. Önceki bölümde, varsayılan AKS Pod güvenlik ilkelerini belirten, *kısıtlı* ilke bu isteği reddetmelidir.

`nginx-privileged.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'ı oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod, aşağıdaki örnek çıktıda gösterildiği gibi zamanlanamaz:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod, zamanlama aşamasına ulaşmıyor, bu nedenle, üzerinde geçiş yapmadan önce silinecek kaynak yok.

## <a name="test-creation-of-an-unprivileged-pod"></a>Ayrıcalıksız Pod 'ın test oluşturması

Önceki örnekte, Pod belirtimi ayrıcalıklı yükseltme istedi. Bu istek, varsayılan *kısıtlı* Pod güvenlik ilkesi tarafından reddedildi, bu nedenle Pod, Pod zamanlanamaz. Artık ayrıcalık yükseltme isteği olmadan aynı NGıNX Pod 'u çalıştırmayı deneyelim.

`nginx-unprivileged.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'ı oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes Zamanlayıcı, Pod isteğini kabul eder. Ancak, `kubectl get pods`kullanarak Pod 'ın durumuna bakarsanız bir hata vardır:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Pod 'un olaylarına bakmak için [kubectl betimleyen Pod][kubectl-describe] komutunu kullanın. Aşağıdaki sıkıştırılmış örnek, kapsayıcının ve görüntünün, gerekli olmasa bile, kök izinleri gerektirdiğini gösterir:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Herhangi bir ayrıcalıklı erişim istemediğimiz halde, NGıNX için kapsayıcı görüntüsünün *80*numaralı bağlantı noktası için bir bağlama oluşturması gerekir. *1024* ve altına bağlantı noktalarını bağlamak için *kök* Kullanıcı gereklidir. Pod başlatılmaya çalışıldığında, *kısıtlı* Pod güvenlik ilkesi bu isteği reddeder.

Bu örnek, AKS tarafından oluşturulan varsayılan Pod güvenlik ilkelerinin geçerli olduğunu ve bir kullanıcının gerçekleştirebileceği eylemleri kısıtlayabileceğini gösterir. Temel NGıNX Pod 'un reddedilme beklenmediğinden, bu varsayılan ilkelerin davranışını anlamanız önemlidir.

Bir sonraki adıma geçmeden önce, [kubectl Delete Pod][kubectl-delete] komutunu kullanarak bu test Pod öğesini silin:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Belirli bir kullanıcı bağlamı ile pod 'ın test oluşturması

Önceki örnekte kapsayıcı görüntüsü, NGıNX 'i 80 numaralı bağlantı noktasına bağlamak için otomatik olarak kök kullanmaya çalıştı. Bu istek varsayılan *kısıtlı* Pod güvenlik ilkesi tarafından reddedildi, bu nedenle Pod başlatılamadı. Şimdi de `runAsUser: 2000`gibi belirli bir Kullanıcı bağlamıyla aynı NGıNX Pod 'ı çalıştırmayı deneyelim.

`nginx-unprivileged-nonroot.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'ı oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes Zamanlayıcı, Pod isteğini kabul eder. Ancak, `kubectl get pods`kullanarak Pod 'ın durumuna bakarsanız, önceki örnekteki farklı bir hata vardır:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Pod 'un olaylarına bakmak için [kubectl betimleyen Pod][kubectl-describe] komutunu kullanın. Aşağıdaki sıkıştırılmış örnek Pod olaylarını göstermektedir:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Olaylar kapsayıcının oluşturulduğunu ve başlatıldığını gösterir. Pod 'un neden başarısız durumda olduğuna ilişkin hiçbir şey yok. [Kubectl logs][kubectl-logs] komutunu kullanarak Pod günlüklerine göz atalım:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

Aşağıdaki örnek günlük çıktısı, NGıNX yapılandırmasının kendisinin kendisi tarafından başlatılmaya çalışıldığında bir izin hatası olduğunu belirten bir gösterge sağlar. Bu hata, 80 numaralı bağlantı noktasına bağlanması gerekmeden oluşur. Pod belirtimi normal bir kullanıcı hesabı tanımlasa da, bu kullanıcı hesabı NGıNX hizmetinin başlatılması ve kısıtlanmış bağlantı noktasına bağlanması için işletim sistemi düzeyinde yeterli değildir.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Yine de, varsayılan Pod güvenlik ilkelerinin davranışını anlamak önemlidir. Bu hata, izlemenin biraz daha zor olduğundan, bir temel NGıNX Pod 'un reddedileceğini beklememiş olabilirsiniz.

Bir sonraki adıma geçmeden önce, [kubectl Delete Pod][kubectl-delete] komutunu kullanarak bu test Pod öğesini silin:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Özel Pod güvenlik ilkesi oluşturma

Artık varsayılan Pod güvenlik ilkelerinin davranışını gördüğünüze göre, *yönetici olmayan kullanıcıların* pods 'yi başarıyla zamanlaması için bir yol sunalım.

Ayrıcalıklı erişim isteyen Pod 'yi reddetmek için bir ilke oluşturalım. *RunAsUser* veya Allowed *birimleri*gibi diğer seçenekler açıkça kısıtlanır. Bu ilke türü, ayrıcalıklı erişim için bir isteği reddeder, ancak Aksi takdirde kümenin istenen pods 'yi çalıştırmasına izin verir.

`psp-deny-privileged.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak ilkeyi oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Kullanılabilir ilkeleri görüntülemek için, aşağıdaki örnekte gösterildiği gibi [kubectl Get PSP][kubectl-get] komutunu kullanın. Bir pod oluşturmak için, bir önceki örneklerde uygulanan varsayılan *kısıtlı* ilkeyle, *PSP-Deny-ayrıcalıklı* ilkesini karşılaştırın. İlkeniz tarafından yalnızca *PRIV* yükseltme kullanımı reddedilir. Kullanıcı veya grup üzerinde, *PSP-Deny-ayrıcalıklı* ilkesi için bir kısıtlama yoktur.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Kullanıcı hesabının özel Pod güvenlik ilkesini kullanmasına izin ver

Önceki adımda, ayrıcalıklı erişim isteyen Pod 'yi reddetmek için bir pod güvenlik ilkesi oluşturdunuz. İlkenin kullanılmasına izin vermek için bir *rol* veya *clusterrole*oluşturun. Ardından, bir *rolebinding* veya *clusterrolebinding*kullanarak bu rollerden birini ilişkilendirirsiniz.

Bu örnekte, önceki adımda oluşturulan *PSP-reddetme-ayrıcalıklı* ilkesini *kullanmanıza* Izin veren bir kümerolü oluşturun. `psp-deny-privileged-clusterrole.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak clusterrole oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Şimdi, önceki adımda oluşturulan ClusterRole öğesini kullanmak için bir ClusterRoleBinding oluşturun. `psp-deny-privileged-clusterrolebinding.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak bir ClusterRoleBinding oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Bu makalenin ilk adımında, AKS kümesinde Pod güvenlik ilkesi özelliği etkinleştirilmiştir. Önerilen uygulama, kendi ilkelerinizi tanımladıktan sonra yalnızca Pod güvenlik ilkesi özelliğini etkinleştirmeiydi. Bu, Pod güvenlik ilkesi özelliğini etkinleştirdiğiniz aşamadır. Bir veya daha fazla özel ilke tanımlanmış ve Kullanıcı hesapları bu ilkelerle ilişkili. Artık Pod güvenlik ilkesi özelliğini güvenle etkinleştirebilir ve varsayılan ilkelerden kaynaklanan sorunları en aza indirmenize izin verebilirsiniz.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Ayrıcalıksız bir pod oluşturmayı yeniden test edin

Özel Pod güvenlik ilkeniz uygulanmış ve ilkeyi kullanmak üzere Kullanıcı hesabı için bir bağlama ile, ayrıcalıksız bir pod oluşturmayı yeniden denemenize imkan tanır. [Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'u oluşturmak için aynı `nginx-privileged.yaml` bildirimini kullanın:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod başarıyla zamanlandı. [Kubectl Get Pod][kubectl-get] komutunu kullanarak Pod 'un durumunu denetlediğinizde, Pod *çalışır*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Bu örnekte, farklı kullanıcılar veya gruplar için AKS kümesine erişimi tanımlamak üzere nasıl özel Pod güvenlik ilkeleri oluşturabileceğiniz gösterilmektedir. Varsayılan AKS ilkeleri, ne kadar fazla çalışma yapabilecekleri konusunda sıkı denetimler sağlar, bu nedenle ihtiyacınız olan kısıtlamaları doğru şekilde tanımlamak için kendi özel ilkelerinizi oluşturun.

[Kubectl Delete][kubectl-delete] komutunu kullanarak NGINX unayrıcalıklı Pod öğesini SILIN ve YAML bildiriminizde adı belirtin:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Pod güvenlik ilkesini devre dışı bırakmak için [az aks Update][az-aks-update] komutunu yeniden kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubundaki *Myakscluster* küme adı üzerinde pod güvenlik ilkesini devre dışı bırakır:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Sonra ClusterRole ve ClusterRoleBinding ' i silin:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

[Kubectl Delete][kubectl-delete] komutunu kullanarak ağ ilkesini SILIN ve YAML bildiriminizde adı belirtin:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Son olarak, *PSP-aks* ad alanını silin:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, ayrıcalıklı erişimin kullanımını engellemek için bir pod güvenlik ilkesi oluşturma konusu gösterilmektedir. Birim türü veya RunAs kullanıcısı gibi bir ilkenin zorlayabildikleri çok sayıda özellik vardır. Kullanılabilir seçenekler hakkında daha fazla bilgi için bkz. [Kubernetes Pod güvenlik ilkesi başvuru belgeleri][kubernetes-policy-reference].

Pod ağ trafiğini sınırlama hakkında daha fazla bilgi için bkz. [aks 'deki ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

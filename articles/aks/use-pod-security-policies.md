---
title: Azure Kubernetes Service (AKS) içinde Pod güvenlik ilkeleri kullanma
description: Azure Kubernetes Service (aks) içinde Pod SecurityPolicy kullanarak Pod sayede denetimini nasıl denetleyeceğinizi öğrenin
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d70b8e8efbf96e50575845ac88993012fed936d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767430"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Önizleme-Azure Kubernetes Service (AKS) ' de Pod güvenlik ilkelerini kullanarak kümenizin güvenliğini sağlama

> [!WARNING]
> **Bu belgede açıklanan özellik, Pod güvenlik ilkesi (Önizleme), Kubernetes sürüm 1,21 ile [kullanımdan kalkmaya](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) başlayacak ve sürüm 1,25 ' de kaldırılır.** Kubernetes yukarı akış, kilometre taşına yaklaşırsa, Kubernetes topluluğu, önemli alternatifleri belgelemek için çalışır. Önceki kullanımdan kaldırma duyurusu, müşteriler için uygun bir seçenek olmadığından zamanında yapılmıştır. Kubernetes Community bir alternatif üzerinde çalıştık. artık Kubernetes 'in önüne geçmek için bir basmayla ilgili değildir. 
>
> Pod güvenlik ilkesi (Önizleme) kullanım dışı olduktan sonra, gelecekteki küme yükseltmelerini gerçekleştirmek ve Azure desteği içinde kalmak için kullanımdan kaldırılan özelliği kullanarak mevcut kümelerin özelliğini devre dışı bırakmanız gerekir.

AKS kümenizin güvenliğini artırmak için, hangi yığınların zamanlanabileceği ile sınırlı olabilirsiniz. İzin vermeyenleri isteyen kaynaklar AKS kümesinde çalıştırılamaz. Bu erişimi Pod güvenlik ilkelerini kullanarak tanımlarsınız. Bu makalede, AKS 'deki yığınların dağıtımını sınırlamak için pod güvenlik ilkelerinin nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Azure CLı sürüm 2.0.61 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI uzantısını yükleme

Pod güvenlik ilkelerini kullanmak için, *aks-Preview* CLI uzantısının sürüm 0.4.1 veya üzeri olması gerekir. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını yükledikten sonra [az Extension Update][az-extension-update] komutunu kullanarak kullanılabilir güncelleştirmeleri denetleyin:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod güvenlik ilkesi Özellik sağlayıcısını Kaydet

**Bu belge ve özellik, 15 Ekim 2020 ' de kullanımdan kaldırılması için ayarlanmıştır.**

Pod güvenlik ilkelerini kullanmak üzere bir AKS kümesi oluşturmak veya güncelleştirmek için öncelikle aboneliğinizde bir özellik bayrağını etkinleştirin. *Pod Securitypolicypreview* Özellik bayrağını kaydetmek için, aşağıdaki örnekte gösterildiği gibi [az Feature Register][az-feature-register] komutunu kullanın:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Durumun *kayıtlı* gösterilmesi birkaç dakika sürer. [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu denetleyebilirsiniz:

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

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Pod güvenlik ilkesi ile Azure Ilkesi arasındaki davranış değişiklikleri

Pod güvenlik ilkesi ve Azure Ilkesi arasındaki davranış değişikliklerinin özeti aşağıda verilmiştir.

|Senaryo| Pod güvenlik ilkesi | Azure İlkesi |
|---|---|---|
|Yükleme|Pod güvenlik ilkesi özelliğini etkinleştir |Azure Ilke eklentisini etkinleştir
|İlkeleri dağıtma| Pod güvenlik ilkesi kaynağı dağıtma| Abonelik veya kaynak grubu kapsamına Azure ilkeleri atayın. Kubernetes kaynak uygulamaları için Azure Ilke eklentisi gereklidir.
| Varsayılan ilkeler | AKS 'de Pod güvenlik ilkesi etkinleştirildiğinde, varsayılan ayrıcalıklı ve Kısıtlanmamış ilkeler uygulanır. | Azure Ilke eklentisi etkinleştirilerek hiçbir varsayılan ilke uygulanmaz. Azure Ilkesinde ilkeleri açıkça etkinleştirmeniz gerekir.
| Kimin ilke oluşturup atayabileceği | Küme Yöneticisi bir pod güvenlik ilkesi kaynağı oluşturur | Kullanıcıların AKS kümesi kaynak grubunda en az ' Owner ' veya ' kaynak Ilkesi katılımcısı ' izinlerine sahip olması gerekir. --API, kullanıcılar AKS kümesi kaynak kapsamına ilke atayabilir. Kullanıcının AKS küme kaynağı üzerinde en az ' Owner ' veya ' kaynak Ilkesi katılımcısı ' izinlerine sahip olması gerekir. -Azure portal, ilkeler yönetim grubu/abonelik/kaynak grubu düzeyinde atanabilir.
| İlkeleri yetkilendirme| Kullanıcılar ve hizmet hesapları, Pod güvenlik ilkelerini kullanmak için açık izinler gerektirir. | İlkeleri yetkilendirmek için ek atama gerekmez. Azure 'da ilkeler atandıktan sonra, tüm küme kullanıcıları bu ilkeleri kullanabilir.
| İlke uygulanabilirliği | Yönetici Kullanıcı, Pod güvenlik ilkelerinin zorlanmasını atlar. | Tüm kullanıcılar (yönetici & yönetici olmayan) aynı ilkeleri görür. Kullanıcılara dayalı özel bir büyük harf yoktur. İlke uygulaması ad alanı düzeyinde dışlenebilir.
| İlke kapsamı | Pod güvenlik ilkeleri gösterilemez değildir | Azure Ilkesi tarafından kullanılan kısıtlama şablonları gösterilemez değildir.
| Reddetme/denetim/mutasyon eylemi | Pod güvenlik ilkeleri yalnızca reddetme eylemlerini destekler. Mutasyon, oluşturma isteklerinde varsayılan değerlerle yapılabilir. Doğrulama, güncelleştirme istekleri sırasında yapılabilir.| Azure Ilkesi, hem denetim & reddetme eylemlerini destekler. Mutasyon henüz desteklenmiyor, ancak planlandı.
| Pod güvenlik ilkesi uyumluluğu | Pod güvenlik ilkesi etkinleştirilmeden önce var olan yığınların uyumluluğuna ilişkin bir görünürlük yoktur. Pod güvenlik ilkeleri etkinleştirildikten sonra oluşturulan uyumlu olmayan Pod 'ler reddedilir. | Azure ilkelerini uygulamadan önce var olan uyumlu olmayan bir pod, ilke ihlallerine göre görünür. İlkeler reddetme efektiyle ayarlandıysa, Azure ilkeleri etkinleştirildikten sonra oluşturulan uyumlu olmayan Pod 'ler reddedilir.
| Kümedeki ilkeleri görüntüleme | `kubectl get psp` | `kubectl get constrainttemplate` -Tüm ilkeler döndürülür.
| Pod güvenlik ilkesi standart-ayrıcalıklı | Özellik etkinleştirilirken ayrıcalıklı bir pod güvenlik ilkesi kaynağı varsayılan olarak oluşturulur. | Ayrıcalıklı mod hiçbir kısıtlama gerektirmez, sonuç olarak herhangi bir Azure Ilke atamasına sahip değildir.
| [Pod güvenlik ilkesi standart-taban çizgisi/varsayılan](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Kullanıcı bir pod güvenlik ilkesi temel kaynağı yüklüyor. | Azure Ilkesi, taban çizgisi Pod güvenlik ilkesiyle eşleşen [yerleşik bir temel girişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) sağlar.
| [Pod güvenlik ilkesi standart-kısıtlı](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Kullanıcı, bir pod güvenlik ilkesi kısıtlanmış kaynağı yüklüyor. | Azure Ilkesi, kısıtlı Pod güvenlik ilkesiyle eşleşen [yerleşik bir kısıtlanmış girişim](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) sağlar.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS kümesinde Pod güvenlik ilkesini etkinleştirme

[Az aks Update][az-aks-update] komutunu kullanarak Pod güvenlik ilkesini etkinleştirebilir veya devre dışı bırakabilirsiniz. Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda *Myakscluster* küme adı üzerinde pod güvenlik ilkesini mümkün bir şekilde sunar.

> [!NOTE]
> Gerçek dünyada kullanım için, kendi özel ilkelerinizi tanımlana kadar Pod güvenlik ilkesini etkinleştirmeyin. Bu makalede, varsayılan ilkelerin Pod dağıtımlarını nasıl sınırlayaöğrenmek için pod güvenlik ilkesini ilk adım olarak etkinleştirirsiniz.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Varsayılan AKS ilkeleri

Pod güvenlik ilkesini etkinleştirdiğinizde AKS, *ayrıcalıklı* adlı bir varsayılan ilke oluşturur. Varsayılan ilkeyi düzenleme veya kaldırma. Bunun yerine, denetlemek istediğiniz ayarları tanımlayan kendi ilkelerinizi oluşturun. İlk olarak, bu varsayılan ilkelerin Pod dağıtımlarını nasıl etkileyeceğini göz atalım.

Kullanılabilir ilkeleri görüntülemek için, aşağıdaki örnekte gösterildiği gibi [kubectl Get PSP][kubectl-get] komutunu kullanın

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*Ayrıcalıklı* Pod güvenlik ilkesi, aks kümesindeki tüm kimliği doğrulanmış kullanıcılara uygulanır. Bu atama ClusterRoles ve ClusterRoleBindings tarafından denetlenir. [Kubectl Get rolebindings][kubectl-get] komutunu kullanın ve *Varsayılan: ayrıcalıklı:* bağlama ' yı *KUIN-System* ad alanında arayın:

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Aşağıdaki sıkıştırılmış Çıktıda gösterildiği gibi, *PSP: ayrıcalıklı* kümerolü herhangi bir *Sistem: kimliği doğrulanmış* kullanıcılara atanır. Bu özellik, kendi ilkelerinizin tanımlanmamaları gerekmeden temel bir ayrıcalık düzeyi sağlar.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
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
  name: system:masters
```

Bu varsayılan ilkelerin, kendi Pod güvenlik ilkelerinizi oluşturmaya başlamadan önce pod 'yi zamanlamaya yönelik Kullanıcı istekleriyle nasıl etkileşime gireceğini anlamak önemlidir. Sonraki birkaç bölümde, bu varsayılan ilkeleri eylemde görmek için bazı Pod zamanlamam.

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS kümesinde test kullanıcısı oluşturma

Varsayılan olarak, [az aks Get-Credentials][az-aks-get-credentials] komutunu kullandığınızda, aks kümesi için *yönetici* kimlik bilgileri, `kubectl` yapılandırmaya eklenir. Yönetici Kullanıcı, Pod güvenlik ilkelerinin zorlanmasını atlar. AKS kümeleriniz için Azure Active Directory tümleştirme kullanırsanız, ilkelerin eylemde uygulanmasını görmek için yönetici olmayan bir kullanıcının kimlik bilgileriyle oturum açın. Bu makalede, AKS kümesinde kullanabileceğiniz bir sınama Kullanıcı hesabı oluşturalım.

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

Kullanarak normal Yönetici Kullanıcı `kubectl` ve önceki adımlarda oluşturulan yönetici olmayan kullanıcı arasındaki farkı vurgulamak için iki komut satırı diğer adı oluşturun:

* **Kubectl-admin** diğer adı normal yönetici kullanıcı içindir ve bu, *PSP-aks* ad alanı kapsamına alınır.
* **Kubectl-nonadminuser** diğer adı, önceki adımda oluşturulan *yönetici olmayan kullanıcılar* içindir ve bu, *PSP-aks* ad alanı kapsamına alınır.

Aşağıdaki komutlarda gösterildiği gibi bu iki diğer adı oluşturun:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Ayrıcalıklı Pod oluşturmayı test etme

' In güvenlik bağlamı ile pod zamanladığınızda, ilk test edelim `privileged: true` . Bu güvenlik bağlamı Pod 'un ayrıcalıklarını ilerletir. Varsayılan AKS Pod güvenlik ilkelerini gösteren önceki bölümde, *ayrıcalık* ilkesinin bu isteği reddetmesi gerekir.

Adlı bir dosya oluşturun `nginx-privileged.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
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

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod, zamanlama aşamasına ulaşmıyor, bu nedenle, üzerinde geçiş yapmadan önce silinecek kaynak yok.

## <a name="test-creation-of-an-unprivileged-pod"></a>Ayrıcalıksız Pod 'ın test oluşturması

Önceki örnekte, Pod belirtimi ayrıcalıklı yükseltme istedi. Bu istek varsayılan *ayrıcalık* Pod güvenlik ilkesi tarafından reddedildi, bu yüzden Pod 'un zamanlanması başarısız olur. Artık ayrıcalık yükseltme isteği olmadan aynı NGıNX Pod 'u çalıştırmayı deneyelim.

Adlı bir dosya oluşturun `nginx-unprivileged.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'ı oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod, aşağıdaki örnek çıktıda gösterildiği gibi zamanlanamaz:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod, zamanlama aşamasına ulaşmıyor, bu nedenle, üzerinde geçiş yapmadan önce silinecek kaynak yok.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Belirli bir kullanıcı bağlamı ile pod 'ın test oluşturması

Önceki örnekte kapsayıcı görüntüsü, NGıNX 'i 80 numaralı bağlantı noktasına bağlamak için otomatik olarak kök kullanmaya çalıştı. Bu istek varsayılan *ayrıcalık* Pod güvenlik ilkesi tarafından reddedildi, bu yüzden Pod başlatılamıyor. Artık aynı NGıNX Pod öğesini gibi belirli bir Kullanıcı bağlamıyla çalıştırmayı deneyelim `runAsUser: 2000` .

Adlı bir dosya oluşturun `nginx-unprivileged-nonroot.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

[Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'ı oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Pod, aşağıdaki örnek çıktıda gösterildiği gibi zamanlanamaz:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod, zamanlama aşamasına ulaşmıyor, bu nedenle, üzerinde geçiş yapmadan önce silinecek kaynak yok.

## <a name="create-a-custom-pod-security-policy"></a>Özel Pod güvenlik ilkesi oluşturma

Artık varsayılan Pod güvenlik ilkelerinin davranışını gördüğünüze göre, *yönetici olmayan kullanıcıların* pods 'yi başarıyla zamanlaması için bir yol sunalım.

Ayrıcalıklı erişim isteyen Pod 'yi reddetmek için bir ilke oluşturalım. *RunAsUser* veya Allowed *birimleri* gibi diğer seçenekler açıkça kısıtlanır. Bu ilke türü, ayrıcalıklı erişim için bir isteği reddeder, ancak Aksi takdirde kümenin istenen pods 'yi çalıştırmasına izin verir.

Adlı bir dosya oluşturun `psp-deny-privileged.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

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

Kullanılabilir ilkeleri görüntülemek için, aşağıdaki örnekte gösterildiği gibi [kubectl Get PSP][kubectl-get] komutunu kullanın. Bir pod oluşturmak için, *PSP-Deny-ayrıcalıklı* ilkesini önceki örneklerde zorlanan varsayılan *ayrıcalık* ilkesiyle karşılaştırın. İlkeniz tarafından yalnızca *PRIV* yükseltme kullanımı reddedilir. Kullanıcı veya grup üzerinde, *PSP-Deny-ayrıcalıklı* ilkesi için bir kısıtlama yoktur.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Kullanıcı hesabının özel Pod güvenlik ilkesini kullanmasına izin ver

Önceki adımda, ayrıcalıklı erişim isteyen Pod 'yi reddetmek için bir pod güvenlik ilkesi oluşturdunuz. İlkenin kullanılmasına izin vermek için bir *rol* veya *clusterrole* oluşturun. Ardından, bir *rolebinding* veya *clusterrolebinding* kullanarak bu rollerden birini ilişkilendirirsiniz.

Bu örnekte, önceki adımda oluşturulan *PSP-reddetme-ayrıcalıklı* ilkesini *kullanmanıza* Izin veren bir kümerolü oluşturun. Adlı bir dosya oluşturun `psp-deny-privileged-clusterrole.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

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

Şimdi, önceki adımda oluşturulan ClusterRole öğesini kullanmak için bir ClusterRoleBinding oluşturun. Adlı bir dosya oluşturun `psp-deny-privileged-clusterrolebinding.yaml` ve aşağıdaki YAML bildirimini yapıştırın:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
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

Özel Pod güvenlik ilkeniz uygulanmış ve ilkeyi kullanmak üzere Kullanıcı hesabı için bir bağlama ile, ayrıcalıksız bir pod oluşturmayı yeniden denemenize imkan tanır. `nginx-privileged.yaml` [Kubectl Apply][kubectl-apply] komutunu kullanarak Pod 'u oluşturmak için aynı bildirimi kullanın:

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

Pod güvenlik ilkesini devre dışı bırakmak için [az aks Update][az-aks-update] komutunu yeniden kullanın. Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubundaki *Myakscluster* küme adı üzerinde pod güvenlik ilkesini devre dışı bırakır:

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

[Kubectl Delete][kubectl-delete] komutunu kullanarak güvenlik ilkesini SILIN ve YAML bildiriminizde adı belirtin:

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
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md

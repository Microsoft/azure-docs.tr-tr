---
title: RBAC ve Azure AD'de Azure Kubernetes hizmeti ile denetim küme kaynakları
description: Azure Kubernetes Service (AKS) rol tabanlı erişim denetimi (RBAC) kullanarak küme kaynaklarına erişimi kısıtlamak için Azure Active Directory grubu üyeliği kullanmayı öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: fba54fd23fefbe0029b9a809b23568490f05b23e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616159"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Azure Kubernetes hizmetinde rol tabanlı erişim denetimi ve Azure Active Directory kimlikleri kullanarak küme kaynaklarına erişimi denetleme

Azure Kubernetes Service (AKS), Azure Active Directory (AD) kullanıcı kimlik doğrulaması için kullanmak üzere yapılandırılabilir. Bu yapılandırmada, bir Azure AD kimlik doğrulama belirteci kullanarak bir AKS kümesi için oturum açın. Ayrıca Kubernetes yapılandırabilirsiniz küme kaynaklarına erişimi sınırlamak için rol tabanlı erişim denetimi (RBAC) bir kullanıcının kimliği veya grup üyeliğine dayalı.

Bu makalede Azure AD grup üyeliği ad alanlarına erişimi denetlemek ve kaynakları Kubernetes RBAC kullanarak bir AKS kümesinde Küme için nasıl kullanılacağı gösterilmektedir. Örnek grupları ve kullanıcıları Azure AD'de oluşturulur, ardından rolleri ve RoleBindings AKS kümesini oluşturma ve kaynakları görüntülemek için uygun izinleri verin oluşturulur.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure AD Tümleştirmesi ile etkin olarak mevcut bir AKS kümesi olduğunu varsayar. Bir AKS kümesi gerekirse bkz [Azure Active Directory Tümleştirme ile AKS][azure-ad-aks-cli].

Azure CLI Sürüm 2.0.61 gerekir veya daha sonra yüklü ve yapılandırılmış. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Azure AD'de tanıtım grup oluşturma

Bu makalede, küme kaynaklarında erişim Kubernetes RBAC ve Azure AD'ye nasıl kontrol göstermek için kullanılan iki kullanıcı rolü oluşturalım. Aşağıdaki iki örnek rolleri kullanılır:

* **Uygulama geliştirici**
    * Adlı bir kullanıcı *aksdev* parçası olan *appdev* grubu.
* **Site güvenilirliği mühendisi**
    * Adlı bir kullanıcı *akssre* parçası olan *opssre* grubu.

Üretim ortamlarında, var olan kullanıcıları ve grupları Azure AD kiracısı içinde kullanabilirsiniz.

İlk olarak, kaynak kimliği bölümünü kullanarak AKS kümenizin alın [az aks show][az-aks-show] komutu. Kaynak Kimliği adlı bir değişkene atayın *AKS_ID* böylece ek komutlar başvurulabilir.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

İlk örnek grubu kullanarak uygulama geliştiricileri için Azure AD'de oluşturma [az ad grubu oluşturmak][az-ad-group-create] komutu. Aşağıdaki örnekte adlı bir grup oluşturur *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Bir Azure rol ataması için şimdi oluşturun *appdev* kullanarak grup [az rol ataması oluşturma][az-role-assignment-create] komutu. Bu atama kullanmak grubunun bir üyesi sağlar `kubectl` bunları vererek bir AKS kümesi ile etkileşim kurmak için *Azure Kubernetes hizmeti küme kullanıcı rolünü*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Gibi bir hata alırsanız `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, Azure AD grubu nesne kimliği daha sonra deneyin dizin aracılığıyla yaymak için birkaç saniye bekleyin `az role assignment create` yeniden komutu.

İkinci bir örnek grubu oluşturun, bunun için SREs adlı *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Yeniden grubunun üyeleri vermek için bir Azure rol ataması oluşturma *Azure Kubernetes hizmeti küme kullanıcı rolünü*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Azure AD'de tanıtım kullanıcılar oluşturma

SREs ve belgelerimizin uygulama geliştiricileri için Azure AD'de oluşturulan iki örnek grupları ile artık iki örnek kullanıcı oluşturma olanak tanır. Makalenin sonunda RBAC tümleştirme test etmek için bu hesapları ile AKS kümesi için oturum açın.

Azure AD kullanarak ilk kullanıcı hesabı oluşturma [az ad kullanıcısı oluşturun][az-ad-user-create] komutu.

Aşağıdaki örnek, bir kullanıcı görünen adı oluşturur. *AKS geliştirme* ve kullanıcı asıl adı (UPN) `aksdev@contoso.com`. Azure AD kiracınız için doğrulanmış bir etki alanı eklemek için UPN güncelleştirin (değiştirin *contoso.com* kendi etki alanı ile) ve kendi güvenli `--password` kimlik bilgisi:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Artık kullanıcı eklemek *appdev* önceki kullanarak bölümünde oluşturduğunuz grup [az ad grubuna üye ekleme][az-ad-group-member-add] komutu:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

İkinci bir kullanıcı hesabı oluşturun. Aşağıdaki örnek, bir kullanıcı görünen adı oluşturur. *AKS SRE* ve kullanıcı asıl adı (UPN) `akssre@contoso.com`. Azure AD kiracınız için doğrulanmış bir etki alanı eklemek için UPN yeniden güncelleştirin (değiştirin *contoso.com* kendi etki alanı ile) ve kendi güvenli `--password` kimlik bilgisi:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Uygulama geliştiricileri için AKS küme kaynaklarını oluşturma

Azure AD grupları ve kullanıcılar artık oluşturulur. Azure rol atamaları, Grup üyelerinin bir AKS kümesi normal bir kullanıcı olarak bağlanmak için oluşturulmuştur. Şimdi, bu belirli kaynakları farklı gruplara erişim izni vermek için AKS kümesi yapılandıralım.

İlk olarak, yönetici kimlik bilgilerini kullanarak küme alın [az aks get-credentials][az-aks-get-credentials] komutu. Aşağıdaki bölümlerde her birinde normal alma *kullanıcı* küme kimlik bilgilerini Azure AD kimlik doğrulaması görmek için akışı çalıştırılırken.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ad alanı kullanarak AKS kümesi oluşturma [kubectl ad alanı oluşturma][kubectl-create] komutu. Aşağıdaki örnek, bir ad alanı adı oluşturur *geliştirme*:

```console
kubectl create namespace dev
```

Kubernetes, *rolleri* vermek için izinleri tanımlamanıza ve *RoleBindings* istenen kullanıcılar veya gruplar için geçerlidir. Bu atamaları, tüm küme üzerinde veya belirtilen bir ad alanı için uygulanabilir. Daha fazla bilgi için [kullanarak RBAC yetkilendirme][rbac-authorization].

İlk olarak, bir rol için oluşturma *geliştirme* ad alanı. Bu rol, ad alanı için tam izinleri verir. Üretim ortamlarında, farklı kullanıcılar veya gruplar için daha ayrıntılı izinler belirtebilirsiniz.

Adlı bir dosya oluşturun `role-dev-namespace.yaml` aşağıdaki YAML bildirimi yapıştırın:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Rolü kullanarak oluşturma [kubectl uygulamak][kubectl-apply] komut ve dosya YAML bildiriminizi adını belirtin:

```console
kubectl apply -f role-dev-namespace.yaml
```

Ardından, kaynak Kimliğini alın *appdev* kullanarak grup [az ad Grup show][az-ad-group-show] komutu. Bu grup, sonraki adımda bir RoleBinding konu olarak ayarlanır.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Bir RoleBinding için şimdi oluşturun *appdev* Grup daha önce oluşturulan rol ad alanı erişimi için kullanılacak. Adlı bir dosya oluşturun `rolebinding-dev-namespace.yaml` aşağıdaki YAML bildirimi yapıştırın. Son satırında değiştirin *groupObjectId* grup nesne kimliği çıktısını önceki komuttan ile:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

RoleBinding kullanarak oluşturduğunuz [kubectl uygulamak][kubectl-apply] komut ve dosya YAML bildiriminizi adını belirtin:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>AKS kümesi kaynakları SREs için oluşturma

Artık, bir ad alanı, rol ve RoleBinding SREs için oluşturmak için önceki adımları yineleyin.

İlk olarak, bir ad alanı oluşturulur *sre* kullanarak [kubectl ad alanı oluşturma][kubectl-create] komutu:

```console
kubectl create namespace sre
```

Adlı bir dosya oluşturun `role-sre-namespace.yaml` aşağıdaki YAML bildirimi yapıştırın:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Rolü kullanarak oluşturma [kubectl uygulamak][kubectl-apply] komut ve dosya YAML bildiriminizi adını belirtin:

```console
kubectl apply -f role-sre-namespace.yaml
```

İçin kaynak Kimliğini alın *opssre* kullanarak grup [az ad Grup show][az-ad-group-show] komutu:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Oluşturmak için bir RoleBinding *opssre* Grup daha önce oluşturulan rol ad alanı erişimi için kullanılacak. Adlı bir dosya oluşturun `rolebinding-sre-namespace.yaml` aşağıdaki YAML bildirimi yapıştırın. Son satırında değiştirin *groupObjectId* grup nesne kimliği çıktısını önceki komuttan ile:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

RoleBinding kullanarak oluşturduğunuz [kubectl uygulamak][kubectl-apply] komut ve dosya YAML bildiriminizi adını belirtin:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Azure AD kimlikleri kullanarak küme kaynaklarıyla etkileşimli çalışma

Şimdi, oluşturup bir AKS kümesindeki kaynaklarını yönetme şimdi beklenen izinleri test etme. Bu örneklerde, zamanlayabilir ve kullanıcının atanan ad alanında pod'ları görüntüleyin. Daha sonra zamanlama ve görünüm pod atanan ad alanı dışında deneyin.

İlk olarak, sıfırlama *kubeconfig'i denetleyin* bağlamını kullanarak [az aks get-credentials][az-aks-get-credentials] komutu. Bir önceki bölümde Küme Yöneticisi kimlik bilgilerini kullanarak bağlamını ayarlayın. Yönetici kullanıcı, Azure AD oturum açma istemleri atlar. Olmadan `--admin` parametresi, kullanıcı bağlamı ile Azure AD kimlik doğrulamasından geçmesi için tüm istekleri gerektiren uygulanır.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Temel bir NGINX pod kullanarak zamanlama [çalıştırma kubectl][kubectl-run] komutunu *geliştirme* ad alanı:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Oturum açma istemi kimlik bilgilerini kendi için girin `appdev@contoso.com` makalenin başında oluşturduğunuz hesabı. Oturumunuz başarıyla açıldıktan sonra hesap belirteci geleceğe yönelik önbelleğe alınmış `kubectl` komutları. NGINX olduğu başarıyla planlamak, aşağıdaki örnek çıktıda gösterildiği gibi:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Artık [kubectl pod'ları alma][kubectl-get] pod'ların görüntülemek için komut *geliştirme* ad alanı.

```console
kubectl get pods --namespace dev
```

Aşağıdaki örnek çıktıda gösterildiği gibi NGINX pod başarıyla olan *çalıştıran*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Oluşturun ve atanan ad alanı dışında küme kaynaklarını görüntüleyin

Pod'ların dışında görüntülemek şimdi deneyin *geliştirme* ad alanı. Kullanım [kubectl pod'ları alma][kubectl-get] yeniden görmek için bu kez komut `--all-namespaces` gibi:

```console
kubectl get pods --all-namespaces
```

Kullanıcının grup üyeliği, aşağıdaki örnek çıktıda gösterildiği gibi bu eylemi izin veren bir Kubernetes rol yok:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Aynı şekilde, bir pod içinde farklı bir ad alanı gibi zamanlamayı deneyin *sre* ad alanı. Kullanıcının grup üyeliğini bu izinleri vermek için bir Kubernetes rol ve RoleBinding aşağıdaki örnek çıktıda gösterildiği gibi işlemiyle uyumlu değildir:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>AKS kümesi kaynakları SRE erişimi test etme

Müşterilerimizin Azure AD grubu üyeliği ve Kubernetes RBAC farklı kullanıcılar ve gruplar arasında düzgün çalışmasını doğrulamak için önceki komutların olarak oturum açarken deneyin *opssre* kullanıcı.

Sıfırlama *kubeconfig'i denetleyin* bağlamını kullanarak [az aks get-credentials][az-aks-get-credentials] için önceden önbelleğe alınan kimlik doğrulama belirteci temizler komut *aksdev* kullanıcı:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Zamanlama ve görünüm pod'ların atanan deneyin *sre* ad alanı. İstendiğinde, kendi bilgilerinizle oturum `opssre@contoso.com` makalenin başında oluşturduğunuz kimlik bilgilerini:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Aşağıdaki örnek çıktıda gösterildiği gibi başarılı bir şekilde oluşturabilir ve pod'ları görüntüleyin:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Atanan SRE ad alanı dışında pod'ları planlamak veya görüntülemek şimdi deneyin:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Bunlar `kubectl` komutları başarısız, aşağıdaki örnek çıktıda gösterildiği gibi. Kullanıcının grup üyeliğini ve Kubernetes rol ve RoleBindings oluşturmak için izinler veya diğer ad manager kaynaklarına izni yok:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede, AKS kümesi ve kullanıcı kaynakları ve Azure AD'de grupları oluşturulur. Tüm bu kaynakları temizlemek için aşağıdaki komutları çalıştırın:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Sonraki adımlar

Kubernetes kümelerini güvenliğini sağlama hakkında daha fazla bilgi için bkz. [AKS için erişim ve kimlik seçeneklerini)][rbac-authorization].

Kimlik ve kaynak denetimi ile ilgili en iyi yöntemler için bkz. [en iyi uygulamalar için kimlik doğrulama ve yetkilendirme aks'deki][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md

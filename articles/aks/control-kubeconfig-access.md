---
title: Azure Kubernetes Service (AKS) içindeki kubeconfig 'e erişimi sınırlama
description: Küme yöneticileri ve küme kullanıcıları için Kubernetes yapılandırma dosyasına (kubeconfig) erişimi denetleme hakkında bilgi edinin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: cbc653b86ed83f9d6a7348d39f51dc7cd49c6892
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67615668"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmetindeki (AKS) Kubernetes yapılandırma dosyasına erişim tanımlamak için Azure rol tabanlı erişim denetimlerini kullanma

`kubectl` Aracı kullanarak Kubernetes kümeleriyle etkileşim kurabilirsiniz. Azure CLı, kullanarak `kubectl`aks kümelerinize bağlanmak için erişim kimlik bilgilerini ve yapılandırma bilgilerini almanın kolay bir yolunu sunar. Bu Kubernetes yapılandırma (*kubeconfig*) bilgilerini kimlerin alabilirim ve sahip oldukları izinleri kısıtlamak için Azure rol tabanlı erişim DENETIMLERINI (RBAC) kullanabilirsiniz.

Bu makalede, bir AKS kümesi için yapılandırma bilgilerini kimlerin alabilirim olduğunu sınırlayan RBAC rollerinin nasıl atanacağı gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Bu makalede, Azure CLı sürüm 2.0.65 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Kullanılabilir küme rolleri izinleri

`kubectl` Aracı kullanarak bir aks kümesiyle etkileşim kurarken, küme bağlantı bilgilerini tanımlayan bir yapılandırma dosyası kullanılır. Bu yapılandırma dosyası genellikle *~/. Kube/config*dizininde depolanır. Bu *kubeconfig* dosyasında birden çok küme tanımlanabilir. [Kubectl config Use-Context][kubectl-config-use-context] komutunu kullanarak kümeler arasında geçiş yapabilirsiniz.

[Az aks Get-Credentials][az-aks-get-credentials] komutu, BIR aks kümesinin erişim kimlik bilgilerini almanıza ve bunları *kubeconfig* dosyasına birleştirmenize imkan tanır. Bu kimlik bilgilerine erişimi denetlemek için Azure rol tabanlı erişim denetimlerini (RBAC) kullanabilirsiniz. Bu Azure RBAC rolleri, *kubeconfig* dosyasını kimlerin alabileceklerini ve daha sonra küme içinde sahip oldukları izinleri tanımlamanızı sağlar.

İki yerleşik rol şunlardır:

* **Azure Kubernetes hizmet kümesi yönetici rolü**  
    * *Microsoft. ContainerService/Managedkümeler/listClusterAdminCredential/Action* API çağrısına erişime izin verir. Bu API çağrısı [, Küme Yöneticisi kimlik bilgilerini listeler][api-cluster-admin].
    * *Clusteradmin* rolü için *kubeconfig* ' i indirir.
* **Azure Kubernetes hizmet kümesi Kullanıcı rolü**
    * *Microsoft. ContainerService/Managedkümeler/listClusterUserCredential/Action* API çağrısına erişime izin verir. Bu API çağrısı [, küme kullanıcı kimlik bilgilerini listeler][api-cluster-user].
    * *Clusteruser* rolü için *kubeconfig* ' i indirir.

Bu RBAC rolleri, bir Azure Active Directory (AD) kullanıcısına veya grubuna uygulanabilir.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Bir kullanıcıya veya gruba rol izinleri atama

Kullanılabilir rollerden birini atamak için AKS kümesinin kaynak KIMLIĞINI ve Azure AD Kullanıcı hesabı veya grubunun KIMLIĞINI almanız gerekir. Aşağıdaki örnek komutlar:

* *Myresourcegroup* kaynak grubundaki *Myakscluster* adlı küme için [az aks Show][az-aks-show] komutunu kullanarak küme kaynak kimliğini alın. Gerektiğinde kendi kümenizi ve kaynak grubu adını sağlayın.
* Kullanıcı KIMLIĞINIZI almak için [az Account Show][az-account-show] ve [az ad User Show][az-ad-user-show] komutlarını kullanır.
* Son olarak, [az role atama Create][az-role-assignment-create] komutunu kullanarak bir rol atar.

Aşağıdaki örnek, *Azure Kubernetes hizmet kümesi Yöneticisi rolünü* bireysel bir kullanıcı hesabına atar:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Bir Azure AD grubuna izinler atamak istiyorsanız, önceki örnekte gösterilen `--assignee` parametreyi bir *Kullanıcı*yerine *grubun* nesne kimliğiyle güncelleştirin. Bir grubun nesne KIMLIĞINI almak için [az Ad Group Show][az-ad-group-show] komutunu kullanın. Aşağıdaki örnek, *AppDev*ADLı Azure AD grubu IÇIN nesne kimliğini alır:`az ad group show --group appdev --query objectId -o tsv`

Daha önce, *küme kullanıcı rolü* için bir önceki atamayı gerektiği şekilde değiştirebilirsiniz.

Aşağıdaki örnek çıktıda, rol atamasının başarıyla oluşturulduğu gösterilmektedir:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Yapılandırma bilgilerini edinme ve doğrulama

RBAC rolleri atandığında, AKS kümenizin *kubeconfig* tanımını almak için [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanın. Aşağıdaki örnekte, kullanıcıya *Küme Yönetici rolü*verildiyse doğru şekilde çalışan *--yönetici* kimlik bilgileri alınır:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Daha sonra, küme *bağlamının* yönetici yapılandırma bilgilerinin uygulandığını gösterdiğini doğrulamak için [kubectl config View][kubectl-config-view] komutunu kullanabilirsiniz:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Rol izinlerini kaldır

Rol atamalarını kaldırmak için [az role atama Delete][az-role-assignment-delete] komutunu kullanın. Önceki komutlarda elde edilen hesap KIMLIĞI ve küme kaynak KIMLIĞI ' ni belirtin. Rolü bir kullanıcı yerine bir gruba atadıysanız, `--assignee` parametre için hesap nesne kimliği yerine uygun Grup nesnesi kimliğini belirtin:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Sonraki adımlar

AKS kümelerine erişim hakkında gelişmiş güvenlik için [Azure Active Directory kimlik doğrulamasını tümleştirin][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show

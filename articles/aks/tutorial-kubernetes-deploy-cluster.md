---
title: Azure’da Kubernetes öğreticisi - Kümeyi dağıtma
description: Bu Azure Kubernetes Service (AKS) öğreticisinde bir AKS kümesi oluşturacak ve kubectl istemcisini kullanarak Kubernetes ana düğümüne bağlanacaksınız.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d7e931a55ec0a9d46a8b92d4353bd2de8edd8818
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777842"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Hızlı Başlangıç: Azure Kubernetes Hizmeti (AKS) kümesini dağıtma

Kubernetes, kapsayıcılı uygulamalar için dağıtılmış bir platform sunar. AKS ile hızlı bir şekilde üretim için hazır bir Kubernetes kümesi oluşturabilirsiniz. Yedi parçalık bu öğreticinin üçüncü kısmında, AKS içinde bir Kubernetes kümesi dağıtılır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Azure Container Registry 'de kimlik doğrulayabilecek bir Kubernetes AKS kümesi dağıtma
> * Kubernetes CLI (kubectl) yükleme
> * kubectl istemcisini AKS kümenize bağlanacak şekilde yapılandırma

Sonraki öğreticilerde, Azure oy uygulaması kümeye dağıtılır, ölçeklendirilir ve güncelleştirilir.

## <a name="before-you-begin"></a>Başlamadan önce

Önceki öğreticilerde, bir kapsayıcı görüntüsü oluşturuldu ve Azure Container Registry örneğine yüklendi. Bu adımları tamamlamadıysanız ve takip etmek istiyorsanız, [öğretici 1 – kapsayıcı görüntüleri oluşturma][aks-tutorial-prepare-app]' dan başlayın.

Bu öğreticide, Azure CLı sürüm 2.0.53 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Kubernetes kümesi oluşturma

AKS kümelerinde Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) kullanılabilir. Bu denetimler, kullanıcılara atanmış olan rollere göre kaynaklara erişim vermenizi sağlayabilir. Bir kullanıcıya birden çok rol atanırsa izinler birleştirilir ve izinler tek bir ad alanı veya tüm küme genelinde kapsam olabilir. Varsayılan olarak, bir AKS kümesi oluşturduğunuzda, Azure CLı, Kubernetes RBAC 'yi otomatik olarak sağlar.

[az aks create][] komutunu kullanarak bir AKS kümesi oluşturun. Aşağıdaki örnek, *myResourceGroup* adlı kaynak grubunda *myAKSCluster* adlı bir küme oluşturur. Bu kaynak grubu, *eastus* bölgesindeki [önceki öğreticide][aks-tutorial-prepare-acr] oluşturulmuştur. Aşağıdaki örnek, *eastus* bölgesinde aks kümesinin de oluşturulması için bir bölge belirtmiyor. Daha fazla bilgi için bkz. [Azure Kubernetes Service (aks) Içindeki kotalar, sanal makine boyutu kısıtlamaları ve bölge kullanılabilirliği][quotas-skus-regions] , aks için kaynak sınırları ve bölge kullanılabilirliği hakkında daha fazla bilgi için.

Bir AKS kümesinin diğer Azure kaynaklarıyla etkileşime geçmesini sağlamak için bir küme kimliği belirtmediği için otomatik olarak oluşturulur. Burada, bu küme kimliğine önceki öğreticide oluşturduğunuz Azure Container Registry (ACR) örneğinden [çekme görüntülerinin hakkı verilir][container-registry-integration] . Komutu başarıyla yürütmek için Azure aboneliğinde bir **sahip** veya **Azure hesabı Yöneticisi** rolüne sahip olmanız gerekir.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

**Sahip** veya **Azure Hesap Yöneticisi** rolüne gerek duymamak için, ACR 'den görüntü çekmek üzere bir hizmet sorumlusu el ile de yapılandırabilirsiniz. Daha fazla bilgi için bkz. [hizmet sorumluları Ile ACR kimlik doğrulaması](../container-registry/container-registry-auth-service-principal.md) veya [çekme gizli anahtarı Ile Kubernetes kimlik doğrulaması](../container-registry/container-registry-auth-kubernetes.md). Alternatif olarak, daha kolay yönetim için hizmet sorumlusu yerine [yönetilen bir kimlik](use-managed-identity.md) kullanabilirsiniz.

Birkaç dakika sonra dağıtım tamamlanır ve AKS dağıtımı hakkında JSON biçimli bilgileri döndürür.

> [!NOTE]
> Kümenizin güvenilir bir şekilde çalışmasını sağlamak için en az 2 (iki) düğüm çalıştırmanız gerekir.

## <a name="install-the-kubernetes-cli"></a>Kubernetes CLI'yi yükleme

Yerel bilgisayarınızdan Kubernetes kümesine bağlanmak için Kubernetes’in komut satırı istemcisini ([kubectl][kubectl]) kullanmanız gerekir.

Azure Cloud Shell'i kullanıyorsanız `kubectl` zaten yüklüdür. [az aks install-cli][] komutunu kullanarak da yerel ortama yükleyebilirsiniz:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>kubectl istemcisini kullanarak kümeye bağlanma

`kubectl` istemcisini Kubernetes kümenize bağlanacak şekilde yapılandırmak için [az aks get-credentials][] komutunu kullanın. Aşağıdaki örnek, *Myresourcegroup* Içinde *Myakscluster* adlı aks kümesinin kimlik bilgilerini alır:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Kümenizin bağlantısını doğrulamak için [kubectl Get Nodes][kubectl-get] komutunu çalıştırarak küme düğümlerinin bir listesini döndürün:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide AKS'de bir Kubernetes kümesi dağıttınız ve `kubectl` istemcisini ona bağlanacak şekilde yapılandırdınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure Container Registry 'de kimlik doğrulayabilecek bir Kubernetes AKS kümesi dağıtma
> * Kubernetes CLI (kubectl) yükleme
> * kubectl istemcisini AKS kümenize bağlanacak şekilde yapılandırma

Kümeye uygulama dağıtmayı öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Kubernetes'te uygulama dağıtma][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az acr show]: /cli/azure/acr#az_acr_show
[az role assignment create]: /cli/azure/role/assignment#az_role_assignment_create
[az aks create]: /cli/azure/aks#az_aks_create
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md

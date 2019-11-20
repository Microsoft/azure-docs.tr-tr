---
title: Azure Kubernetes hizmeti (AKS) kümesi için kimlik bilgilerini sıfırlama
description: Azure Kubernetes Service (AKS) içindeki bir küme için hizmet sorumlusu kimlik bilgilerini güncelleştirme veya sıfırlama hakkında bilgi edinin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: bda0ab50b829fa2e6d58e73b51e3a0a0f6c9e2af
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432924"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de bir hizmet sorumlusu için kimlik bilgilerini güncelleştirme veya döndürme

Varsayılan olarak, AKS kümeleri bir yıllık sona erme saati olan bir hizmet sorumlusu ile oluşturulur. Sona erme tarihinin yakınında, hizmet sorumlusunu ek bir süre için uzatmak üzere kimlik bilgilerini sıfırlayabilirsiniz. Ayrıca, kimlik bilgilerini tanımlanmış güvenlik ilkesinin bir parçası olarak güncelleştirmek veya döndürmek isteyebilirsiniz. Bu makalede bir AKS kümesi için bu kimlik bilgilerini güncelleştirme ayrıntıları anlatılmaktadır.

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.0.65 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için @ no__t-0 ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Hizmet sorumlusu güncelleştirmeyi veya oluşturmayı seçin

Bir AKS kümesinin kimlik bilgilerini güncellemek istediğinizde şunları yapabilirsiniz:

* küme tarafından kullanılan mevcut hizmet sorumlusunun kimlik bilgilerini güncelleştirin veya
* bir hizmet sorumlusu oluşturun ve kümeyi bu yeni kimlik bilgilerini kullanacak şekilde güncelleştirin.

### <a name="update-existing-service-principal-expiration"></a>Mevcut hizmet sorumlusu süre sonunu güncelleştirme

Mevcut hizmet sorumlusunun kimlik bilgilerini güncelleştirmek için [az aks Show][az-aks-show] komutunu kullanarak kümenizin HIZMET sorumlusu kimliğini alın. Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki *Myakscluster* adlı kümenin kimliğini alır. Hizmet sorumlusu KIMLIĞI, ek komutta kullanılmak üzere *SP_ID* adlı bir değişken olarak ayarlanır.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Hizmet sorumlusu KIMLIĞINI içeren bir değişken kümesi ile, [az ad SP kimlik bilgisi sıfırlaması][az-ad-sp-credential-reset]' nı kullanarak kimlik bilgilerini sıfırlayın. Aşağıdaki örnek, Azure platformunun hizmet sorumlusu için yeni bir güvenli gizlilik oluşturmasına imkan tanır. Bu yeni güvenli gizli dizi da bir değişken olarak depolanır.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Şimdi [yeni kimlik bilgileriyle AKS kümesini güncelleştirmek](#update-aks-cluster-with-new-credentials)için devam edin. Bu adım, hizmet sorumlusu değişikliklerinin, AKS kümesinde yansıtılması için gereklidir.

### <a name="create-a-new-service-principal"></a>Yeni bir hizmet sorumlusu oluşturun

Önceki bölümde mevcut hizmet sorumlusu kimlik bilgilerini güncelleştirmeyi seçerseniz, bu adımı atlayın. [AKS kümesini yeni kimlik bilgileriyle güncelleştirmeye](#update-aks-cluster-with-new-credentials)devam edin.

Bir hizmet sorumlusu oluşturmak ve ardından bu yeni kimlik bilgilerini kullanmak üzere AKS kümesini güncelleştirmek için [az ad SP Create-for-RBAC][az-ad-sp-create] komutunu kullanın. Aşağıdaki örnekte, `--skip-assignment` parametresi, ek varsayılan atamaların atanmasını engellemektedir:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Çıktı aşağıdaki örneğe benzerdir. Kendi `appId` ve `password`’lerinizi not edin. Bu değerler bir sonraki adımda kullanılır.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Şimdi, aşağıdaki örnekte gösterildiği gibi, [az ad SP Create-for-RBAC][az-ad-sp-create] komutunuzu kullanarak HIZMET sorumlusu kimliği ve istemci gizli anahtarı için değişkenleri tanımlayın. *SP_ID* , *uygulama*uygulamasıdır ve *SP_SECRET* *parolandır*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Şimdi [yeni kimlik bilgileriyle AKS kümesini güncelleştirmek](#update-aks-cluster-with-new-credentials)için devam edin. Bu adım, hizmet sorumlusu değişikliklerinin, AKS kümesinde yansıtılması için gereklidir.

## <a name="update-aks-cluster-with-new-credentials"></a>AKS kümesini yeni kimlik bilgileriyle Güncelleştir

Mevcut hizmet sorumlusu için kimlik bilgilerini güncelleştirmeyi veya bir hizmet sorumlusu oluşturmayı seçtiğinizden bağımsız olarak, [az aks Update-Credentials][az-aks-update-credentials] komutunu kullanarak aks kümesini yeni kimlik bilgilerinizle güncelleştirmeniz gerekir. *--Service-Principal* ve *--Client-Secret* değişkenleri kullanılır:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Hizmet sorumlusu kimlik bilgilerinin AKS 'te güncelleştirilebilmesi birkaç dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümesinin kendisi için hizmet sorumlusu güncelleştirildi. Bir küme içindeki iş yüklerinin kimliğini yönetme hakkında daha fazla bilgi için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset

---
title: Bir kümenin kimlik bilgilerini sıfırlama
titleSuffix: Azure Kubernetes Service
description: Bir Azure Kubernetes hizmeti (AKS) kümesi için hizmet sorumlusu veya AAD uygulama kimlik bilgilerini güncelleştirme veya sıfırlama hakkında bilgi edinin
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 0b750eb9af7dfd7bcbada7500b6ef71b015db11f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767484"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) için kimlik bilgilerini güncelleştirme veya döndürme

Hizmet sorumlusu ile oluşturulan AKS kümelerinin bir yıllık sona erme saati vardır. Sona erme tarihinin yakınında, hizmet sorumlusunu ek bir süre için uzatmak üzere kimlik bilgilerini sıfırlayabilirsiniz. Ayrıca, kimlik bilgilerini tanımlanmış güvenlik ilkesinin bir parçası olarak güncelleştirmek veya döndürmek isteyebilirsiniz. Bu makalede bir AKS kümesi için bu kimlik bilgilerini güncelleştirme ayrıntıları anlatılmaktadır.

Ayrıca [AKS kümenizi Azure Active Directory ile tümleştirmiş][aad-integration]ve bunu kümeniz için bir kimlik doğrulama sağlayıcısı olarak kullanmanıza da olabilirsiniz. Bu durumda, kümeniz, AAD sunucu uygulaması ve AAD Istemci uygulaması için 2 daha fazla kimliğe sahip olursunuz. bu kimlik bilgilerini de sıfırlayabilirsiniz.

Alternatif olarak, bir hizmet sorumlusu yerine izinler için yönetilen bir kimlik de kullanabilirsiniz. Yönetilen kimlikler, hizmet sorumlularından daha kolay yönetilebilir ve güncelleştirmeler veya döndürmeler gerektirmez. Daha fazla bilgi için bkz. [yönetilen kimlikleri kullanma](use-managed-identity.md).

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.0.65 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>AKS kümeniz için yeni bir hizmet sorumlusu güncelleştirme veya oluşturma

Bir AKS kümesinin kimlik bilgilerini güncellemek istediğinizde aşağıdakilerden birini seçebilirsiniz:

* Mevcut hizmet sorumlusu için kimlik bilgilerini güncelleştirin.
* Yeni bir hizmet sorumlusu oluşturun ve kümeyi bu yeni kimlik bilgilerini kullanacak şekilde güncelleştirin. 

> [!WARNING]
> *Yeni* bir hizmet sorumlusu oluşturmayı seçerseniz, bu kimlik bilgilerini kullanmak için büyük bir aks kümesini güncelleştirme işleminin tamamlanması uzun zaman alabilir.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Hizmet sorumlunun sona erme tarihini denetleyin

Hizmet sorumlunun sona erme tarihini denetlemek için [az ad SP Credential List][az-ad-sp-credential-list] komutunu kullanın. Aşağıdaki örnek, [az aks Show][az-aks-show] komutunu kullanarak *myresourcegroup* kaynak grubundaki *myakscluster* adlı kümenin hizmet sorumlusu kimliğini alır. Hizmet sorumlusu KIMLIĞI, [az ad SP kimlik bilgileri listesi][az-ad-sp-credential-list] komutuyla kullanılmak üzere *SP_ID* adlı bir değişken olarak ayarlanır.

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Mevcut hizmet sorumlusu kimlik bilgisini sıfırlayın

Mevcut hizmet sorumlusunun kimlik bilgilerini güncelleştirmek için [az aks Show][az-aks-show] komutunu kullanarak kümenizin HIZMET sorumlusu kimliğini alın. Aşağıdaki örnek, *Myresourcegroup* kaynak grubundaki *Myakscluster* adlı kümenin kimliğini alır. Hizmet sorumlusu KIMLIĞI, ek komutta kullanılmak üzere *SP_ID* adlı bir değişken olarak ayarlanır. Bu komutlar Bash söz dizimini kullanır.

> [!WARNING]
> Azure sanal makine ölçek kümeleri kullanan bir AKS kümesindeki küme kimlik bilgilerinizi sıfırladığınızda, yeni kimlik bilgileri ile düğümlerinizi güncelleştirmek için bir [düğüm görüntüsü yükseltmesi][node-image-upgrade] yapılır.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Hizmet sorumlusu KIMLIĞINI içeren bir değişken kümesi ile, [az ad SP kimlik bilgisi sıfırlaması][az-ad-sp-credential-reset]' nı kullanarak kimlik bilgilerini sıfırlayın. Aşağıdaki örnek, Azure platformunun hizmet sorumlusu için yeni bir güvenli gizlilik oluşturmasına imkan tanır. Bu yeni güvenli gizli dizi da bir değişken olarak depolanır.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Şimdi [yeni hizmet sorumlusu kimlik bilgileriyle AKS kümesini güncelleştirmek](#update-aks-cluster-with-new-service-principal-credentials)için devam edin. Bu adım, hizmet sorumlusu değişikliklerinin, AKS kümesinde yansıtılması için gereklidir.

### <a name="create-a-new-service-principal"></a>Yeni bir hizmet sorumlusu oluşturun

Önceki bölümde mevcut hizmet sorumlusu kimlik bilgilerini güncelleştirmeyi seçerseniz, bu adımı atlayın. [AKS kümesini yeni hizmet sorumlusu kimlik bilgileriyle güncelleştirmeye](#update-aks-cluster-with-new-service-principal-credentials)devam edin.

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

Şimdi, aşağıdaki örnekte gösterildiği gibi, [az ad SP Create-for-RBAC][az-ad-sp-create] komutunuzu kullanarak HIZMET sorumlusu kimliği ve istemci gizli anahtarı için değişkenleri tanımlayın. *SP_ID* *uygulama* *uygulamasıdır* ve *SP_SECRET* parolanız olur:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Şimdi [yeni hizmet sorumlusu kimlik bilgileriyle AKS kümesini güncelleştirmek](#update-aks-cluster-with-new-service-principal-credentials)için devam edin. Bu adım, hizmet sorumlusu değişikliklerinin, AKS kümesinde yansıtılması için gereklidir.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>AKS kümesini yeni hizmet sorumlusu kimlik bilgileriyle Güncelleştir

> [!IMPORTANT]
> Büyük kümeler için, AKS kümesinin yeni bir hizmet sorumlusu ile güncelleştirilmesi uzun zaman alabilir.

Mevcut hizmet sorumlusu için kimlik bilgilerini güncelleştirmeyi veya bir hizmet sorumlusu oluşturmayı seçtiğinizden bağımsız olarak, [az aks Update-Credentials][az-aks-update-credentials] komutunu kullanarak aks kümesini yeni kimlik bilgilerinizle güncelleştirmeniz gerekir. *--Service-Principal* ve *--Client-Secret* değişkenleri kullanılır:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Küçük ve orta ölçekli kümeler için hizmet sorumlusu kimlik bilgilerinin AKS 'te güncelleştirilebilmesi birkaç dakika sürer.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>AKS kümesini yeni AAD uygulaması kimlik bilgileriyle Güncelleştir

[AAD tümleştirme adımlarını][create-aad-app]IZLEYEREK yeni AAD sunucusu ve istemci uygulamaları oluşturabilirsiniz. Veya mevcut AAD uygulamalarınızı [hizmet sorumlusu sıfırlama ile aynı yöntemi](#reset-the-existing-service-principal-credential)izleyerek sıfırlayın. Bundan sonra, yalnızca aynı [az aks Update-Credentials][az-aks-update-credentials] komutunu kullanarak, ancak *--Reset-AAD* değişkenlerini kullanarak kümenizin AAD uygulaması kimlik bilgilerinizi güncelleştirmeniz gerekir.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS kümesi için hizmet sorumlusu ve AAD tümleştirme uygulamaları güncelleştirildi. Bir küme içindeki iş yüklerinin kimliğini yönetme hakkında daha fazla bilgi için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-update-credentials]: /cli/azure/aks#az_aks_update_credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az_ad_sp_credential_list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[node-image-upgrade]: ./node-image-upgrade.md

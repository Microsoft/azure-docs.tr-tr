---
title: Azure Kubernetes Service’in (AKS) hizmet sorumluları
description: Azure Kubernetes Service’te bir küme için Azure Active Directory hizmet sorumlusu oluşturma ve yönetme
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2f32ce96097e008ac1100c62c04b6bb7001ae972
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779642"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Hizmeti (AKS) ile hizmet sorumluları

Azure API 'Leri ile etkileşime geçmek için bir AKS kümesi, [Azure Active Directory (ad) hizmet sorumlusu][aad-service-principal] ya da [yönetilen kimlik](use-managed-identity.md)gerektirir. Bir Azure yük dengeleyici veya kapsayıcı kayıt defteri (ACR) gibi diğer Azure kaynaklarını dinamik olarak oluşturmak ve yönetmek için hizmet sorumlusu veya yönetilen kimlik gerekir.

Bu makalede, AKS kümeleriniz için bir hizmet sorumlusunun nasıl oluşturulacağı ve kullanılacağı gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Azure AD hizmet sorumlusu oluşturmak için, Azure AD kiracınızla bir uygulamayı kaydetme ve uygulamanızı aboneliğinizdeki bir role atama izinlerinizin olması gerekir. Gerekli izinlere sahip değilseniz Azure AD veya abonelik yöneticinizden gerekli izinleri atamasını istemeniz veya AKS kümesiyle kullanmanız için bir hizmet sorumlusu oluşturma ön işlemlerini tamamlamanız gerekebilir.

Farklı bir Azure AD kiracısından bir hizmet sorumlusu kullanıyorsanız, kümeyi dağıtırken kullanılabilen izinlerle ilgili ek konular vardır. Dizin bilgilerini okumak ve yazmak için uygun izinleriniz olmayabilir. Daha fazla bilgi için bkz [. Azure Active Directory varsayılan kullanıcı Izinleri nelerdir?][azure-ad-permissions]

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Otomatik olarak bir hizmet sorumlusu oluşturma ve kullanma

Azure portalda bir AKS kümesi oluşturduğunuzda veya [az aks create][az-aks-create] komutunu kullanarak, Azure otomatik olarak bir hizmet sorumlusu oluşturabilir.

Aşağıdaki Azure CLI örneğinde bir hizmet sorumlusu belirtilmemiştir. Bu senaryoda Azure CLI, AKS kümesi için bir hizmet sorumlusu oluşturur. İşlemi başarıyla tamamlamak için Azure hesabınızın gerekli hizmet sorumlusu oluşturma haklarına sahip olması gerekir.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>El ile hizmet sorumlusu oluşturma

El ile hizmet sorumlusunu Azure CLI ile oluşturmak için [az ad sp create-for-rbac][az-ad-sp-create] komutunu kullanın. Aşağıdaki örnekte, `--skip-assignment` parametresi, ek varsayılan atamaların atanmasını engellemektedir:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

Çıktı aşağıdaki örneğe benzerdir. Kendi `appId` ve `password`’lerinizi not edin. Bu değerler bir sonraki bölümde AKS kümesi oluşturduğunuzda kullanılır.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Bir AKS kümesi için hizmet sorumlusu belirtin

[az aks create][az-aks-create] komutunu kullanarak bir AKS kümesi oluşturduğunuzda var olan bir hizmet sorumlusunu kullanmak üzere, [az ad sp create-for-rbac][az-ad-sp-create] komutunun çıktısından `appId` ve `password`’i belirtmek için `--service-principal` ve `--client-secret` parametrelerini kullanın:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Özelleştirilmiş gizli dizi var olan bir hizmet sorumlusunu kullanıyorsanız, gizliliğin 190 bayttan daha uzun olmadığından emin olun.

Azure portalı kullanarak bir AKS kümesi dağıtırsanız, **Kubernetes kümesi oluştur** iletişim kutusunun *Kimlik doğrulaması* sayfasında **Hizmet sorumlusu yapılandır**’ı seçin. **Var olanı kullan**’ı seçin ve aşağıdaki değerleri belirtin:

- **Hizmet sorumlusu istemci kimliği**, *appId*’nizdir
- **Hizmet sorumlusu istemci parolası**, *parola* değeridir

![Azure Vote’a göz atma görüntüsü](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Diğer Azure kaynaklarına erişim yetkisi verme

AKS kümesi için hizmet sorumlusu diğer kaynaklara erişmek için kullanılabilir. Örneğin, AKS kümenizi var olan bir Azure sanal ağ alt ağına dağıtmak veya Azure Container Registry (ACR) hizmetine bağlanmak istiyorsanız, hizmet sorumlusu için bu kaynaklara erişim yetkisi almanız gerekir.

İzinleri devretmek için [az role atama Create][az-role-assignment-create] komutunu kullanarak bir rol ataması oluşturun. `appId`' Yi, kaynak grubu veya sanal ağ kaynağı gibi belirli bir kapsama atayın. Daha sonra bir rol, aşağıdaki örnekte gösterildiği gibi, hizmet sorumlusunun kaynak üzerinde hangi izinlere sahip olduğunu tanımlar:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

`--scope`Bir kaynağın */Subscriptions/ \<guid\> /ResourceGroups/myresourcegroup* veya */Subscriptions/ \<guid\> /resourceGroups/myResourceGroupVnet/Providers/Microsoft.Network/virtualNetworks/myVnet* gibi tam kaynak kimliği olması gerekir

> [!NOTE]
> Katılımcı rolü atamasını düğüm kaynak grubundan kaldırdıysanız, aşağıdaki işlemler başarısız olabilir.  

Aşağıdaki bölümlerde, yapmanız gerekebilecek ortak temsilciler ayrıntılandırır.

### <a name="azure-container-registry"></a>Azure Container Registry

Kapsayıcı görüntü depolukça Azure Container Registry (ACR) kullanıyorsanız, görüntüleri okumak ve çekmek için AKS kümenizin hizmet sorumlusu için izinler vermeniz gerekir. Şu anda önerilen yapılandırma, [az aks Create][az-aks-create] veya [az aks Update][az-aks-update] komutunu bir kayıt defteriyle tümleştirilecek ve hizmet sorumlusu için uygun rolü atayacak şekilde kullanmaktır. Ayrıntılı adımlar için bkz. [Azure Kubernetes hizmetinden Azure Container Registry kimlik doğrulaması][aks-to-acr].

### <a name="networking"></a>Ağ

Sanal ağ ve alt ağ ya da genel IP adreslerinin başka bir kaynak grubunda olduğu gelişmiş ağ kullanabilirsiniz. [Ağ katılımcısı][rbac-network-contributor] yerleşik rolünü, sanal ağ içindeki alt ağ üzerinde atayın. Alternatif olarak, bu kaynak grubundaki ağ kaynaklarına erişim izinleri ile [özel bir rol][rbac-custom-role] oluşturabilirsiniz. Daha fazla bilgi için bkz. [aks hizmet izinleri][aks-permissions] .

### <a name="storage"></a>Depolama

Başka bir kaynak grubundaki mevcut disk kaynaklarına erişmeniz gerekebilir. Aşağıdaki rol izinleri kümesinden birini atayın:

- Özel bir [rol][rbac-custom-role] oluşturun ve aşağıdaki rol izinlerini tanımlayın:
  - *Microsoft. COMPUTE/Disks/Read*
  - *Microsoft. COMPUTE/Disks/Write*
- Veya, kaynak grubunda [depolama hesabı katılımcısı][rbac-storage-contributor] yerleşik rolünü atama

### <a name="azure-container-instances"></a>Azure Container Instances

Sanal Kubelet 'i AKS ile tümleştirilebilir ve kaynak grubunda AKS kümesine göre Azure Container Instances (ACI) çalıştırmayı seçerseniz, AKS hizmet sorumlusuna acı kaynak grubunda *katkıda bulunan* izinleri verilmelidir.

## <a name="additional-considerations"></a>Diğer konular

AKS ve Azure AD hizmet sorumlularını kullanılırken aşağıdaki noktalara dikkat edin.

- Kubernetes için hizmet sorumlusu, küme yapılandırmasının bir parçasıdır. Ancak, kümeyi dağıtmak için kimlik kullanmayın.
- Varsayılan olarak, hizmet sorumlusu kimlik bilgileri bir yıl için geçerlidir. [Hizmet sorumlusu kimlik bilgilerini dilediğiniz zaman güncelleştirebilir veya döndürebilirsiniz][update-credentials] .
- Her hizmet sorumlusunun bir Azure AD uygulamasıyla ilişkilendirilmiş olması gerekir. Bir Kubernetes kümesinin hizmet sorumlusu, geçerli herhangi bir Azure AD uygulama adıyla ilişkilendirilebilir (örneğin: *https://www.contoso.org/example* ). Uygulama URL'sinin gerçek bir uç nokta olması gerekmez.
- Hizmet sorumlusu **İstemci kimliğini** belirttiğinizde `appId` değerini kullanın.
- Kubernetes kümesindeki aracı düğümü VM 'lerinde hizmet sorumlusu kimlik bilgileri dosyada depolanır `/etc/kubernetes/azure.json`
- Hizmet sorumlusunu otomatik olarak oluşturmak için [az aks create][az-aks-create] komutunu kullandığınızda, hizmet sorumlusu kimlik bilgileri komutun çalıştırıldığı makinede `~/.azure/aksServicePrincipal.json` dosyasına yazılır.
- Ek AKS CLı komutlarına özel olarak bir hizmet sorumlusu geçirmezseniz, adresinde bulunan varsayılan hizmet sorumlusu `~/.azure/aksServicePrincipal.json` kullanılır.  
- Ayrıca, isteğe bağlı olarak dosyadaki aksServicePrincipal.jskaldırabilirsiniz ve AKS 'ler yeni bir hizmet sorumlusu oluşturur.
- [az aks create][az-aks-create] komutu tarafından oluşturulan bir AKS kümesini sildiğinizde, otomatik olarak oluşturulan hizmet sorumlusu silinmez.
    - Hizmet sorumlusunu silmek için, kümenizin *Serviceprincipalprofile. ClientID* ' yi sorgulayın ve [az ad SP Delete][az-ad-sp-delete]ile silin. Aşağıdaki kaynak grubunu ve küme adlarını kendi değerlerinizle değiştirin:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Sorun giderme

Bir AKS kümesi için hizmet sorumlusu kimlik bilgileri, Azure CLı tarafından önbelleğe alınır. Bu kimlik bilgilerinin geçerliliği dolmuşsa AKS kümelerini dağıtma hatalarıyla karşılaşırsınız. [Az aks Create][az-aks-create] çalıştırılırken aşağıdaki hata iletisi, önbelleğe alınmış hizmet sorumlusu kimlik bilgileriyle ilgili bir sorun olduğunu gösteriyor olabilir:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Aşağıdaki komutu kullanarak kimlik bilgileri dosyasının yaşını denetleyin:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Hizmet sorumlusu kimlik bilgileri için varsayılan süre sonu zamanı bir yıldır. Dosyadaki *aksServicePrincipal.js* bir yıldan eskiyse, dosyayı silin ve bır aks kümesini yeniden dağıtmayı deneyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Active Directory hizmet sorumluları hakkında daha fazla bilgi için bkz. [uygulama ve hizmet sorumlusu nesneleri][service-principal].

Kimlik bilgilerini güncelleştirme hakkında daha fazla bilgi için bkz. [AKS 'de hizmet sorumlusu için kimlik bilgilerini güncelleştirme veya döndürme][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az_ad_app_list
[az-ad-app-delete]: /cli/azure/ad/app#az_ad_app_delete
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions

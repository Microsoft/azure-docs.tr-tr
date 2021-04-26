---
title: Azure 'da Kubernetes 'te Azure RBAC 'yi yönetme
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes hizmeti (AKS) ile Kubernetes yetkilendirmesi için Azure RBAC kullanmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c708a577a1c2e4bb8f7ddff90f458afd0d9e566f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783008"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Kubernetes Yetkilendirmesi için Azure RBAC kullanma (önizleme)

Bugün, [Azure Active Directory (Azure AD) ve AKS arasında tümleşik kimlik doğrulamasından](managed-aad.md)zaten yararlanabilirsiniz. Bu tümleştirme etkinleştirildiğinde, müşterilerin Azure AD kullanıcılarını, gruplarını veya hizmet sorumlularını Kubernetes RBAC 'de konu olarak kullanmasına izin verir. daha fazla bilgi için [buraya](azure-ad-rbac.md)bakın.
Bu özellik, Kubernetes için Kullanıcı kimliklerini ve kimlik bilgilerini ayrı ayrı yönetme zorunluluğundan kurtarır. Ancak, hala Azure RBAC ve Kubernetes RBAC 'i ayrı olarak ayarlayıp yönetmeniz gerekir. AKS üzerinde RBAC ile kimlik doğrulama ve yetkilendirme hakkında daha fazla bilgi için [buraya](concepts-identity.md)bakın.

Bu belge, Azure kaynakları, AKS ve Kubernetes kaynakları genelinde Birleşik yönetim ve erişim denetimine izin veren yeni bir yaklaşım içerir.

## <a name="before-you-begin"></a>Başlamadan önce

Azure 'dan Kubernetes kaynakları için RBAC 'yi yönetme özelliği, Azure veya yerel Kubernetes mekanizmalarını kullanarak küme kaynakları için RBAC yönetme seçeneği sunar. Etkinleştirildiğinde, Azure AD sorumluları, normal Kubernetes kullanıcıları ve hizmet hesapları, Kubernetes RBAC tarafından özel olarak doğrulanırken Azure RBAC tarafından özel olarak doğrulanacak. AKS üzerinde RBAC ile kimlik doğrulama ve yetkilendirme hakkında daha fazla bilgi için [buraya](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)bakın.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Önkoşullar 
- Azure CLı sürüm 2.9.0 veya sonraki bir sürüme sahip olduğunuzdan emin olun
- `EnableAzureRBACPreview`Özellik bayrağının etkinleştirildiğinden emin olun.
- `aks-preview` [CLI uzantısının][az-extension-add] v 0.4.55 veya üzeri yüklü olduğundan emin olun
- [Kubectl v 1.18.3 +][az-aks-install-cli]' i yüklediğinizden emin olun.

#### <a name="register-enableazurerbacpreview-preview-feature"></a>`EnableAzureRBACPreview`Önizleme özelliğini Kaydet

Kubernetes yetkilendirmesi için Azure RBAC kullanan bir AKS kümesi oluşturmak için `EnableAzureRBACPreview` aboneliğinizdeki Özellik bayrağını etkinleştirmeniz gerekir.

`EnableAzureRBACPreview`Aşağıdaki örnekte gösterildiği gibi [az Feature Register][az-feature-register] komutunu kullanarak özellik bayrağını kaydedin:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 [Az Feature List][az-feature-list] komutunu kullanarak kayıt durumunu denetleyebilirsiniz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Hazırlandığınızda, [az Provider Register][az-provider-register] komutunu kullanarak *Microsoft. Containerservice* kaynak sağlayıcısı kaydını yenileyin:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI uzantısını yükleme

Azure RBAC kullanan bir AKS kümesi oluşturmak için, *aks-Preview* CLI uzantısı sürüm 0.4.55 veya üzeri gerekir. [Az Extension Add][az-extension-add] komutunu kullanarak *aks-Preview* Azure CLI uzantısını veya [az Extension Update][az-extension-update] komutunu kullanarak tüm kullanılabilir güncelleştirmeleri yüklemeyi kullanın:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Sınırlamalar

- [Yönetilen Azure AD tümleştirmesi](managed-aad.md)gerektirir.
- Önizleme sırasında Kubernetes yetkilendirmesi için Azure RBAC 'yi var olan kümelerle tümleştirmezsiniz, ancak genel kullanıma (GA) erişebilirsiniz.
- [Kubectl v 1.18.3 +][az-aks-install-cli]kullanın.
- CRD 'Ler varsa ve özel rol tanımları oluşturuyorsanız, şimdi CRDs 'yi kapsamanın tek yolu sağlamaktır `Microsoft.ContainerService/managedClusters/*/read` . AKS, CRD 'Ler için daha ayrıntılı izinler sağlamaya çalışır. Kalan nesneler için, belirli API gruplarını kullanabilirsiniz, örneğin: `Microsoft.ContainerService/apps/deployments/read` .
- Yeni rol atamalarının, bir yetkilendirme sunucusu tarafından yayılması ve güncellenmesi en fazla 5 dakika sürebilir.
- Kimlik doğrulaması için yapılandırılmış Azure AD kiracının, AKS kümesini tutan aboneliğin kiracısıyla aynı olması gerekir. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Azure RBAC ve yönetilen Azure AD tümleştirmesi kullanarak yeni bir küme oluşturma

Aşağıdaki CLı komutlarını kullanarak bir AKS kümesi oluşturun.

Azure Kaynak grubu oluşturun:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Yönetilen Azure AD tümleştirmesi ve Kubernetes yetkilendirmesi için Azure RBAC ile AKS kümesi oluşturun.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Azure AD tümleştirmesi ve Kubernetes yetkilendirmesi için Azure RBAC ile bir kümenin başarıyla oluşturulması, yanıt gövdesinde aşağıdaki bölüme sahiptir:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Kullanıcıların kümeye erişmesi için rol atamaları oluşturma

AKS aşağıdaki dört yerleşik rolü sağlar:


| Rol                                | Açıklama  |
|-------------------------------------|--------------|
| Azure Kubernetes hizmeti RBAC okuyucusu  | Bir ad alanındaki birçok nesneyi görmek için salt okuma erişimine izin verir. Rollerin veya rol bağlamalarının görüntülenmesine izin vermez. `Secrets`Gizli dizi içeriğini okumak, ad alanındaki ServiceAccount kimlik bilgilerine erişim sağladığından, ad alanındaki (ayrıcalık yükseltme bir biçimde) API erişimine izin veren bu rol görüntülemeye izin vermez  |
| Azure Kubernetes hizmeti RBAC yazıcı | Bir ad alanındaki nesnelerin çoğuna okuma/yazma erişimi sağlar. Bu rol, rolleri veya rol bağlamalarını görüntülemeye veya değiştirmeye izin vermez. Bununla birlikte, bu rol `Secrets` ad alanındaki herhangi bir ServiceAccount olarak, bu rol, ad alanındaki herhangi bir ServiceAccount 'ın API erişim düzeylerini kazanmak için kullanılabilir. |
| Azure Kubernetes hizmeti RBAC Yöneticisi  | Yönetici erişimine izin verir, bir ad alanı içinde verilmek üzere tasarlanmıştır. Ad alanı içinde roller ve rol bağlamaları oluşturma özelliği de dahil olmak üzere bir ad alanındaki (veya küme kapsamındaki) kaynakların çoğuna okuma/yazma erişimi sağlar. Bu rol, kaynak kotasına veya ad alanının kendine yazma erişimine izin vermez. |
| Azure Kubernetes hizmeti RBAC kümesi Yöneticisi  | Süper Kullanıcı erişiminin herhangi bir kaynak üzerinde herhangi bir işlem gerçekleştirmesine izin verir. Kümedeki her kaynak ve tüm ad alanlarında tam denetim sağlar. |


**Tüm AKS kümesi** kapsamındaki rol atamaları, Azure Portal küme kaynağının Access Control (IAM) dikey penceresinde ya da aşağıda gösterildiği gıbı Azure CLI komutları kullanılarak yapılabilir:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

`<AAD-ENTITY-ID>`bir Kullanıcı adı (örneğin, user@contoso.com ) veya hizmet sorumlusu Için de ClientID olabilir.

Küme içindeki belirli bir **ad alanı** için kapsamlı rol atamaları da oluşturabilirsiniz:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Bugün, ad alanları kapsamındaki rol atamalarının Azure CLı aracılığıyla yapılandırılması gerekir.


### <a name="create-custom-roles-definitions"></a>Özel rol tanımları oluşturma

İsteğe bağlı olarak kendi rol tanımınızı oluşturup daha sonra atayabilirsiniz.

Aşağıda, bir kullanıcının yalnızca dağıtımları okumasına ve başka hiçbir şey yapmasına izin veren bir rol tanımı örneği verilmiştir. Olası eylemlerin tam listesini [burada](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)görebilirsiniz.


Aşağıdaki JSON öğesini adlı bir dosyaya kopyalayın `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

`<YOUR SUBSCRIPTION ID>`ABONELIĞINIZDEKI kimliğe göre değiştirin ve aşağıdakileri çalıştırarak alabilirsiniz:

```azurecli-interactive
az account show --query id -o tsv
```


Şimdi, kaydettiğiniz klasörden aşağıdaki komutu çalıştırarak rol tanımını oluşturuyoruz `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Rol tanımınıza sahip olduğunuza göre, şunu çalıştırarak bir kullanıcıya veya başka bir kimliğe atayabilirsiniz:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>İle Kubernetes yetkilendirmesi için Azure RBAC kullanma `kubectl`

> [!NOTE]
> Aşağıdaki komutu çalıştırarak en son kubectl 'ye sahip olduğunuzdan emin olun:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Ayrıcalıklarla çalıştırmanız gerekebilir `sudo` . 

Artık istediğiniz rolü ve izinleri atadığınıza göre. Kubernetes API 'sini çağırmaya başlayabilirsiniz, örneğin, `kubectl` .

Bu amaçla, ilk olarak aşağıdaki komutu kullanarak kümenin kubeconfig 'i alalım:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Yukarıdaki adımı gerçekleştirmek için [Azure Kubernetes hizmet kümesi Kullanıcı](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) yerleşik rolüne sahip olmanız gerekir.

Şimdi, kubectl 'yi, örneğin, kümedeki düğümleri listelemek için kullanabilirsiniz. İlk kez çalıştırdığınızda, oturum açmanız gerekir ve sonraki komutlar ilgili erişim belirtecini kullanır.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>İle Kubernetes yetkilendirmesi için Azure RBAC kullanma `kubelogin`

Etkileşimli olmayan oturum açma işlemleri, eski `kubectl` sürümler veya birden çok küme genelınde SSO 'yu kullanarak, yeni kümede oturum açmaya gerek kalmadan, belirteçlerinizin hala geçerli olduğundan, AKS 'nin adlı bir exec eklentisi oluşturduğuna izin veren ek senaryoların engellemesini kaldırmak için [`kubelogin`](https://github.com/Azure/kubelogin) .

Şunu çalıştırarak kullanabilirsiniz:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

İlk kez normal kubectl ile etkileşimli olarak oturum açmanız gerekir, ancak bundan sonra yeni Azure AD kümeleri için bile ihtiyacınız olmayacaktır (belirteciniz hala geçerli olduğu sürece).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Temizleme

### <a name="clean-role-assignment"></a>Rol atamasını temizle

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
KIMLIĞI veya kimlikleri, yaptığınız tüm atamalardan ve sonra kopyalayın.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Rol tanımını temizle

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Küme ve kaynak grubunu sil

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

- AKS kimlik doğrulaması, yetkilendirme, Kubernetes RBAC ve Azure RBAC hakkında daha fazla bilgi [edinin.](concepts-identity.md)
- Azure [RBAC hakkında](../role-based-access-control/overview.md)daha fazla bilgi edinin.
- [Burada](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)Kubernetes yetkilendirmesi Için özel Azure rolleri tanımlamak üzere kullanabileceğiniz tüm eylemler hakkında daha fazla bilgi edinin.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register

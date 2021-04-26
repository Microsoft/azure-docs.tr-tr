---
title: Azure Kubernetes hizmetinde Azure AD kullanma
description: Azure Kubernetes hizmetinde (AKS) Azure AD 'yi nasıl kullanacağınızı öğrenin
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 3db9f8d895b4c13b5f969859f422e7b566722ffc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783080"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS tarafından yönetilen Azure Active Directory tümleştirme

AKS tarafından yönetilen Azure AD tümleştirmesi, kullanıcıların daha önce bir istemci uygulaması, bir sunucu uygulaması oluşturmak ve dizin okuma izinleri vermek için Azure AD kiracısının gerekli olduğu Azure AD tümleştirme deneyimini basitleştirmek üzere tasarlanmıştır. Yeni sürümde, AKS kaynak sağlayıcısı, istemci ve sunucu uygulamalarını sizin için yönetir.

## <a name="azure-ad-authentication-overview"></a>Azure AD kimlik doğrulamasına genel bakış

Küme yöneticileri, Kubernetes rol tabanlı erişim denetimini (Kubernetes RBAC) bir kullanıcının kimliğine veya dizin grubu üyeliğine göre yapılandırabilir. Azure AD kimlik doğrulaması, OpenID Connect ile AKS kümelerine sağlanır. OpenID Connect, OAuth 2,0 protokolünün üstünde oluşturulmuş bir kimlik katmanıdır. OpenID Connect hakkında daha fazla bilgi için bkz. [Açık kimlik bağlantısı belgeleri][open-id-connect].

[Azure Active Directory tümleştirme kavramları belgelerindeki](concepts-identity.md#azure-ad-integration)Azure AD tümleştirme akışı hakkında daha fazla bilgi edinin.

## <a name="limitations"></a>Sınırlamalar 

* AKS tarafından yönetilen Azure AD tümleştirmesi devre dışı bırakılamaz
* AKS tarafından yönetilen bir Azure AD Tümleşik kümesini eski AAD ile değiştirme desteklenmiyor
* AKS tarafından yönetilen Azure AD tümleştirmesi için Kubernetes tabanlı olmayan kümeler desteklenmez
* AKS tarafından yönetilen Azure AD tümleştirmesi ile ilişkili Azure AD kiracısı 'nin değiştirilmesi desteklenmiyor

## <a name="prerequisites"></a>Önkoşullar

* Azure CLı sürüm 2.11.0 veya üzeri
* En düşük [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) veya [kubelogin](https://github.com/Azure/kubelogin) sürümü ile kubectl
* Held, en [](https://github.com/helm/helm)düşük held 3,3 sürümünü kullanıyorsanız.

> [!Important]
> Kubectl 'yi minimum 1.18.1 veya kubelogin sürümü ile kullanmanız gerekir. Kubernetes ve kubectl 'nin ikincil sürümleri arasındaki fark 1 ' den büyük olmalıdır. Doğru sürümü kullanmıyorsanız, kimlik doğrulama sorunları olduğunu fark edeceksiniz.

Kubectl ve kubelogin yüklemek için aşağıdaki komutları kullanın:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Diğer işletim sistemleri için [Bu yönergeleri](https://kubernetes.io/docs/tasks/tools/install-kubectl/) kullanın.

## <a name="before-you-begin"></a>Başlamadan önce

Kümeniz için bir Azure AD grubuna ihtiyacınız vardır. Bu grup, kümenin yönetici izinleri vermesi için yönetici grubu olarak gereklidir. Mevcut bir Azure AD grubunu kullanabilir veya yeni bir tane oluşturabilirsiniz. Azure AD grubunuzun nesne KIMLIĞINI kaydedin.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Küme yöneticileriniz için yeni bir Azure AD grubu oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD özellikli bir AKS kümesi oluşturma

Aşağıdaki CLı komutlarını kullanarak bir AKS kümesi oluşturun.

Azure Kaynak grubu oluşturun:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

AKS kümesi oluşturma ve Azure AD grubunuz için yönetim erişimini etkinleştirme

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

AKS tarafından yönetilen bir Azure AD kümesinin başarılı bir şekilde oluşturulması yanıt gövdesinde aşağıdaki bölüme sahiptir
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Küme oluşturulduktan sonra erişime başlayabilirsiniz.

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD özellikli bir kümeye erişme

Aşağıdaki adımları uygulamak için [Azure Kubernetes hizmet kümesi Kullanıcı](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) yerleşik rolüne sahip olmanız gerekir.

Kümeye erişmek için Kullanıcı kimlik bilgilerini alın:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Oturum açmak için yönergeleri izleyin.

Kümedeki düğümleri görüntülemek için kubectl Get Nodes komutunu kullanın:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Kümeleriniz için ek güvenlik grupları yapılandırmak üzere [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](./azure-ad-rbac.md) yapılandırın.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Azure AD ile erişim sorunlarını giderme

> [!Important]
> Aşağıda açıklanan adımlar, normal Azure AD grubu kimlik doğrulamasını atlama. Onları yalnızca bir acil durum içinde kullanın.

Kümenize erişimi olan geçerli bir Azure AD grubuna erişim olmadan kalıcı olarak engelleniyorsa, doğrudan kümeye erişmek için yönetici kimlik bilgilerini almaya devam edebilirsiniz.

Bu adımları uygulamak için [Azure Kubernetes hizmet kümesi Yöneticisi](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) yerleşik rolüne erişiminizin olması gerekir.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Mevcut kümenizde AKS tarafından yönetilen Azure AD tümleştirmesini etkinleştirme

Mevcut Kubernetes RBAC etkin kümenizde AKS tarafından yönetilen Azure AD tümleştirmesini etkinleştirebilirsiniz. Kümenizde erişim sağlamak için yönetici grubunuzu ayarlamayı doğrulayın.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

AKS tarafından yönetilen bir Azure AD kümesinin başarılı bir şekilde etkinleştirilmesi yanıt gövdesinde aşağıdaki bölüme sahiptir

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

[Buradaki][access-cluster]adımları izleyerek kümenize erişmek için Kullanıcı kimlik bilgilerini yeniden indirin.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>AKS tarafından yönetilen Azure AD tümleştirmesine yükseltme

Kümeniz eski Azure AD tümleştirmesini kullanıyorsa, AKS tarafından yönetilen Azure AD tümleştirmesine yükseltebilirsiniz.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

AKS tarafından yönetilen bir Azure AD kümesinin başarılı bir geçişi yanıt gövdesinde aşağıdaki bölüme sahiptir

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Kümeye erişmek istiyorsanız [buradaki][access-cluster]adımları izleyin.

## <a name="non-interactive-sign-in-with-kubelogin"></a>Kubelogin ile etkileşimli olmayan oturum açma

Şu anda kubectl ile kullanılamayan sürekli tümleştirme işlem hatları gibi etkileşimli olmayan bazı senaryolar vardır. [`kubelogin`](https://github.com/Azure/kubelogin)Etkileşimli olmayan hizmet sorumlusu oturum açma ile kümeye erişmek için kullanabilirsiniz.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Azure AD ve AKS ile koşullu erişim kullanma

Azure AD 'yi AKS kümeniz ile tümleştirdiğinizde, kümenize erişimi denetlemek için [koşullu erişimi][aad-conditional-access] de kullanabilirsiniz.

> [!NOTE]
> Azure AD koşullu erişim Azure AD Premium bir yetenektir.

AKS ile kullanılacak örnek bir koşullu erişim ilkesi oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal en üstünde Azure Active Directory ' i arayıp seçin.
1. Sol taraftaki Azure Active Directory menüsünde *Kurumsal uygulamalar*' ı seçin.
1. Sol taraftaki kurumsal uygulamalar menüsünde *koşullu erişim*' i seçin.
1. Sol taraftaki koşullu erişim menüsünde *ilkeler* ' i ve ardından *Yeni ilke*' yi seçin.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Koşullu erişim ilkesi ekleme":::
1. İlke için *aks-Policy* gibi bir ad girin.
1. *Kullanıcılar ve gruplar*' ı seçin ve ardından *Ekle* ' nin altında *Kullanıcı ve Grup Seç*' i İlkeyi uygulamak istediğiniz kullanıcıları ve grupları seçin. Bu örnekte, kümenize yönetici erişimi olan aynı Azure AD grubunu seçin.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Koşullu erişim ilkesini uygulamak için kullanıcıları veya grupları seçme":::
1. *Bulut uygulamaları veya eylemler*' i seçin, sonra da *Ekle* ' nin altında *uygulamaları seçin*' *Azure Kubernetes hizmetini* arayın ve *Azure KUBERNETES hizmeti AAD sunucusu*' nu seçin.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Koşullu erişim ilkesini uygulamak için Azure Kubernetes hizmet AD sunucusu seçiliyor":::
1. *Erişim denetimleri* altında *Ver*’i seçin. *Erişim ver* ' i seçin ve *cihazın uyumlu olarak işaretlenmesini gerektir*' i seçin.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Koşullu erişim ilkesi için yalnızca uyumlu cihazlara izin vermeyi seçme":::
1. *Ilkeyi etkinleştir* altında, sonra da *Oluştur*' *u seçin.*
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Koşullu erişim ilkesini etkinleştirme":::

Kümeye erişmek için Kullanıcı kimlik bilgilerini alın, örneğin:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Oturum açmak için yönergeleri izleyin.

`kubectl get nodes`Kümedeki düğümleri görüntülemek için komutunu kullanın:

```azurecli-interactive
kubectl get nodes
```

Yeniden oturum açmak için yönergeleri izleyin. Başarılı bir şekilde oturum açtığınızı belirten bir hata iletisi olduğuna dikkat edin, ancak yöneticinizin kaynağa erişmek için Azure AD 'niz tarafından yönetilmek üzere cihazın erişim isteğinde bulunmasını istiyor.

Azure portal Azure Active Directory gidin, *Kurumsal uygulamalar* ' ı seçin, ardından *etkinlik* ' in altında *oturum açma* işlemleri ' ni seçin. En üstte, *durumu* *başarısız* ve *koşullu erişim* olan bir *girdi olduğunu fark edin.* Girdiyi seçin ve ardından ayrıntılı *koşullu erişim* ' i seçin. Koşullu erişim ilkenizin listelendiğine dikkat edin.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Koşullu erişim ilkesi nedeniyle oturum açma girişi başarısız oldu":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Azure AD ve AKS ile tam zamanında küme erişimi yapılandırma

Küme erişim denetimi için başka bir seçenek de tam zamanında istekler için Privileged Identity Management (PıM) kullanmaktır.

>[!NOTE]
> PıM, Premium P2 SKU 'SU gerektiren Azure AD Premium bir özelliktir. Azure AD SKU 'Ları hakkında daha fazla bilgi için bkz. [fiyatlandırma Kılavuzu][aad-pricing].

AKS tarafından yönetilen Azure AD tümleştirmesini kullanarak bir AKS kümesiyle tam zamanında erişim isteklerini tümleştirmek için aşağıdaki adımları izleyin:

1. Azure portal en üstünde Azure Active Directory ' i arayıp seçin.
1. Bir Web tarayıcısında olduğu gibi bu yönergelerin geri kalanı için başvurulan kiracı KIMLIĞINI, `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="Azure Active Directory için Azure Portal ekranının, kiracının kimliği vurgulanmış olarak gösterildiğini.":::
1. Sol taraftaki Azure Active Directory menüsünde, *Yönet* *grupları* ' nın altında, *Yeni Grup*' u seçin.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="' Yeni Grup ' seçeneğinin vurgulandığı Azure portal Active Directory grupları ekranını gösterir.":::
1. Bir grup *güvenlik* türünün seçili olduğundan emin olun ve bir grup adı girin (örneğin, *myjgroup*). *Azure AD rolleri altında bu gruba (Önizleme) atanabilir*, *Evet*' i seçin. Son olarak *Oluştur*' u seçin.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Azure portal yeni Grup oluşturma ekranını gösterir.":::
1. *Gruplar* sayfasına geri getirilecektir. Yeni oluşturduğunuz grubunuzu seçin ve bu yönergelerin geri kalanı için olarak adlandırılan nesne KIMLIĞINI aklınızda yapın `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Yeni oluşturulan grubun Azure portal ekranını gösterir, nesne kimliğini vurgular":::
1. `<tenant-id>`Daha önce ve değerlerini kullanarak aks tarafından yönetilen Azure AD tümleştirmesiyle BIR aks kümesi dağıtın `<object-id>` :
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Azure portal geri döndüğünüzde, sol taraftaki *etkinlik* menüsünde *ayrıcalıklı erişim (Önizleme)* öğesini seçin ve *ayrıcalıklı erişimi etkinleştir*' i seçin.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Azure portal ayrıcalıklı erişim (Önizleme) sayfası, ' ayrıcalıklı erişimi etkinleştir ' vurgulanmış şekilde gösteriliyor":::
1. Erişim vermeye başlamak için *atama Ekle* ' yi seçin.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Azure portal ayrıcalıklı erişim (Önizleme) ekranı etkinleştirildikten sonra görüntülenir. ' Atama Ekle ' seçeneği vurgulanır.":::
1. *Üyenin* bir rolünü seçin ve küme erişimi vermek istediğiniz kullanıcıları ve grupları seçin. Bu atamalar, bir grup yöneticisi tarafından herhangi bir zamanda değiştirilebilir. ' I taşımaya hazırsanız *İleri*' yi seçin.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Azure portal atamaları Ekle üyelik ekranı, bir üye olarak eklenmek üzere bir örnek Kullanıcı seçiliyken görüntülenir. ' Ileri ' seçeneği vurgulanır.":::
1. *Etkin*, istenen süre ve bir atama türü seçin ve bir gerekçe belirtin. Devam etmeye hazırsanız *ata*' yı seçin. Atama türleri hakkında daha fazla bilgi için bkz. [Privileged Identity Management bir ayrıcalıklı erişim grubu (Önizleme) için uygunluk atama][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Azure portal atamaları ekleme ayarı ekranı görüntülenir. ' Active ' atama türü seçildi ve örnek bir gerekçe verildi. ' Assign ' seçeneği vurgulanır.":::

Atamalar yapıldıktan sonra, kümeye erişerek tam zamanında erişimin çalıştığını doğrulayın. Örnek:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Oturum açmak için adımları izleyin.

`kubectl get nodes`Kümedeki düğümleri görüntülemek için komutunu kullanın:

```azurecli-interactive
kubectl get nodes
```

Kimlik doğrulama gereksinimini aklınızda ve kimlik doğrulaması için adımları izleyin. Başarılı olursa aşağıdakine benzer bir çıktı görmeniz gerekir:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Sorun giderme

`kubectl get nodes`Şuna benzer bir hata döndürürse:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Güvenlik grubunun yöneticisinin hesabınıza *etkin* bir atama vermiş olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

* [Kubernetes yetkilendirmesi Için Azure RBAC tümleştirmesi][azure-rbac-integration] hakkında bilgi edinin
* [Kubernetes RBAC Ile Azure AD tümleştirmesi][azure-ad-rbac]hakkında bilgi edinin.
* Kubectl 'de kullanılamayan Azure kimlik doğrulaması özelliklerine erişmek için [kubelogin](https://github.com/Azure/kubelogin) kullanın.
* [Aks ve Kubernetes kimlik kavramları][aks-concepts-identity]hakkında daha fazla bilgi edinin.
* AKS tarafından yönetilen Azure AD etkinleştirilmiş kümeler oluşturmak için [Azure Resource Manager (ARM) şablonları ][aks-arm-template] kullanın.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group

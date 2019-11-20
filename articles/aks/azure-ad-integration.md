---
title: Azure Active Directory Azure Kubernetes hizmeti ile tümleştirme
description: Azure Active Directory etkin Azure Kubernetes hizmeti (AKS) kümeleri oluşturma
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 26f1544cab5cf5be2edd52f97c758d46eb835514
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103782"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Active Directory Azure Kubernetes hizmeti ile tümleştirme

Azure Kubernetes hizmeti (AKS), Kullanıcı kimlik doğrulaması için Azure Active Directory (Azure AD) kullanacak şekilde yapılandırılabilir. Bu yapılandırmada, Azure AD kimlik doğrulama belirtecinizi kullanarak bir AKS kümesinde oturum açabilirsiniz.

Küme yöneticileri, Kubernetes rol tabanlı erişim denetimini (RBAC) bir kullanıcının kimliğine veya dizin grubu üyeliğine göre yapılandırabilir.

Bu makalede nasıl yapılacağı açıklanmaktadır:

- AKS ve Azure AD için önkoşulları dağıtın.
- Azure AD özellikli bir küme dağıtın.
- Azure portal kullanarak AKS kümesinde temel bir RBAC rolü oluşturun.

Ayrıca, [Azure CLI][azure-ad-cli]kullanarak bu adımları tamamlayabilirsiniz.

> [!NOTE]
> Azure AD, yalnızca yeni bir RBAC etkin küme oluşturduğunuzda etkinleştirilebilir. Mevcut bir AKS kümesinde Azure AD 'yi etkinleştiremezsiniz.

## <a name="authentication-details"></a>Kimlik doğrulaması ayrıntıları

Azure AD kimlik doğrulaması, OpenID Connect içeren AKS kümelerine sağlanır. OpenID Connect, OAuth 2,0 protokolünün üstünde oluşturulmuş bir kimlik katmanıdır.

OpenID Connect hakkında daha fazla bilgi için bkz. [OpenID Connect ve Azure ad kullanarak Web uygulamalarına erişim yetkisi verme][open-id-connect].

Bir Kubernetes kümesi içinde, Web kancası belirteci kimlik doğrulaması, kimlik doğrulama belirteçleri için kullanılır. Web kancası belirteci kimlik doğrulaması, AKS kümesinin bir parçası olarak yapılandırılır ve yönetilir.

Web kancası belirteci kimlik doğrulaması hakkında daha fazla bilgi için, Kubernetes belgelerindeki [Web kancası belirteci kimlik doğrulaması][kubernetes-webhook] bölümüne bakın.

Bir AKS kümesi için Azure AD kimlik doğrulaması sağlamak üzere iki Azure AD uygulaması oluşturulur. İlk uygulama, Kullanıcı kimlik doğrulaması sağlayan bir sunucu bileşenidir. İkinci uygulama, kimlik doğrulaması için CLı tarafından istendiğinde kullanılan bir istemci bileşenidir. Bu istemci uygulaması, istemci tarafından belirtilen kimlik bilgilerinin gerçek kimlik doğrulaması için sunucu uygulamasını kullanır.

> [!NOTE]
> Azure AD 'yi AKS kimlik doğrulaması için yapılandırdığınızda, iki Azure AD uygulaması yapılandırılır. Her uygulama için izin verme adımları bir Azure kiracı yöneticisi tarafından tamamlanmalıdır.

## <a name="create-the-server-application"></a>Sunucu uygulaması oluşturma

İlk Azure AD uygulaması, bir kullanıcının Azure AD grubu üyeliğini almak için uygulanır. Bu uygulamayı Azure portal oluşturmak için:

1. **Yeni kayıt** **uygulama kayıtları** > AzureActiveDirectory > seçin.

    a. Uygulamaya *AKSAzureADServer*gibi bir ad verin.

    b. **Desteklenen hesap türleri**için **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
    
    c. Yeniden yönlendirme URI 'SI türü için **Web** ' i seçin ve ardından, *https://aksazureadserver* URI biçimli herhangi bir değer (gibi) girin.

    d. İşiniz bittiğinde **Kaydet** ' i seçin.

2. **Bildirim**' ı seçin ve ardından **groupmembershipclaim:** değerini **All**olarak düzenleyin. Güncelleştirmeler ile işiniz bittiğinde **Kaydet**' i seçin.

    ![Grup üyeliğini tümüne Güncelleştir](media/aad-integration/edit-manifest.png)

3. Azure AD uygulamasının sol bölmesinde, **sertifikalar & gizlilikler**' ı seçin.

    a. **+ Yeni istemci parolası**' nı seçin.

    b. *Aks Azure AD Server*gibi bir anahtar açıklaması ekleyin. Bir sona erme saati seçin ve ardından **Ekle**' yi seçin.

    c. Yalnızca şu anda gösterilecek olan anahtar değerini aklınızda edin. Azure AD özellikli bir AKS kümesi dağıttığınızda, bu değere sunucu uygulama gizli dizisi denir.

4. Azure AD uygulamasının sol bölmesinde, **API izinleri**' ni seçin ve **+ izin Ekle**' yi seçin.

    a. **Microsoft API 'leri**altında **Microsoft Graph**' yi seçin.

    b. **Temsilci izinleri**' ni seçin ve ardından Dizin > Dizin ' in yanındaki onay kutusunu Işaretleyin. **tümünü oku (dizin verilerini oku)** .

    c. Kullanıcı > Kullanıcı için varsayılan bir temsilci izni varsa **. okuma (oturum açma ve kullanıcı profilini okuma)** yoksa, yanındaki onay kutusunu işaretleyin.

    d. **Uygulama izinleri**' ni seçin ve ardından Dizin > Dizin ' in yanındaki onay kutusunu Işaretleyin. **tümünü oku (dizin verilerini oku)** .

    ![Grafik izinlerini ayarla](media/aad-integration/graph-permissions.png)

    e. Güncelleştirmeleri kaydetmek için **Izinleri Ekle** ' yi seçin.

    f. **Izin ver**' in altında, **yönetici izni ver**' i seçin. Bu düğme kullanılamayacak geçerli hesap, kiracı yöneticisi olarak listelenmez.

    İzinler başarıyla verildiğinde, portalda aşağıdaki bildirim görüntülenir:

   ![Başarılı izin verilen bildirim](media/aad-integration/permissions-granted.png)

5. Azure AD uygulamasının sol bölmesinde, **BIR API 'Yi kullanıma**sunma ' yı seçin ve **+ Kapsam Ekle**' yi seçin.
    
    a. **Kapsam adı**, **yönetici onayı görünen adı**ve *AKSAzureADServer*gibi bir **Yönetici onay açıklaması** girin.

    b. **Durumun** **etkin**olarak ayarlandığından emin olun.

    ![Sunucu uygulamasını diğer hizmetlerle kullanılmak üzere API olarak kullanıma sunma](media/aad-integration/expose-api.png)

    c. **Kapsam Ekle**' yi seçin.

6. Uygulamaya **genel bakış** sayfasına dönün ve **uygulamanın (istemci) kimliğini**aklınızda edin. Azure AD özellikli bir AKS kümesi dağıttığınızda, bu değere sunucu uygulama KIMLIĞI denir.

    ![Uygulama KIMLIĞINI al](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>İstemci uygulaması oluşturma

İkinci Azure AD uygulaması, Kubernetes CLı (kubectl) ile oturum açtığınızda kullanılır.

1. **Yeni kayıt** **uygulama kayıtları** > AzureActiveDirectory > seçin.

    a. Uygulamaya *AKSAzureADClient*gibi bir ad verin.

    b. **Desteklenen hesap türleri**için **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.

    c. Yeniden yönlendirme URI 'SI türü için **Web** ' i seçin ve ardından gibi *https://aksazureadclient* URI biçimli herhangi bir değer girin.

    d. İşiniz bittiğinde **Kaydet** ' i seçin.

2. Azure AD uygulamasının sol bölmesinde, **API izinleri**' ni seçin ve **+ izin Ekle**' yi seçin.

    a. **API 'Lerim**' i seçin ve ardından önceki adımda oluşturulan Azure ad sunucu uygulamanızı *AKSAzureADServer*gibi seçin.

    b. **Temsilci izinleri**' ni seçin ve ardından Azure AD Server uygulamanızın yanındaki onay kutusunu işaretleyin.

    ![Uygulama izinlerini yapılandırma](media/aad-integration/select-api.png)

    c. **Izin Ekle**' yi seçin.

    d. **Izin ver**' in altında, **yönetici izni ver**' i seçin. Geçerli hesap kiracı yöneticisi değilse bu düğme kullanılamaz. İzinler verildiğinde, portalda aşağıdaki bildirim görüntülenir:

    ![Başarılı izin verilen bildirim](media/aad-integration/permissions-granted.png)

3. Azure AD uygulamasının sol bölmesinde **kimlik doğrulaması**' nı seçin.

    - **Varsayılan istemci türü**altında, **istemciyi ortak istemci olarak değerlendirmek**için **Evet** ' i seçin.

5. Azure AD uygulamasının sol bölmesinde uygulama KIMLIĞI ' ni aklınızda edin. Azure AD özellikli bir AKS kümesi dağıttığınızda, bu değere istemci uygulama KIMLIĞI denir.

   ![Uygulama KIMLIĞINI al](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Kiracı KIMLIĞINI al

Ardından, Azure kiracınızın KIMLIĞINI alın. Bu değer, AKS kümesini oluştururken kullanılır.

Azure Portal, **Azure Active Directory** > **Özellikler** ' i seçin ve **dizin kimliğini**aklınızda yapın. Azure AD özellikli bir AKS kümesi oluşturduğunuzda, bu değere kiracı KIMLIĞI denir.

![Azure kiracı KIMLIĞI 'ni al](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>AKS kümesini dağıtma

AKS kümesi için bir kaynak grubu oluşturmak için [az Group Create][az-group-create] komutunu kullanın.

```azurecli
az group create --name myResourceGroup --location eastus
```

AKS kümesini dağıtmak için [az aks Create][az-aks-create] komutunu kullanın. Sonra, aşağıdaki örnek komutundaki değerleri değiştirin. Sunucu uygulama KIMLIĞI, uygulama gizli anahtarı, istemci uygulama KIMLIĞI ve kiracı KIMLIĞI için Azure AD uygulamalarını oluştururken toplanan değerleri kullanın.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

AKS kümesinin oluşturulması birkaç dakika sürer.

## <a name="create-an-rbac-binding"></a>RBAC bağlaması oluşturma

> [!NOTE]
> Küme rolü bağlama adı büyük/küçük harfe duyarlıdır.

AKS kümesiyle bir Azure Active Directory hesabı kullanmadan önce, rol bağlama veya küme rolü bağlama oluşturmanız gerekir. Roller, verilecek izinleri tanımlar ve bağlamalar onları istenen kullanıcılara uygular. Bu atamalar, belirli bir ad alanına veya tüm küme genelinde uygulanabilir. Daha fazla bilgi için bkz. [RBAC yetkilendirmesi kullanma][rbac-authorization].

İlk olarak, yönetici erişimiyle kümede oturum açmak için, `--admin` bağımsız değişkeniyle [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanın.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ardından, AKS kümesine erişim vermek istediğiniz bir Azure AD hesabı için ClusterRoleBinding oluşturun. Aşağıdaki örnek, hesaba kümedeki tüm ad alanları için tam erişim sağlar:

- RBAC bağlamasını verdiğiniz kullanıcı aynı Azure AD kiracısında ise, Kullanıcı asıl adına (UPN) göre izinler atayın. ClusterRoleBinding için YAML bildirimini oluşturmak üzere adıma geçin.

- Kullanıcı farklı bir Azure AD kiracısında ise, için sorgulama yapın ve onun yerine **ObjectID** özelliğini kullanın. Gerekirse, [az ad User Show][az-ad-user-show] komutunu kullanarak gerekli Kullanıcı hesabının ObjectID 'sini alın. Gerekli hesabın kullanıcı asıl adını (UPN) belirtin:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

*RBAC-AAD-User. YAML*gibi bir dosya oluşturun ve aşağıdaki içeriği yapıştırın. Son satırda **userPrincipalName_or_objectId** değerini UPN veya nesne kimliğiyle değiştirin. Bu seçenek, kullanıcının aynı Azure AD kiracısı olup olmamasına bağlıdır.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Aşağıdaki örnekte gösterildiği gibi, [kubectl Apply][kubectl-apply] komutunu kullanarak bağlamayı uygulayın:

```console
kubectl apply -f rbac-aad-user.yaml
```

Bir Azure AD grubunun tüm üyeleri için rol bağlama da oluşturulabilir. Azure AD grupları, aşağıdaki örnekte gösterildiği gibi grup nesne KIMLIĞI kullanılarak belirtilir.

*RBAC-AAD-Group. YAML*gibi bir dosya oluşturun ve aşağıdaki içeriği yapıştırın. Grup nesnesi KIMLIĞINI Azure AD kiracınızdan bir tane ile güncelleştirin:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Aşağıdaki örnekte gösterildiği gibi, [kubectl Apply][kubectl-apply] komutunu kullanarak bağlamayı uygulayın:

```console
kubectl apply -f rbac-aad-group.yaml
```

Bir Kubernetes kümesinin RBAC ile güvenliğini sağlama hakkında daha fazla bilgi için bkz. [RBAC yetkilendirmesi kullanma][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Azure AD ile kümeye erişme

[Az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak yönetici olmayan kullanıcı bağlamını çekin.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

`kubectl` Komutu çalıştırdıktan sonra, Azure kullanarak kimlik doğrulaması yapmanız istenir. Aşağıdaki örnekte gösterildiği gibi, işlemi tamamlaması için ekrandaki yönergeleri izleyin:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

İşlem tamamlandığında, kimlik doğrulama belirteci önbelleğe alınır. Yalnızca belirtecin süresi dolarsa veya Kubernetes yapılandırma dosyası yeniden oluşturulduğunda yeniden oturum açmanız istenir.

Başarıyla oturum açtıktan sonra bir yetkilendirme hata iletisi görürseniz, aşağıdaki ölçütleri kontrol edin:

```console
error: You must be logged in to the server (Unauthorized)
```


- Kullanıcı hesabının aynı Azure AD kiracısında olup olmadığı ile ilgili olarak uygun nesne KIMLIĞINI veya UPN 'yi tanımlamış olursunuz.
- Kullanıcı 200 taneden fazla grubun üyesi değil.
- Sunucu için uygulama kaydında tanımlanan gizli dizi kullanılarak `--aad-server-app-secret`yapılandırılan değerle eşleşir.

## <a name="next-steps"></a>Sonraki adımlar

Küme kaynaklarına erişimi denetlemek için Azure AD kullanıcıları ve grupları 'nı kullanmak için bkz. [AKS 'de rol tabanlı erişim denetimi ve Azure AD kimlikleri kullanarak küme kaynaklarına erişimi denetleme][azure-ad-rbac].

Kubernetes kümelerinin güvenliğini sağlama hakkında daha fazla bilgi için bkz. [AKS Için erişim ve kimlik seçenekleri][rbac-authorization].

Kimlik ve kaynak denetimi hakkında daha fazla bilgi için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

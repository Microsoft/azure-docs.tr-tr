---
title: Azure Active Directory Azure Kubernetes hizmeti ile tümleştirme
description: Azure CLı 'yı kullanarak Azure Kubernetes Service (AKS) kümesi oluşturma ve Azure Active Directory etkin yapma hakkında bilgi edinin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: 5b99d76ef20c288d6ae0bd33e1e2b6a75a359d3a
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616284"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Azure CLı kullanarak Azure Kubernetes hizmeti ile Azure Active Directory tümleştirme

Azure Kubernetes hizmeti (AKS), Kullanıcı kimlik doğrulaması için Azure Active Directory (AD) kullanacak şekilde yapılandırılabilir. Bu yapılandırmada, bir Azure AD kimlik doğrulama belirteci kullanarak bir AKS kümesinde oturum açabilirsiniz. Küme işleçleri Ayrıca, bir kullanıcının kimliğine veya dizin grubu üyeliğine bağlı olarak Kubernetes rol tabanlı erişim denetimi 'ni (RBAC) yapılandırabilir.

Bu makalede, gerekli Azure AD bileşenlerini oluşturma, ardından Azure AD özellikli bir kümeyi dağıtma ve AKS kümesinde temel RBAC rolü oluşturma işlemlerinin nasıl yapılacağı gösterilir. Ayrıca [, Azure Portal kullanarak bu adımları tamamlayabilirsiniz][azure-ad-portal].

Bu makalede kullanılan tam örnek betik için bkz. Azure [CLI örnekleri-Azure AD Ile AKS tümleştirmesi][complete-script].

Aşağıdaki sınırlamalar geçerlidir:

- Azure AD yalnızca yeni, RBAC özellikli bir küme oluşturduğunuzda etkinleştirilebilir. Mevcut bir AKS kümesinde Azure AD 'yi etkinleştiremezsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.0.61 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][install-azure-cli].

Tutarlılık için ve bu makaledeki komutları çalıştırmaya yardımcı olmak için, istediğiniz AKS kümesi adı için bir değişken oluşturun. Aşağıdaki örnek *myakscluster*adını kullanır:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD kimlik doğrulamasına genel bakış

Azure AD kimlik doğrulaması, OpenID Connect ile AKS kümelerine sağlanır. OpenID Connect, OAuth 2,0 protokolünün üstünde oluşturulmuş bir kimlik katmanıdır. OpenID Connect hakkında daha fazla bilgi için bkz. [Açık kimlik bağlantısı belgeleri][open-id-connect].

Kubernetes kümesinin içinden, kimlik doğrulama belirteçlerini doğrulamak için Web kancası belirteci kimlik doğrulaması kullanılır. Web kancası belirteci kimlik doğrulaması, AKS kümesinin bir parçası olarak yapılandırılır ve yönetilir. Web kancası belirteci kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Web kancası kimlik doğrulaması belgeleri][kubernetes-webhook].

> [!NOTE]
> Azure AD 'yi AKS kimlik doğrulaması için yapılandırırken iki Azure AD uygulaması yapılandırılır. Bu işlemin bir Azure kiracı yöneticisi tarafından tamamlanması gerekir.

## <a name="create-azure-ad-server-component"></a>Azure AD Server bileşeni oluşturma

AKS ile tümleştirme için, kimlik istekleri için uç nokta görevi gören bir Azure AD uygulaması oluşturup kullanırsınız. İhtiyacınız olan ilk Azure AD uygulaması, bir kullanıcı için Azure AD grup üyeliğini alır.

[Az ad App Create][az-ad-app-create] komutunu kullanarak sunucu uygulama bileşenini oluşturun, sonra [az ad App Update][az-ad-app-update] komutunu kullanarak Grup üyeliği taleplerini güncelleştirin. Aşağıdaki örnek, [başlamadan önce](#before-you-begin) bölümünde tanımlanan *aksname* değişkenini kullanır ve bir değişken oluşturur

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Şimdi [az ad SP Create][az-ad-sp-create] komutunu kullanarak sunucu uygulaması için bir hizmet sorumlusu oluşturun. Bu hizmet sorumlusu, Azure platformunda kimliğini doğrulamak için kullanılır. Ardından, [az ad SP kimlik bilgisi sıfırlama][az-ad-sp-credential-reset] komutunu kullanarak hizmet sorumlusu gizliliğini alın ve aşağıdaki adımlardan birinde kullanmak üzere *serverapplicationsecret* adlı değişkene atayın:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Aşağıdaki eylemleri gerçekleştirmek için Azure AD 'nin izinleri gerekir:

* Dizin verilerini okuma
* Oturum açma ve kullanıcı profilini okuma

[Az ad App Permission Add][az-ad-app-permission-add] komutunu kullanarak bu izinleri atayın:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Son olarak, [az ad uygulama izni verme][az-ad-app-permission-grant] komutunu kullanarak sunucu uygulaması için önceki adımda atanan izinleri verin. Geçerli hesap bir kiracı yöneticisi değilse bu adım başarısız olur. Alternatif olarak, [az ad uygulama izin yöneticisi-onayı][az-ad-app-permission-admin-consent]kullanılarak yönetici onayı gerektirmeyen bilgiler Istemek IÇIN Azure AD uygulaması için izinler eklemeniz gerekir:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD istemci bileşeni oluşturma

İkinci Azure AD uygulaması, bir Kullanıcı AKS kümesine Kubernetes CLı (`kubectl`) ile oturum açtığında kullanılır. Bu istemci uygulaması kullanıcıdan kimlik doğrulama isteğini alır ve kimlik bilgilerini ve izinlerini doğrular. [Az ad App Create][az-ad-app-create] komutunu kullanarak istemci bileşeni IÇIN Azure AD uygulamasını oluşturun:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

[Az ad SP Create][az-ad-sp-create] komutunu kullanarak istemci uygulaması için bir hizmet sorumlusu oluşturun:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

[Az ad App Show][az-ad-app-show] komutunu kullanarak iki uygulama bileşeni arasında kimlik doğrulama akışına izin vermek üzere sunucu UYGULAMASıNıN oAuth2 kimliğini alın. Bu oAuth2 KIMLIĞI bir sonraki adımda kullanılır.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

[Az ad App Permission Add][az-ad-app-permission-add] komutunu kullanarak oAuth2 iletişim akışını kullanmak için istemci uygulaması ve sunucu uygulaması bileşenlerine ilişkin izinleri ekleyin. Daha sonra, [az ad uygulama izni verme][az-ad-app-permission-grant] komutunu kullanarak istemci uygulaması için sunucu uygulamasıyla iletişim kurmak için izin verin:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Kümeyi Dağıtma

İki Azure AD uygulaması oluşturulmuş olarak, şimdi AKS kümesinin kendisini oluşturun. İlk olarak, [az Group Create][az-group-create] komutunu kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnek *EastUS* bölgesinde kaynak grubunu oluşturur:

Küme için bir kaynak grubu oluşturun:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

[Az Account Show][az-account-show] komutunu kullanarak Azure ABONELIĞINIZIN Kiracı kimliğini alın. Ardından, [az aks Create][az-aks-create] komutunu kullanarak aks kümesini oluşturun. AKS kümesini oluşturma komutu sunucu ve istemci uygulama kimliklerini, sunucu uygulama hizmeti asıl gizli anahtarını ve kiracı KIMLIĞINIZI sağlar:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Son olarak, [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanarak küme yöneticisi kimlik bilgilerini alın. Aşağıdaki adımlardan birinde, Azure AD kimlik doğrulama akışını eylemde görmek için normal *Kullanıcı* kümesi kimlik bilgilerini alırsınız.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>RBAC bağlaması oluşturma

Bir Azure Active Directory hesabının AKS kümesiyle kullanılabilmesi için önce bir rol bağlama veya küme rolü bağlamasının oluşturulması gerekir. *Roller* , verilecek izinleri tanımlar ve *bağlamalar* onları istenen kullanıcılara uygular. Bu atamalar, belirli bir ad alanına veya tüm küme genelinde uygulanabilir. Daha fazla bilgi için bkz. [RBAC yetkilendirmesi kullanma][rbac-authorization].

[Az ad oturum açan kullanıcı göster][az-ad-signed-in-user-show] komutunu kullanarak şu anda oturum açmış olan kullanıcının Kullanıcı asıl adını (UPN) alın. Bu Kullanıcı hesabı, bir sonraki adımda Azure AD tümleştirmesi için etkinleştirilmiştir.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> RBAC bağlamasını verdiğiniz kullanıcı aynı Azure AD kiracısında ise, *userPrincipalName*öğesine göre izinler atayın. Kullanıcı farklı bir Azure AD kiracısında ise, için sorgulama yapın ve onun yerine *ObjectID* özelliğini kullanın.

Adlı `basic-azure-ad-binding.yaml` bir YAML bildirimi oluşturun ve aşağıdaki içeriği yapıştırın. Son satırda, *userPrincipalName_or_objectId* değerini ÖNCEKI komutun UPN veya nesne kimliği çıkışıyla değiştirin:

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

[Kubectl Apply][kubectl-apply] komutunu kullanarak ClusterRoleBinding oluşturun ve YAML bildiriminizde dosya adını belirtin:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Azure AD ile küme erişimi

Şimdi AKS kümesi için Azure AD kimlik doğrulaması tümleştirmesini test edelim. `kubectl` Yapılandırma bağlamını normal kullanıcı kimlik bilgilerini kullanacak şekilde ayarlayın. Bu bağlam, tüm kimlik doğrulama isteklerini Azure AD aracılığıyla geri geçirir.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Şimdi [kubectl Get Pod][kubectl-get] komutunu kullanarak tüm ad alanları genelinde Pod 'leri görüntüleyin:

```console
kubectl get pods --all-namespaces
```

Bir Web tarayıcısı kullanarak Azure AD kimlik bilgilerini kullanarak kimlik doğrulaması gerçekleştirmek için bir oturum açma istemi alırsınız. Kimliği başarıyla doğrulandıktan sonra, `kubectl` komut aşağıdaki örnek çıktıda gösterildiği gibi aks kümesindeki Pod 'leri görüntüler:

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

İçin `kubectl` alınan kimlik doğrulama belirteci önbelleğe alındı. Yalnızca belirtecin süresi dolduğunda veya Kubernetes yapılandırma dosyası yeniden oluşturulduğunda oturum açmanız yeniden istenir.

Aşağıdaki örnek çıktıda olduğu gibi bir Web tarayıcısını kullanarak başarıyla oturum açtıktan sonra bir yetkilendirme hata iletisi görürseniz, aşağıdaki olası sorunları kontrol edin:

```console
error: You must be logged in to the server (Unauthorized)
```

* Kullanıcı hesabının aynı Azure AD kiracısında olup olmadığı ile ilgili olarak uygun nesne KIMLIĞINI veya UPN 'yi tanımlamış olursunuz.
* Kullanıcı 200 taneden fazla grubun üyesi değil.
* Sunucu için uygulama kaydında tanımlanan gizli dizi, kullanılarak yapılandırılan değerle eşleşiyor`--aad-server-app-secret`

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede gösterilen komutları içeren tüm betik için, [AKS örnekleri deposunda Azure AD tümleştirme betiğine][complete-script]bakın.

Küme kaynaklarına erişimi denetlemek için Azure AD kullanıcıları ve grupları 'nı kullanmak için bkz. [AKS 'de rol tabanlı erişim denetimi ve Azure AD kimlikleri kullanarak küme kaynaklarına erişimi denetleme][azure-ad-rbac].

Kubernetes kümelerinin güvenliğini sağlama hakkında daha fazla bilgi için bkz. [AKS Için erişim ve kimlik seçenekleri][rbac-authorization].

Kimlik ve kaynak denetiminde en iyi uygulamalar için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md

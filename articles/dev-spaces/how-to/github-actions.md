---
title: GitHub eylemleri & Azure Kubernetes hizmeti (Önizleme)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: GitHub eylemleri ve Azure Dev Spaces kullanarak doğrudan Azure Kubernetes hizmetindeki çekme isteğinden yapılan değişiklikleri gözden geçirin ve test edin
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, GitHub eylemleri, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777554"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub eylemleri & Azure Kubernetes hizmeti (Önizleme)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces, çekme isteği deponuzdaki ana dalınızla birleştirilmeden önce doğrudan AKS 'de yapılan değişiklikleri test etmenize olanak sağlayan GitHub eylemleri kullanarak bir iş akışı sağlar. Bir çekme isteğinin değişikliklerini gözden geçirmek için çalışan bir uygulamanın olması, hem geliştiricinin hem de takım üyelerinin güvenini artırabilir. Bu çalışan uygulama, ürün yöneticileri ve tasarımcılar gibi takım üyelerinin, geliştirme aşamaları sırasında inceleme sürecinin bir parçası haline gelmesine de yardımcı olabilir.

Bu kılavuzda şunların nasıl yapıldığını öğreneceksiniz:

* Azure 'da yönetilen bir Kubernetes kümesinde Azure Dev Spaces ayarlayın.
* Birden fazla mikro hizmet ile bir geliştirme alanına büyük bir uygulama dağıtın.
* GitHub eylemleri ile CI/CD ayarlayın.
* Tek bir mikro hizmeti, tam uygulamanın bağlamı içinde yalıtılmış bir geliştirme alanında test edin.

> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/free) oluşturabilirsiniz.
* [Yüklü Azure CLI][azure-cli-installed].
* [Held 3 yüklendi][helm-installed].
* [GitHub eylemleri etkin][github-actions-beta-signup]olan bir GitHub hesabı.
* Bir AKS kümesinde çalışan [Örnek uygulamayı Azure dev Spaces bisiklet paylaşımı](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) .

## <a name="create-an-azure-container-registry"></a>Azure Container Registry oluşturma

Azure Container Registry oluşturma (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> ACR 'nizin adı Azure içinde benzersiz olmalıdır ve 5-50 alfasayısal karakter içermelidir. Kullandığınız tüm mektuplar küçük harf olmalıdır.

Sonraki bir adımda kullanıldığından, çıkışta *Loginserver* değerini kaydedin.

## <a name="create-a-service-principal-for-authentication"></a>Kimlik doğrulaması için bir hizmet sorumlusu oluşturma

Hizmet sorumlusu oluşturmak için [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] kullanın. Örnek:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Sonraki bir adımda kullanıldığından JSON çıkışını kaydedin.

AKS kümenizin *kimliğini* görüntülemek için [az aks Show][az-aks-show] kullanın:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

ACR 'nin *kimliğini* görüntülemek için [az ACR Show][az-acr-show] kullanın:

```azurecli
az acr show --name <acrName> --query id
```

AKS kümenize *katkıda bulunan* erişim sağlamak ve ACR 'Nize *acrpush* erişimi sağlamak için [az role atama oluştur][az-role-assignment-create] kullanın.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Hizmet sorumlusuna bu kaynaklara erişim sağlamak için hem AKS kümenizin hem de ACR 'nizin sahibi olmanız gerekir.

## <a name="configure-your-github-action"></a>GitHub eyleminizi yapılandırma

> [!IMPORTANT]
> Deponuz için GitHub eylemlerinin etkinleştirilmiş olması gerekir. Deponuzdaki GitHub eylemlerini etkinleştirmek için GitHub 'daki deponuza gidin, eylemler sekmesine tıklayın ve bu depo için eylemleri etkinleştirmeyi seçin.

Ele geçirilen deponuza gidin ve *Ayarlar*' a tıklayın. Sol kenar çubuğundaki *sırlar* ' a tıklayın. Yeni gizli dizi *Ekle* ' ye tıklayarak aşağıdaki her bir parolayı ekleyin:

1. *AZURE_CREDENTIALS*: hizmet sorumlusu oluşturma işleminden tümüyle çıkış.
1. *RESOURCE_GROUP*: Bu örnekte *MYRESOURCEGROUP* olan aks kümeniz için kaynak grubu.
1. *CLUSTER_NAME*: Bu örnekte *MYAKS* olan aks Kümenizin adı.
1. *CONTAINER_REGISTRY*: ACR Için *loginserver* .
1. *Ana bilgisayar*: *<MASTER_SPACE>. <APP_NAME>. <HOST_SUFFIX*>olan geliştirme alanınız için ana bilgisayar. Bu örnekte, *dev.bikesharingweb.fedcab0987.EUS.azds.io*.
1. *IMAGE_PULL_SECRET*: kullanmak istediğiniz gizli dizi adı, örneğin *demo-gizli*.
1. *MASTER_SPACE*: Bu örnekte *dev* olan üst geliştirme alanının adı.
1. *REGISTRY_USERNAME*: hizmet sorumlusu oluşturma IŞLEMINDEN gelen JSON çıktısından *ClientID* .
1. *REGISTRY_PASSWORD*: hizmet sorumlusu oluşturma IŞLEMINDEN gelen JSON çıktısından *ClientSecret* .

> [!NOTE]
> Tüm bu gizlilikler GitHub eylemi tarafından kullanılır ve [. GitHub/iş akışları/bisiklet. yıml][github-action-yaml]içinde yapılandırılır.

İsteğe bağlı olarak, çekme isteği birleştirildikten sonra ana alanı güncelleştirmek istiyorsanız, bu örnekte *dev.gateway.fedcab0987.eus.azds.io* *MASTER_SPACE>. GATEWAY. <HOST_SUFFIX><* formunu alan *GATEWAY_HOST* gizli anahtarını ekleyin. Değişikliklerinizi çatalınızdaki Ana dala birleştirdiğinizde, uygulamanın tamamını ana geliştirme alanında yeniden oluşturmak ve çalıştırmak için başka bir eylem çalıştırılır. Bu örnekte, ana alan *dev* olur. Bu eylem [. GitHub/iş akışları/bıkesharing. yıml][github-action-bikesharing-yaml]içinde yapılandırılır.

Ayrıca, çekme yaptığınız değişikliklerin bir en alt alanda çalışmasını istiyorsanız, *MASTER_SPACE* ve *konak* sırları güncelleştirin. Örneğin, uygulamanız geliştirme */azureuser1* bir alt alan *dev 'da çalışıyorsa* , çekme isteği *dev/azureuser1* alt alanında çalışır:

* *MASTER_SPACE* , üst alan olarak istediğiniz alt alana güncelleştirin, bu örnekte *azureuser1*.
* *Ana bilgisayarı* *<GRANDPARENT_SPACE>. <APP_NAME>. <* HOST_SUFFIX>, bu örnekte *dev.bikesharingweb.fedcab0987.EUS.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Kod değişiklikleri için yeni dal oluştur

`BikeSharingApp/`' A gidin ve *Bisiklet görüntüleri* adlı yeni bir dal oluşturun.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

232 ve 233 satırlarını kaldırmak için [Bisiklet/server.js][bikes-server-js] düzenleme:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Bölüm şu şekilde görünmelidir:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Dosyayı kaydedin `git add` ve sonra `git commit` değişikliklerinizi hazırlamak için ve kullanın.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Değişikliklerinizi gönderin

`git push`Yeni dalınızı, kullanılan deponuza göndermek için kullanın:

```cmd
git push origin bike-images
```

Gönderme işlemi tamamlandıktan sonra, *Bisiklet görüntüleri* dalına kıyasla temel dal olarak, dallanmış deponuzdaki *ana* dala sahip bir çekme isteği oluşturmak için GitHub 'daki dallanmış deponuza gidin.

Çekme isteğiniz açıldıktan sonra, *Eylemler* sekmesine gidin. Yeni bir eylemin başlatıldığını ve *Bisiklet* hizmetini oluşturuyor olduğunu doğrulayın.

## <a name="view-the-child-space-with-your-changes"></a>Değişikliklerinizin bulunduğu alt alanı görüntüleyin

Eylem tamamlandıktan sonra, çekme isteğindeki değişiklikleri temel alan yeni alt alana yönelik URL ile bir açıklama görürsünüz.

> [!div class="mx-imgBorder"]
> ![GitHub eylem URL 'Si](../media/github-actions/github-action-url.png)

Açıklamadan URL 'YI açarak *bıkesharingweb* hizmetine gidin. Kullanıcı olarak *Aurelia Briggs (müşteri)* öğesini seçin ve ardından kiralamak istediğiniz bir bisiklet seçin. Bisiklet için yer tutucu görüntüsünü artık görmediğinizi doğrulayın.

Değişikliklerinizi çatalınızdaki *ana* dala birleştirirseniz, uygulamanın tamamını üst geliştirme alanında yeniden oluşturmak ve çalıştırmak için başka bir eylem çalıştırılır. Bu örnekte, üst alan *dev* olur. Bu eylem [. GitHub/iş akışları/bıkesharing. yıml][github-action-bikesharing-yaml]içinde yapılandırılır.

## <a name="clean-up-your-azure-resources"></a>Azure kaynaklarınızı Temizleme

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces nasıl çalışır?](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md

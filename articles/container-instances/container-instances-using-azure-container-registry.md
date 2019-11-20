---
title: Azure Container Registry Azure Container Instances dağıtım
description: Azure Container Registry 'de kapsayıcı görüntülerini kullanarak Azure Container Instances kapsayıcıları dağıtmayı öğrenin.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 502f178b66e7ba233552d7db4e095363c8bb8628
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325555"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Registry Azure Container Instances dağıtım

[Azure Container Registry](../container-registry/container-registry-intro.md) , özel Docker kapsayıcı görüntülerini depolamak Için kullanılan Azure tabanlı, yönetilen bir kapsayıcı kayıt defteri hizmetidir. Bu makalede, Azure Container Instances için bir Azure Container Registry 'de depolanan kapsayıcı görüntülerinin nasıl dağıtılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

**Azure Container kayıt defteri**: Bu makaledeki adımları tamamlayabilmeniz için bir Azure Container Registry 'ye ve kayıt defterinde en az bir kapsayıcı görüntüsüne ihtiyacınız vardır. Bir kayıt defterine ihtiyacınız varsa bkz. [Azure CLI kullanarak kapsayıcı kayıt defteri oluşturma](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Bu makaledeki komut satırı örnekleri, [Azure CLI](/cli/azure/) 'yi kullanır ve bash kabuğu için biçimlendirilir. [Azure CLI](/cli/azure/install-azure-cli) 'yı yerel olarak yükleyebilir veya [Azure Cloud Shell][cloud-shell-bash]kullanabilirsiniz.

## <a name="configure-registry-authentication"></a>Kayıt defteri kimlik doğrulamasını yapılandırma

Herhangi bir üretim senaryosunda, [hizmet sorumluları](../container-registry/container-registry-auth-service-principal.md)kullanılarak bir Azure Container Registry erişimi sağlanmalıdır. Hizmet sorumluları, kapsayıcı görüntülerinize [rol tabanlı erişim denetimi](../container-registry/container-registry-roles.md) sağlamanıza olanak tanır. Örneğin, bir hizmet sorumlusunu bir kayıt defterine yalnızca çekme erişimiyle yapılandırabilirsiniz.

Aşağıdaki bölümde, bir Azure Anahtar Kasası ve hizmet sorumlusu oluşturursunuz ve hizmet sorumlusunun kimlik bilgilerini kasada depoladığınızda. 

### <a name="create-key-vault"></a>Anahtar kasası oluştur

[Azure Key Vault](../key-vault/key-vault-overview.md) içinde henüz bir kasanız yoksa, aşağıdaki komutları kullanarak Azure CLI ile bir kasa oluşturun.

Değişkeni, anahtar kasasının oluşturulacağı mevcut bir kaynak grubunun adıyla ve `ACR_NAME` kapsayıcı kayıt defterinizin adıyla güncelleştirin. `RES_GROUP` İçinde `AKV_NAME`yeni anahtar kasanızın adını belirtin. Kasa adı Azure içinde benzersiz olmalıdır ve 3-24 alfasayısal karakter uzunluğunda olmalı, bir harfle başlamalı, harf veya rakamla bitmelidir ve ardışık kısa çizgi içeremez.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Hizmet sorumlusu oluşturma ve kimlik bilgilerini depolama

Şimdi bir hizmet sorumlusu oluşturup kimlik bilgilerini anahtar kasanızda depolamanız gerekiyor.

Aşağıdaki komut hizmet sorumlusu oluşturmak için [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] kullanır ve hizmet sorumlusu **parolasını** kasada depolamak için [az keykasa gizli dizisi][az-keyvault-secret-set] .

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Yukarıdaki komutta bağımsız değişken hizmet sorumlusunu, BT 'nin kayıt defterine yalnızca çekme erişimi veren *acrpull* rolüyle yapılandırır. `--role` Hem itme hem de çekme erişimi sağlamak için `--role` bağımsız değişkenini *acrpush*olarak değiştirin.

Ardından, hizmet sorumlusunun *AppID* 'sini kasada depolayın, bu kullanıcı kimlik doğrulaması için Azure Container Registry geçirdiğiniz **Kullanıcı** adıdır.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Bir Azure Anahtar Kasası oluşturdunuz ve içinde iki gizli dizi depoladınız:

* `$ACR_NAME-pull-usr`: Kapsayıcı kayıt defteri **Kullanıcı adı**olarak kullanılacak HIZMET sorumlusu kimliği.
* `$ACR_NAME-pull-pwd`: Kapsayıcı kayıt defteri **parolası**olarak kullanılmak üzere hizmet sorumlusu parolası.

Artık siz veya uygulamalarınız ve hizmetleriniz kayıt defterinden görüntüleri çektiğinde bu gizli dizilere ada göre başvurabilirsiniz.

## <a name="deploy-container-with-azure-cli"></a>Azure CLI ile kapsayıcı dağıtma

Hizmet sorumlusu kimlik bilgileri Azure Key Vault gizli dizilerle depolandığına göre, uygulamalarınız ve hizmetleriniz bunları özel kayıt defterinize erişmek için kullanabilir.

Önce [az ACR Show][az-acr-show] komutunu kullanarak kayıt defterinin oturum açma sunucusu adını alın. Oturum açma sunucusu adı tümüyle küçük ve şuna benzerdir `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Bir kapsayıcı örneği dağıtmak için aşağıdaki [az Container Create][az-container-create] komutunu yürütün. Komut, kapsayıcı kayıt defterinizde kimlik doğrulamak için Azure Key Vault depolanan hizmet sorumlusu kimlik bilgilerini kullanır ve daha önce [aci-HelloWorld](container-instances-quickstart.md) görüntüsünü Kayıt defterinize göndermiş olduğunuzu varsayar. Kayıt defterinizden farklı bir görüntü kullanmak istiyorsanız değerigüncelleştirin.`--image`

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

`--dns-name-label` Değerin Azure içinde benzersiz olması gerekir, bu nedenle önceki komut kapsayıcının DNS adı etiketine rastgele bir sayı ekler. Komutun çıktısı, kapsayıcının tam etki alanı adını (FQDN) gösterir, örneğin:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Kapsayıcı başarıyla başlatıldıktan sonra, uygulamanın başarıyla çalıştığını doğrulamak için tarayıcınızda FQDN 'sine gidebilirsiniz.

## <a name="deploy-with-azure-resource-manager-template"></a>Azure Resource Manager şablonuyla dağıtma

`imageRegistryCredentials` Özelliği kapsayıcı grubu tanımına ekleyerek bir Azure Resource Manager şablonunda Azure Container Registry özelliklerini belirtebilirsiniz:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Bir Kaynak Yöneticisi şablonundaki gizli dizileri Azure Key Vault başvurma hakkında daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Azure portal ile dağıtma

Azure Container Registry 'de kapsayıcı görüntülerini korudıysanız, Azure portal kullanarak Azure Container Instances bir kapsayıcıyı kolayca oluşturabilirsiniz. Bir kapsayıcı kayıt defterinden kapsayıcı örneği dağıtmak için portalını kullanırken, kayıt defterinin [yönetici hesabını](../container-registry/container-registry-authentication.md#admin-account)etkinleştirmeniz gerekir. Yönetici hesabı, genellikle test amacıyla tek bir kullanıcının kayıt defterine erişmesi için tasarlanmıştır. 

1. Azure portal, kapsayıcı Kayıt defterinize gidin.

1. Yönetici hesabının etkinleştirildiğini doğrulamak için **erişim anahtarları**' nı seçin ve **Yönetici Kullanıcı** altında **Etkinleştir**' i seçin.

1. **Depolar**' ı seçin, sonra dağıtmak istediğiniz depoyu seçin, dağıtmak istediğiniz kapsayıcı görüntüsünün etiketine sağ tıklayın ve **örneği Çalıştır**' ı seçin.

    ![Azure portal Azure Container Registry içindeki "örneği Çalıştır"][acr-runinstance-contextmenu]

1. Kapsayıcı için bir ad ve kaynak grubu için bir ad girin. İsterseniz varsayılan değerleri de değiştirebilirsiniz.

    ![Azure Container Instances için menü oluştur][acr-create-deeplink]

1. Dağıtım tamamlandıktan sonra, IP adresini ve diğer özelliklerini bulmak için bildirimler bölmesinden kapsayıcı grubuna gidebilirsiniz.

    ![Azure Container Instances kapsayıcı grubu için Ayrıntılar görünümü][aci-detailsview]

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Registry kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure Container Registry Ile kimlik](../container-registry/container-registry-authentication.md)doğrulama.

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set

---
title: Azure Container Registry kapsayıcı görüntüsünü dağıtma
description: Azure Container Registry 'den kapsayıcı görüntülerini çekerek Azure Container Instances kapsayıcıları dağıtmayı öğrenin.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7aba107ac58538245c16f581afa2c493f546ca01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786954"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Registry’den Azure Container Instances’a dağıtma

[Azure Container Registry](../container-registry/container-registry-intro.md) , özel Docker kapsayıcı görüntülerini depolamak Için kullanılan Azure tabanlı, yönetilen bir kapsayıcı kayıt defteri hizmetidir. Bu makalede, Azure Container Instances dağıtım sırasında Azure Container Registry 'de depolanan kapsayıcı görüntülerinin nasıl çekeceğini açıklanmaktadır. Kayıt defteri erişimini yapılandırmak için önerilen bir yol, bir Azure Active Directory hizmet sorumlusu ve parolası oluşturmak ve oturum açma kimlik bilgilerini bir Azure anahtar kasasında depomaktır.

## <a name="prerequisites"></a>Önkoşullar

**Azure Container Registry**: Bu makaledeki adımları tamamlayabilmeniz Için bir Azure Container Registry 'ye ve kayıt defterinde en az bir kapsayıcı görüntüsüne ihtiyacınız vardır. Bir kayıt defterine ihtiyacınız varsa bkz. [Azure CLI kullanarak kapsayıcı kayıt defteri oluşturma](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Bu makaledeki komut satırı ÖRNEKLERI [Azure CLI](/cli/azure/) 'Yi kullanır ve bash kabuğu için biçimlendirilir. [Azure CLI](/cli/azure/install-azure-cli) 'yı yerel olarak yükleyebilir veya [Azure Cloud Shell][cloud-shell-bash]kullanabilirsiniz.

## <a name="limitations"></a>Sınırlamalar

* Aynı kapsayıcı grubunda yapılandırılmış bir [yönetilen kimlik](container-instances-managed-identity.md) kullanarak, kapsayıcı grubu dağıtımı sırasında görüntü çekmek için Azure Container Registry kimlik doğrulaması yapamazsınız.
* Şu anda Azure sanal ağına dağıtılan [Azure Container Registry](../container-registry/container-registry-vnet.md) görüntüleri çekemiyorum.

## <a name="configure-registry-authentication"></a>Kayıt defteri kimlik doğrulamasını yapılandırma

"Gözetimsiz" hizmetlere ve uygulamalarına erişim sağlayan bir üretim senaryosunda, bir [hizmet sorumlusu](../container-registry/container-registry-auth-service-principal.md)kullanarak kayıt defteri erişiminin yapılandırılması önerilir. Hizmet sorumlusu, kapsayıcı görüntülerinize [Azure rol tabanlı erişim denetimi (Azure RBAC)](../container-registry/container-registry-roles.md) sağlamanıza olanak tanır. Örneğin, bir hizmet sorumlusunu bir kayıt defterine yalnızca çekme erişimiyle yapılandırabilirsiniz.

Azure Container Registry ek [kimlik doğrulama seçenekleri](../container-registry/container-registry-authentication.md)sağlar.

Aşağıdaki bölümde, bir Azure Anahtar Kasası ve hizmet sorumlusu oluşturursunuz ve hizmet sorumlusunun kimlik bilgilerini kasada depoladığınızda.

### <a name="create-key-vault"></a>Anahtar kasası oluşturma

[Azure Key Vault](../key-vault/general/overview.md) içinde henüz bir kasanız yoksa, aşağıdaki komutları kullanarak Azure CLI ile bir kasa oluşturun.

Değişkeni, `RES_GROUP` anahtar kasasının oluşturulacağı mevcut bir kaynak grubunun adıyla ve `ACR_NAME` kapsayıcı kayıt defterinizin adıyla güncelleştirin. Kısaltma için, bu makaledeki komutlar kayıt defterinizin, anahtar kasasının ve kapsayıcı örneklerinin tümünün aynı kaynak grubunda oluşturulduğunu varsayar.

 İçinde yeni anahtar kasanızın adını belirtin `AKV_NAME` . Kasa adı Azure içinde benzersiz olmalıdır ve 3-24 alfasayısal karakter uzunluğunda olmalı, bir harfle başlamalı, harf veya rakamla bitmelidir ve ardışık kısa çizgi içeremez.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Hizmet sorumlusu oluşturma ve kimlik bilgilerini depolama

Şimdi bir hizmet sorumlusu oluşturun ve kimlik bilgilerini Anahtar Kasanızda depolayın.

Aşağıdaki komut hizmet sorumlusu oluşturmak için [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] kullanır ve hizmet sorumlusu **parolasını** kasada depolamak için [az keykasa gizli dizisi][az-keyvault-secret-set] .

```azurecli
# Create service principal, store its password in vault (the registry *password*)
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

`--role`Yukarıdaki komutta bağımsız değişken hizmet sorumlusunu, BT 'nin kayıt defterine yalnızca çekme erişimi veren *acrpull* rolüyle yapılandırır. Hem itme hem de çekme erişimi sağlamak için `--role` bağımsız değişkenini *acrpush* olarak değiştirin.

Ardından, hizmet sorumlusunun *AppID* 'sini kasada depolayın, bu kullanıcı kimlik doğrulaması için Azure Container Registry geçirdiğiniz **Kullanıcı** adıdır.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Bir Azure anahtar kasası oluşturdunuz ve içinde iki gizli dizi depoladınız:

* `$ACR_NAME-pull-usr`: Kapsayıcı kayıt defterinin **kullanıcı adı** olarak kullanılacak hizmet sorumlusu kimliği.
* `$ACR_NAME-pull-pwd`: Kapsayıcı kayıt defterinin **parolası** olarak kullanılacak hizmet sorumlusu parolası.

Artık siz veya uygulamalarınız ve hizmetleriniz kayıt defterinden görüntüleri çektiğinde bu gizli dizilere ada göre başvurabilirsiniz.

## <a name="deploy-container-with-azure-cli"></a>Azure CLI ile kapsayıcı dağıtma

Hizmet sorumlusu kimlik bilgileri Azure Key Vault gizli dizilerle depolandığına göre, uygulamalarınız ve hizmetleriniz bunları özel kayıt defterinize erişmek için kullanabilir.

Önce [az ACR Show][az-acr-show] komutunu kullanarak kayıt defterinin oturum açma sunucusu adını alın. Oturum açma sunucusu adı tümüyle küçük ve şuna benzerdir `myregistry.azurecr.io` .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Bir kapsayıcı örneği dağıtmak için aşağıdaki [az container create][az-container-create] komutunu yürütün. Komut, kapsayıcı kayıt defterinizde kimlik doğrulamak için Azure Key Vault depolanan hizmet sorumlusu kimlik bilgilerini kullanır ve daha önce [aci-HelloWorld](container-instances-quickstart.md) görüntüsünü Kayıt defterinize göndermiş olduğunuzu varsayar. `--image`Kayıt defterinizden farklı bir görüntü kullanmak istiyorsanız değeri güncelleştirin.

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

`--dns-name-label`Değerin Azure içinde benzersiz olması gerekir, bu nedenle önceki komut KAPSAYıCıNıN DNS adı etiketine rastgele bir sayı ekler. Komutun çıktısı, kapsayıcının tam etki alanı adını (FQDN) gösterir, örneğin:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Kapsayıcı başarıyla başlatıldıktan sonra, uygulamanın başarıyla çalıştığını doğrulamak için tarayıcınızda FQDN 'sine gidebilirsiniz.

## <a name="deploy-with-azure-resource-manager-template"></a>Azure Resource Manager şablonuyla dağıtma

Özelliği kapsayıcı grubu tanımına ekleyerek, Azure Container kayıt defterinizin özelliklerini bir Azure Resource Manager şablonunda belirtebilirsiniz `imageRegistryCredentials` . Örneğin, kayıt defteri kimlik bilgilerini doğrudan belirtebilirsiniz:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Tüm kapsayıcı grubu ayarları için [Kaynak Yöneticisi şablonu başvurusuna](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups)bakın.    

Bir Kaynak Yöneticisi şablonundaki gizli dizileri Azure Key Vault başvurma hakkında daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](../azure-resource-manager/templates/key-vault-parameter.md).

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
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set

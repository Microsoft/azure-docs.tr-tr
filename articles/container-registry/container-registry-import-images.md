---
title: Kapsayıcı görüntülerini içeri aktarma
description: Docker komutlarını çalıştırmaya gerek kalmadan Azure API 'Lerini kullanarak kapsayıcı görüntülerini bir Azure Container Registry 'ye aktarın.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e7becadab7f23acd7b85d6d82fd8abbfa7608add
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781532"
---
# <a name="import-container-images-to-a-container-registry"></a>Kapsayıcı görüntülerini kapsayıcı kayıt defterine aktarma

Kapsayıcı görüntülerini Docker komutlarını kullanmadan bir Azure Container Registry 'ye kolayca içeri aktarabilirsiniz (kopyalayabilirsiniz). Örneğin, bir geliştirme kayıt defterindeki görüntüleri bir üretim kayıt defterine aktarın veya temel görüntüleri ortak bir kayıt defterinden kopyalayın.

Azure Container Registry, var olan bir kayıt defterinden görüntüleri ve diğer yapıları kopyalamak için bir dizi yaygın senaryoyu işler:

* Ortak bir kayıt defterinden görüntüleri içeri aktarma

* Aynı ya da farklı bir Azure aboneliğinde veya kiracıda, başka bir Azure Container Registry 'den Helu 3 grafikleri dahil görüntüleri veya OCı yapılarını içeri aktarın

* Azure olmayan özel kapsayıcı kayıt defterinden içeri aktarma

Azure Container Registry 'de görüntü içeri aktarma işlemi Docker CLı komutlarını kullanarak aşağıdaki avantajlara sahiptir:

* İstemci ortamınız yerel bir Docker yüklemesine ihtiyaç duymadığından, desteklenen işletim sistemi türünden bağımsız olarak tüm kapsayıcı görüntülerini içeri aktarın.

* Multi-Architecture görüntülerini (resmi Docker görüntüleri gibi) içeri aktardığınızda, bildirim listesinde belirtilen tüm mimarilerin ve platformların görüntüleri bir kopyası alınır.

* Hedef kayıt defterine erişimin, kayıt defterinin genel uç noktasını kullanması gerekmez.

Kapsayıcı görüntülerini içeri aktarmak için bu makale, Azure CLı 'yı Azure Cloud Shell veya yerel olarak (sürüm 2.0.55 veya üzeri önerilir) çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli].

> [!NOTE]
> Aynı kapsayıcı görüntülerini birden çok Azure bölgesinde dağıtmanız gerekiyorsa Azure Container Registry [Coğrafi çoğaltmayı](container-registry-geo-replication.md)da destekler. Bir kayıt defteri coğrafi olarak çoğaltılırken (Premium hizmet katmanı gereklidir), tek bir kayıt defterinden aynı görüntü ve etiket adları ile birden çok bölgeye hizmet verebilirsiniz.
>

> [!IMPORTANT]
> İki Azure Kapsayıcı kayıt defterleri arasında görüntü içeri aktarma işleminde yapılan değişiklikler 2021 Ocak 'tan itibaren sunulmuştur:
> * Ağ kısıtlamalı bir Azure Container Registry 'ye veya bu bilgisayardan içeri aktarma, kısıtlanmış kayıt defterinin, ağı atlayıp [**Güvenilen hizmetlere erişmesine izin**](allow-access-trusted-services.md) vermek için gerekir. Varsayılan olarak, ayar etkin olur ve içeri aktarmaya izin verir. Ayar, yeni oluşturulmuş bir kayıt defterinde özel bir uç nokta veya kayıt defteri güvenlik kuralları ile etkin değilse, içeri aktarma başarısız olur. 
> * İçeri aktarma kaynağı veya hedefi olarak kullanılan mevcut bir ağ kısıtlamalı Azure Container Registry 'de, bu ağ güvenlik özelliğinin etkinleştirilmesi isteğe bağlıdır ancak önerilir.

## <a name="prerequisites"></a>Önkoşullar

Zaten bir Azure Container kayıt defteriniz yoksa bir kayıt defteri oluşturun. Adımlar için bkz. [hızlı başlangıç: Azure CLI kullanarak özel kapsayıcı kayıt defteri oluşturma](container-registry-get-started-azure-cli.md).

Bir görüntüyü Azure Container Registry 'ye aktarmak için, kimliğinizin hedef kayıt defterine (en az katkıda bulunan rolü veya ımportımage eylemine izin veren özel bir rol) yazma izinleri olması gerekir. Bkz. [Azure Container Registry rolleri ve izinleri](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Ortak bir kayıt defterinden içeri aktarma

### <a name="import-from-docker-hub"></a>Docker Hub 'dan içeri aktar

Örneğin, [][az-acr-import] `hello-world:latest` Docker Hub 'ından Multi-Architecture görüntüsünü *myregistry* adlı bir kayıt defterine aktarmak için az ACR Import komutunu kullanın. `hello-world`, Docker Hub 'ından resmi bir görüntü olduğundan, bu görüntü varsayılan `library` depodadır. Depo adını ve isteğe bağlı olarak Image parametresinin değerine bir etiketi ekleyin `--source` . (Bir görüntüyü, bir görüntünün belirli bir sürümünü garanti eden etiketi yerine, bildirim özetine göre belirleyebilirsiniz.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Komutu çalıştırarak bu görüntüyle ilişkili birden fazla bildirimin olduğunu doğrulayabilirsiniz `az acr repository show-manifests` :

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

[Docker Hub hesabınız](https://www.docker.com/pricing)varsa, Docker Hub 'ından bir görüntü içeri aktarırken kimlik bilgilerini kullanmanızı öneririz. Docker Hub Kullanıcı adını ve parolayı veya [kişisel erişim belirtecini](https://docs.docker.com/docker-hub/access-tokens/) parametre olarak geçirin `az acr import` . Aşağıdaki örnek, Docker Hub `tensorflow` kimlik bilgilerini kullanarak Docker Hub 'daki depodan ortak bir görüntü içeri aktarır:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Microsoft Container Registry içeri aktar

Örneğin, `ltsc2019` Windows Server çekirdek görüntüsünü `windows` Microsoft Container Registry deposundan içeri aktarın.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Aynı AD kiracısındaki bir Azure Container Registry 'den içeri aktarma

Tümleşik Azure Active Directory izinleri kullanarak aynı AD kiracısındaki bir Azure Container kayıt defterinden bir görüntüyü içeri aktarabilirsiniz.

* Kimliğiniz, kaynak kayıt defterinden (okuyucu rolü) okumak ve hedef kayıt defterine (katkıda bulunan rolü veya ımportımage eylemine izin veren [özel bir rol](container-registry-roles.md#custom-roles) ) aktarmak için Azure Active Directory izinlere sahip olmalıdır.

* Kayıt defteri aynı Active Directory kiracısında aynı veya farklı bir Azure aboneliğinde olabilir.

* Kaynak kayıt defterine [genel erişim](container-registry-access-selected-networks.md#disable-public-network-access) devre dışı bırakılmış olabilir. Ortak erişim devre dışıysa, kaynak kayıt defterini kayıt defteri oturum açma sunucusu adı yerine kaynak KIMLIĞIYLE belirtin.

* Kaynak kayıt defteri ve/veya hedef kayıt defteri için özel bir uç nokta veya kayıt defteri güvenlik kuralları uygulanmışsa, kısıtlanmış kayıt defterinin [Güvenilen hizmetlerin ağa erişmesine izin verdiğinden](allow-access-trusted-services.md) emin olun.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Aynı abonelikteki bir kayıt defterinden içeri aktarma

Örneğin, `aci-helloworld:latest` bir kaynak kayıt defteri *mysourceregyıpdan* aynı Azure aboneliğindeki *myregistry* 'e ait görüntüyü içeri aktarın.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Aşağıdaki örnek, kayıt defterinin `aci-helloworld:latest` genel uç noktasına erişimin devre dışı bırakılmasıyla, bir kaynak kayıt defteri *mysourceregyımonu* *myregistry* 'e aktarır. Kaynak kayıt defterinin parametresi ile kaynak KIMLIĞINI sağlayın `--registry` . `--source`Parametresinin kayıt defteri oturum açma sunucusu adını değil yalnızca kaynak depoyu ve etiketi belirttiğinden emin olun.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Aşağıdaki örnek, bir görüntüyü etiketi yerine manifest Digest (SHA-256 karması, olarak temsil edilir) ile içe aktarır `sha256:...` :

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Farklı bir abonelikteki kayıt defterinden içeri aktarma

Aşağıdaki örnekte, *mysourceregbakanlığı* aynı Active Directory kiracısındaki *myregistry* 'den farklı bir abonelikte yer verilir. Kaynak kayıt defterinin parametresi ile kaynak KIMLIĞINI sağlayın `--registry` . `--source`Parametresinin kayıt defteri oturum açma sunucusu adını değil yalnızca kaynak depoyu ve etiketi belirttiğinden emin olun.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Hizmet sorumlusu kimlik bilgilerini kullanarak bir kayıt defterinden içeri aktarma

Tümleşik Active Directory izinleri kullanarak erişemeyen bir kayıt defterinden içeri aktarmak için, kaynak kayıt defterine hizmet sorumlusu kimlik bilgilerini (varsa) kullanabilirsiniz. Kaynak kayıt defterine ACRPull erişimi olan Active Directory [hizmet sorumlusunun](container-registry-auth-service-principal.md) AppID 'sini ve parolasını sağlayın. Hizmet sorumlusu kullanmak, derleme sistemleri ve görüntüleri kayıt defterinize aktarması gereken diğer katılımsız sistemler için yararlıdır.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Farklı bir AD kiracısındaki Azure Container Registry 'den içeri aktarma

Farklı bir Azure Active Directory kiracısındaki bir Azure Container Registry 'den içeri aktarmak için, kaynak kayıt defteri 'ni oturum açma sunucusu adına göre belirtin ve kayıt defterine çekme erişimi sağlayan Kullanıcı adı ve parola kimlik bilgilerini sağlayın. Örneğin, [Depo kapsamlı bir belirteç](container-registry-repository-scoped-permissions.md) ve parola veya kaynak kayıt defterine ACRPull erişimi olan bir Active Directory [hizmet sorumlusu](container-registry-auth-service-principal.md) için AppID ve parola kullanın. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Azure olmayan özel kapsayıcı kayıt defterinden içeri aktarma

Kayıt defterine çekme erişimini etkinleştiren kimlik bilgilerini belirterek Azure olmayan özel bir kayıt defterinden görüntü içeri aktarın. Örneğin, özel bir Docker kayıt defterinden bir görüntü çekin: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, kapsayıcı görüntülerini genel bir kayıt defterine veya başka bir özel kayıt defterinden Azure Container Registry 'ye aktarma hakkında bilgi edindiniz. Ek görüntü içeri aktarma seçenekleri için, [az ACR Import][az-acr-import] komut başvurusuna bakın. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli

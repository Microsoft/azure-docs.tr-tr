---
title: Azure Container Registry içindeki depoların izinleri
description: Görüntü çekmek veya göndermek için bir kayıt defterindeki belirli depolara kapsamlı izinlere sahip bir belirteç oluşturun
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2019
ms.author: danlep
ms.openlocfilehash: 7b9d220ac7e507513458eab6b55276b3aa434739
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742753"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Azure Container Registry içinde depo kapsamlı izinler 

Azure Container Registry, tüm kayıt defterine [rol tabanlı erişim](container-registry-roles.md) olan kimlikleri kullanarak çeşitli [kimlik doğrulama seçeneklerini](container-registry-authentication.md) destekler. Ancak, belirli senaryolarda yalnızca bir kayıt defterindeki belirli *depolara* erişim sağlamanız gerekebilir. 

Bu makalede, bir kayıt defterindeki yalnızca belirli depolarda eylem gerçekleştirme izinleri olan bir erişim belirtecinin nasıl oluşturulacağı ve kullanılacağı gösterilir. Bir erişim belirteciyle, görüntü çekmek veya göndermek veya başka eylemler gerçekleştirmek için depolara kapsamlı, zaman sınırlı erişim sağlayan kullanıcılar veya hizmetler sağlayabilirsiniz. 

Belirteç kavramları ve senaryolar hakkında arka plan için bu makalenin sonraki kısımlarında yer alarak, [Depo kapsamındaki Izinler hakkında](#about-repository-scoped-permissions)bölümüne bakın.

> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

* Bu özellik yalnızca bir **Premium** kapsayıcı kayıt defterinde kullanılabilir. Kayıt defteri hizmeti katmanları ve limitleri hakkında bilgi için bkz. [Azure Container Registry SKU 'lar](container-registry-skus.md).
* Şu anda, hizmet sorumlusu veya yönetilen kimlik gibi bir Azure Active Directory nesnesine depo kapsamlı izinler atayamazsınız.

## <a name="prerequisites"></a>Ön koşullar

* **Azure CLI** -Bu makale, Azure CLI 'nin (sürüm 2.0.76 veya üzeri) yerel olarak yüklenmesini gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).
* **Docker** -kayıt defteriyle kimlik doğrulaması yapmak için yerel bir Docker yüklemesine de ihtiyacınız vardır. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) sistemleri için yükleme yönergeleri sağlar.
* **Depolarla kapsayıcı kayıt defteri** -bir tane yoksa, Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın. 

  Test amaçları için kayıt defterine bir veya daha fazla örnek görüntü [gönderin](container-registry-get-started-docker-cli.md) veya [içeri aktarın](container-registry-import-images.md) . Bu makaledeki örnekler, iki depoda aşağıdaki görüntülere başvurur: `samples/hello-world:v1` ve `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Erişim belirteci oluşturma

[Az ACR Token Create][az-acr-token-create] komutunu kullanarak bir belirteç oluşturun. Bir belirteç oluştururken, her depoda bir veya daha fazla depo ve ilişkili eylem belirtin ya da bu ayarlarla mevcut bir kapsam Haritası belirtin.

### <a name="create-access-token-and-specify-repositories"></a>Erişim belirteci oluşturma ve depoları belirtme

Aşağıdaki örnek, `samples/hello-world` deposunda `content/write` ve `content/read` eylemleri ve `samples/nginx` deposundaki `content/read` eylemini gerçekleştirmek için izinlere sahip bir erişim belirteci oluşturur. Varsayılan olarak, komut iki parola üretir. 

Bu örnek, belirteç durumunu `enabled` (varsayılan ayar) olarak ayarlar, ancak belirteci dilediğiniz zaman güncelleştirebilir ve durumu `disabled`olarak ayarlayabilirsiniz.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

Çıktı, oluşturulan parolalar ve kapsam Haritası dahil olmak üzere belirteçle ilgili ayrıntıları gösterir. Daha sonra `docker login`ile kullanmak için parolaları güvenli bir yere kaydetmeniz önerilir. Parolalar tekrar alınamaz, ancak yeni olanlar oluşturulabilir.

Çıktı Ayrıca, `MyToken-scope-map`adlı bir kapsam eşlemesinin otomatik olarak oluşturulduğunu gösterir. Aynı depo eylemlerini diğer belirteçlere uygulamak için kapsam eşlemesini kullanabilirsiniz. Ya da, belirteç izinlerini değiştirmek için kapsam eşlemini daha sonra güncelleştirin.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>Kapsam eşlemesi ve ilişkili belirteç oluşturma

Alternatif olarak, bir belirteç oluştururken depolarla ve ilişkili eylemlerle bir kapsam Haritası belirtin. Kapsam eşlemesi oluşturmak için [az ACR Scope-Map Create][az-acr-scope-map-create] komutunu kullanın.

Aşağıdaki örnek komut, önceki örnekte kullanılan izinlerle aynı izinlere sahip bir kapsam haritası oluşturur. `samples/hello-world` deposunda `content/write` ve `content/read` eylemlere ve `samples/nginx` deposundaki `content/read` eylemine izin verir:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

Çıktı aşağıdakine benzer:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

*Kapsamım* kapsam eşlemesiyle ilişkili bir belirteç oluşturmak için [az ACR Token Create][az-acr-token-create] komutunu çalıştırın. Varsayılan olarak, komut iki parola üretir. Bu örnek, belirteç durumunu `enabled` (varsayılan ayar) olarak ayarlar, ancak belirteci dilediğiniz zaman güncelleştirebilir ve durumu `disabled`olarak ayarlayabilirsiniz.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

Çıktı, oluşturulan parolalar ve uyguladığınız kapsam Haritası dahil olmak üzere belirteçle ilgili ayrıntıları gösterir. Daha sonra `docker login`ile kullanmak için parolaları güvenli bir yere kaydetmeniz önerilir. Parolalar tekrar alınamaz, ancak yeni olanlar oluşturulabilir.

## <a name="generate-passwords-for-token"></a>Belirteç için parolalar oluştur

Belirteci oluştururken parolalar oluşturulduysa, [kayıt defteri Ile kimlik doğrulaması](#authenticate-using-token)yapmaya devam edin.

Belirteç parolanız yoksa veya yeni parolalar oluşturmak istiyorsanız [az ACR Token Credential Generate][az-acr-token-credential-generate] komutunu çalıştırın.

Aşağıdaki örnek, 30 günlük süre sonu süresi ile oluşturduğunuz belirteç için yeni bir parola oluşturur. Parolayı TOKEN_PWD ortam değişkeninde depolar. Bu örnek bash kabuğu için biçimlendirilir.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Belirteç kullanarak kimlik doğrulama

Belirteç kimlik bilgilerini kullanarak kayıt defteri ile kimlik doğrulaması yapmak için `docker login` çalıştırın. Kullanıcı adı olarak belirteç adını girin ve parolalarından birini sağlayın. Aşağıdaki örnek bash kabuğu için biçimlendirilir ve ortam değişkenlerini kullanarak değerleri sağlar.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Çıkış başarılı kimlik doğrulaması göstermelidir:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Kapsamlı erişimi doğrulama

Belirtecin kayıt defterindeki depolara kapsamlı izinler sağladığını doğrulayabilirsiniz. Bu örnekte, aşağıdaki `docker pull` komutları `samples/hello-world` ve `samples/nginx` depolarında bulunan görüntüleri çekmek için başarıyla tamamlanır:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Örnek belirteç `content/write` eyleme yalnızca `samples/hello-world` deposunda izin verdiğinden `docker push`, bu depoya başarılı olur ancak `samples/nginx`için başarısız olur:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Kapsam eşlemesini ve belirtecini Güncelleştir

Belirteç izinlerini güncelleştirmek için, [az ACR Scope-Map Update][az-acr-scope-map-update]kullanarak ilişkili kapsam eşlemesindeki izinleri güncelleştirin. Örneğin, `samples/hello-world` deposundaki `content/write` eylemini kaldırmak üzere *hayal Copemap* 'i güncelleştirmek için:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Kapsam eşlemesi birden fazla belirteçle ilişkiliyse, komut tüm ilişkili belirteçlerin iznini günceller.

Farklı bir kapsam eşlemesine sahip bir belirteci güncelleştirmek istiyorsanız [az ACR Token Update][az-acr-token-update]' i çalıştırın. Örneğin:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Bir belirteci güncelleştirdikten veya bir belirteçle ilişkili kapsam eşlemesinden sonra, izin değişiklikleri, belirteci kullanarak bir sonraki `docker login` veya diğer kimlik doğrulamasında geçerli olur.

Bir belirteci güncelleştirdikten sonra, kayıt defterine erişmek için yeni parolalar oluşturmak isteyebilirsiniz. Çalıştır [az ACR Token Credential Generate][az-acr-token-credential-generate]. Örneğin:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>Depo kapsamlı izinler hakkında

### <a name="concepts"></a>Kavramlar

Depo kapsamındaki izinleri yapılandırmak için, Azure CLı 'daki komutları kullanarak bir *erişim belirteci* ve ilişkili *Kapsam haritası* oluşturursunuz.

* **Erişim belirteci** , kayıt defteri ile kimlik doğrulaması için parola ile kullanılan bir kimlik bilgileridir. Her belirteçle ilişkili, bir veya daha fazla depo kapsamındaki *eylemlere* izin verilir. Her belirteç için bir sona erme saati ayarlayabilirsiniz. 

* Belirtilen her bir depodaki **Eylemler** aşağıdakilerden birini veya daha fazlasını içerir.

  |Eylem  |Açıklama  |
  |---------|---------|
  |`content/read`     |  Depodan verileri okuyun. Örneğin, bir yapıt çekin.  |
  |`metadata/read`    | Depodan meta verileri okuyun. Örneğin, etiketleri listeleyin veya bildirim meta verilerini gösterebilirsiniz.   |
  |`content/write`     |  Verileri depoya yazın. Yapıtı göndermek için `content/read` ile kullanın.    |
  |`metadata/write`     |  Meta verileri depoya yazın. Örneğin, bildirim özniteliklerini güncelleştirin.  |
  |`content/delete`    | Verileri depodan kaldırın. Örneğin, bir depoyu veya bildirimi silin. |

* **Kapsam eşlemesi** , bir belirteç için uyguladığınız depo izinlerini gruplandıran veya diğer belirteçlere yeniden uygulayabileceğiniz bir kayıt defteri nesnesidir. Bir belirteç oluştururken kapsam eşlemesi uygulamazsanız, izin ayarlarını kaydetmek için bir kapsam eşlemesi sizin için otomatik olarak oluşturulur. 

  Kapsam eşlemesi, birden fazla kullanıcıyı aynı depolar kümesine aynı erişimle yapılandırmanıza yardımcı olur. Azure Container Registry, erişim belirteçleri oluştururken uygulayabileceğiniz sistem tanımlı kapsam eşlemeleri de sağlar.

Aşağıdaki görüntüde belirteçler ve kapsam haritaları arasındaki ilişki özetlenmektedir. 

![Kayıt defteri kapsamı haritaları ve belirteçleri](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Senaryolar

Erişim belirteci kullanmaya yönelik senaryolar şunlardır:

* Bir depodan bir görüntü çekmek için bağımsız belirteçler olan IoT cihazları sağlama
* Belirli bir depoya yönelik izinlere sahip bir dış kuruluş sağlayın 
* Depolama erişimini kuruluşunuzdaki belirli kullanıcı gruplarıyla sınırlayın. Örneğin, belirli depoları hedefleyen görüntüler oluşturan geliştiriciler için yazma ve okuma erişimi ve bu depolardan dağıtım yapan takımlara okuma erişimi sağlayın.

### <a name="authentication-using-token"></a>Belirteç kullanarak kimlik doğrulama

Hedef kayıt defteriyle kimlik doğrulamak için Kullanıcı adı ve ilişkili parolalarından biri olarak bir belirteç adı kullanın. Kimlik doğrulama yöntemi yapılandırılan eylemlere bağlıdır.

### <a name="contentread-or-contentwrite"></a>İçerik/okuma veya içerik/yazma

Belirteç yalnızca `content/read` veya `content/write` eylemlerine izin veriyorsa, aşağıdaki kimlik doğrulama akışlarının her birinde belirteç kimlik bilgilerini sağlayın:

* `docker login` kullanarak Docker ile kimlik doğrulama
* Azure CLı 'de [az ACR Login][az-acr-login] komutunu kullanarak kayıt defteriyle kimlik doğrulama

Aşağıdaki kimlik doğrulama, belirteç kapsamlı depoda veya depolarda yapılandırılan eylemlere izin verir. Örneğin, belirteç bir depodaki `content/read` eyleme izin veriyorsa, bu depodaki görüntülerde `docker pull` işlemlerine izin verilir.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>meta veri/okuma, meta veri/yazma veya içerik/silme

Belirteç bir depoda `metadata/read`, `metadata/write`veya `content/delete` eylemlere izin veriyorsa, belirteç kimlik bilgileri, Azure CLı 'de ilgili [az ACR Repository][az-acr-repository] komutlarıyla birlikte parametre olarak sağlanmalıdır.

Örneğin, bir depoda `metadata/read` eylemlere izin verildiğinde, etiketleri listelemek için [az ACR Repository Show-Tags][az-acr-repository-show-tags] komutunu çalıştırırken belirteç kimlik bilgilerini geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Kapsam haritalarını ve erişim belirteçlerini yönetmek için [az ACR Scope-Map][az-acr-scope-map] ve [az ACR Token][az-acr-token] komut gruplarındaki ek komutları kullanın.
* Yönetici hesabı veya Azure Active Directory kimliği kullanarak bir Azure Container Registry ile kimlik doğrulaması yapmak için bkz. senaryolara [genel bakış](container-registry-authentication.md) .


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate

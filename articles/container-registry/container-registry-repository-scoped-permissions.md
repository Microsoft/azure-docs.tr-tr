---
title: Azure Container Registry içindeki depoların izinleri
description: Bir Premium kayıt defterindeki belirli depolara kapsamlı, görüntü çekmek veya göndermek veya başka eylemler gerçekleştirmek için izinler içeren bir belirteç oluşturun
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: 0ac479b696a377509cee6459efd8bbb9de940d2a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781406"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Depo kapsamlı izinlerle belirteç oluşturma

Bu makalede, kapsayıcı kayıt defterinizde belirli depolara erişimi yönetmek için belirteçlerin ve kapsam eşlemelerinin nasıl oluşturulacağı açıklanır. Belirteç oluşturarak, bir kayıt defteri sahibi, görüntüleri çekme veya anında iletme veya başka eylemler gerçekleştirme amacıyla depolara kapsamlı, zaman sınırlı erişim sağlayan kullanıcılar veya hizmetler sağlayabilir. Bir belirteç, kayıt defterinin tamamına izin veren diğer kayıt defteri [kimlik doğrulama seçeneklerinden](container-registry-authentication.md)daha ayrıntılı izinler sağlar. 

Belirteç oluşturma senaryoları şunları içerir:

* Bağımsız belirteçlere sahip IoT cihazlarının bir depodan bir görüntü çekmesine izin ver
* Belirli bir depoya yönelik izinlere sahip bir dış kuruluş sağlayın 
* Depo erişimini kuruluşunuzdaki farklı kullanıcı gruplarıyla sınırlayın. Örneğin, belirli depoları hedefleyen görüntüler oluşturan geliştiriciler için yazma ve okuma erişimi ve bu depolardan dağıtım yapan takımlara okuma erişimi sağlayın.

Bu özellik **Premium** kapsayıcı kayıt defteri hizmet katmanında kullanılabilir. Kayıt defteri hizmeti katmanları ve limitleri hakkında bilgi için bkz. [Azure Container Registry hizmet katmanları](container-registry-skus.md).

> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

* Şu anda, hizmet sorumlusu veya yönetilen kimlik gibi Azure Active Directory bir kimliğe depo kapsamındaki izinler atayamazsınız.
* [Anonim çekme erişimi](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)için etkinleştirilmiş bir kayıt defterinde kapsam eşlemesi oluşturamazsınız.

## <a name="concepts"></a>Kavramlar

Depo kapsamındaki izinleri yapılandırmak için, ilişkili *kapsam haritasına* sahip bir *belirteç* oluşturursunuz. 

* Oluşturulan bir parolayla birlikte bir **belirteç** , kullanıcının kayıt defteriyle kimlik doğrulamasını sağlar. Belirteç parolası için bir sona erme tarihi ayarlayabilir veya bir belirteci dilediğiniz zaman devre dışı bırakabilirsiniz.  

  Bir belirteç ile kimlik doğrulamasından sonra, Kullanıcı veya hizmet bir veya daha fazla depoda kapsamı belirlenmiş bir veya daha fazla *eylem* gerçekleştirebilir.

  |Eylem  |Açıklama  | Örnek |
  |---------|---------|--------|
  |`content/delete`    | Depodan veri kaldırma  | Bir depoyu veya bildirimi silme |
  |`content/read`     |  Depodan veri okuma |  Yapıt çekme |
  |`content/write`     |  Verileri depoya yaz     | `content/read`Yapıt göndermek için ile kullanın |
  |`metadata/read`    | Depodan meta verileri oku   | Etiketleri veya bildirimleri listeleme |
  |`metadata/write`     |  Meta verileri depoya yaz  | Okuma, yazma veya silme işlemlerini etkinleştirme veya devre dışı bırakma |

* Bir **kapsam eşlemesi** , belirteç için uyguladığınız depo izinlerini gruplandırır ve diğer belirteçlere yeniden uygulanabilir. Her belirteç tek bir kapsam eşlemesiyle ilişkilendirilir. 

   Kapsam haritası ile:

    * Birden çok belirteci bir depolar kümesi için özdeş izinlerle yapılandırma
    * Kapsam eşlemesinde depo eylemleri eklediğinizde veya kaldırdığınızda veya farklı bir kapsam eşlemesi uyguladığınızda belirteç izinlerini güncelleştirme 

  Azure Container Registry, belirteçleri oluştururken uygulayabileceğiniz, sistem tarafından tanımlanan çeşitli kapsam eşlemeleri de sağlar. Sistem tanımlı kapsam eşlemelerinin izinleri, kayıt defterinizde bulunan tüm depolar için geçerlidir.

Aşağıdaki görüntüde belirteçler ve kapsam haritaları arasındaki ilişki gösterilmektedir. 

![Kayıt defteri belirteçleri ve kapsam haritaları](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Önkoşullar

* Azure **CLI** -bu makaledeki Azure CLI komutları komut örnekleri IÇIN Azure CLI sürüm 2.17.0 veya üzeri gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).
* **Docker** -görüntüleri çekmek veya göndermek için kayıt defteriyle kimlik doğrulamak için yerel bir Docker yüklemesine ihtiyacınız vardır. Docker, [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ve [Linux](https://docs.docker.com/engine/installation/#supported-platforms) sistemleri için yükleme yönergeleri sağlar.
* **Kapsayıcı kayıt defteri** -bir hesabınız yoksa, Azure aboneliğinizde bir Premium kapsayıcı kayıt defteri oluşturun veya mevcut bir kayıt defterini yükseltin. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın. 

## <a name="create-token---cli"></a>Belirteç oluşturma-CLı

### <a name="create-token-and-specify-repositories"></a>Belirteç oluşturma ve depoları belirtme

[Az ACR Token Create][az-acr-token-create] komutunu kullanarak bir belirteç oluşturun. Belirteç oluştururken, her depoda bir veya daha fazla depo ve ilişkili eylem belirtebilirsiniz. Depoların kayıt defterinde henüz olması gerekmez. Var olan bir kapsam haritasını belirterek belirteç oluşturmak için bir [sonraki bölüme](#create-token-and-specify-scope-map)bakın.

Aşağıdaki örnek, kayıt defteri *myregistry* dosyasında depo üzerinde aşağıdaki izinlerle bir belirteç oluşturur `samples/hello-world` : `content/write` ve `content/read` . Varsayılan olarak, komut varsayılan belirteç durumunu olarak ayarlar `enabled` , ancak durumu istediğiniz `disabled` zaman güncelleştirebilirsiniz.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Çıktı, belirteç hakkındaki ayrıntıları gösterir. Varsayılan olarak, süresi dolmayan iki parola oluşturulur, ancak isteğe bağlı olarak bir sona erme tarihi ayarlayabilirsiniz. Daha sonra kimlik doğrulaması için kullanmak üzere parolaları güvenli bir yere kaydetmeniz önerilir. Parolalar yeniden alınamaz, ancak yeni olanlar oluşturulabilir.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
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

> [!NOTE]
> Belirteç parolalarını ve süre sonu dönemlerini yeniden oluşturmak için, bu makalede daha sonra [belirteç parolalarını](#regenerate-token-passwords) yeniden oluşturma bölümüne bakın.

Çıktı, oluşturulan komutun kapsam eşlemesi hakkındaki ayrıntıları içerir. `MyToken-scope-map`Aynı depo eylemlerini diğer belirteçlere uygulamak için burada adlı kapsam haritasını kullanabilirsiniz. Ya da, ilişkili belirteçlerin izinlerini değiştirmek için kapsam eşlemini daha sonra güncelleştirin.

### <a name="create-token-and-specify-scope-map"></a>Belirteç oluştur ve kapsam eşlemesini belirt

Belirteç oluşturmanın alternatif bir yolu var olan bir kapsam eşlemesini belirtmektir. Zaten bir kapsam haritanız yoksa, önce depoları ve ilişkili eylemleri belirterek bir tane oluşturun. Ardından, belirteç oluştururken kapsam eşlemesini belirtin. 

Kapsam eşlemesi oluşturmak için [az ACR Scope-Map Create][az-acr-scope-map-create] komutunu kullanın. Aşağıdaki komut, `samples/hello-world` daha önce kullanılan depoda aynı izinlere sahip bir kapsam haritası oluşturur. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Bir belirteç oluşturmak için [az ACR Token Create][az-acr-token-create] komutunu çalıştırın, bu, *Kapsamım* kapsam eşlemesini belirterek. Önceki örnekte olduğu gibi, komut varsayılan belirteç durumunu olarak ayarlar `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Çıktı, belirteç hakkındaki ayrıntıları gösterir. Varsayılan olarak, iki parola oluşturulur. Daha sonra kimlik doğrulaması için kullanmak üzere parolaları güvenli bir yere kaydetmeniz önerilir. Parolalar yeniden alınamaz, ancak yeni olanlar oluşturulabilir.

> [!NOTE]
> Belirteç parolalarını ve süre sonu dönemlerini yeniden oluşturmak için, bu makalede daha sonra [belirteç parolalarını](#regenerate-token-passwords) yeniden oluşturma bölümüne bakın.

## <a name="create-token---portal"></a>Belirteç oluşturma-Portal

Belirteçleri ve kapsam eşlemelerini oluşturmak için Azure portal kullanabilirsiniz. `az acr token create`CLI komutunda olduğu gibi, bir veya daha fazla depo ve ilişkili eylemleri belirterek bir belirteç oluşturduğunuzda, var olan bir kapsam haritasını uygulayabilir veya bir kapsam eşlemesi oluşturabilirsiniz. Depoların kayıt defterinde henüz olması gerekmez. 

Aşağıdaki örnek bir belirteç oluşturur ve depo üzerinde aşağıdaki izinlerle bir kapsam haritası oluşturur `samples/hello-world` : `content/write` ve `content/read` .

1. Portalda kapsayıcı Kayıt defterinize gidin.
1. **Depo izinleri** altında **belirteçler (Önizleme) > + Ekle**' yi seçin.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Portalda belirteç oluşturma":::
1. Bir belirteç adı girin.
1. **Kapsam eşlemesi** altında **Yeni oluştur**' u seçin.
1. Kapsam eşlemesini yapılandırma:
    1. Kapsam eşlemesi için bir ad ve açıklama girin. 
    1. **Depolar**' ın altında `samples/hello-world` , ve **izinler** altında, ve ' ı seçin `content/read` `content/write` . Ardından **+ Ekle**' yi seçin.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Portalda kapsam haritası oluşturma":::

    1. Depoları ve izinleri ekledikten sonra, kapsam eşlemesini eklemek için **Ekle** ' yi seçin.
1. **Etkin** ' in varsayılan belirteç **durumunu** kabul edin ve **Oluştur**' u seçin.

Belirteç doğrulandıktan ve oluşturulduktan sonra, **belirteçler** ekranında belirteç ayrıntıları görüntülenir.

### <a name="add-token-password"></a>Belirteç parolası Ekle

Portalda oluşturulan bir belirteci kullanmak için bir parola oluşturmanız gerekir. Bir veya iki parola oluşturabilir ve her biri için bir sona erme tarihi ayarlayabilirsiniz. 

1. Portalda kapsayıcı Kayıt defterinize gidin.
1. **Depo izinleri** altında **belirteçler (Önizleme)** öğesini seçin ve bir belirteç seçin.
1. Belirteç ayrıntılarında **Parola1** veya **paSsWorD2**' yi seçin ve Oluştur simgesini seçin.
1. Parola ekranında, isteğe bağlı olarak parola için bir sona erme tarihi ayarlayın ve **Oluştur**' u seçin. Sona erme tarihi ayarlamanız önerilir.
1. Parola oluşturduktan sonra güvenli bir konuma kopyalayın ve kaydedin. Ekran kapatıldıktan sonra oluşturulan bir parolayı alamazsınız, ancak yeni bir tane oluşturabilirsiniz.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Portalda belirteç parolası oluşturma":::

## <a name="authenticate-with-token"></a>Belirteç ile kimlik doğrulama

Bir kullanıcı veya hizmet, hedef kayıt defteri ile kimlik doğrulaması yapmak için bir belirteç kullandığında, belirteç adını Kullanıcı adı ve oluşturduğu parolalardan biri olarak sağlar. 

Kimlik doğrulama yöntemi, belirteç ile ilişkili eyleme veya eylemlere bağlıdır.

|Eylem  |Kimlik doğrulaması nasıl yapılır?  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` Azure CLı 'da<br/><br/>Örnek: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` Azure CLı 'da<br/><br/>Örnek: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` Azure CLı 'da     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` Azure CLı 'da   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` Azure CLı 'da |

## <a name="examples-use-token"></a>Örnekler: belirteci kullan

Aşağıdaki örneklerde, bir depoda yaygın işlemleri gerçekleştirmek için bu makalenin önceki kısımlarında oluşturulan belirteç kullanılmaktadır: gönderme ve çekme görüntüleri, görüntüleri silme ve havuz etiketlerini listeleme. Belirteç başlangıçta depodaki anında iletme izinleri ( `content/write` ve `content/read` eylemleri) ile ayarlandı `samples/hello-world` .

### <a name="pull-and-tag-test-images"></a>Sınama görüntülerini çekme ve etiketleme

Aşağıdaki örneklerde, `hello-world` `nginx` Microsoft Container Registry 'den ortak ve resim çekin ve bunları kayıt defteriniz ve deponuzla etiketleyerek etiketleyin.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Belirteç kullanarak kimlik doğrulama

`docker login` `az acr login` Görüntüleri göndermek veya çekmek için kayıt defteri ile kimlik doğrulaması yapmak için veya çalıştırın. Kullanıcı adı olarak belirteç adını sağlayın ve parolalarından birini sağlayın. Belirtecin `Enabled` durumu olmalıdır.

Aşağıdaki örnek bash kabuğu için biçimlendirilir ve ortam değişkenlerini kullanarak değerleri sağlar.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Çıkış başarılı kimlik doğrulaması göstermelidir:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Kayıt defterine görüntü gönderme

Başarılı oturum açma işleminden sonra, etiketli görüntüleri kayıt defterine göndermeye çalışır. Belirtecin depoya görüntü gönderme izinleri olduğundan `samples/hello-world` , şu gönderim başarılı olur:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Belirtecin depoya yönelik izinleri yoktur `samples/nginx` , bu nedenle aşağıdaki gönderme denemesi şuna benzer bir hata ile başarısız olur `requested access to the resource is denied` :

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Belirteç izinlerini güncelleştirme

Bir belirtecin izinlerini güncelleştirmek için, ilişkili kapsam eşlemesindeki izinleri güncelleştirin. Güncelleştirilmiş kapsam eşlemesi tüm ilişkili belirteçlere hemen uygulanır. 

Örneğin, `MyToken-scope-map` `content/write` `content/read` depolama üzerinde ve eylemleriyle güncelleştirin `samples/ngnx` ve `content/write` depodaki eylemi kaldırın `samples/hello-world` .  

Azure CLı 'yi kullanmak için, kapsam eşlemesini güncelleştirmek üzere [az ACR Scope-Map Update][az-acr-scope-map-update] ' i çalıştırın:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

Azure portalında:

1. Kapsayıcı Kayıt defterinize gidin.
1. **Depo izinleri** altında **kapsam haritaları ' nı (Önizleme)** seçin ve güncelleştirilecek kapsam eşlemesini seçin.
1. **Depolar**' ın altında `samples/nginx` , ve **izinler** altında, ve ' ı seçin `content/read` `content/write` . Ardından **+ Ekle**' yi seçin.
1. **Depolar**' ın altında, `samples/hello-world` **izinler**' i seçin ve sonra seçimini kaldırın `content/write` . Sonra **Kaydet**'i seçin.

Kapsam eşlemesini güncelleştirdikten sonra, şu gönderim başarılı olur:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Kapsam eşlemesi yalnızca depodaki izne sahip olduğundan `content/read` `samples/hello-world` , depoya şimdi bir gönderme denemesi `samples/hello-world` başarısız olur:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Kapsam eşlemesi her iki depoda da izinler sağladığından, her iki depoya da görüntü çekme başarılı oldu `content/read` :

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Görüntüleri silme

Depoya eylemi ekleyerek kapsam eşlemesini güncelleştirin `content/delete` `nginx` . Bu eylem depodaki görüntülerin silinmesine veya tüm deponun silinmesine izin verir.

Kısaltma için, kapsam eşlemesini güncelleştirmek üzere yalnızca [az ACR Scope-Map Update][az-acr-scope-map-update] komutunu göstereceğiz:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

Portalı kullanarak kapsam eşlemesini güncelleştirmek için [önceki bölüme](#update-token-permissions)bakın.

Depoyu silmek için aşağıdaki [az ACR Repository Delete][az-acr-repository-delete] komutunu kullanın `samples/nginx` . Görüntüleri veya depoları silmek için, belirtecin adını ve parolasını komuta geçirin. Aşağıdaki örnek, makalesinde daha önce oluşturulan ortam değişkenlerini kullanır:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Depo etiketlerini göster 

Depoya eylemi ekleyerek kapsam eşlemesini güncelleştirin `metadata/read` `hello-world` . Bu eylem, depodaki bildirim ve etiket verilerinin okunmasına izin verir.

Kısaltma için, kapsam eşlemesini güncelleştirmek üzere yalnızca [az ACR Scope-Map Update][az-acr-scope-map-update] komutunu göstereceğiz:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

Portalı kullanarak kapsam eşlemesini güncelleştirmek için [önceki bölüme](#update-token-permissions)bakın.

Depodaki meta verileri okumak için `samples/hello-world` [az ACR Repository Show-bildirimleri][az-acr-repository-show-manifests] veya [az ACR Repository Show-Tags][az-acr-repository-show-tags] komutunu çalıştırın. 

Meta verileri okumak için, belirtecin adını ve parolasını her iki komuta de geçirin. Aşağıdaki örnek, makalesinde daha önce oluşturulan ortam değişkenlerini kullanır:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Örnek çıktı:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Belirteçleri ve kapsam haritalarını yönetme

### <a name="list-scope-maps"></a>Kapsam haritalarını Listele

Bir kayıt defterinde yapılandırılmış tüm kapsam haritalarını listelemek için [az ACR Scope-Map List][az-acr-scope-map-list] komutunu veya portaldaki **kapsam haritaları (Önizleme)** ekranını kullanın. Örnek:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Çıktı, sistem tarafından tanımlanan üç kapsam haritasından ve sizin tarafınızdan oluşturulan diğer kapsam eşlemlerinden oluşur. Belirteçler, bu kapsam eşlemelerinden herhangi biriyle yapılandırılabilir.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Belirteç ayrıntılarını göster

Durum ve parola süre sonu tarihleri gibi bir belirtecin ayrıntılarını görüntülemek için [az ACR Token Show][az-acr-token-show] komutunu çalıştırın veya portalda **belirteçler (Önizleme)** ekranında belirteci seçin. Örnek:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Bir kayıt defterinde yapılandırılan tüm belirteçleri listelemek için [az ACR Token List][az-acr-token-list] komutunu veya portalda **belirteçler (Önizleme)** ekranını kullanın. Örnek:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Belirteç parolalarını yeniden oluştur

Belirteç parolası oluşturmadıysanız veya yeni parolalar oluşturmak istiyorsanız [az ACR Token Credential Generate][az-acr-token-credential-generate] komutunu çalıştırın. 

Aşağıdaki örnek, Parola1 for the *Mytoken* token için 30 günlük süre sonu ile yeni bir değer oluşturur. Parolayı ortam değişkeninde depolar `TOKEN_PWD` . Bu örnek bash kabuğu için biçimlendirilir.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Belirteç parolası oluşturmak için Azure portal kullanmak için, bu makalenin önceki kısımlarında yer alarak [belirteç oluşturma-Portal](#create-token---portal) ' daki adımlara bakın.

### <a name="update-token-with-new-scope-map"></a>Belirteci yeni kapsam eşlemesiyle Güncelleştir

Farklı bir kapsam eşlemesine sahip bir belirteci güncelleştirmek istiyorsanız [az ACR Token Update][az-acr-token-update] ' i çalıştırın ve yeni kapsam haritasını belirtin. Örnek:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Portalda, **belirteçler (Önizleme)** ekranında belirteci seçin ve **Kapsam haritası** altında farklı bir kapsam Haritası seçin.

> [!TIP]
> Yeni bir kapsam eşlemesiyle bir belirteci güncelleştirdikten sonra yeni belirteç parolaları oluşturmak isteyebilirsiniz. [Az ACR Token Credential Generate][az-acr-token-credential-generate] komutunu kullanın veya Azure Portal belirteç parolasını yeniden oluşturun.

## <a name="disable-or-delete-token"></a>Belirteci devre dışı bırak veya Sil

Bir kullanıcı veya hizmet için belirteç kimlik bilgilerinin kullanımını geçici olarak devre dışı bırakmanız gerekebilir. 

Azure CLı 'yı kullanarak, şu şekilde ayarlamak için [az ACR Token Update][az-acr-token-update] komutunu çalıştırın `status` `disabled` :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

Portalda **belirteçler (Önizleme)** ekranındaki belirteci seçin ve **durum**' un altında **devre dışı** ' yı seçin.

Kimlik bilgilerini kullanan herkes tarafından erişimi kalıcı olarak geçersiz kılmak üzere bir belirteci silmek için [az ACR Token Delete][az-acr-token-delete] komutunu çalıştırın. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

Portalda **belirteçler (Önizleme)** ekranındaki belirteci seçin ve **at**' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

* Kapsam haritaları ve belirteçleri yönetmek için [az ACR Scope-Map][az-acr-scope-map] ve [az ACR Token][az-acr-token] komut gruplarındaki ek komutları kullanın.
* Azure Active Directory kimliği, hizmet sorumlusu veya yönetici hesabı gibi bir Azure Container Registry ile kimlik doğrulaması yapmak için diğer seçeneklere yönelik [kimlik doğrulamaya genel bakış](container-registry-authentication.md) bölümüne bakın.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az_acr_repository_show_manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az_acr_repository_delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az_acr_scope_map_create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az_acr_scope_map_update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az_acr_token_show
[az-acr-token-list]: /cli/azure/acr/token/#az_acr_token_list
[az-acr-token-delete]: /cli/azure/acr/token/#az_acr_token_delete
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-update]: /cli/azure/acr/token/#az_acr_token_update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az_acr_token_credential_generate

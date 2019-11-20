---
title: OCı yapılarını özel Azure Container Registry 'ye gönderme
description: Azure 'da özel bir kapsayıcı kayıt defteri kullanarak açık kapsayıcı girişimi (OCı) yapılarını gönderme ve çekme
services: container-registry
author: SteveLasker
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.custom: ''
ms.openlocfilehash: 69423f85aecdc3f8049a7e784888e1f71d0bc702
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182716"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Azure Container Registry kullanarak bir OCı yapıtı gönderme ve çekme

[Açık kapsayıcı girişimi (OCI) yapıtlarını](container-registry-image-formats.md#oci-artifacts) ve Docker Ile Docker uyumlu kapsayıcı görüntülerini depolamak ve yönetmek Için bir Azure Container Registry kullanabilirsiniz.

Bu özelliği göstermek için, bu makalede bir örnek yapıtı (bir metin dosyası) Azure Container Registry 'ye göndermek için [OCI kayıt defteri (ORAS)](https://github.com/deislabs/oras) aracının nasıl kullanılacağı gösterilmektedir. Ardından, yapıtı kayıt defterinden çekin. Her yapıya uygun farklı komut satırı araçlarını kullanarak bir Azure Container Registry 'de çeşitli OCı yapılarını yönetebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* **Azure kapsayıcısı kayıt defteri** -Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın.
* **Oras aracı** - [GitHub deposundan](https://github.com/deislabs/oras/releases)işletim sisteminiz için geçerli bir oras sürümü indirin ve yükleyin. Araç, sıkıştırılmış bir tarbol (`.tar.gz` dosya) olarak serbest bırakılır. İşletim sisteminiz için standart yordamları kullanarak dosyayı ayıklayın ve yükledikten sonra.
* **Azure Active Directory hizmet sorumlusu (isteğe bağlı)** -doğrudan kimlik doğrulaması yapmak için, Kayıt defterinize erişmek üzere bir [hizmet sorumlusu](container-registry-auth-service-principal.md) oluşturun. Hizmet sorumlusu ' nın AcrPush gibi bir rol atandığından emin olun ve böylece yapıtlar gönderme ve çekme yapılarına izinleri vardır.
* **Azure CLI (isteğe bağlı)** -tek bir kimlik kullanmak IÇIN Azure CLI 'nin yerel yüklemesi gerekir. Sürüm 2.0.71 veya üzeri önerilir. Sürümü `az --version `bulmak için ' i çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli).
* **Docker (isteğe bağlı)** -tek bir kimlik kullanmak için, kayıt defteriyle kimlik doğrulaması yapmak Için Docker 'ın yerel olarak yüklü olması gerekir. Docker, tüm [MacOS][docker-mac], [Windows][docker-windows]veya [Linux][docker-linux] sistemlerinde Docker 'ı kolayca yapılandıran paketler sağlar.


## <a name="sign-in-to-a-registry"></a>Kayıt defterinde oturum açma

Bu bölümde, kullanılan kimliğe bağlı olarak kayıt defterinde oturum açmak için önerilen iki iş akışı gösterilmektedir. Ortamınız için uygun yöntemi seçin.

### <a name="sign-in-with-oras"></a>ORAS ile oturum açın

Hizmet [sorumlusu](container-registry-auth-service-principal.md) 'nı gönderim haklarıyla kullanarak, hizmet sorumlusu uygulama `oras login` kimliğini ve parolasını kullanarak kayıt defterinde oturum açmak için komutunu çalıştırın. Tam kayıt defteri adını (tümü küçük harf), bu örnekte *myregistry.azurecr.io*olarak belirtin. Hizmet sorumlusu uygulama kimliği, ortam değişkenine `$SP_APP_ID`ve değişkendeki `$SP_PASSWD`parolaya geçirilir.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

STDIN 'den parolayı okumak için kullanın `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Azure CLI'de oturum açma

Kapsayıcı kayıt defterinden yapıtları göndermek ve çekmek için kimliğinizi kullanarak Azure CLı ['Da oturum açın](/cli/azure/authenticate-azure-cli) .

Ardından, kayıt defterine erişmek için [az ACR oturum açma](/cli/azure/acr?view=azure-cli-latest#az-acr-login) Azure CLI komutunu kullanın. Örneğin, *myregistry*adlı bir kayıt defterine kimlik doğrulaması yapmak için:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`, `docker.config` dosyasında Azure Active Directory belirteç ayarlamak için Docker istemcisini kullanır. Tek tek kimlik doğrulama akışının tamamlanabilmesi için Docker istemcisinin yüklü ve çalışıyor olması gerekir.

## <a name="push-an-artifact"></a>Yapıtı gönder

Yerel çalışma çalışma dizininde bazı örnek metinle bir metin dosyası oluşturun. Örneğin, bir bash kabuğu 'nda:

```bash
echo "Here is an artifact!" > artifact.txt
```

Bu metin dosyasını kayıt defterinize göndermek için komutunukullanın.`oras push` Aşağıdaki örnek örnek metin dosyasını `samples/artifact` depoya gönderir. Kayıt defteri, tam olarak nitelenmiş kayıt defteri adı *myregistry.azurecr.io* (tümü küçük harf) ile tanımlanır. Yapıt etiketlendi `1.0`. Yapıtın, varsayılan olarak, dosya adını `artifact.txt`izleyen *medya türü* dizesi tarafından tanımlanan tanımlanmamış bir türü vardır. Ek türler için bkz. [OCI yapıtları](https://github.com/opencontainers/artifacts) . 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

Başarılı bir gönderme için çıkış aşağıdakine benzer:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Kayıt defterinizde yapıtları yönetmek için Azure CLI kullanıyorsanız, görüntüleri yönetmek için standart `az acr` komutları çalıştırın. Örneğin, [az ACR Repository Show][az-acr-repository-show] komutunu kullanarak yapıtın özniteliklerini alın:

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Çıktı aşağıdakine benzer:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Yapıt çekme

Yapıtı kayıt defterinden çekmek için komutunuçalıştırın.`oras pull`

Önce metin dosyasını yerel çalışma dizininden kaldırın:

```bash
rm artifact.txt
```

Yapıtı çekmek ve yapıtı göndermek için kullanılan medya türünü belirtmek için öğesini çalıştırın `oras pull` :

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Çekin başarılı olduğunu doğrulayın:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Yapıtı Kaldır (isteğe bağlı)

Yapıtı Azure Container Registry 'nizden kaldırmak için [az ACR Repository Delete][az-acr-repository-delete] komutunu kullanın. Aşağıdaki örnek, orada depoladığınız yapıtı kaldırır:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Sonraki adımlar

* Yapıt için bildirim yapılandırma dahil olmak üzere [ORAS kitaplığı](https://github.com/deislabs/oras/tree/master/docs)hakkında daha fazla bilgi edinin
* Yeni yapıt türleri hakkında başvuru bilgileri için [OCI yapay yapılarını](https://github.com/opencontainers/artifacts) ziyaret edin



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete

---
title: Gönderim ve çekme OCı yapıtı
description: Azure 'da özel bir kapsayıcı kayıt defteri kullanarak açık kapsayıcı girişimi (OCı) yapılarını gönderme ve çekme
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 399bb001432759556cd0ba8bf15f7738dd4edb7c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781496"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Azure Container Registry kullanarak bir OCı yapıtı gönderme ve çekme

[Açık kapsayıcı girişimi (OCI) yapıtlarını](container-registry-image-formats.md#oci-artifacts) ve Docker Ile Docker uyumlu kapsayıcı görüntülerini depolamak ve yönetmek Için bir Azure Container Registry kullanabilirsiniz.

Bu özelliği göstermek için, bu makalede bir örnek yapıtı (bir metin dosyası) Azure Container Registry 'ye göndermek için [OCI kayıt defteri (ORAS)](https://github.com/deislabs/oras) aracının nasıl kullanılacağı gösterilmektedir. Ardından, yapıtı kayıt defterinden çekin. Her yapıya uygun farklı komut satırı araçlarını kullanarak bir Azure Container Registry 'de çeşitli OCı yapılarını yönetebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* **Azure kapsayıcısı kayıt defteri** -Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın.
* **Oras aracı** - [GitHub deposundan](https://github.com/deislabs/oras/releases)işletim sisteminiz için geçerli bir oras sürümü indirin ve yükleyin. Araç, sıkıştırılmış bir tarbol (dosya) olarak serbest bırakılır `.tar.gz` . İşletim sisteminiz için standart yordamları kullanarak dosyayı ayıklayın ve yükledikten sonra.
* **Azure Active Directory hizmet sorumlusu (isteğe bağlı)** -doğrudan kimlik doğrulaması yapmak için, Kayıt defterinize erişmek üzere bir [hizmet sorumlusu](container-registry-auth-service-principal.md) oluşturun. Hizmet sorumlusu ' nın AcrPush gibi bir rol atandığından emin olun ve böylece yapıtlar gönderme ve çekme yapılarına izinleri vardır.
* **Azure CLI (isteğe bağlı)** -tek bir kimlik kullanmak IÇIN Azure CLI 'nin yerel yüklemesi gerekir. Sürüm 2.0.71 veya üzeri önerilir. `az --version `Sürümü bulmak için ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).
* **Docker (isteğe bağlı)** -tek bir kimlik kullanmak için, kayıt defteriyle kimlik doğrulaması yapmak Için Docker 'ın yerel olarak yüklü olması gerekir. Docker [macOS][docker-mac], [Windows][docker-windows] veya [Linux][docker-linux]'ta Docker'ı kolayca yapılandırmanızı sağlayan paketler sağlar.


## <a name="sign-in-to-a-registry"></a>Kayıt defterinde oturum açma

Bu bölümde, kullanılan kimliğe bağlı olarak kayıt defterinde oturum açmak için önerilen iki iş akışı gösterilmektedir. Ortamınız için uygun yöntemi seçin.

### <a name="sign-in-with-oras"></a>ORAS ile oturum açın

Hizmet [sorumlusu](container-registry-auth-service-principal.md) 'nı gönderim haklarıyla kullanarak, `oras login` HIZMET sorumlusu uygulama kimliğini ve parolasını kullanarak kayıt defterinde oturum açmak için komutunu çalıştırın. Tam kayıt defteri adını (tümü küçük harf), bu örnekte *myregistry.azurecr.io* olarak belirtin. Hizmet sorumlusu uygulama KIMLIĞI, ortam değişkenine `$SP_APP_ID` ve değişkendeki parolaya geçirilir `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

STDIN 'den parolayı okumak için kullanın `--password-stdin` .

### <a name="sign-in-with-azure-cli"></a>Azure CLI'de oturum açma

Kapsayıcı kayıt defterinden yapıtları göndermek ve çekmek için kimliğinizi kullanarak Azure CLı ['Da oturum açın](/cli/azure/authenticate-azure-cli) .

Ardından, kayıt defterine erişmek için [az ACR oturum açma](/cli/azure/acr#az_acr_login) Azure CLI komutunu kullanın. Örneğin, *myregistry* adlı bir kayıt defterine kimlik doğrulaması yapmak için:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` , dosyasında Azure Active Directory belirteç ayarlamak için Docker istemcisini kullanır `docker.config` . Tek tek kimlik doğrulama akışının tamamlanabilmesi için Docker istemcisinin yüklü ve çalışıyor olması gerekir.

## <a name="push-an-artifact"></a>Yapıtı gönder

Yerel çalışma çalışma dizininde bazı örnek metinle bir metin dosyası oluşturun. Örneğin, bir bash kabuğu 'nda:

```bash
echo "Here is an artifact" > artifact.txt
```

`oras push`Bu metin dosyasını kayıt defterinize göndermek için komutunu kullanın. Aşağıdaki örnek örnek metin dosyasını `samples/artifact` depoya gönderir. Kayıt defteri, tam olarak nitelenmiş kayıt defteri adı *myregistry.azurecr.io* (tümü küçük harf) ile tanımlanır. Yapıt etiketlendi `1.0` . Yapıtın, varsayılan olarak, dosya adını izleyen *medya türü* dizesi tarafından tanımlanan tanımlanmamış bir türü vardır `artifact.txt` . Ek türler için bkz. [OCI yapıtları](https://github.com/opencontainers/artifacts) . 

**Linux veya macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Başarılı bir gönderme için çıkış aşağıdakine benzer:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Kayıt defterinizde yapıtları yönetmek için Azure CLı kullanıyorsanız, `az acr` görüntüleri yönetmek için standart komutları çalıştırın. Örneğin, [az ACR Repository Show][az-acr-repository-show] komutunu kullanarak yapıtın özniteliklerini alın:

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

`oras pull`Yapıtı kayıt defterinden çekmek için komutunu çalıştırın.

Önce metin dosyasını yerel çalışma dizininden kaldırın:

```bash
rm artifact.txt
```

`oras pull`Yapıtı çekmek ve yapıtı göndermek için kullanılan medya türünü belirtmek için öğesini çalıştırın:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Çekin başarılı olduğunu doğrulayın:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Yapıtı Kaldır (isteğe bağlı)

Yapıtı Azure Container Registry 'nizden kaldırmak için [az ACR Repository Delete][az-acr-repository-delete] komutunu kullanın. Aşağıdaki örnek, orada depoladığınız yapıtı kaldırır:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Örnek: OCı yapıtınızdan Docker görüntüsü oluşturma

Bir kapsayıcı görüntüsü derlemek için kaynak kodu ve ikili dosyalar, bir Azure Container Registry 'de OCı yapıtları olarak depolanabilir. Bir [ACR görevi](container-registry-tasks-overview.md)için derleme bağlamı olarak bir kaynak yapıtı başvurabilirsiniz. Bu örnek, bir Dockerfile dosyasının bir OCı yapıtı olarak nasıl depolanacağını ve ardından bir kapsayıcı görüntüsü oluşturmak için yapıya nasıl başvurulacağını gösterir.

Örneğin, tek satırlık bir Dockerfile oluşturun:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Hedef kapsayıcı kayıt defterinde oturum açın.

```azurecli
az login
az acr login --name myregistry
```

Komutunu kullanarak hedef kayıt defterine yeni bir OCı yapıtı oluşturun ve gönderin `oras push` . Bu örnek, yapıt için varsayılan medya türünü ayarlar.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Yeni yapıyı derleme bağlamı olarak kullanarak Hello-World görüntüsünü oluşturmak için [az ACR Build](/cli/azure/acr#az_acr_build) komutunu çalıştırın:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Sonraki adımlar

* Yapıt için bildirim yapılandırma dahil olmak üzere [ORAS kitaplığı](https://github.com/deislabs/oras/tree/master/docs)hakkında daha fazla bilgi edinin
* Yeni yapıt türleri hakkında başvuru bilgileri için [OCI yapay yapılarını](https://github.com/opencontainers/artifacts) ziyaret edin



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete

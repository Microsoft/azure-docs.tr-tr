---
title: Docker görüntüsünü özel Azure Container Registry 'ye gönderme
description: Docker CLI’yı kullanarak Azure’da özel bir kapsayıcı kayıt defterine Docker görüntüleri itme ve kapsayıcıdan görüntü çekme
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6944755619ea5e8e63af04b9b3bca6f7376e29a9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309438"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Docker CLI’yı kullanarak özel bir Dockler kapsayıcı kayıt defterine ilk görüntünüzü itme

Azure kapsayıcısı kayıt defteri, [Docker Hub](https://hub.docker.com/)’ın genel Docker görüntülerini depolama yöntemine benzer şekilde özel [Docker](https://hub.docker.com) kapsayıcı görüntülerini depolar ve yönetir. Kapsayıcı kayıt defterinizde [oturum açma](https://docs.docker.com/engine/reference/commandline/login/), [gönderim](https://docs.docker.com/engine/reference/commandline/push/), [çekme](https://docs.docker.com/engine/reference/commandline/pull/)ve diğer işlemler için [DOCKER komut satırı arabirimini](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) kullanabilirsiniz.

Aşağıdaki adımlarda, genel Docker Hub kayıt defterinden resmi bir [NGINX görüntüsü](https://store.docker.com/images/nginx) indirir, özel Azure Container kayıt defteriniz için etiketleyerek Kayıt defterinize gönderirsiniz ve sonra kayıt defterinden çekebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* **Azure kapsayıcısı kayıt defteri** -Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın.
* **DOCKER CLI** -Ayrıca Docker 'ın yerel olarak yüklü olması gerekir. Docker, tüm [MacOS][docker-mac], [Windows][docker-windows]veya [Linux][docker-linux] sistemlerinde Docker 'ı kolayca yapılandıran paketler sağlar.

## <a name="log-in-to-a-registry"></a>Kayıt defterinde oturum açma

Özel kapsayıcı kayıt defterinizde [kimlik doğrulamanın birkaç yolu](container-registry-authentication.md) vardır. Komut satırında çalışırken önerilen yöntem, [az ACR Login](/cli/azure/acr?view=azure-cli-latest#az-acr-login)Azure CLI komutuna sahip olur. Örneğin, *myregistry*adlı bir kayıt defterinde oturum açmak için:

```azurecli
az acr login --name myregistry
```

[Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/)ile de oturum açabilirsiniz. Örneğin, bir Otomasyon senaryosunda Kayıt defterinize [bir hizmet sorumlusu atamış](container-registry-authentication.md#service-principal) olabilirsiniz. Aşağıdaki komutu çalıştırdığınızda, istendiğinde hizmet sorumlusu AppID (Kullanıcı adı) ve parolayı etkileşimli olarak sağlayın. Oturum açma kimlik bilgilerini yönetmek için en iyi uygulamalar için bkz. [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) komut başvurusu:

```
docker login myregistry.azurecr.io
```

Her iki komut `Login Succeeded` de tamamlandığında döndürülür.

> [!TIP]
> `docker login` ' İ kullanırken ve Kayıt defterinize göndermek üzere resimleri etiketlediğinizde, her zaman tam olarak nitelenmiş kayıt defteri adını (tümü küçük harf) belirtin. Bu makaledeki örneklerde, tam nitelikli ad *myregistry.azurecr.io*' dir.

## <a name="pull-the-official-nginx-image"></a>Resmi NGINX görüntüsünü çekme

İlk olarak, genel NGINX görüntüsünü yerel bilgisayarınıza çekin.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Kapsayıcıyı yerel olarak çalıştırma

8080 numaralı bağlantı noktasında NGINX kapsayıcısının etkileşimli (`-it`) yerel bir örneğini başlatmak için aşağıdaki [Docker Run](https://docs.docker.com/engine/reference/run/) komutunu yürütün. `--rm` Bağımsız değişkeni kapsayıcıyı durdurduğunuzda kaldırılması gerektiğini belirtir.

```
docker run -it --rm -p 8080:80 nginx
```

Çalışan kapsayıcıda `http://localhost:8080` NGINX tarafından sunulan varsayılan Web sayfasını görüntülemek için öğesine gidin. Aşağıdakine benzer bir sayfa görmeniz gerekir:

![Yerel bilgisayarda Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

Kapsayıcıyı ile `-it`etkileşimli olarak başlattığınız için, tarayıcınızda gezindikten sonra, komut satırında NGINX sunucusunun çıkışını görebilirsiniz.

Kapsayıcıyı durdurmak ve kaldırmak için tuşuna basın `Control`. + `C`

## <a name="create-an-alias-of-the-image"></a>Görüntünün diğer adını oluşturma

Kayıt defterinizin tam yolunu içeren görüntünün diğer adını oluşturmak için [Docker Tag](https://docs.docker.com/engine/reference/commandline/tag/) ' i kullanın. Bu örnek, kayıt defterinin kökünde dağınıklığı önlemek için `samples` ad alanını belirtir.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Ad alanları ile etiketleme hakkında daha fazla bilgi için, [Azure Container Registry Için en iyi yöntemlerin](container-registry-best-practices.md) [Depo ad alanları](container-registry-best-practices.md#repository-namespaces) bölümüne bakın.

## <a name="push-the-image-to-your-registry"></a>Görüntüyü Kayıt defterinize gönderin

Görüntüyü özel kayıt defterinizin tam yoluna etiketledikten sonra [Docker Push](https://docs.docker.com/engine/reference/commandline/push/)ile kayıt defterine gönderebilirsiniz:

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Görüntüyü Kayıt defterinizden çekin

Görüntüyü kayıt defterinden çekmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>NGINX kapsayıcısını başlatma

Kayıt defterinizden çekolduğunuz görüntüyü çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/run/) komutunu kullanın:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Çalışan kapsayıcıyı `http://localhost:8080` görüntülemek için öğesine gidin.

Kapsayıcıyı durdurmak ve kaldırmak için tuşuna basın `Control`. + `C`

## <a name="remove-the-image-optional"></a>Görüntüyü kaldırma (isteğe bağlı)

Artık NGINX görüntüsüne ihtiyacınız yoksa [Docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) komutuyla yerel olarak silebilirsiniz.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Azure Container Registry 'nizden görüntüleri kaldırmak için [az ACR Repository Delete](/cli/azure/acr/repository#az-acr-repository-delete)Azure CLI komutunu kullanabilirsiniz. Örneğin, aşağıdaki komut `samples/nginx:latest` etiket tarafından başvurulan bildirimi, tüm benzersiz katman verilerini ve bildirime başvuran diğer tüm etiketleri siler.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Sonraki adımlar

Temel bilgileri öğrenmiş olduğunuza göre artık kayıt defterinizi kullanmaya başlamaya hazırsınız demektir! Örneğin, Kayıt defterinizden kapsayıcı görüntülerini şu şekilde dağıtın:

* [Azure Kubernetes Service'i (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

İsteğe bağlı olarak [Visual Studio Code Için Docker uzantısını](https://code.visualstudio.com/docs/azure/docker) ve Azure Container Registry 'larınız ile birlikte çalışmak Için [Azure hesap](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) uzantısını yükler. Azure Container Registry 'ye görüntü çekme ve gönderme veya ACR görevlerini Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

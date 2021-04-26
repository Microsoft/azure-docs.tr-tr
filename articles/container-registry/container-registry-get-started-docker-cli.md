---
title: Çekme & istek kapsayıcı görüntüsü gönder
description: Docker CLı kullanarak Azure 'daki özel kapsayıcı Kayıt defterinize Docker görüntüleri gönderme ve çekme
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 48f5f1707881ac8461e12212be631d3b80c16ca7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783836"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Docker CLı kullanarak ilk görüntünüzü Azure Container Registry 'nize gönderin

Azure Container Registry, [Docker Hub 'ın](https://hub.docker.com/) ortak Docker kapsayıcı görüntülerini depoladığı yönteme benzer şekilde özel kapsayıcı görüntülerini ve diğer yapıtları depolar ve yönetir. Kapsayıcı kayıt defterinizde [oturum açma](https://docs.docker.com/engine/reference/commandline/login/), [gönderim](https://docs.docker.com/engine/reference/commandline/push/), [çekme](https://docs.docker.com/engine/reference/commandline/pull/)ve diğer kapsayıcı görüntüsü işlemleri için [DOCKER komut satırı arabirimini](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) kullanabilirsiniz.

Aşağıdaki adımlarda, bir genel [NGINX görüntüsünü](https://store.docker.com/images/nginx)indirir, özel Azure Container kayıt defteriniz için etiketleyerek, Kayıt defterinize gönderirsiniz ve sonra kayıt defterinden çekolursunuz.

## <a name="prerequisites"></a>Önkoşullar

* **Azure kapsayıcısı kayıt defteri** -Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın.
* **DOCKER CLI** -Ayrıca Docker 'ın yerel olarak yüklü olması gerekir. Docker [macOS][docker-mac], [Windows][docker-windows] veya [Linux][docker-linux]'ta Docker'ı kolayca yapılandırmanızı sağlayan paketler sağlar.

## <a name="log-in-to-a-registry"></a>Kayıt defterinde oturum açma

Özel kapsayıcı kayıt defterinizde [kimlik doğrulamanın birkaç yolu](container-registry-authentication.md) vardır. Komut satırında çalışırken önerilen yöntem, [az ACR Login](/cli/azure/acr#az_acr_login)Azure CLI komutuna sahip olur. Örneğin, *myregistry* adlı bir kayıt defterinde oturum açmak IÇIN Azure CLI 'da oturum açın ve kayıt defterinizde kimlik doğrulaması yapın:

```azurecli
az login
az acr login --name myregistry
```

[Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/)ile de oturum açabilirsiniz. Örneğin, bir Otomasyon senaryosunda Kayıt defterinize [bir hizmet sorumlusu atamış](container-registry-authentication.md#service-principal) olabilirsiniz. Aşağıdaki komutu çalıştırdığınızda, istendiğinde hizmet sorumlusu AppID (Kullanıcı adı) ve parolayı etkileşimli olarak sağlayın. Oturum açma kimlik bilgilerini yönetmek için en iyi uygulamalar için bkz. [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) komut başvurusu:

```
docker login myregistry.azurecr.io
```

Her iki komut de `Login Succeeded` tamamlandığında döndürülür.
> [!NOTE]
>* Daha hızlı ve daha uygun bir oturum açma için Docker uzantılı Visual Studio Code kullanmak isteyebilirsiniz.

> [!TIP]
> `docker login`' İ kullanırken ve Kayıt defterinize göndermek üzere resimleri etiketlediğinizde, her zaman tam olarak nitelenmiş kayıt defteri adını (tümü küçük harf) belirtin. Bu makaledeki örneklerde, tam nitelikli ad *myregistry.azurecr.io*' dir.

## <a name="pull-a-public-nginx-image"></a>Genel NGINX görüntüsü çekme

İlk olarak, yerel bilgisayarınıza ortak bir NGINX görüntüsü çekin. Bu örnek, Microsoft Container Registry bir görüntü çeker.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Kapsayıcıyı yerel olarak çalıştırma

8080 numaralı bağlantı noktasında NGINX kapsayıcısının etkileşimli () yerel bir örneğini başlatmak için aşağıdaki [Docker Run](https://docs.docker.com/engine/reference/run/) komutunu yürütün `-it` . `--rm`Bağımsız değişkeni kapsayıcıyı durdurduğunuzda kaldırılması gerektiğini belirtir.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

`http://localhost:8080`Çalışan kapsayıcıda NGINX tarafından sunulan varsayılan Web sayfasını görüntülemek için öğesine gidin. Aşağıdakine benzer bir sayfa görmeniz gerekir:

![Yerel bilgisayarda Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

Kapsayıcıyı ile etkileşimli olarak başlattığınız `-it` için, tarayıcınızda gezindikten sonra, komut satırında NGINX sunucusunun çıkışını görebilirsiniz.

Kapsayıcıyı durdurmak ve kaldırmak için tuşuna basın `Control` + `C` .

## <a name="create-an-alias-of-the-image"></a>Görüntünün diğer adını oluşturma

Kayıt defterinizin tam yolunu içeren görüntünün diğer adını oluşturmak için [Docker Tag](https://docs.docker.com/engine/reference/commandline/tag/) ' i kullanın. Bu örnek, kayıt defterinin kökünde dağınıklığı önlemek için `samples` ad alanını belirtir.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Ad alanları ile etiketleme hakkında daha fazla bilgi için, [Azure Container Registry Için en iyi yöntemlerin](container-registry-best-practices.md) [Depo ad alanları](container-registry-best-practices.md#repository-namespaces) bölümüne bakın.

## <a name="push-the-image-to-your-registry"></a>Görüntüyü kayıt defterinize itme

Görüntüyü özel kayıt defterinizin tam yoluna etiketledikten sonra [Docker Push](https://docs.docker.com/engine/reference/commandline/push/)ile kayıt defterine gönderebilirsiniz:

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Görüntüyü kayıt defterinizden çekme

Görüntüyü kayıt defterinden çekmek için [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) komutunu kullanın:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Nginx kapsayıcısını başlatma

Kayıt defterinizden çekolduğunuz görüntüyü çalıştırmak için [Docker Run](https://docs.docker.com/engine/reference/run/) komutunu kullanın:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

`http://localhost:8080`Çalışan kapsayıcıyı görüntülemek için öğesine gidin.

Kapsayıcıyı durdurmak ve kaldırmak için tuşuna basın `Control` + `C` .

## <a name="remove-the-image-optional"></a>Görüntüyü kaldırma (isteğe bağlı)

Artık NGINX görüntüsüne ihtiyacınız yoksa [Docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) komutuyla yerel olarak silebilirsiniz.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Azure Container Registry 'nizden görüntüleri kaldırmak için [az ACR Repository Delete](/cli/azure/acr/repository#az_acr_repository_delete)Azure CLI komutunu kullanabilirsiniz. Örneğin, aşağıdaki komut etiket tarafından başvurulan bildirimi `samples/nginx:latest` , tüm benzersiz katman verilerini ve bildirime başvuran diğer tüm etiketleri siler.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Sonraki adımlar

Temel bilgileri öğrenmiş olduğunuza göre artık kayıt defterinizi kullanmaya başlamaya hazırsınız demektir! Örneğin, Kayıt defterinizden kapsayıcı görüntülerini şu şekilde dağıtın:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

İsteğe bağlı olarak [Visual Studio Code Için Docker uzantısını](https://code.visualstudio.com/docs/azure/docker) ve Azure Container Registry 'larınız ile birlikte çalışmak Için [Azure hesap](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) uzantısını yükler. Azure Container Registry 'ye görüntü çekme ve gönderme veya ACR görevlerini Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

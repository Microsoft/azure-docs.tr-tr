---
title: Öğretici-kapsayıcı kayıt defterini görüntü dağıtmak için hazırlama
description: Azure Container Instances öğreticisi Bölüm 2/3-bir Azure Container Registry hazırlama ve görüntü gönderme
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 36c2e6cda728a85ccab080bbbb6f1a0b74824f2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786990"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Öğretici: Azure Container Registry oluşturma ve kapsayıcı görüntüsü gönderme

Bu öğretici, üç bölümden oluşan bir serinin ikinci bölümüdür. Öğreticinin [birinci bölümünde](container-instances-tutorial-prepare-app.md), Node.js web uygulaması için bir Docker kapsayıcı görüntüsü oluşturuldu. Bu öğreticide, görüntüyü Azure Container Registry’ye göndereceksiniz. Henüz kapsayıcı görüntüsünü oluşturmadıysanız [Öğretici 1 - Kapsayıcı görüntüsü oluşturma](container-instances-tutorial-prepare-app.md) bölümüne geri dönün.

Azure Container Registry sizin Azure’daki özel Docker kayıt defterinizdir. Serinin ikinci kısmı olan bu öğreticide şunları yapabilirsiniz:

> [!div class="checklist"]
> * Azure CLı ile Azure Container Registry örneği oluşturma
> * Azure kapsayıcı kayıt defteriniz için bir kapsayıcı görüntüsü etiketleyeceksiniz
> * Görüntüyü kayıt defterinize yükleyeceksiniz

Serinin son öğreticisi olan sonraki makalede, özel kayıt defterinizdeki kapsayıcıyı Azure Container Instances’a dağıtacaksınız.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>Kapsayıcı görüntüsünü etiketleme

Azure Container Registry gibi bir özel kayıt defterine kapsayıcı görüntüsü göndermek için önce görüntüyü kayıt defterinin oturum açma sunucusunun tam adıyla etiketlemeniz gerekir.

İlk olarak Azure kapsayıcı kayıt defteriniz için tam oturum açma sunucu adını alın. Aşağıdaki [az acr show][az-acr-show] komutunu çalıştırın ve `<acrName>` değerini, yeni oluşturduğunuz kayıt defterinin adıyla değiştirin:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Örneğin, kayıt defterinizin adı *mycontainerregistry082* ise:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

Şimdi [docker images][docker-images] komutunu kullanarak yerel görüntülerinizin listesini görüntüleyin:

```bash
docker images
```

Makinenizdeki diğer görüntülerle birlikte, [önceki öğreticide](container-instances-tutorial-prepare-app.md) derlediğiniz *aci-tutorial-app* görüntüsünü görmeniz gerekir:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

*Aci-öğreticisi-App* görüntüsünü kapsayıcı kayıt defterinizin oturum açma sunucusu ile etiketleyin. Ayrıca görüntü sürüm numarasını belirtmek için görüntü adının sonuna `:v1` etiketini ekleyin. `<acrLoginServer>` değerini, az önce çalıştırdığınız [az acr show][az-acr-show] komutunun sonucuyla değiştirin.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Etiketleme işlemini doğrulamak için tekrar `docker images` komutunu çalıştırın:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry’ye görüntü gönderme

*Aci-öğreticisi-App* görüntüsünü özel kayıt defterinizin tam oturum açma sunucusu adıyla etiketledikten sonra, [Docker Push][docker-push] komutuyla görüntüyü kayıt defterine gönderebilirsiniz. `<acrLoginServer>` değerini, önceki adımda aldığınız tam oturum açma sunucusu adıyla değiştirin.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push` işlemi, internet bağlantınıza bağlı olarak birkaç saniye ile birkaç dakika arasında sürebilir ve çıktı şuna benzer:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Azure Container Registry’deki görüntüleri listeleme

Gönderdiğiniz görüntünün aslında Azure kapsayıcı kayıt defterinizde olduğunu doğrulamak için, [az acr repository list][az-acr-repository-list] komutunu kullanarak kayıt defterinizdeki görüntüleri listeleyin. `<acrName>` komutunu, kapsayıcı kayıt defterinizin adıyla değiştirin.

```azurecli
az acr repository list --name <acrName> --output table
```

Örnek:

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

Belirli bir görüntünün *etiketlerini* görmek için, [az acr repository show-tags][az-acr-repository-show-tags] komutunu kullanın.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Aşağıdakine benzer bir çıktı görmeniz gerekir:

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Container Instances ile kullanım için bir Azure kapsayıcı kayıt defteri hazırladınız ve bu kayıt defterine bir kapsayıcı görüntüsü gönderdiniz. Aşağıdaki adımlar tamamlandı:

> [!div class="checklist"]
> * Azure CLı ile Azure Container Registry örneği oluşturma
> * Azure Container Registry için bir kapsayıcı görüntüsü etiketlendi
> * Azure Container Registry’ye görüntü yüklendi

Azure Container Instances kullanarak Azure’a kapsayıcıyı dağıtma hakkında bilgi edinmek için sonraki öğreticiye geçin:

> [!div class="nextstepaction"]
> [Azure Container Instances‘a kapsayıcı dağıtma](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli

---
title: Azure'da Kubernetes öğreticisi - Kapsayıcı kayıt defteri oluşturma
description: Bu Azure Kubernetes Service (AKS) öğreticisinde bir Azure Container Registry örneği oluşturacak ve kapsayıcı görüntüsüne örnek uygulama yükleyeceksiniz.
services: container-service
ms.topic: tutorial
ms.date: 01/31/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd53fab577797ad8bfdbf29b4a6d219e61ee3ef4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764270"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Öğretici: Azure Container Registry’yi dağıtma ve kullanma

Azure Container Registry (ACR), kapsayıcı görüntüleri için özel bir kayıt defteridir. Özel kapsayıcı kayıt defteri, uygulamalarınızı ve özel kodlarınızı güvenli bir şekilde derlemenizi ve dağıtmanızı sağlar. Yedi öğreticiden oluşan bu serinin ikinci kısmında, bir ACR örneği dağıtacak ve ona bir kapsayıcı görüntüsü göndereceksiniz. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Azure Container Registry (ACR) örneği oluşturma
> * ACR için kapsayıcı görüntüsü etiketleme
> * Görüntüyü ACR’ye yükleme
> * Kayıt defterinizdeki görüntüleri görüntüleme

Sonraki öğreticilerde, bu ACR örneği AKS 'teki bir Kubernetes kümesiyle tümleşiktir ve görüntüden bir uygulama dağıtılır.

## <a name="before-you-begin"></a>Başlamadan önce

[Önceki öğreticide][aks-tutorial-prepare-app], basit bir Azure Voting uygulaması için kapsayıcı görüntüsü oluşturulacaktır. Azure Voting uygulaması görüntüsünü oluşturmadıysanız [Öğretici 1 - Kapsayıcı görüntüleri oluştur][aks-tutorial-prepare-app]’a dönün.

Bu öğreticide, Azure CLı sürüm 2.0.53 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry oluşturma

Bir Azure Container Registry oluşturmak için önce bir kaynak grubuna ihtiyaç duyarsınız. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[az group create][az-group-create] komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnekte, *eastus* bölgesinde *myResourceGroup* adlı bir kaynak grubu oluşturulur:

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create][az-acr-create] komutuyla bir Azure Container Registry örneği oluşturun ve bir kayıt defteri adı belirleyin. Kaynak defteri adı Azure’da benzersiz olmalı ve 5-50 arası alfasayısal karakter içermelidir. Bu öğreticinin geri kalan aşamalarında, kapsayıcı kayıt defteri adı için yer tutucu olarak `<acrName>` kullanılacaktır. Kendi benzersiz kayıt defteriniz adını sağlayın. *Temel* SKU, geliştirme amaçlı dağıtımlar için uygun maliyetli, depolama ve aktarım hızı açısından dengeli bir giriş noktasıdır.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Kapsayıcı kayıt defterinde oturum açma

ACR örneğini kullanmak için oturum açmanız gerekir. [az acr login][az-acr-login] komutunu kullanarak önceki adımda kapsayıcı kayıt defterine verdiğiniz benzersiz adı belirtin.

```azurecli
az acr login --name <acrName>
```

Komut tamamlandığında bir *oturum açma başarılı* iletisi döndürür.

## <a name="tag-a-container-image"></a>Kapsayıcı görüntüsünü etiketleme

Mevcut yerel görüntülerinizin listesini görüntülemek için [docker images][docker-images] komutunu kullanın:

```console
docker images
```
Yukarıdaki komutun çıktısı, geçerli yerel görüntülerinizin listesini gösterir:

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

*azure-vote-front* kapsayıcı görüntüsünü ACR ile birlikte kullanmak için görüntünün, kayıt defterinizin oturum açma sunucusunun adresiyle etiketlenmesi gerekir. Bu etiket, görüntü kayıt defterine kapsayıcı görüntüleri gönderilirken kullanılır.

Oturum açma sunucusunun adresini almak için aşağıda gösterilen şekilde [az acr list][az-acr-list] komutunu kullanın ve *loginServer* sorgusunu gerçekleştirin:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Şimdi, yerel *Azure-oyön* görüntünüzü kapsayıcı kayıt defterinin *Acrloginserver* adresiyle etiketleyin. Görüntü sürümünü belirtmek için görüntü adının sonuna *:v1* ekleyin:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

Etiketlerin uygulandığını doğrulamak için [docker images][docker-images] komutunu yeniden çalıştırın.

```console
docker images
```

Böylece görüntü, ACR örneği adresi ve sürüm numarasıyla etiketlenmiş olur.

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>Kayıt defterine görüntü gönderme

Görüntünüz oluşturulup etiketledikten sonra, *Azure-oy ön* görüntüsünü ACR örneğinizle gönderin. [docker push][docker-push] komutunu kullanın ve görüntü adı olarak aşağıda gösterilen şekilde kendi *acrLoginServer* adresinizi belirtin:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Görüntünün ACR'ye gönderilmesi birkaç dakika sürebilir.

## <a name="list-images-in-registry"></a>Kayıt defterindeki görüntüleri listeleme

ACR örneğinize gönderilen görüntülerin listesini döndürmek için [az acr repository list][az-acr-repository-list] komutunu kullanın. Aşağıda gösterilen şekilde kendi `<acrName>` değerinizi belirtin:

```azurecli
az acr repository list --name <acrName> --output table
```

Aşağıdaki örnek çıktıda *azure-vote-front* görüntüsünün kayıt defterinde kullanılabilir durumda olduğu gösterilmektedir:

```output
Result
----------------
azure-vote-front
```

Belirli bir görüntünün etiketlerini görmek için aşağıdaki gibi [az acr repository show-tags][az-acr-repository-show-tags] komutunu kullanın:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Aşağıdaki örnekte önceki adımda etiketlenen *v1* görüntüsü gösterilmektedir:

```output
Result
--------
v1
```

Artık özel bir Azure Container Registry örneğinde depolanmış olan bir kapsayıcı görüntünüz var. Bir sonraki öğreticide bu görüntüyü, ACR’den bir Kubernetes kümesine dağıtacaksınız.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure Container Service oluşturduğunuz ve AKS kümesinde kullanılmak üzere bir görüntü gönderdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure Container Registry (ACR) örneği oluşturma
> * ACR için kapsayıcı görüntüsü etiketleme
> * Görüntüyü ACR’ye yükleme
> * Kayıt defterinizdeki görüntüleri görüntüleme

Azure’da Kubernetes kümesi dağıtmayla ilgili daha fazla bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Kubernetes kümesi dağıtma][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-list]: /cli/azure/acr#az_acr_list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md

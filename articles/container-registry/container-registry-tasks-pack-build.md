---
title: Cloud Native Buildpack ile görüntü oluşturma
description: Bir uygulamadan kapsayıcı görüntüsü oluşturmak için az ACR Pack Build komutunu kullanın ve Dockerfile kullanmadan Azure Container Registry gönderin.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1700c8fda8ac91e7d447d35c0989da2d5fc3aefe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780938"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Cloud Native Buildpack kullanarak bir uygulamadan görüntü oluşturma ve gönderme

Azure CLı komutu, `az acr pack build` [`pack`](https://github.com/buildpack/pack) [buildpacks](https://buildpacks.io/)'ten bir uygulama oluşturmak ve görüntüsünü bir Azure Container Registry 'ye göndermek için CLI aracını kullanır. Bu özellik, bir Dockerfile tanımlamak zorunda kalmadan Node.js, Java ve diğer dillerdeki uygulama kaynak kodunuzda bir kapsayıcı görüntüsünü hızlıca oluşturmak için bir seçenek sunar.

Bu makaledeki örnekleri çalıştırmak için Azure CLı 'nın Azure Cloud Shell veya yerel bir yüklemesini kullanabilirsiniz. Yerel olarak kullanmak isterseniz, 2.0.70 veya üzeri sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="use-the-build-command"></a>Build komutunu kullanma

Cloud Native Buildpacks kullanarak bir kapsayıcı görüntüsü derlemek ve göndermek için [az ACR Pack Build][az-acr-pack-build] komutunu çalıştırın. [Az ACR Build][az-acr-build] komutu, bir Dockerfile kaynağından ve ilgili koddan `az acr pack build` doğrudan bir uygulama kaynak ağacı belirttiğinizde bir görüntü oluşturur ve gönderir.

En azından, çalıştırdığınızda şunları belirtin `az acr pack build` :

* Komutu çalıştırdığınız bir Azure Container kayıt defteri
* Elde edilen görüntü için bir görüntü adı ve etiketi
* Bir yerel dizin, GitHub deposu veya uzak bir tarbol gibi ACR görevleri için [desteklenen bağlam konumlarından](container-registry-tasks-overview.md#context-locations) biri
* Uygulamanız için uygun bir Buildpack Oluşturucu görüntüsünün adı. Azure Container Registry daha hızlı derlemeler gibi Oluşturucu görüntülerini önbelleğe alır `cloudfoundry/cnb:0.0.34-cflinuxfs3` .  

`az acr pack build` , akan ve ayrıca daha sonra alınabilmeleri için [çalışan değişkenleri](container-registry-tasks-reference-yaml.md#run-variables) ve [görev çalıştırma günlüklerini](container-registry-tasks-logs.md) içeren ACR görevleri komutlarının diğer özelliklerini destekler.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Örnek: Cloud Foundry Oluşturucu ile derleme Node.js görüntüsü

Aşağıdaki örnek, oluşturucuyu kullanarak [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) depoındaki bir Node.js uygulamasından bir kapsayıcı görüntüsü oluşturur `cloudfoundry/cnb:0.0.34-cflinuxfs3` . Bu Oluşturucu Azure Container Registry tarafından önbelleğe alınır, bu nedenle bir `--pull` parametre gerekli değildir:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Bu örnek, `node-app` görüntüsünü `1.0` etiketiyle oluşturur ve *myregistry* kapsayıcı kayıt defterine gönderir. Bu örnekte, hedef kayıt defteri adı, görüntü adına açıkça sona erer. Belirtilmezse, kayıt defteri oturum açma sunucusu adı otomatik olarak görüntü adına eklenir.

Komut çıktısı, görüntüyü oluşturma ve gönderme sürecini gösterir. 

Görüntü başarıyla oluşturulduktan sonra, yüklüyse Docker ile çalıştırabilirsiniz. İlk olarak kayıt defterinizde oturum açın:

```azurecli
az acr login --name myregistry
```

Görüntüyü çalıştırın:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

`localhost:1337`Örnek Web uygulamasını görmek için en sevdiğiniz tarayıcınıza gidin. `[Ctrl]+[C]`Kapsayıcıyı durdurmak için tuşuna basın.

## <a name="example-build-java-image-with-heroku-builder"></a>Örnek: Heroku Builder ile Java görüntüsü oluşturma

Aşağıdaki örnek, oluşturucuyu kullanarak [buildpack/Sample-Java-App](https://github.com/buildpack/sample-java-app) deposunda Java uygulamasından bir kapsayıcı görüntüsü oluşturur `heroku/buildpacks:18` . `--pull`Parametresi, komutun en son Oluşturucu görüntüsünü çekmesini belirtir. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Bu örnekte `java-app` , komutun çalıştırma kimliğiyle etiketlenmiş görüntü oluşturulur ve bunu *myregistry* kapsayıcı kayıt defterine iter.

Komut çıktısı, görüntüyü oluşturma ve gönderme sürecini gösterir. 

Görüntü başarıyla oluşturulduktan sonra, yüklüyse Docker ile çalıştırabilirsiniz. İlk olarak kayıt defterinizde oturum açın:

```azurecli
az acr login --name myregistry
```

Çalışma *kimliği* için görüntü etiketinizi değiştirerek görüntüyü çalıştırın:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

`localhost:8080`Örnek Web uygulamasını görmek için en sevdiğiniz tarayıcınıza gidin. `[Ctrl]+[C]`Kapsayıcıyı durdurmak için tuşuna basın.


## <a name="next-steps"></a>Sonraki adımlar

Bir kapsayıcı görüntüsünü derleyip gönderdikten sonra `az acr pack build` , istediğiniz bir hedefe herhangi bir görüntü gibi dağıtabilirsiniz. Azure dağıtım seçenekleri, [App Service](../app-service/tutorial-custom-container.md) veya [Azure Kubernetes hizmetinde](../aks/tutorial-kubernetes-deploy-cluster.md), diğerleri arasında çalıştırmayı içerir.

ACR görevleri özellikleri hakkında daha fazla bilgi için bkz. [ACR görevleriyle kapsayıcı görüntüsü derlemelerini ve bakımını otomatikleştirme](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build

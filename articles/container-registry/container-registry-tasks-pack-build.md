---
title: Uygulamadan Azure Container Registry görüntüsü oluşturma
description: Bir uygulamadan kapsayıcı görüntüsü oluşturmak için az ACR Pack Build komutunu kullanın ve Dockerfile kullanmadan Azure Container Registry gönderin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/24/2019
ms.author: danlep
ms.openlocfilehash: 34ef0fe4be00cfa7ce3e73c23eec636784071e56
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965903"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Cloud Native Buildpack kullanarak bir uygulamadan görüntü oluşturma ve gönderme

Azure CLı komutu `az acr pack build`, [Buildpacks](https://buildpacks.io/)'ten bir uygulama oluşturmak ve görüntüsünü bir Azure Container Registry 'ye göndermek için [`pack`](https://github.com/buildpack/pack) CLI aracını kullanır. Bu özellik, bir Dockerfile tanımlamak zorunda kalmadan Node. js, Java ve diğer dillerdeki uygulama kaynak kodunuzun hızlı bir kapsayıcı görüntüsünü oluşturmak için bir seçenek sunar.

Bu makaledeki örnekleri çalıştırmak için Azure CLı 'nın Azure Cloud Shell veya yerel bir yüklemesini kullanabilirsiniz. Yerel olarak kullanmak isterseniz, 2.0.70 veya üzeri sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

> [!IMPORTANT]
> Bu özellik şu anda önizleme sürümündedir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="use-the-build-command"></a>Build komutunu kullanma

Cloud Native Buildpacks kullanarak bir kapsayıcı görüntüsü derlemek ve göndermek için [az ACR Pack Build][az-acr-pack-build] komutunu çalıştırın. [Az ACR Build][az-acr-build] komutu, bir Dockerfile kaynağından ve ilgili koddan bir görüntüyü oluşturup `az acr pack build` ile ilgili koddan doğrudan bir uygulama kaynak ağacı belirlersiniz.

`az acr pack build`çalıştırdığınızda, en azından aşağıdakileri belirtin:

* Komutu çalıştırdığınız bir Azure Container kayıt defteri
* Elde edilen görüntü için bir görüntü adı ve etiketi
* Bir yerel dizin, GitHub deposu veya uzak bir tarbol gibi ACR görevleri için [desteklenen bağlam konumlarından](container-registry-tasks-overview.md#context-locations) biri
* Uygulamanız için uygun bir Buildpack Oluşturucu görüntüsünün adı. Azure Container Registry daha hızlı derlemeler için `cloudfoundry/cnb:0.0.34-cflinuxfs3` gibi Oluşturucu görüntülerini önbelleğe alır.  

`az acr pack build`, akan [Çalıştır](container-registry-tasks-reference-yaml.md#run-variables) ve [görev çalıştırma günlükleri](container-registry-tasks-overview.md#view-task-logs) de dahil olmak üzere ACR görevleri komutlarının diğer özelliklerini destekler ve daha sonra alımı için de kaydedilir.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Örnek: Cloud Foundry Builder ile Node. js görüntüsü oluşturma

Aşağıdaki örnek, `cloudfoundry/cnb:0.0.34-cflinuxfs3` oluşturucuyu kullanarak [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) Depoındaki bir Node. js uygulamasından bir kapsayıcı görüntüsü oluşturur. Bu Oluşturucu Azure Container Registry tarafından önbelleğe alınır, bu nedenle `--pull` parametresi gerekli değildir:

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

Örnek Web uygulamasını görmek için sık kullandığınız tarayıcınızda `localhost:1337` ' a gidin. Kapsayıcıyı durdurmak için `[Ctrl]+[C]` tuşuna basın.

## <a name="example-build-java-image-with-heroku-builder"></a>Örnek: Heroku Builder ile Java görüntüsü oluşturma

Aşağıdaki örnek, `heroku/buildpacks:18` oluşturucuyu kullanarak [buildpack/Sample-Java-App](https://github.com/buildpack/sample-java-app) deposunda Java uygulamasından bir kapsayıcı görüntüsü oluşturur. `--pull` parametresi, komutun en son Oluşturucu görüntüsünü çekmesini belirtir. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Bu örnek, komutun çalıştırma KIMLIĞIYLE etiketlenmiş `java-app` görüntüsünü oluşturur ve bunu *myregistry* kapsayıcı kayıt defterine gönderir.

Komut çıktısı, görüntüyü oluşturma ve gönderme sürecini gösterir. 

Görüntü başarıyla oluşturulduktan sonra, yüklüyse Docker ile çalıştırabilirsiniz. İlk olarak kayıt defterinizde oturum açın:

```azurecli
az acr login --name myregistry
```

Çalışma *kimliği*için görüntü etiketinizi değiştirerek görüntüyü çalıştırın:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Örnek Web uygulamasını görmek için sık kullandığınız tarayıcınızda `localhost:8080` ' a gidin. Kapsayıcıyı durdurmak için `[Ctrl]+[C]` tuşuna basın.


## <a name="next-steps"></a>Sonraki adımlar

`az acr pack build`bir kapsayıcı görüntüsü oluşturup gönderdikten sonra, istediğiniz bir hedefe herhangi bir görüntü gibi dağıtabilirsiniz. Azure dağıtım seçenekleri, [App Service](../app-service/containers/tutorial-custom-docker-image.md) veya [Azure Kubernetes hizmetinde](../aks/tutorial-kubernetes-deploy-cluster.md), diğerleri arasında çalıştırmayı içerir.

ACR görevleri özellikleri hakkında daha fazla bilgi için bkz. [ACR görevleriyle kapsayıcı görüntüsü derlemelerini ve bakımını otomatikleştirme](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

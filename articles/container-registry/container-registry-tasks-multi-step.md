---
title: '& yama görüntüsünü derlemek, test etmek için çok adımlı görev'
description: Bulutta kapsayıcı görüntüleri oluşturmak, test etmek ve düzeltme eki uygulamak için görev tabanlı iş akışları sağlayan, Azure Container Registry ' de ACR görevlerinin bir özelliği olan çok adımlı görevlere giriş.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: d57044fa8a0db7d661eb50284b34a6bbec9a1879
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781028"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>ACR görevlerinde çok adımlı derleme, test ve düzeltme eki görevleri çalıştırma

Çok adımlı görevler, çok adımlı ve çok Kapsayıcılı iş akışlarıyla ACR görevlerinin tek görüntü derleme ve gönderim yeteneklerini genişletir. Birden çok adımlı görevleri kullanarak, seriler veya paralel olarak birden çok görüntü oluşturun ve gönderin. Sonra bu görüntüleri tek bir görev çalıştırması içinde komut olarak çalıştırın. Her adım bir kapsayıcı görüntüsü oluşturma veya itme işlemini tanımlar ve bir kapsayıcının yürütülmesini de tanımlayabilir. Çok adımlı bir görevdeki her bir adım, yürütme ortamı olarak bir kapsayıcı kullanır.

> [!IMPORTANT]
> Önizlemede daha önce `az acr build-task` komutuyla görev oluşturduysanız [az acr task][az-acr-task] komutuyla bu görevleri yeniden oluşturmanız gerekebilir.

Örneğin, aşağıdaki mantığı otomatikleştiren adımlarla bir görevi çalıştırabilirsiniz:

1. Web uygulaması görüntüsü oluşturma
1. Web uygulaması kapsayıcısını çalıştırma
1. Web uygulaması test görüntüsü oluşturma
1. Çalışan uygulama kapsayıcısına karşı testler gerçekleştiren Web uygulaması test kapsayıcısını çalıştırın
1. Testler başarılı olursa bir Helu grafik arşiv paketi oluşturun
1. `helm upgrade`Yeni helmchart arşiv paketini kullanarak bir gerçekleştir

Tüm adımlar Azure 'da gerçekleştirilir, iş yükünü Azure 'un işlem kaynaklarına devrederek ve altyapıyı altyapı yönetiminden serbest bırakır. Azure Container Registry 'nizin yanı sıra, yalnızca kullandığınız kaynaklar için ödeme yaparsınız. Fiyatlandırma hakkında daha fazla bilgi için [Azure Container Registry fiyatlandırması][pricing]Içindeki **kapsayıcı derlemesi** bölümüne bakın.


## <a name="common-task-scenarios"></a>Ortak görev senaryoları

Çok adımlı görevler aşağıdaki mantık gibi senaryoları etkinleştirir:

* Dizide veya paralel olarak bir veya daha fazla kapsayıcı görüntüsü oluşturun, etiketleyin ve gönderin.
* Birim testi ve kod kapsamı sonuçlarını çalıştırın ve yakalayın.
* İşlevsel testleri çalıştırma ve yakalama. ACR görevleri birden fazla kapsayıcıyı çalıştırmayı, aralarında bir dizi isteği yürütmeyi destekler.
* Bir kapsayıcı görüntüsü derlemesinin ön/son adımları dahil olmak üzere görev tabanlı yürütme gerçekleştirin.
* En sevdiğiniz dağıtım altyapısından hedef ortamınıza bir veya daha fazla kapsayıcı dağıtın.

## <a name="multi-step-task-definition"></a>Çok adımlı görev tanımı

ACR görevlerinde çok adımlı bir görev, YAML dosyası içindeki bir dizi adım olarak tanımlanır. Her adım bir veya daha fazla önceki adımın başarıyla tamamlanmasına ilişkin bağımlılıkları belirtebilir. Aşağıdaki görev adımı türleri kullanılabilir:

* [`build`](container-registry-tasks-reference-yaml.md#build): Tanıdık sözdizimini kullanarak veya paralel olarak bir veya daha fazla kapsayıcı görüntüsü oluşturun `docker build` .
* [`push`](container-registry-tasks-reference-yaml.md#push): Bir kapsayıcı kayıt defterine oluşturulan görüntüleri gönderin. Azure Container Registry gibi özel kayıt defterleri, genel Docker Hub olduğu gibi desteklenir.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Çalışan görevin bağlamı içinde işlev olarak çalışabilme gibi bir kapsayıcı çalıştırın. Parametreleri kapsayıcıya geçirebilir `[ENTRYPOINT]` ve env, detach ve diğer tanıdık parametreler gibi özellikleri belirtebilirsiniz `docker run` . `cmd`Adım türü, eşzamanlı kapsayıcı yürütmesi ile birim ve işlevsel teste olanak tanıyor.

Aşağıdaki kod parçacıkları, bu görev adımı türlerinin nasıl birleştirileceğini gösterir. Çok adımlı görevler, bir Dockerfile 'dan tek bir görüntü oluşturup kayıt defterinize, aşağıdakine benzer bir YAML dosyası ile göndermek kadar basit olabilir:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Ya da daha karmaşık olan bu kurgusal çok adımlı tanım, derleme, test, helmpackage ve Held dağıtımı (kapsayıcı kayıt defteri ve Held depo yapılandırması gösterilmez) için adımları içerir:

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build: -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Çeşitli senaryolar için bkz. çok adımlı görev YAML dosyaları ve Dockerfiles için [görev örnekleri](container-registry-tasks-samples.md) .

## <a name="run-a-sample-task"></a>Örnek görev çalıştırma

Görevler, "hızlı çalışma" adı verilen ve git işlemesinde veya temel görüntü güncelleştirmesinde otomatik yürütme olarak adlandırılan el ile yürütmeyi destekler.

Bir görevi çalıştırmak için önce görevin adımlarını bir YAML dosyasında tanımlamanız, sonra da [az ACR Run][az-acr-run]Azure CLI komutunu yürütmeniz gerekir.

Örnek bir görev YAML dosyası kullanarak bir görevi çalıştıran örnek bir Azure CLı komutu aşağıda verilmiştir. Adımları bir görüntü oluşturur ve ardından gönderir. `\<acrName\>`Komutunu çalıştırmadan önce kendi Azure Container Registry 'nizin adıyla güncelleştirin.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Görevi çalıştırdığınızda, çıktıda YAML dosyasında tanımlanan her adımın ilerleme durumu gösterilmelidir. Aşağıdaki çıktıda, adımlar ve olarak görünür `acb_step_0` `acb_step_1` .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Git işlemesinde veya temel görüntü güncelleştirmesinde otomatik yapılar hakkında daha fazla bilgi için bkz. [Otomatik görüntü yapıları](container-registry-tutorial-build-task.md) ve [temel görüntü güncelleştirme derleme](container-registry-tutorial-base-image-update.md) öğreticisi makaleleri.

## <a name="next-steps"></a>Sonraki adımlar

Çok adımlı görev başvurusunu ve örnekleri buradan bulabilirsiniz:

* [Görev başvurusu](container-registry-tasks-reference-yaml.md) -görev adımı türleri, özellikleri ve kullanımı.
* [Görev örnekleri](container-registry-tasks-samples.md) - `task.yaml` çok çeşitli senaryolar Için örnek ve Docker dosyaları, basit-karmaşık.
* [Cmd deposu](https://github.com/AzureCR/cmd) -ACR görevleri için komut olarak kapsayıcı koleksiyonu.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task]: /cli/azure/acr/task

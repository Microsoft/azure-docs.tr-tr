---
title: Hızlı başlangıç-Azure 'da isteğe bağlı bir kapsayıcı görüntüsü oluşturma
description: Azure bulutunda, isteğe bağlı olarak bir Docker kapsayıcı görüntüsünü hızlıca oluşturmak, göndermek ve çalıştırmak için Azure Container Registry komutlarını kullanın.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d3ef126812cb36874b74c8569d141787f71a29da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781460"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Hızlı başlangıç: Azure Container Registry görevleri kullanarak kapsayıcı görüntüsü oluşturma ve çalıştırma

Bu hızlı başlangıçta, yerel bir Docker yüklemesi olmadan Azure 'da bir Docker kapsayıcı görüntüsünü hızlıca oluşturmak, göndermek ve çalıştırmak için [Azure Container Registry görevleri][container-registry-tasks-overview] komutlarını kullanırsınız. ACR görevleri kapsayıcı görüntülerini kapsayıcı yaşam döngüsü genelinde yönetmenize ve değiştirmenize yardımcı olmak üzere Azure Container Registry içindeki bir özellik paketidir. Bu örnek, yerel bir Dockerfile kullanarak isteğe bağlı derlemeler ile buluta "iç döngüsü" kapsayıcı görüntüsü geliştirme döngüsünün nasıl boşalmasını gösterir. 

Bu hızlı başlangıç sonrasında [öğreticilerini](container-registry-tutorial-quick-task.md)kullanarak ACR görevlerinin daha gelişmiş özelliklerine göz atın. ACR görevleri kod yürütmelerine veya temel görüntü güncelleştirmelerine göre görüntü derlemelerini otomatikleştirebilir veya birden çok kapsayıcıyı paralel olarak diğer senaryolar arasında test edebilir. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.58 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Zaten bir kapsayıcı kayıt defteriniz yoksa, önce [az Group Create][az-group-create] komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

[Az ACR Create][az-acr-create] komutunu kullanarak bir kapsayıcı kayıt defteri oluşturun. Kaynak defteri adı Azure’da benzersiz olmalı ve 5-50 arası alfasayısal karakter içermelidir. Aşağıdaki örnekte, *myContainerRegistry008* kullanılır. Bunu benzersiz bir değerle güncelleştirin.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

Bu örnek, Azure Container Registry hakkında bilgi edinmek için uygun maliyetli bir seçenek olan *temel* bir kayıt oluşturur. Kullanılabilir hizmet katmanları hakkında daha fazla bilgi için bkz. [kapsayıcı kayıt defteri hizmet katmanları][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Dockerfile dosyasından görüntü oluşturma ve gönderme

Şimdi bir görüntü derlemek ve göndermek için Azure Container Registry kullanın. İlk olarak, yerel bir çalışma dizini oluşturun ve ardından tek satırlık *dockerfile* adlı bir dockerfile oluşturun: `FROM mcr.microsoft.com/hello-world` . Bu, `hello-world` Microsoft Container Registry 'de barındırılan görüntüden Linux kapsayıcı görüntüsü oluşturmak için basit bir örnektir. Kendi standart Dockerfile dosyanızı oluşturabilir ve diğer platformlar için görüntü oluşturabilirsiniz. Bash kabuğu 'nda çalışıyorsanız, Dockerfile komutunu aşağıdaki komutla oluşturun:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Görüntüyü oluşturan [az ACR Build][az-acr-build] komutunu çalıştırın ve görüntü başarıyla derlendikten sonra, Kayıt defterinize gönderin. Aşağıdaki örnek görüntüyü oluşturur ve gönderir `sample/hello-world:v1` . `.`Komutun sonundaki, Dockerfile dosyasının konumunu, bu durumda geçerli dizin olarak belirler.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

Başarılı bir derleme ve gönderim çıkışı aşağıdakine benzer:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Görüntüyü çalıştırma

Şimdi oluşturduğunuz ve Kayıt defterinize gönderdiğiniz görüntüyü hızlıca çalıştırın. Burada, Container komutunu çalıştırmak için [az ACR Run][az-acr-run] komutunu kullanın. Kapsayıcı geliştirme iş akışınızda bu, görüntüyü dağıtmadan önce bir doğrulama adımı olabilir veya komutu [çok adımlı BIR YAML dosyasına][container-registry-tasks-multi-step]ekleyebilirsiniz. 

Aşağıdaki örnek, `$Registry` komutunu çalıştırdığınız kayıt defterini belirtmek için kullanır:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

`cmd`Bu örnekteki parametresi kapsayıcıyı varsayılan yapılandırmasında çalıştırır, ancak `cmd` ek `docker run` parametreleri veya diğer `docker` komutları destekler.

Çıktı aşağıdakine benzer:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete][az-group-delete] komutunu kullanarak kaynak grubunu, kapsayıcı kayıt defterini ve orada depolanan kapsayıcı görüntülerini kaldırabilirsiniz.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, yerel bir Docker yüklemesi olmadan Azure 'da bir Docker kapsayıcı görüntüsünü hızlıca oluşturmak, göndermek ve çalıştırmak için ACR görevlerinin özelliklerini kullandınız. Görüntü yapılarını ve güncelleştirmelerini otomatikleştirmek üzere ACR görevlerini kullanma hakkında bilgi edinmek için Azure Container Registry görev öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Container Registry görevleri öğreticileri][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli

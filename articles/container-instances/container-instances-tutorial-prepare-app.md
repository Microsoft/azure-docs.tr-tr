---
title: Öğretici-Azure Container Instances için kapsayıcı görüntüsü hazırlama
description: Azure Container Instances öğreticisi Bölüm 1/3-Azure Container Instances dağıtım için bir kapsayıcı görüntüsünde uygulama hazırlama
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 719237f63d387cf56ab7947f8f168e0aa4351376
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325578"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Öğretici: Azure Container Instances dağıtım için bir kapsayıcı görüntüsü oluşturun

Azure Container Instances, Docker kapsayıcılarının herhangi bir sanal makine sağlama veya herhangi bir üst düzey hizmet benimsenmesi gerekmeden Azure altyapısına dağıtılmasını sağlar. Bu öğreticide, Azure Container Instances kullanılarak çalıştırılabilen bir kapsayıcı görüntüsüne küçük bir Node.js web uygulamasını paketlersiniz.

Serinin ilk bölümündeki bu makalede şunları yapacaksınız:

> [!div class="checklist"]
> * Uygulama kaynak kodunu GitHub’dan kopyalama
> * Uygulama kaynağından kapsayıcı görüntüsü oluşturma
> * Görüntüyü yerel bir Docker ortamında test etme

Öğreticinin ikinci ve üçüncü bölümünde, görüntünüzü Azure Container Registry’ye yükler ve Azure Container Instances’a dağıtırsınız.

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Uygulama kodunu alma

Bu öğreticideki örnek uygulama, [Node. js][nodejs]' de yerleşik olarak bulunan basit bir Web uygulamasıdır. Uygulama, statik bir HTML sayfası görevi görür ve aşağıdaki ekran görüntüsüne benzer:

![Tarayıcıda gösterilen öğretici uygulama][aci-tutorial-app]

Örnek uygulamanın deposunu kopyalamak için Git kullanın:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

Ayrıca [, ZIP arşivini][aci-helloworld-zip] GitHub 'dan doğrudan indirebilirsiniz.

## <a name="build-the-container-image"></a>Kapsayıcı görüntüsünü oluşturma

Örnek uygulamada bulunan Dockerfile, kapsayıcının nasıl derlendiğini gösterir. Kapsayıcılarla kullanım için uygun olan küçük bir dağıtım olan [alp Linux][alpine-linux]tabanlı [resmi bir Node. js görüntüsünden][docker-hub-nodeimage] başlar. Ardından uygulama dosyalarını kapsayıcıya kopyalar, Node Package Manager’ı kullanarak bağımlılıkları yükler ve son olarak uygulamayı başlatır.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Kapsayıcı görüntüsünü oluşturmak için [Docker Build][docker-build] komutunu kullanın ve bunu *aci-öğreticisi-App*olarak etiketleyin:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

[Docker Build][docker-build] komutunun çıktısı aşağıdakine benzer (okunabilirlik için kesildi):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Oluşturulan görüntüyü görmek için [Docker görüntüleri][docker-images] komutunu kullanın:

```bash
docker images
```

Yeni derlenen görüntü listede görünmelidir:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Kapsayıcıyı yerel olarak çalıştırma

Kapsayıcıyı Azure Container Instances ' a dağıtmadan önce, [Docker Run][docker-run] ' ı kullanarak yerel olarak çalıştırın ve çalıştığını onaylayın. `-d` anahtarı kapsayıcının arka planda çalışmasını sağlar. `-p` ise işleminizdeki rastgele bağlantı noktalarından birini kapsayıcının 80 numaralı bağlantı noktasına eşlemenizi sağlar.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Komut başarılı olduysa `docker run` komutundan elde edilen çıktı, çalıştırılan kapsayıcının kimliğini görüntüler:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Şimdi kapsayıcının çalıştırıldığını onaylamak için tarayıcınızda `http://localhost:8080` adresine gidin. Aşağıdakine benzer bir web sayfası görmeniz gerekir:

![Uygulamayı tarayıcıda yerel olarak çalıştırma][aci-tutorial-app-local]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Container Instances’ta dağıtılabilen bir kapsayıcı görüntüsü oluşturdunuz ve bunun yerel olarak çalıştırıldığını doğruladınız. Şu ana kadar aşağıdakileri yaptınız:

> [!div class="checklist"]
> * GitHub’dan uygulama kaynağı kopyalandı
> * Uygulama kaynağından bir kapsayıcı görüntüsü oluşturuldu
> * Kapsayıcı yerel olarak test edildi

Kapsayıcı görüntünüzü Azure Container Registry’de depolama hakkında bilgi edinmek için sonraki öğreticiye geçin:

> [!div class="nextstepaction"]
> [Azure Container Registry’ye görüntü gönderme](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli

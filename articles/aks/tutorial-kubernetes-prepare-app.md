---
title: Azure’da Kubernetes öğreticisi - Uygulamayı hazırlama
description: Bu Azure Kubernetes Service (AKS) öğreticisinde Docker Compose ile AKS'ye dağıtabileceğiniz bir çok kapsayıcılı uygulama hazırlamayı ve derlemeyi öğreneceksiniz.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 3ff93d006b7599eaa3f97c33efb047ce480c301c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71264464"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Öğretici: Azure Kubernetes hizmeti (AKS) için uygulama hazırlama

Bu yedi parçalık öğreticinin ilk bölümünde, bir çoklu kapsayıcı uygulaması Kubernetes’te kullanılmak üzere hazırlanmaktadır. Uygulamayı yerel ortamda derlemek ve test etmek için Docker Compose gibi var olan geliştirme araçları kullanılmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Örnek bir uygulama kaynağını GitHub’dan kopyalama
> * Örnek uygulama kaynağından kapsayıcı görüntüsü oluşturma
> * Çok kapsayıcılı uygulamayı yerel bir Docker ortamında test etme

Tamamlandıktan sonra, aşağıdaki uygulama yerel geliştirme ortamınızda çalışacaktır:

![Azure’da Kubernetes kümesinin görüntüsü](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

Ek öğreticilerde, kapsayıcı görüntüsü bir Azure Container Registry yüklenir ve ardından bir AKS kümesine dağıtılır.

## <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticide kapsayıcılar, kapsayıcı görüntüleri ve `docker` komutları gibi temel Docker kavramları hakkında bilgi sahibi olduğunuz varsayılmıştır. Kapsayıcı temelleri hakkında bilgi için bkz. [Docker ile çalışmaya başlama][docker-get-started].

Bu öğreticiyi tamamlamak için Linux kapsayıcılarını çalıştıran yerel bir Docker geliştirme ortamı gerekir. Docker, bir [Mac][docker-for-mac], [Windows][docker-for-windows]veya [Linux][docker-for-linux] sisteminde Docker 'ı yapılandıran paketler sağlar.

Azure Cloud Shell, bu öğreticilerdeki her adımı tamamlamak için gerekli olan Docker bileşenlerini içermez. Bu yüzden, eksiksiz bir Docker geliştirme ortamı kullanmanızı öneririz.

## <a name="get-application-code"></a>Uygulama kodunu alma

Bu öğreticide kullanılan örnek uygulama, temel oylama uygulamasıdır. Bu uygulama, ön uç bileşen ile arka uç Redis örneğinden oluşur. Web bileşeni, özel kapsayıcı görüntüsüne paketlenmiştir. Redis örneği, Docker Hub’dan alınan değiştirilmemiş bir görüntü kullanır.

Örnek uygulamayı geliştirme ortamınıza kopyalamak için [git][] komutunu kullanın:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Kopyalanmış dizine geçin.

```console
cd azure-voting-app-redis
```

Dizinin içinde uygulama kaynak kodu, önceden oluşturulmuş Docker Compose dosyası ve Kubernetes bildirim dosyası bulunur. Bu dosyalar öğretici kümesi boyunca kullanılır.

## <a name="create-container-images"></a>Kapsayıcı görüntüleri oluşturma

[Docker Compose][docker-compose] , kapsayıcı görüntüleri oluşturmayı ve çok Kapsayıcılı uygulamaların dağıtımını otomatikleştirmek için kullanılabilir.

Kapsayıcı görüntüsünü oluşturmak için örnek `docker-compose.yaml` dosyasını çalıştırın, Redis görüntüsünü indirin ve uygulamayı başlatın:

```console
docker-compose up -d
```

Tamamlandığında, oluşturulan görüntüleri görmek için [docker images][docker-images] komutunu kullanın. Üç görüntü indirilir veya oluşturulur. *azure-vote-front* görüntüsü uygulamayı içerir ve temel olarak `nginx-flask` görüntüsünü kullanır. `redis` görüntüsü bir Redis örneği başlatmak için kullanılır.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Çalışan kapsayıcıları görmek için [Docker PS][docker-ps] komutunu çalıştırın:

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Uygulamayı yerel olarak test etme

Çalışan uygulamayı görmek için yerel web tarayıcısına `http://localhost:8080` yazın. Örnek uygulama aşağıdaki örnekte gösterilen şekilde yüklenir:

![Azure’da Kubernetes kümesinin görüntüsü](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık uygulama işlevselliği doğrulandığından, çalışan kapsayıcılar durdurulup kaldırılabilir. Kapsayıcı görüntülerini silmeyin. Bir sonraki öğreticide *azure-vote-front* görüntüsü bir Azure Container Registry örneğine yüklenecektir.

[Docker-Compose aşağı][docker-compose-down] komutuyla kapsayıcı örneklerini ve kaynakları durdurun ve kaldırın:

```console
docker-compose down
```

Yerel uygulama kaldırıldığında, sonraki öğreticide kullanılmak üzere Azure oy uygulamasını, *Azure-oyönünü*Içeren bir Docker görüntüsüne sahip olursunuz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir uygulama test edildi ve bu uygulamaya yönelik kapsayıcı görüntüleri oluşturuldu. Şunları öğrendiniz:

> [!div class="checklist"]
> * Örnek bir uygulama kaynağını GitHub’dan kopyalama
> * Örnek uygulama kaynağından kapsayıcı görüntüsü oluşturma
> * Çok kapsayıcılı uygulamayı yerel bir Docker ortamında test etme

Kapsayıcı görüntülerini Azure Container Registry’de depolamayı öğrenmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Container Registry’ye görüntüleri gönderme][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md

---
title: Azure Container Registry görevleri örnekleri
description: Kapsayıcı görüntülerini derlemek, çalıştırmak ve düzeltme eki uygulamak için örnek Azure Container Registry görevleri (ACR görevleri)
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152859"
---
# <a name="acr-tasks-samples"></a>ACR görevleri örnekleri

Bu makalede birkaç [Azure Container Registry görevi](container-registry-tasks-overview.md) (ACR görevi) senaryosu için örnek `task.yaml` dosyaları ve Ilişkili Dockerfiles bağlantısı yer aldığı bir bağlantı vardır. 

Daha fazla örnek için bkz. [Azure örnekleri][task-examples] deposu.

## <a name="scenarios"></a>Senaryolar

* **Derleme görüntüsü** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml) - **kapsayıcıyı Çalıştır**

* [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) - **derleme ve gönderme resmi**

*  - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) **görüntüsünü derleyin ve çalıştırın**

* [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) -  **birden çok görüntü oluşturun ve gönderin**

* Paralel -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) **'da görüntü oluşturma ve test** etme

* [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile) - **birden çok kayıt defterlerine görüntü oluşturun ve gönderin**


## <a name="next-steps"></a>Sonraki adımlar

ACR görevleri hakkında daha fazla bilgi edinin:

* [Çok adımlı görevler](container-registry-tasks-multi-step.md) -bulutta kapsayıcı görüntüleri oluşturmaya, test etmeye ve düzeltme eki uygulamaya yönelik görev tabanlı iş akışlarıdır.
* [Görev başvurusu](container-registry-tasks-reference-yaml.md) -görev adımı türleri, özellikleri ve kullanımı.
* [Cmd deposu](https://github.com/AzureCR/cmd) -ACR görevleri için komut olarak kapsayıcı koleksiyonu.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

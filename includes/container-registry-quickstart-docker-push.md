---
title: include dosyası
description: include dosyası
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67188927"
---
## <a name="push-image-to-registry"></a>Kayıt defterine görüntü gönderme

Azure Container kayıt defterine görüntü gönderebilmeniz için önce bir görüntünüz olmalıdır. Henüz herhangi bir yerel kapsayıcı görüntünüz yoksa, Docker Hub 'ından mevcut bir görüntüyü çekmek için aşağıdaki [Docker Pull][docker-pull] komutunu çalıştırın. Bu örnek için `hello-world` görüntüyü çekin.

```
docker pull hello-world
```

Kayıt defterinize görüntü gönderebilmeniz için, ACR oturum açma sunucunuzun tam adını etiketlemeniz gerekir. Oturum açma sunucusu adı  *\<kayıt defteri-\>adı. azurecr.io* (tümü küçük harf) biçimindedir (örneğin, *mycontainerregistry007.azurecr.io*).

Görüntüyü [docker tag][docker-tag] komutunu kullanarak etiketleyin. `<acrLoginServer>` değerini, ACR örneğinizin sunucu adıyla değiştirin.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Son olarak, [docker push][docker-push] komutunu kullanarak görüntüyü ACR örneğine gönderin. `<acrLoginServer>` değerini, ACR örneğinizin sunucu adıyla değiştirin. Bu örnek, `hello-world:v1` görüntüyü içeren **Merhaba-Dünya** deposunu oluşturur.

```
docker push <acrLoginServer>/hello-world:v1
```

Görüntüyü kapsayıcı Kayıt defterinize gönderdikten sonra, `hello-world:v1` görüntüyü yerel Docker ortamınızdan kaldırın. (Bu [Docker rmi][docker-rmi] komutunun, görüntüyü Azure Container Registry 'nizin **Hello-World** deposundan kaldırmadığını unutmayın.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->


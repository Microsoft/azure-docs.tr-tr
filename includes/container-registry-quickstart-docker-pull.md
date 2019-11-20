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
ms.openlocfilehash: cd97c61e7493249785293ae331713ba1a98efee3
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67188926"
---
## <a name="run-image-from-registry"></a>Görüntüyü kayıt defterinden Çalıştır

Şimdi `hello-world:v1` [Docker Run][docker-run]kullanarak kapsayıcı Kayıt defterinizden kapsayıcı görüntüsünü çekebilir ve çalıştırabilirsiniz:

```
docker run <acrLoginServer>/hello-world:v1  
```

Örnek çıktı: 

```
Unable to find image 'mycontainerregistry007.azurecr.io/hello-world:v1' locally
v1: Pulling from hello-world
Digest: sha256:662dd8e65ef7ccf13f417962c2f77567d3b132f12c95909de6c85ac3c326a345
Status: Downloaded newer image for mycontainerregistry007.azurecr.io/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.

[...]
```

<!-- LINKS - External -->
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/

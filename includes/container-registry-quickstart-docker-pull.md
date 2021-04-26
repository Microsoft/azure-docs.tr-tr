---
title: include dosyası
description: include dosyası
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c99bfd9fb69beb7b5b7088ebea84082d744b12e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020032"
---
## <a name="run-image-from-registry"></a>Görüntüyü kayıt defterinden Çalıştır

Şimdi `hello-world:v1` [Docker Run][docker-run]kullanarak kapsayıcı Kayıt defterinizden kapsayıcı görüntüsünü çekebilir ve çalıştırabilirsiniz:

```
docker run <login-server>/hello-world:v1  
```

Örnek çıktı: 

```
Unable to find image 'mycontainerregistry.azurecr.io/hello-world:v1' locally
v1: Pulling from hello-world
Digest: sha256:662dd8e65ef7ccf13f417962c2f77567d3b132f12c95909de6c85ac3c326a345
Status: Downloaded newer image for mycontainerregistry.azurecr.io/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.

[...]
```

<!-- LINKS - External -->
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/

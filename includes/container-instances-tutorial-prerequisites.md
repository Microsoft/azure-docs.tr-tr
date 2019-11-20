---
title: include dosyası
description: include dosyası
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67188929"
---
Bu öğreticiyi tamamlamak için aşağıdaki gereksinimleri karşılamanız gerekir:

**Azure CLI**: Yerel bilgisayarınızda Azure CLı sürüm 2.0.29 veya daha yeni bir sürümün yüklü olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme][azure-cli-install].

**Docker**: Bu öğreticide kapsayıcılar, kapsayıcı görüntüleri ve temel `docker` komutlar gibi temel Docker kavramlarının temel bir şekilde anlaşıldığı varsayılır. Docker ve kapsayıcı temelleri hakkında bilgi için bkz: [Docker'a genel bakış][docker-get-started].

**Docker altyapısı**: Bu öğreticiyi tamamlayabilmeniz için, Docker altyapısının yerel olarak yüklü olması gerekir. Docker, [MacOS][docker-mac], [Windows][docker-windows]ve [Linux][docker-linux]'ta Docker ortamını yapılandıran paketler sağlar.

> [!IMPORTANT]
> Azure Cloud shell, Docker programını içermediğinden bu öğreticiyi tamamlamak için *yerel bilgisayarınıza* hem Azure CLI’yi hem de Docker Altyapısı’nı yüklemeniz *gerekir*. Bu öğretici için Azure Cloud Shell kullanamazsınız.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli

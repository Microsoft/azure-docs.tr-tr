---
title: Azure Container Registry içerik biçimleri
description: Docker ile uyumlu kapsayıcı görüntüleri, Held grafikleri, OCı görüntüleri ve OCı yapıtları dahil Azure Container Registry tarafından desteklenen içerik biçimleri hakkında bilgi edinin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2019
ms.author: danlep
ms.openlocfilehash: 38639f22457d923643e8de09cfbbb2fd7f4d2985
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007489"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry desteklenen içerik biçimleri

Aşağıdaki içerik biçimlerinden birini yönetmek için Azure Container Registry bir özel depo kullanın. 

## <a name="docker-compatible-container-images"></a>Docker ile uyumlu kapsayıcı görüntüleri

Aşağıdaki Docker kapsayıcı görüntü biçimleri desteklenir:

* [Docker görüntü bildirimi v2, şema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image manifest v2, şema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -kayıt defterlerinin çoklu platform görüntülerini tek bir "Image: Tag" başvurusu altında depolamasına Izin veren bildirim listeleri içerir

## <a name="oci-images"></a>OCı görüntüleri

Azure Container Registry, [Açık kapsayıcı girişimi (OCI) görüntü biçimi belirtimini](https://github.com/opencontainers/image-spec/blob/master/spec.md)karşılayan görüntüleri destekler. Paketleme biçimleri, [Singularımage Format (SIF)](https://github.com/sylabs/sif)içerir.

## <a name="oci-artifacts"></a>OCı yapıtları

Azure Container Registry, kapsayıcı görüntülerini ve diğer içerik türlerini (yapıtları) depolamak, paylaşmak, güvenli hale getirmek ve dağıtmak için satıcı tarafsız, buluttan bağımsız bir belirtim olan [OCI dağıtım belirtimini](https://github.com/opencontainers/distribution-spec)destekler. Belirtim, bir kayıt defterinin kapsayıcı görüntülerine ek olarak çok çeşitli yapıtları depolamasına izin verir. Yapıtları göndermek ve çekmek için yapıtı uygun araçları kullanın. Bir örnek için bkz. [Azure Container Registry kullanarak BIR OCI yapıtı gönderme ve çekme](container-registry-oci-artifacts.md).

OCı yapıtları hakkında daha fazla bilgi edinmek için bkz. [depolama (ORAS) deposu olarak OCI kayıt defteri](https://github.com/deislabs/oras) ve GitHub 'Daki [OCI yapıtları](https://github.com/opencontainers/artifacts) deposu.

## <a name="helm-charts"></a>Helb grafikleri

Azure Container Registry, Kubernetes için uygulamaları hızlı bir şekilde yönetmek ve dağıtmak üzere kullanılan bir paketleme biçimi olan [helk grafikleri](https://helm.sh/)için depolar barındırabilir. [Held Client](https://docs.helm.sh/using_helm/#installing-helm) sürüm 2 (2.11.0 veya üzeri) desteklenir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Registry görüntüleri [gönderme ve çekme](container-registry-get-started-docker-cli.md) hakkında bilgi edinme.

* Kapsayıcı görüntülerini derlemek ve test etmek için [ACR görevlerini](container-registry-tasks-overview.md) kullanın. 

* Kapsayıcıları OCı biçiminde derlemek ve paketlemek için [Moby BuildKit](https://github.com/moby/buildkit) ' i kullanın.

* Azure Container Registry barındırılan bir [Held deposu](container-registry-helm-repos.md) ayarlayın. 



---
title: Desteklenen içerik biçimleri
description: Docker ile uyumlu kapsayıcı görüntüleri, Held grafikleri, OCı görüntüleri ve OCı yapıtları dahil Azure Container Registry tarafından desteklenen içerik biçimleri hakkında bilgi edinin.
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 218d98f3f16e8d0ca76a24692afbb2b69606564b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223073"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry desteklenen içerik biçimleri

Aşağıdaki içerik biçimlerinden birini yönetmek için Azure Container Registry bir özel depo kullanın. 

## <a name="docker-compatible-container-images"></a>Docker ile uyumlu kapsayıcı görüntüleri

Aşağıdaki Docker kapsayıcı görüntü biçimleri desteklenir:

* [Docker görüntü bildirimi v2, şema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image manifest v2, şema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -kayıt defterlerinin tek bir başvuru altında [Multi-Architecture görüntülerini](push-multi-architecture-images.md) depolamasına izin veren bildirim listeleri içerir `image:tag`

## <a name="oci-images"></a>OCı görüntüleri

Azure Container Registry, isteğe bağlı [görüntü dizini](https://github.com/opencontainers/image-spec/blob/master/image-index.md) belirtimi dahil olmak üzere [Açık KAPSAYıCı girişim (OCI) görüntü biçimi belirtimini](https://github.com/opencontainers/image-spec/blob/master/spec.md)karşılayan görüntüleri destekler. Paketleme biçimleri, [Singularımage Format (SIF)](https://github.com/sylabs/sif)içerir.

## <a name="oci-artifacts"></a>OCı yapıtları

Azure Container Registry, kapsayıcı görüntülerini ve diğer içerik türlerini (yapıtları) depolamak, paylaşmak, güvenli hale getirmek ve dağıtmak için satıcı tarafsız, buluttan bağımsız bir belirtim olan [OCI dağıtım belirtimini](https://github.com/opencontainers/distribution-spec)destekler. Belirtim, bir kayıt defterinin kapsayıcı görüntülerine ek olarak çok çeşitli yapıtları depolamasına izin verir. Yapıtları göndermek ve çekmek için yapıtı uygun araçları kullanın. Bir örnek için bkz. [Azure Container Registry kullanarak BIR OCI yapıtı gönderme ve çekme](container-registry-oci-artifacts.md).

OCı yapıtları hakkında daha fazla bilgi edinmek için bkz. [depolama (ORAS) deposu olarak OCI kayıt defteri](https://github.com/deislabs/oras) ve GitHub 'Daki [OCI yapıtları](https://github.com/opencontainers/artifacts) deposu.

## <a name="helm-charts"></a>Helm grafikleri

Azure Container Registry, Kubernetes için uygulamaları hızlı bir şekilde yönetmek ve dağıtmak üzere kullanılan bir paketleme biçimi olan [helk grafikleri](https://helm.sh/)için depolar barındırabilir. [Held istemcisi](https://docs.helm.sh/using_helm/#installing-helm) sürüm 3 önerilir. Bkz. [Azure Container Registry 'de itme ve çekme ve çekme grafikleri](container-registry-helm-repos.md).

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Registry görüntüleri [gönderme ve çekme](container-registry-get-started-docker-cli.md) hakkında bilgi edinme.

* Kapsayıcı görüntülerini derlemek ve test etmek için [ACR görevlerini](container-registry-tasks-overview.md) kullanın. 

* Kapsayıcıları OCı biçiminde derlemek ve paketlemek için [Moby BuildKit](https://github.com/moby/buildkit) ' i kullanın.

* Azure Container Registry barındırılan bir [Held deposu](container-registry-helm-repos.md) ayarlayın. 



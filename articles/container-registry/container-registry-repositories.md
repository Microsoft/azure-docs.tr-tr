---
title: Portalda depoları görüntüleme
description: Docker kapsayıcı görüntülerini ve desteklenen diğer yapıları barındıran Azure Container Registry depoları görüntülemek için Azure portal kullanın.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74456285"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Azure portal kapsayıcı kayıt defteri depolarını Görüntüleme

Azure Container Registry, Docker kapsayıcı görüntülerini depolarda depolamanıza olanak tanır. Görüntüleri depolarda depolayarak, yalıtılmış ortamlarda görüntü gruplarını (veya görüntülerin sürümlerini) saklayabilirsiniz. Kayıt defterinize görüntü gönderdiğinizde bu depoları belirtebilir ve Azure portal içeriğini görüntüleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* **Kapsayıcı kayıt defteri**: Azure aboneliğinizde bir kapsayıcı kayıt defteri oluşturun. Örneğin, [Azure Portal](container-registry-get-started-portal.md) veya [Azure CLI](container-registry-get-started-azure-cli.md)'yi kullanın.
* **DOCKER CLI**: size Docker komut satırı arabirimi sağlayan yerel makinenize [Docker][docker-install] 'yi yükler.
* **Kapsayıcı görüntüsü**: kapsayıcı Kayıt defterinize bir görüntü gönderin. Görüntü gönderme ve çekme hakkında yönergeler için bkz. [görüntü gönderme ve çekme](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Azure portal depoları görüntüle

Görüntülerinizi barındıran depoların listesini ve Azure portal resim etiketlerini görebilirsiniz.

[Gönderme ve görüntü çekme](container-registry-get-started-docker-cli.md) (daha sonra görüntüyü silme) içindeki adımları izlediyseniz, kapsayıcı kayıt defterinizde bir NGINX görüntüsü olmalıdır. Bu makaledeki yönergeler, görüntüyü bir ad alanı ile etiketleyerek, içindeki "örnekler" olarak belirtilir `/samples/nginx` . Yenileyici olarak, bu makalede belirtilen [Docker Push][docker-push] komutu:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Azure Container Registry, bu tür çok düzeyli depo ad alanlarını desteklediğinden, belirli bir uygulamayla veya uygulama koleksiyonuyla ilişkili görüntü koleksiyonlarını farklı geliştirme veya operasyonel takımlara göre kapsam kapsamına alabilirsiniz. Kapsayıcı kayıt defterlerinde depolar hakkında daha fazla bilgi için bkz. [Azure 'Da özel Docker kapsayıcısı kayıt defterleri](container-registry-intro.md).

Bir depoyu görüntülemek için:

1. [Azure portalda][portal] oturum açma
1. NGINX görüntüsünü gönderdiğiniz **Azure Container Registry** seçin
1. Kayıt defterindeki görüntüleri içeren depoların bir listesini görmek için **depolar** ' ı seçin
1. Bu depodaki görüntü etiketlerini görmek için bir depo seçin

Örneğin, [bir görüntü gönderme ve çekme](container-registry-get-started-docker-cli.md)sırasında NGINX görüntüsünü göndertiyse şuna benzer bir şey görmeniz gerekir:

![Portaldaki depolar](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Sonraki adımlar

Portalda depolarla görüntüleme ve bunlarla çalışmanın temellerini öğrendiğinizden, [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) kümesiyle Azure Container Registry kullanmayı deneyin.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com

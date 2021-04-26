---
title: Görüntüleri kilitleme
description: Bir Azure Container Registry 'de silinememesi veya üzerine yazılmaması için bir kapsayıcı görüntüsü veya deposunun özniteliklerini ayarlayın.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 340beb1bb6666ddf0de7de38adee6be71f5f52bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772352"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Azure Container Registry 'de kapsayıcı görüntüsünü kilitleme

Bir Azure Container Registry 'de, bir görüntü sürümünü veya depoyu, silinememesi veya güncelleştirilemeyebilir. Bir görüntüyü veya depoyu kilitlemek için [az ACR Repository Update][az-acr-repository-update]olan Azure CLI komutunu kullanarak özniteliklerini güncelleştirin. 

Bu makale, Azure CLı 'yı Azure Cloud Shell veya yerel olarak çalıştırmanızı gerektirir (sürüm 2.0.55 veya üzeri önerilir). Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli].

> [!IMPORTANT]
> Bu makale, bir kayıt defterinin tamamını kilitlemek için (örneğin, Azure portal **> kilitleri** veya `az lock` Azure CLI içindeki komutları kullanarak), bu makaleye uygulanmaz. Kayıt defteri kaynağını kilitlemek, depolarda veri oluşturmanızı, güncelleştirmenizi veya silmenizi önler. Kayıt defterinin kilitlenmesi, yalnızca çoğaltmaları ekleme veya silme veya kayıt defterinin kendisini silme gibi yönetim işlemlerini etkiler. [Beklenmedik değişiklikleri engellemek için kaynakları kilitle ' ye](../azure-resource-manager/management/lock-resources.md)daha fazla bilgi.

## <a name="scenarios"></a>Senaryolar

Varsayılan olarak, Azure Container Registry etiketli bir görüntü *değiştirilebilir*, bu nedenle uygun izinlerle bir görüntüyü bir kayıt defterine yeniden güncelleştirebilir ve aynı etiketle birlikte görüntüleyebilirsiniz. Kapsayıcı görüntüleri de gerektiğinde [silinebilir](container-registry-delete.md) . Bu davranış, görüntü geliştirirken ve kayıt defteriniz için bir boyut korumanız gerektiğinde faydalıdır.

Ancak, üretime bir kapsayıcı görüntüsü dağıttığınızda, *sabit* bir kapsayıcı görüntüsü gerekebilir. Sabit görüntü, yanlışlıkla silememe veya üzerine yazılmayacağı bir görüntüdür.

Kayıt defterinizde bulunan görüntüleri ve sürüm görüntülerini etiketlemek için [kapsayıcı görüntülerini etiketleme ve sürüm oluşturma önerilerine](container-registry-image-tag-version.md) bakın.

Depo özniteliklerini ayarlamak için [az ACR Repository Update][az-acr-repository-update] komutunu kullanın, böylece şunları yapabilirsiniz:

* Bir görüntü sürümünü veya tüm depoyu kilitleme

* Görüntü sürümünü veya depoyu silinmeye karşı koruma, ancak güncelleştirmelere izin verme

* Görüntü sürümünde veya tüm depoda okuma (çekme) işlemlerini engelle

Örnekler için aşağıdaki bölümlere bakın. 

## <a name="lock-an-image-or-repository"></a>Bir görüntüyü veya depoyu kilitleme 

### <a name="show-the-current-repository-attributes"></a>Geçerli depo özniteliklerini göster
Bir deponun geçerli özniteliklerini görmek için şu [az ACR Repository Show][az-acr-repository-show] komutunu çalıştırın:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Geçerli görüntü özniteliklerini göster
Bir etiketin geçerli özniteliklerini görmek için şu [az ACR Repository Show][az-acr-repository-show] komutunu çalıştırın:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Resmi etikete göre kilitle

*Myrepo/MyImage:* *myregistry* içindeki Tag Image etiketini kilitlemek için şu [az ACR Repository Update][az-acr-repository-update] komutunu çalıştırın:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Bir görüntüyü bildirim özetine göre kilitleme

Bildirim Özeti (SHA-256 karması) tarafından tanımlanan bir *myrepo/MyImage* görüntüsünü kilitlemek için `sha256:...` aşağıdaki komutu çalıştırın. (Bir veya daha fazla görüntü etiketleriyle ilişkili bildirim özetini bulmak için [az ACR Repository Show-bildirimleri][az-acr-repository-show-manifests] komutunu çalıştırın.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Depoyu kilitleme

*Myrepo/MyImage* deposunu ve içindeki tüm görüntüleri kilitlemek için aşağıdaki komutu çalıştırın:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Bir görüntüyü veya depoyu silinmeye karşı koruma

### <a name="protect-an-image-from-deletion"></a>Bir görüntüyü silinmeye karşı koruma

*Myrepo/MyImage: Tag* resminin güncelleştirilmesine izin vermek, ancak silinmemelidir, şu komutu çalıştırın:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Depoyu silinmeye karşı koruma

Aşağıdaki komut, *myrepo/MyImage* deposunu, silinememesi için ayarlar. Tek tek görüntüler yine de güncelleştirilemeyebilir veya silinebilir.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Görüntüde veya depoda okuma işlemlerini engelle

*Myrepo/MyImage: Tag* görüntüsündeki okuma (çekme) işlemlerini engellemek için aşağıdaki komutu çalıştırın:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

*Myrepo/MyImage* deposundaki tüm görüntülerde okuma işlemlerini engellemek için aşağıdaki komutu çalıştırın:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Bir görüntünün veya deponun kilidini açma

*Myrepo/MyImage: Tag* resminin varsayılan davranışını silmek ve güncelleştirilmesini sağlamak üzere geri yüklemek için şu komutu çalıştırın:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

*Myrepo/MyImage* deposunun ve tüm görüntülerinin varsayılan davranışını silmek ve güncelleştiribilecekleri şekilde geri yüklemek için şu komutu çalıştırın:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir depodaki görüntü sürümlerinin silinmesini veya güncelleştirilmesini engellemek için [az ACR Repository Update][az-acr-repository-update] komutunu kullanma hakkında bilgi edindiniz. Ek öznitelikler ayarlamak için, [az ACR Repository Update][az-acr-repository-update] komut başvurusuna bakın.

Bir görüntü sürümü veya depo için ayarlanan öznitelikleri görmek için [az ACR Repository Show][az-acr-repository-show] komutunu kullanın.

Silme işlemleri hakkında daha fazla bilgi için bkz. [Azure Container Registry kapsayıcı görüntülerini silme][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

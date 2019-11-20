---
title: Azure Container Registry etiketlenmemiş bildirimleri tutma ilkesi
description: Tanımlı bir dönemden sonra etiketlenmemiş bildirimlerin otomatik olarak silinmesi için, Azure Container kayıt defterinizde bekletme ilkesini etkinleştirmeyi öğrenin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/02/2019
ms.author: danlep
ms.openlocfilehash: 79b3e48373114bfcee6dca2e6142f23bed1699e6
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972645"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Etiketlenmemiş bildirimler için bekletme ilkesi ayarlama

Azure Container Registry, ilişkili etiketleri (*etiketlenmemiş bildirimler*) olmayan depolanan görüntü bildirimleri için bir *bekletme ilkesi* ayarlama seçeneği sunar. Bir bekletme ilkesi etkinleştirildiğinde, kayıt defterindeki etiketlenmemiş bildirimler, bir dizi gün sonra otomatik olarak silinir. Bu özellik, kayıt defterinin gerekmeyen yapıtlara doldurmasını ve depolama maliyetlerinden tasarruf etmenize yardımcı olmasını önler. Etiketlenmemiş bildirimin `delete-enabled` özniteliği `false` olarak ayarlandıysa, bildirim silinemez ve bekletme ilkesi uygulanmaz.

Bu makaledeki komut örneklerini çalıştırmak için Azure CLı 'nın Azure Cloud Shell veya yerel bir yüklemesini kullanabilirsiniz. Yerel olarak kullanmak isterseniz, 2.0.74 veya üzeri sürümü gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli].

> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

> [!WARNING]
> Bakım ile bir bekletme ilkesi ayarlama--silinen görüntü verileri KURTARıLAMAZ. Bildirim özetine (görüntü adından farklı olarak) görüntüleri çeken sistemleriniz varsa, etiketlenmemiş bildirimler için bir bekletme ilkesi ayarlanmamalıdır. Etiketlenmemiş görüntüleri silmek, bu sistemlerin Kayıt defterinizden görüntüleri çekmesini engeller. Bildirime göre çekmek yerine, [Önerilen en iyi yöntem](container-registry-image-tag-version.md)olan *benzersiz etiketleme* düzenini benimsede düşünün.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

* Yalnızca bir **Premium** kapsayıcı kayıt defteri, bekletme ilkesiyle yapılandırılabilir. Kayıt defteri hizmeti katmanları hakkında bilgi için bkz. [Azure Container Registry SKU 'lar](container-registry-skus.md).
* Etiketlenmemiş bildirimler için yalnızca bir bekletme ilkesi ayarlayabilirsiniz.
* Bekletme ilkesi Şu anda yalnızca ilke etkinleştirildikten *sonra* etiketlenmemiş bildirimler için geçerlidir. Kayıt defterindeki mevcut etiketlenmemiş bildirimler ilkeye tabi değildir. Mevcut etiketlenmemiş bildirimleri silmek için, bkz. [Azure Container Registry kapsayıcı resimlerini silme](container-registry-delete.md)örnekleri.

## <a name="about-the-retention-policy"></a>Bekletme ilkesi hakkında

Azure Container Registry, kayıt defterindeki bildirimler için başvuru saymasına sahiptir. Bir bildirim etiketsiz olduğunda, bekletme ilkesini denetler. Bir bekletme ilkesi etkinse, ilkede ayarlanan gün sayısına göre belirli bir tarihle bir bildirim silme işlemi sıraya alınır.

Ayrı bir kuyruk yönetimi işi sürekli olarak iletileri işler ve gerektikçe ölçeklendirin. Örnek olarak, 30 günlük bir bekletme ilkesiyle bir kayıt defterindeki 1 saat olmak üzere iki bildirimin etiketlenmemiş olduğunu varsayalım. İki ileti sıraya alınır. Ardından, 30 gün sonra yaklaşık 1 saat sonra, iletiler kuyruktan alınır ve işlenir ve bu da ilkenin hala geçerli olduğu kabul edilir.

## <a name="set-a-retention-policy---cli"></a>Bekletme ilkesi ayarlama-CLı

Aşağıdaki örnekte, bir kayıt defterindeki etiketlenmemiş bildirimler için bir bekletme ilkesi ayarlamak üzere Azure CLı 'nın nasıl kullanılacağı gösterilmektedir.

### <a name="enable-a-retention-policy"></a>Bekletme ilkesini etkinleştirme

Varsayılan olarak, bir kapsayıcı kayıt defterinde hiçbir bekletme ilkesi ayarlanmadı. Bir bekletme ilkesi ayarlamak veya güncelleştirmek için, Azure CLı 'de [az ACR config bekletme güncelleştirme][az-acr-config-retention-update] komutunu çalıştırın. Etiketlenmemiş bildirimleri sürdürmek için 0 ile 365 arasında bir gün sayısı belirtebilirsiniz. Gün sayısı belirtmezseniz, komut varsayılan olarak 7 gün ayarlar. Saklama süresinden sonra, kayıt defterindeki etiketlenmemiş tüm bildirimler otomatik olarak silinir.

Aşağıdaki örnek, kayıt defteri *myregistry*içindeki etiketlenmemiş bildirimler için 30 günlük bir bekletme ilkesi ayarlar:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

Aşağıdaki örnek, kayıt defterindeki herhangi bir bildirimi etiketsiz olduğu anda silmek için bir ilke ayarlar. 0 günlük bir bekletme dönemi ayarlayarak bu ilkeyi oluşturun. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Bekletme ilkesini doğrulama

Önceki ilkeyi 0 günlük bir bekletme süresiyle etkinleştirirseniz etiketlenmemiş bildirimlerin silindiğini hızlı bir şekilde doğrulayabilirsiniz:

1. Kayıt defterinize bir test görüntüsünü @no__t gönderin veya seçtiğiniz başka bir test görüntüsünü değiştirin.
1. Örneğin, [az ACR Repository untag][az-acr-repository-untag] komutunu kullanarak `hello-world:latest` resminin etiketini kaldırın. Etiketlenmemiş bildirim kayıt defterinde kalır.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Birkaç saniye içinde etiketlenmemiş bildirim silinir. Örneğin, [az ACR Repository Show-bildirimleri][az-acr-repository-show-manifests] komutunu kullanarak depodaki bildirimleri listeleyerek silme işlemini doğrulayabilirsiniz. Test görüntüsü depodaki tek tek ise, deponun kendisi silinir.

### <a name="disable-a-retention-policy"></a>Bekletme ilkesini devre dışı bırakma

Kayıt defterinde ayarlanan bekletme ilkesini görmek için [az ACR config bekletme göster][az-acr-config-retention-show] komutunu çalıştırın:

```azurecli
az acr config retention show --registry myregistry
```

Bir kayıt defterinde bekletme ilkesini devre dışı bırakmak için [az ACR config bekletme güncelleştirme][az-acr-config-retention-update] komutunu çalıştırın ve `--status disabled` olarak ayarlayın:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Bekletme ilkesi ayarlama-Portal

Ayrıca, [Azure Portal](https://portal.azure.com)bir kayıt defterinin bekletme ilkesini ayarlayabilirsiniz. Aşağıdaki örnekte, bir kayıt defterindeki etiketlenmemiş bildirimler için bir bekletme ilkesi ayarlamak üzere portalın nasıl kullanılacağı gösterilmektedir.

### <a name="enable-a-retention-policy"></a>Bekletme ilkesini etkinleştirme

1. Azure Container Registry 'nize gidin. **İlkeler**altında, **bekletme** (Önizleme) öğesini seçin.
1. **Durum**' da **etkin**' i seçin.
1. Etiketlenmemiş bildirimleri sürdürmek için 0 ile 365 arasında bir gün sayısı seçin. **Kaydet**’i seçin.

![Azure portal bir bekletme ilkesi etkinleştirme](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Bekletme ilkesini devre dışı bırakma

1. Azure Container Registry 'nize gidin. **İlkeler**altında, **bekletme** (Önizleme) öğesini seçin.
1. **Durum**' da **devre dışı**' yı seçin. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Registry [görüntüleri ve depoları silmeye](container-registry-delete.md) yönelik seçenekler hakkında daha fazla bilgi edinin

* Seçili görüntüleri ve bildirimleri bir kayıt defterinden [otomatik olarak temizlemeyi](container-registry-auto-purge.md) öğrenin

* Kayıt defterindeki [görüntüleri ve bildirimleri kilitleme](container-registry-image-lock.md) seçenekleri hakkında daha fazla bilgi edinin

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests

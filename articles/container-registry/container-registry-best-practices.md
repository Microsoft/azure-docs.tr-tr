---
title: Azure Container Registry için en iyi yöntemler
description: Bu en iyi yöntemleri izleyerek Azure kapsayıcı kayıt defterinizi nasıl verimli bir şekilde kullanabileceğinizi öğrenin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/27/2018
ms.author: danlep
ms.openlocfilehash: a1ab010300d3f7bec3aeb5969a9a09fa9ee9a6a5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309763"
---
# <a name="best-practices-for-azure-container-registry"></a>Azure Container Registry için en iyi yöntemler

Bu en iyi yöntemleri izlemek, Azure’daki özel Docker kayıt defterinizin performansını ve maliyetini en iyi duruma getirmenize yardımcı olabilir.

## <a name="network-close-deployment"></a>Yakın ağ dağıtımı

Kapsayıcı kayıt defterinizi, kapsayıcıları dağıttığınız Azure bölgesinde oluşturun. Kayıt defterinizin ağ açısından kapsayıcı konaklarınıza yakın bir bölgeye yerleştirilmesi hem gecikmeyi hem de maliyeti düşürmeye yardımcı olabilir.

Yakın ağ dağıtımı, özel kapsayıcı kayıt defteri kullanmanın birincil nedenlerinden biridir. Docker görüntülerinin kademeli dağıtıma imkan tanıyan etkili bir [katman yapısı](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) vardır. Bununla birlikte, yeni düğümlerin belirli bir görüntü için gereken tüm katmanları çekmesi gerekir. Bu ilk `docker pull` hızla büyüyerek birkaç gigabayta ulaşabilir. Dağıtımınıza yakın özel bir kayıt defterinin olması bu ağ gecikmesini en aza indirir.
Buna ek olarak, Azure dahil tüm genel bulutlar ağ çıkışı ücretleri uygular. Bir veri merkezinden diğerine görüntü çekmek, gecikmeye ek olarak ağ çıkışı ücretlerine yol açar.

## <a name="geo-replicate-multi-region-deployments"></a>Çok bölgeli dağıtımları coğrafi olarak çoğaltma

Birden çok bölgeye kapsayıcı dağıtıyorsanız Azure Container Registry'nin [coğrafi çoğaltma](container-registry-geo-replication.md) özelliğini kullanın. İster yerel veri merkezlerinden küresel müşterilere hizmet sunuyor olun ister geliştirme takımınız farklı konumlarda çalışıyor olsun, kayıt defterinizi coğrafi olarak çoğaltarak kayıt defteri yönetimini basitleştirebilir ve gecikmeyi en aza indirebilirsiniz. Coğrafi çoğaltma yalnızca [Premium](container-registry-skus.md) kayıt defterleriyle kullanılabilir.

Coğrafi çoğaltma özelliğini kullanmayı öğrenmek için [Azure Container Registry’de coğrafi çoğaltma](container-registry-tutorial-prepare-registry.md) başlıklı üç bölümlü öğreticiye bakın.

## <a name="repository-namespaces"></a>Depo ad alanları

Depo ad alanlarından yararlanarak kuruluşunuz içinde tek bir kayıt defterinin birden çok grupta paylaşılmasına imkan tanıyabilirsiniz. Kayıt defterleri farklı dağıtımlarla ve takımlarla paylaşılabilir. İç içe ad alanlarını destekleyen Azure Container Registry, grup yalıtımı imkanı sunar.

Örneğin, aşağıdaki kapsayıcı görüntüsü etiketlerini göz önünde bulundurun. Kurumsal çapta kullanılan `aspnetcore` gibi görüntüler kök ad alanına yerleştirilirken, Üretim ve Pazarlama gruplarına ait her bir kapsayıcı görüntüsü kendi ad alanını kullanır.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Ayrılmış kaynak grubu

Kapsayıcı kayıt defterleri birden çok kapsayıcı ana bilgisayar genelinde kullanılan kaynaklar olduğundan, kayıt defteri kendi kaynak grubunda yer almalıdır.

Azure Container Instances gibi belirli bir konak türüyle denemeler yapabilecek olsanız da muhtemelen işiniz bittiğinde kapsayıcı örneğini silmek isteyeceksinizdir. Bununla birlikte, Azure Container Registry’ye gönderdiğiniz görüntü koleksiyonunu korumak da isteyebilirsiniz. Kayıt defterinizi kendi kaynak grubuna yerleştirerek, kapsayıcı örneğinin kaynak grubunu silerken yanlışlıkla kayıt defterindeki görüntü koleksiyonunu da silme riskini en aza indirirsiniz.

## <a name="authentication"></a>Authentication

Bir Azure kapsayıcı kayıt defteri ile kimlik doğrulama için iki ana senaryo vardır: bireysel kimlik doğrulama ve hizmet (veya "gözetimsiz") kimlik doğrulaması. Aşağıdaki tabloda, bu senaryolara kısa bir genel bakış ve her biri için önerilen kimlik doğrulama yöntemi sağlanmıştır.

| Type | Örnek senaryo | Önerilen metot |
|---|---|---|
| Bireysel kimlik | Geliştirme makinesinden görüntü çeken veya gönderen bir geliştirici. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Gözetimsiz kimlik/hizmet kimliği | Kullanıcıyla doğrudan ilgili olmayan derleme ve dağıtım işlem hatları. | [Hizmet sorumlusu](container-registry-authentication.md#service-principal) |

Azure Container Registry kimlik doğrulaması hakkında ayrıntılı bilgi edinmek için bkz. [Azure kapsayıcı kayıt defteri ile kimlik doğrulama](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Kayıt defteri boyutunu yönetme

Her [kapsayıcı kayıt DEFTERI SKU][container-registry-skus] 'sunun depolama kısıtlamaları tipik bir senaryoya göre hizalanmaya yöneliktir: Başlangıç için **temel** , üretim uygulamalarının çoğunluğu için **Standart** ve Hyper-ölçek performansı ve [coğrafi çoğaltma][container-registry-geo-replication]için **Premium** . Kayıt defterinizin kullanım ömrü boyunca kullanılmayan içerikleri düzenli olarak silerek boyutunu yönetmeniz gerekir.

Kayıt defterinizin geçerli boyutunu görüntülemek için [az ACR Show-Usage][az-acr-show-usage] Azure CLI komutunu kullanın:

```console
$ az acr show-usage --resource-group myResourceGroup --name myregistry --output table
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Azure portal Kayıt defterinize **genel bakış** bölümünde kullanılan geçerli depolamayı da bulabilirsiniz:

![Azure portalındaki kayıt defteri kullanım bilgileri][registry-overview-quotas]

### <a name="delete-image-data"></a>Görüntü verilerini sil

Azure Container Registry, kapsayıcı Kayıt defterinizden görüntü verilerini silmeye yönelik çeşitli yöntemleri destekler. Resimleri etiketle veya bildirim özetine göre silebilir veya bir depoyu tümüyle silebilirsiniz.

Etiketsiz (bazen "Dangling" veya "yalnız bırakılmış" olarak adlandırılır) görüntü verilerini kayıt defterinizden silme hakkında daha fazla bilgi için, bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Container Registry, SKU adı verilen ve her biri farklı özellikler sağlayan birkaç katmanda sunulmaktadır. Sunulan SKU’lar hakkında ayrıntılı bilgi için bkz. [Azure Container Registry SKU’ları](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md

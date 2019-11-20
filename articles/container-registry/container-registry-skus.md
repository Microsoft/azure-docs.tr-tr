---
title: Azure Container Registry SKU 'Ları
description: Azure Container Registry temel, standart ve Premium hizmet katmanlarında (SKU 'Lar) Özellikler ve sınırlar hakkında bilgi edinin.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/05/2019
ms.author: danlep
ms.openlocfilehash: 19b1fb78413f82d422779b12227b4a5e2361d813
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681823"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU 'Ları

Azure Container Registry (ACR), SKU 'Lar olarak bilinen birden çok hizmet katmanında kullanılabilir. Bu SKU 'Lar, Azure 'daki özel Docker kayıt defterinizin kapasitesini ve kullanım düzenlerini hizalamak için öngörülebilir fiyatlandırma ve çeşitli seçenekler sağlar.

| SKU | Açıklama |
| --- | ----------- |
| **Temel** | Azure Container Registry hakkında bilgi edinen geliştiriciler için düşük maliyetli bir giriş noktası. Temel kayıt defterleri standart ve Premium ile aynı programlı yeteneklere sahiptir (Azure Active Directory [kimlik doğrulaması tümleştirmesi](container-registry-authentication.md#individual-login-with-azure-ad), [görüntü silme][container-registry-delete]ve [Web kancaları][container-registry-webhook]gibi). Ancak, dahil edilen depolama ve görüntü üretimi en düşük kullanım senaryoları için uygundur. |
| **Standart** | Standart kayıt defterleri, artırılmış dahil edilen depolama ve görüntü işleme özelliklerine sahip temel ile aynı özellikleri sunar. Standart kayıt defterleri, çoğu üretim senaryosu gereksinimlerini karşılayabilir. |
| **Premium** | Premium kayıt defterleri, yüksek hacimli senaryoları etkinleştirerek en yüksek miktarda dahil edilen depolama ve eşzamanlı işlem sağlar. Daha yüksek görüntü işleme özelliklerine ek olarak Premium, birden çok bölgede tek bir kayıt defterini yönetmek için [coğrafi çoğaltma][container-registry-geo-replication] , resim etiketi imzalama, [güvenlik duvarları ve sanal ağlar (Önizleme)](container-registry-vnet.md) için [içerik güveni](container-registry-content-trust.md) gibi özellikler ekler. kayıt defterine erişimi kısıtlayın. |

Temel, standart ve Premium SKU 'Ların hepsi aynı programlı özellikleri sağlar. Ayrıca, tamamen Azure tarafından yönetilen [görüntü depolamadan][container-registry-storage] de faydalanır. Daha yüksek düzeyde bir SKU seçilmesi daha fazla performans ve ölçek sağlar. Birden çok hizmet katmanı ile, temel ile çalışmaya başlayabilir ve kayıt defteri kullanımınız arttıkça standart ve Premium 'a dönüştürebilirsiniz.

## <a name="sku-features-and-limits"></a>SKU özellikleri ve sınırları

Aşağıdaki tabloda temel, standart ve Premium hizmet katmanlarının özellikleri ve limitleri ayrıntılı olarak verilmiştir.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU 'Ları değiştirme

Azure CLı ile bir kayıt defteri SKU 'sunu veya Azure portal değiştirebilirsiniz. Geçiş yaptığınız SKU 'nun gerekli maksimum depolama kapasitesine sahip olduğu sürece SKU 'Lar arasında serbestçe geçiş yapabilirsiniz. 

### <a name="azure-cli"></a>Azure CLI

Azure CLı 'daki SKU 'Lar arasında geçiş yapmak için [az ACR Update][az-acr-update] komutunu kullanın. Örneğin, Premium 'a geçmek için:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure portal

Azure portal kapsayıcı kayıt defterine **genel bakış** ' da **Güncelleştir**' i seçin ve ardından SKU açılır listesinden yeni bir **SKU** seçin.

![Azure portal içinde kapsayıcı kayıt defteri SKU 'SU güncelleştirme][update-registry-sku]

## <a name="pricing"></a>Fiyatlandırma

Azure Container Registry SKU 'Larının her biri hakkında fiyatlandırma bilgileri için bkz. [Container Registry fiyatlandırması][container-registry-pricing].

Veri aktarımlarına ilişkin fiyatlandırma hakkında ayrıntılı bilgi için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Sonraki adımlar

**Azure Container Registry yol haritası**

Hizmette yaklaşan özellikler hakkında bilgi edinmek için GitHub 'daki [ACR yol haritasını][acr-roadmap] ziyaret edin.

**UserVoice Azure Container Registry**

[ACR UserVoice][container-registry-uservoice]'ta yeni özellik önerilerini gönderir ve oylayın.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md

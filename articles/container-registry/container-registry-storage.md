---
title: Azure Container Registry görüntü depolaması
description: Docker kapsayıcı görüntülerinizin güvenlik, artıklık ve kapasite dahil olmak üzere Azure Container Registry nasıl depolandığı hakkında ayrıntılar.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: danlep
ms.openlocfilehash: 4517cc21ca0087358e750cd480288d4ec3718791
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310530"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry kapsayıcı görüntüsü depolaması

Her [temel, standart ve Premium](container-registry-skus.md) Azure Container Registry, görüntü verileri güvenliği için bekleyen şifreleme ve görüntü verileri koruması için coğrafi yedeklilik gibi gelişmiş Azure depolama özelliklerinden yararlanır. Aşağıdaki bölümlerde, Azure Container Registry (ACR) ' de görüntü depolamanın özellikleri ve sınırları açıklanır.

## <a name="encryption-at-rest"></a>Bekleyen şifreleme

Kayıt defterinizde bulunan tüm kapsayıcı görüntüleri bekleyen olarak şifrelenir. Azure, bir görüntüyü depolamadan önce otomatik olarak şifreler ve siz veya Uygulamalarınız ve hizmetleriniz görüntüyü çektiğinizde BT 'nin şifresini çözer.

## <a name="geo-redundant-storage"></a>Coğrafi olarak yedekli depolama

Azure, kapsayıcı görüntülerinin kaybedilmesine karşı koruma sağlamak için coğrafi olarak yedekli bir depolama şeması kullanır. Azure Container Registry, kapsayıcı görüntülerinizi otomatik olarak birden çok coğrafi olarak uzak veri merkezine çoğaltarak bölgesel bir depolama arızası durumunda kaybedilmesini önler.

## <a name="geo-replication"></a>Coğrafi çoğaltma

Daha da yüksek kullanılabilirlik güvencesi gerektiren senaryolar için Premium kayıt defterlerinin [coğrafi çoğaltma](container-registry-geo-replication.md) özelliğini kullanmayı düşünün. Coğrafi çoğaltma, yalnızca bir depolama hatası değil, *Toplam* bölgesel bir hata durumunda kayıt defterinize erişimi kaybetmekten korunmaya yardımcı olur. Coğrafi çoğaltma, dağıtılmış geliştirme veya dağıtım senaryolarında daha hızlı gönderim ve çekmelerde diğer avantajlar sağlar.

## <a name="image-limits"></a>Görüntü sınırları

Aşağıdaki tabloda, Azure Container Registry için kapsayıcı görüntüsü ve depolama sınırları açıklanmaktadır.

| Resource | Sınır |
| -------- | :---- |
| Depoları | Sınırsız |
| Görüntüler | Sınırsız |
| Katmanlarda | Sınırsız |
| Tags | Sınırsız|
| Depolama | 5 TB |

Çok yüksek sayıda depolar ve Etiketler, kayıt defterinizin performansını etkileyebilir. Kullanılmayan depoları, etiketleri ve görüntüleri kayıt defteri bakım yordamlarınızın bir parçası olarak düzenli aralıklarla silin. Depolar, görüntüler *ve Etiketler gibi* silinen kayıt defteri kaynakları silinmeden sonra kurtarılamaz. Kayıt defteri kaynaklarını silme hakkında daha fazla bilgi için bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

## <a name="storage-cost"></a>Depolama maliyeti

Fiyatlandırma hakkında tam Ayrıntılar için bkz. [Azure Container Registry fiyatlandırması][pricing].

## <a name="next-steps"></a>Sonraki adımlar

Farklı Azure Container Registry SKU 'Lar (temel, standart, Premium) hakkında daha fazla bilgi için bkz. [Azure Container Registry SKU 'ları](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->

---
title: Kapsayıcı görüntüsü depolaması
description: Kapsayıcı görüntülerinizin ve diğer yapılarınızın güvenlik, artıklık ve kapasite dahil Azure Container Registry nasıl depolandığı hakkında ayrıntılar.
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047754"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry kapsayıcı görüntüsü depolaması

Her [temel, standart ve Premium](container-registry-skus.md) Azure Container Registry, bekleyen şifreleme dahil gelişmiş Azure depolama özelliklerinden faydalanır. Aşağıdaki bölümlerde Azure Container Registry (ACR) ' de görüntü depolamanın özellikleri ve sınırları açıklanır.

## <a name="encryption-at-rest"></a>Bekleyen şifreleme

Kayıt defterinizde bulunan tüm kapsayıcı görüntüleri ve diğer yapıtlar Rest 'de şifrelenir. Azure, bir görüntüyü depolamadan önce otomatik olarak şifreler ve siz veya Uygulamalarınız ve hizmetleriniz görüntüyü çektiğinizde BT 'nin şifresini çözer. İsteğe bağlı olarak, [müşteri tarafından yönetilen bir anahtarla](container-registry-customer-managed-keys.md)ek bir şifreleme katmanı uygulayın.

## <a name="regional-storage"></a>Bölgesel depolama

Azure Container Registry, müşterilerin veri fazlalığını ve uyumluluk gereksinimlerini karşılamasına yardımcı olmak için kayıt defterinin oluşturulduğu bölgedeki verileri depolar. Brezilya Güney ve Güneydoğu Asya dışındaki tüm bölgelerde Azure, kayıt defteri verilerini aynı coğrafya içindeki eşleştirilmiş bir bölgede de saklayabilir. Brezilya Güney ve Güneydoğu Asya bölgelerinde, kayıt defteri verileri her zaman bölgeye göre sınırlandırılan bu bölgeler için veri yerleşimi gereksinimlerine uyum sağlar.

Bölgesel bir kesinti oluşursa, kayıt defteri verileri kullanılamaz hale gelebilir ve otomatik olarak kurtarılamaz. Farklı coğrafi bölgeler arasında daha iyi performans sağlamak veya bir bölgesel kesinti durumunda dayanıklılık sağlamak isteyen bir veya daha fazla esneklik sağlamak için kayıt defteri verilerinin birden çok bölgede depolanmasını isteyen müşteriler [Coğrafi çoğaltmayı](container-registry-geo-replication.md)etkinleştirmelidir.

## <a name="geo-replication"></a>Coğrafi çoğaltma

Yüksek kullanılabilirlik güvencesi gerektiren senaryolar için Premium kayıt defterlerinin [coğrafi çoğaltma](container-registry-geo-replication.md) özelliğini kullanmayı düşünün. Coğrafi çoğaltma, bölgesel bir hata durumunda kayıt defterinize erişiminizi kaybetme konusunda koruma sağlamaya yardımcı olur. Coğrafi çoğaltma, dağıtılmış geliştirme veya dağıtım senaryolarında daha hızlı gönderim ve çekmelerde diğer avantajlar sağlar.

## <a name="zone-redundancy"></a>Bölge yedekliliği

Esnek ve yüksek kullanılabilirliğe sahip bir Azure Kapsayıcı kayıt defteri oluşturmak için isteğe bağlı olarak Azure bölgelerinde [bölge yedekliliği](zone-redundancy.md) etkinleştirin. Premium hizmet katmanının bir özelliği olan bölge artıklığı, kayıt defterinizi her bir etkin bölgedeki en az üç ayrı bölgeye çoğaltmak için Azure [kullanılabilirlik bölgelerini](../availability-zones/az-overview.md) kullanır. Bir kayıt defterinin güvenilirliğini ve performansını geliştirmek için coğrafi çoğaltma ve bölge yedekliliği birleştirin. 

## <a name="scalable-storage"></a>Ölçeklenebilir depolama

Azure Container Registry, [kayıt defteri depolama sınırına](container-registry-skus.md#service-tier-features-and-limits)kadar, ihtiyacınız olan sayıda depo, resim, katman veya etiket oluşturmanızı sağlar. 

Yüksek sayıda depolar ve Etiketler, kayıt defterinizin performansını etkileyebilir. Kullanılmayan depoları, etiketleri ve görüntüleri kayıt defteri bakım yordamlarınızın bir parçası olarak düzenli olarak silin ve etiketlenmemiş bildirimler için isteğe bağlı olarak bir [bekletme ilkesi](container-registry-retention-policy.md) ayarlayın. Depolar, görüntüler *ve Etiketler gibi* silinen kayıt defteri kaynakları silinmeden sonra kurtarılamaz. Kayıt defteri kaynaklarını silme hakkında daha fazla bilgi için bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

## <a name="storage-cost"></a>Depolama maliyeti

Fiyatlandırma hakkında tam Ayrıntılar için bkz. [Azure Container Registry fiyatlandırması][pricing].

## <a name="next-steps"></a>Sonraki adımlar

Temel, standart ve Premium kapsayıcı kayıt defterleri hakkında daha fazla bilgi için bkz. [Azure Container Registry hizmet katmanları](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->

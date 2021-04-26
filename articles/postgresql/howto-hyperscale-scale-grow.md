---
title: Ölçek sunucu grubu-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Daha fazla yük ile başa çıkmak için sunucu grubu belleği, disk ve CPU kaynaklarını ayarlayın
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012538"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Hiper ölçek (Citus) sunucu grubunu ölçeklendirme

PostgreSQL için Azure veritabanı-Hyperscale (Citus), daha fazla yük ile başa çıkmak için self servis Ölçeklendirmesi sağlar. Azure portal, yeni çalışan düğümleri eklemeyi ve mevcut düğümlerin sanal çekirdeğini artırmayı kolaylaştırır. Düğüm eklemek kapalı kalma gerektirmez ve hatta [parçaları](howto-hyperscale-scale-rebalance.md)yeni düğümlere (parça olarak adlandırılır), sorguları kesintiye uğramadan hareket ettirilmesine neden olur.

## <a name="add-worker-nodes"></a>Çalışan düğümleri Ekle

Düğüm eklemek için, Hyperscale (Citus) sunucu grubundaki **işlem + depolama** sekmesine gidin.  **Çalışan düğümü sayısı** için kaydırıcıyı sürüklemek değeri değiştirir.

> [!NOTE]
>
> [Temel katmanla (Önizleme)](concepts-hyperscale-tiers.md) oluşturulan bir hiper ölçek (Citus) sunucu grubunun çalışanı yok. Çalışan sayısını artırmak sunucu grubunu Standart katmana otomatik olarak ayırır.
> Bir sunucu grubunu Standart katmana mezun olduktan sonra, temel katmana geri indirgeyemezsiniz.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Kaynak sürgüleri":::

Değiştirilen değerin etkili olması için **Kaydet** düğmesine tıklayın.

> [!NOTE]
> Sayıları artırılıp kaydedildikten sonra çalışan düğümlerinin sayısı kaydırıcı kullanılarak düşürülemez.

> [!NOTE]
> Yeni eklenen düğümlerin avantajlarından yararlanmak için, [Dağıtılmış tablo](howto-hyperscale-scale-rebalance.md)parçaları 'nı yeniden dengelemeniz gerekir, bu [da bazı parçaları](concepts-hyperscale-distributed-data.md#shards) mevcut düğümlerden yeni olanlara taşımak anlamına gelir.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Düğümlerdeki sanal çekirdekleri artırma veya azaltma

Yeni düğümler eklemek yerine mevcut düğümlerin yeteneklerini artırabilirsiniz. İşlem kapasitesini yukarı ve aşağı ayarlamak, performans denemeleri ve trafik taleplerine yönelik kısa veya uzun süreli değişiklikler için yararlı olabilir.

Tüm çalışan düğümlerinin sanal çekirdeğini değiştirmek için **yapılandırma (çalışan düğümü başına)** altındaki **vçekirdekler** kaydırıcısını ayarlayın. Düzenleyici düğümünün sanal çekirdekleri bağımsız olarak ayarlanabilir. **Yapılandırma (düzenleyici düğümü)** altında **vçekirdekler** kaydırıcısını ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

- Sunucu grubu [performans seçenekleri](concepts-hyperscale-configuration-options.md)hakkında daha fazla bilgi edinin.
- Tüm çalışan düğümlerinin paralel sorgulara katılmasını sağlamak için [Dağıtılmış tablo parçaları yeniden dengeleyin](howto-hyperscale-scale-rebalance.md)

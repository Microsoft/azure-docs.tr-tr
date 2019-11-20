---
title: PostgreSQL için Azure veritabanı-hiper ölçek (Citus) sunucu grubu ölçeklendirme
description: Daha fazla yük ile başa çıkmak için sunucu grubu belleği, disk ve CPU kaynaklarını ayarlayın
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 6053ba37bf330f6b59e291dade822a5ca9de8c85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492315"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Hiper ölçek (Citus) sunucu grubunu ölçeklendirme

PostgreSQL için Azure veritabanı-Hyperscale (Citus), daha fazla yük ile başa çıkmak için self servis Ölçeklendirmesi sağlar. Azure portal yeni çalışan düğümleri eklemeyi ve mevcut düğümlerin kapasitesini artırmayı kolaylaştırır.

## <a name="add-worker-nodes"></a>Çalışan düğümleri Ekle

Düğüm eklemek için, Hyperscale (Citus) sunucu grubundaki **Yapılandır** sekmesine gidin.  **Çalışan düğümü sayısı** için kaydırıcıyı sürüklemek değeri değiştirir.

![Kaynak sürgüleri](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Değiştirilen değerin etkili olması için **Kaydet** düğmesine tıklayın.

> [!NOTE]
> Arttırıldıktan ve kaydedildikten sonra, çalışan düğümlerinin sayısı kaydırıcı kullanılarak azaltılemez.

### <a name="rebalance-shards"></a>Parçaları yeniden dengeleme

Yeni eklenen düğümlerin avantajlarından yararlanmak için, [Dağıtılmış tablo parçaları](concepts-hyperscale-distributed-data.md#shards)'nı yeniden dengelemeniz gerekir, bu da bazı parçaları mevcut düğümlerden yeni olanlara taşımak anlamına gelir. İlk olarak, yeni çalışanların sağlamayı başarıyla bitirdiğini doğrulayın. Ardından, psql ile küme Düzenleyicisi düğümüne bağlanarak ve çalıştıran parça yeniden dengeleyiciyi başlatın:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards` işlevi, bağımsız değişkeninde adlı tablonun birlikte bulundurma [grubundaki tüm](concepts-hyperscale-colocation.md) tabloları yeniden dengeler. Bu nedenle, her dağıtılmış tablo için işlevi çağırmanız gerekmez, bunu her bir birlikte bulundurma grubundan temsili bir tabloda çağırmanız yeterlidir.

## <a name="increase-vcores-or-storage-space"></a>Sanal çekirdekleri veya depolama alanını artırma

Yeni düğümler eklemenin yanı sıra, mevcut düğümlerin yeteneklerini de artırabilirsiniz. Hiper ölçek (Citus) sunucu grubunuzdaki **Yapılandır** sekmesine gidin ve **sanal çekirdekler** ve **depolama** için kaydırıcıyı sürükleyerek tüm çalışan düğümleri için bu değerleri değiştirin. Değişiklikleri uygulamak için **Kaydet** ' e tıkladığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Sunucu grubu [performans seçenekleri](concepts-hyperscale-configuration-options.md)hakkında daha fazla bilgi edinin.

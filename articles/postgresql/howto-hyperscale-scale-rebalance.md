---
title: Parça yeniden dengeleme-hiper ölçek (Citus)-PostgreSQL için Azure veritabanı
description: Daha iyi performans için parçaları sunucular arasında eşit olarak dağıtın
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305710"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Hiperscale (Citus) sunucu grubundaki parçaları yeniden dengeleme

Yeni eklenen düğümlerin avantajlarından yararlanmak için, [Dağıtılmış tablo parçaları](concepts-hyperscale-distributed-data.md#shards)'nı yeniden dengelemeniz gerekir, bu da bazı parçaları mevcut düğümlerden yeni olanlara taşımak anlamına gelir.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Sunucu grubunun yeniden dengelenmesi gerekip gerekmediğini belirleme

Azure portal, verilerin bir sunucu grubundaki çalışan düğümleri arasında eşit olarak dağıtılıp dağıtılmadığını gösterebilir. Görmek için, **sunucu grubu yönetim** menüsünde parça **yeniden dengeleyici** sayfasına gidin. Veriler çalışanlar arasında eğilense, her bir düğümün boyutunun bir listesi ile birlikte, ileti **yeniden dengelemesinin önerildiğini** görürsünüz.

Veriler zaten dengese, şu **anda Ileti yeniden dengelemesinin önerilmediğini** görürsünüz.

## <a name="run-the-shard-rebalancer"></a>Parça yeniden dengeleyiciyi çalıştırın

Parça yeniden dengeleyiciyi başlatmak için sunucu grubunun düzenleyici düğümüne bağlanmanız ve dağıtılmış tablolarda [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL işlevini çalıştırmanız gerekir. İşlevi, bağımsız değişkeninde adlı tablonun birlikte bulundurma [grubundaki tüm](concepts-hyperscale-colocation.md) tabloları yeniden dengeler. Bu nedenle, her dağıtılmış tablo için işlevi çağırmanız gerekmez, bunu her bir birlikte bulundurma grubundan temsili bir tabloda çağırmanız yeterlidir.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Yeniden dengeleme ilerlemesini izleme

Başlattıktan sonra yeniden dengeleyiciyi izlemek için Azure portal geri dönün. **Sunucu grubu yönetimi**'nde parça **yeniden dengeleme** sayfasını açın. İleti **yeniden dengelemenin** iki tabloyla birlikte devam ettiği gösterilir.

İlk tablo, düğüm içine veya dışına taşınan parça sayısını gösterir. Örneğin, "6/24 ' te taşınır." İkinci tablo veritabanı tablosu başına ilerlemeyi gösterir: ad, parça sayısı etkilendi, veri boyutu etkilendi ve yeniden dengeleme durumu.

Sayfayı güncelleştirmek için **Yenile** düğmesini seçin. Yeniden dengeleme tamamlandığında, yeniden **dengelemenin Şu anda önerilmediğini** varsayalım.

## <a name="next-steps"></a>Sonraki adımlar

- Sunucu grubu [performans seçenekleri](concepts-hyperscale-configuration-options.md)hakkında daha fazla bilgi edinin.
- [Sunucu grubunu](howto-hyperscale-scale-grow.md) yukarı veya aşağı ölçeklendirme
- [Rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) başvuru malzemesini inceleyin

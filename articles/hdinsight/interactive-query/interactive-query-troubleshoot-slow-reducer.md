---
title: Azure HDInsight 'ta Reducer yavaş
description: Reducer, Azure HDInsight 'ta olası veri eğme için yavaş
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930811"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Reducer yavaş

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Sorgu planı gibi bir sorgu çalıştırırken `insert into table1 partition(a,b) select a,b,c from table2` bir azaltıcının, ancak her bölümdeki veriler tek bir Reducer gider. Bu, sorgunun en büyük bölüm Reducer tarafından geçen süre kadar yavaş olmasına neden olur.

## <a name="cause"></a>Nedeni

[Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) açın ve küme değerini doğrulayın `hive.optimize.sort.dynamic.partition` .

Bu değişkenin değeri, verilerin doğası temelinde doğru/yanlış olarak ayarlanmalıdır.

Giriş tablosundaki bölümler daha az (10 ' dan küçük) ise ve bu nedenle çıkış bölümlerinin sayısı ise ve değişkeni olarak ayarlanırsa `true` , bu, verilerin bölüm başına tek bir Reducer kullanılarak genel olarak sıralanmasını ve yazılmasına neden olur. Kullanılabilir azaltıcının sayısı daha büyük olsa bile, veri eğriliği nedeniyle, en yüksek paralellik için bir kaç azaltıcının geri alınamaz. Olarak değiştirildiğinde `false` , birden fazla Reducer tek bir bölümü işleyebilir ve birden çok daha küçük dosya yazılır ve daha hızlı ekleme olur. Bu, daha küçük dosyaların varlığı nedeniyle daha fazla sorgu etkileyebilir.

Bir değeri, `true` bölüm sayısının büyük olduğu ve verilerin Eğilemez olduğu durumlarda anlamlı hale gelir. Bu gibi durumlarda, her bölüm tek bir Reducer tarafından işleneceğinden, daha iyi bir sonraki sorgu performansına neden olacak şekilde harita aşamasının sonucu yazılır.

## <a name="resolution"></a>Çözüm

1. Verileri birden çok bölüme normalleştirmek için yeniden bölümlendirmenize çalışın.

1. #1 mümkün değilse, Beeline oturumunda yapılandırma değerini false olarak ayarlayın ve sorguyu yeniden deneyin. `set hive.optimize.sort.dynamic.partition=false`. Küme düzeyinde değerin false olarak ayarlanması önerilmez. Değeri `true` en iyi deyişle, veri ve sorgu yapısına bağlı olarak parametreyi gereken şekilde ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
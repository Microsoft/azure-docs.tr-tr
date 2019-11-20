---
title: Azure HDInsight 'ta Reducer yavaş
description: Reducer, Azure HDInsight 'ta olası veri eğme için yavaş
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 6b16cc0423d2e6c45e399fd332ecd8cfca8c2933
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091288"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Reducer yavaş

Bu makalede, Azure HDInsight kümelerinde etkileşimli sorgu bileşenlerini kullanırken sorunlar için sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Sorgu planı gibi bir sorgu `insert into table1 partition(a,b) select a,b,c from table2` çalıştırırken bir azaltıcının, ancak her bölümdeki veriler tek bir Reducer gider. Bu, sorgunun en büyük bölüm Reducer tarafından geçen süre kadar yavaş olmasına neden olur.

## <a name="cause"></a>Nedeni

[Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) açın ve küme `hive.optimize.sort.dynamic.partition`değerini doğrulayın.

Bu değişkenin değeri, verilerin doğası temelinde doğru/yanlış olarak ayarlanmalıdır.

Giriş tablosundaki bölümler daha az (10 ' dan küçük) ise ve bu nedenle çıkış bölümlerinin sayısı ise ve değişkeni olarak `true`ayarlanırsa, bu, verilerin bölüm başına tek bir Reducer kullanılarak genel olarak sıralanmasını ve yazılmasına neden olur. Kullanılabilir azaltıcının sayısı daha büyük olsa bile, veri eğriliği nedeniyle, en yüksek paralellik için bir kaç azaltıcının geri alınamaz. Olarak `false`değiştirildiğinde, birden fazla Reducer tek bir bölümü işleyebilir ve birden çok daha küçük dosya yazılır ve daha hızlı ekleme olur. Bu, daha küçük dosyaların varlığı nedeniyle daha fazla sorgu etkileyebilir.

Bir değeri `true` , bölüm sayısının büyük olduğu ve verilerin Eğilemez olduğu durumlarda anlamlı hale gelir. Bu gibi durumlarda, her bölüm tek bir Reducer tarafından işleneceğinden, daha iyi bir sonraki sorgu performansına neden olacak şekilde harita aşamasının sonucu yazılır.

## <a name="resolution"></a>Çözüm

1. Verileri birden çok bölüme normalleştirmek için yeniden bölümlendirmenize çalışın.

1. #1 mümkün değilse, Beeline oturumunda yapılandırma değerini false olarak ayarlayın ve sorguyu yeniden deneyin. `set hive.optimize.sort.dynamic.partition=false`. Küme düzeyinde değerin false olarak ayarlanması önerilmez. Değeri en iyi `true` deyişle, veri ve sorgu yapısına bağlı olarak parametreyi gereken şekilde ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* Azure Community [@AzureSupport](https://twitter.com/azuresupport) 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için lütfen [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.

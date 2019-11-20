---
title: Azure Stream Analytics işleri iyileştirmek için yeniden bölümleme kullanma
description: Bu makalede, paralelleştirilmedi Azure Stream Analytics işleri iyileştirmek için yeniden bölümlemeden nasıl kullanılacağı açıklanır.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: bbea71464e8a1f4e93e510106d372257f155b0c6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935058"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Azure Stream Analytics ile işlemeyi iyileştirmek için yeniden bölümleme kullanın

Bu makalede, tam olarak [Paralelleştirilmesi](stream-analytics-scale-jobs.md)gereken senaryolara yönelik Azure Stream Analytics sorgunuzu ölçeklendirmek için yeniden bölümlemeden nasıl kullanılacağı gösterilmektedir.

Şunu yaparsanız [paralelleştirme](stream-analytics-parallelization.md) kullanmeyebilirsiniz:

* Giriş akışınız için bölüm anahtarını denetlememeniz gerekir.
* Kaynak "sprtikleriniz", daha sonra birleştirilmek zorunda olan birden çok bölüm genelinde giriş. 

## <a name="how-to-repartition"></a>Yeniden bölümleme

Event Hubs için **PartitionID** gibi bir doğal giriş şemasına göre parçalı olmayan bir akışta verileri işleçalıştığınızda yeniden bölümlendirip veya reshuffling gereklidir. Yeniden bölümlerseniz, her parça bağımsız olarak işlenebilir ve bu da akış işlem hattınızı daha erken ölçeklendirmenize olanak tanır.

Yeniden bölümlemek için, sorginizdeki bir **bölüm by** ifadesinden **sonra anahtar sözcüğünü** kullanın. Aşağıdaki örnek, verileri **DeviceID** 'yi 10 bölüm sayısına göre bölümlendirir. **DeviceID** 'nin karması, hangi bölümün hangi alt akışı kabul edeceğini tespit etmek için kullanılır. Veriler bölümlenmiş her akış için bağımsız olarak temizlenir ve çıktının bölümlenmiş yazmaları desteklediği kabul edilir ve 10 bölüm vardır.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Aşağıdaki örnek sorgu, yeniden bölümlenmiş verilerin iki akışını birleştirir. Yeniden bölümlenmiş verilerin iki akışı birleştirilirken akışlar aynı bölüm anahtarına ve sayıya sahip olmalıdır. Sonuç, aynı bölüm düzenine sahip bir akıştır.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Çıkış şeması, her alt akışın bağımsız olarak temizleneceği şekilde akış şeması anahtarıyla ve sayısıyla eşleşmelidir. Akış, temizlenmeden önce farklı bir düzen tarafından birleştirilebilir ve yeniden bölümlenebilir, ancak işlemin genel gecikme süresine eklediğinden ve kaynak kullanımını arttığı için bu yöntemden kaçının.

## <a name="streaming-units-for-repartitions"></a>Yeniden bölümler için akış birimleri

İhtiyacınız olan bölümlerin tam sayısını öğrenmek için işinizin kaynak kullanımını deneyin ve gözlemleyin. [Akış birimlerinin sayısı (su)](stream-analytics-streaming-unit-consumption.md) , her bölüm için gereken fiziksel kaynaklara göre ayarlanmalıdır. Genel olarak, her bölüm için altı SUs gerekir. İşe atanan yeterli kaynak yoksa, sistem yalnızca işi yararlılığı durumunda yeniden bölümlendirmesi uygular.

## <a name="repartitions-for-sql-output"></a>SQL çıkışı için yeniden bölümler

İşiniz çıktı için SQL veritabanı kullandığında, üretilen iş miktarını en üst düzeye çıkarmak için en iyi bölüm sayısını eşleştirmek üzere açık yeniden bölümleme kullanın. SQL sekiz yazıcı ile en iyi şekilde çalıştığından, temizlemeye başlamadan önce akışı sekiz olarak yeniden bölümlendirip veya daha fazla yukarı akış, iş performansına yarar sağlayabilir. 

8 ' den fazla giriş bölümü olduğunda, giriş bölümleme düzenini devralma uygun bir seçenek olmayabilir. Çıktı yazıcılarının sayısını açıkça belirtmek için [sorgunuzda ' i kullanmayı göz](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) önünde bulundurun. 

Aşağıdaki örnek, doğal olarak bölümlenmeden bağımsız olarak girdiden ve veri kümesini DeviceID boyutuna göre yeniden bölümleyip akışa alarak çıktıyı temizler. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Daha fazla bilgi için bkz. [Azure SQL veritabanı 'na Azure Stream Analytics çıktısı](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics kullanmaya başlayın](stream-analytics-introduction.md)
* [Azure Stream Analytics sorgu paralelleştirme özelliğinden yararlanın](stream-analytics-parallelization.md)

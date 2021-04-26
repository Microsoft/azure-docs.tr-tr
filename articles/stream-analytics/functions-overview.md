---
title: Azure Stream Analytics 'de Kullanıcı tanımlı işlevler
description: Bu makale, Azure Stream Analytics içindeki kullanıcı tanımlı işlevlere genel bir bakış.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020511"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics 'de Kullanıcı tanımlı işlevler

Azure Stream Analytics SQL benzeri sorgu dili, akış verilerinde gerçek zamanlı analiz mantığını uygulamayı kolaylaştırır. Stream Analytics, sorgunuzda çağrılan özel işlevlerle daha fazla esneklik sağlar. Aşağıdaki kod örneği, `sampleFunction` bir parametre kabul eden ADLı UDF 'dir, işin aldığı her giriş kaydı ve sonuç çıkışa yazılır `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>İşlev türleri

Azure Stream Analytics aşağıdaki dört işlev türünü destekler: 

* JavaScript kullanıcı tanımlı işlevleri 
* JavaScript Kullanıcı tanımlı toplamalar 
* C# Kullanıcı tanımlı işlevler (Visual Studio kullanarak) 
* Azure Machine Learning 

Makine öğrenimi modelleri, dize düzenlemeleri, karmaşık matematik hesaplamaları, kodlama ve kod çözme verileri ile gerçek zamanlı Puanlama gibi senaryolar için bu işlevleri kullanabilirsiniz. 

## <a name="limitations"></a>Sınırlamalar

Kullanıcı tanımlı işlevler durumsuz ve dönüş değeri yalnızca skaler bir değer olabilir. Bu Kullanıcı tanımlı işlevlerden dış REST uç noktalarına çağrı yapılamaz, çünkü işinizin performansı büyük olasılıkla etkiler. 

Azure Stream Analytics, tüm işlev etkinleştirmeleri ve döndürülen sonuçlar için bir kayıt yapmaz. Örneğin, yinelenebilirlik sağlamak için, işinizi eski zaman damgasından yeniden çalıştırmak aynı sonuçları yeniden üretir `Date.GetData()` `Math.random()` . bu işlevler her bir çağrı için aynı sonucu döndürmediğinden, veya gibi işlevleri kullanmayın.  

## <a name="resource-logs"></a>Kaynak günlükleri

Tüm çalışma zamanı hataları önemli olarak değerlendirilir ve etkinlik ve kaynak günlükleri aracılığıyla ortaya çıkmış demektir. İşlevinizin tüm özel durumları ve hataları işlemesi ve sorgunuza geçerli bir sonuç döndürmesi önerilir. Bu, işinizin [başarısız durumuna](job-states.md)gitmesini engeller.  

## <a name="exception-handling"></a>Özel durum işleme

Veri işleme sırasında herhangi bir özel durum, Azure Stream Analytics veri tükettiği zaman çok zararlı bir hata olarak değerlendirilir. Kullanıcı tanımlı işlevlerin özel durumlar oluşturması ve işlemenin durdurulmasına neden olma olasılığı yüksektir. Bu sorundan kaçınmak için, kod yürütme sırasında özel durumları yakalamak Için JavaScript veya C# ' de bir *try-catch* bloğu kullanın. Yakalanan özel durumlar, sistem hatasına neden olmadan günlüğe kaydedilebilir ve işlenebilirler. İşlem altyapısına beklenmedik özel durumlar oluşturmamak için özel kodunuzu her zaman bir *try-catch* bloğunda kaydırabilmeniz önerilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics 'de Kullanıcı tanımlı JavaScript işlevleri](stream-analytics-javascript-user-defined-functions.md)
* [JavaScript Kullanıcı tanımlı toplamaları Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Azure Stream Analytics işleri için .NET Standard Kullanıcı tanımlı işlevler geliştirme](stream-analytics-edge-csharp-udf-methods.md)
* [Azure Stream Analytics Azure Machine Learning ile tümleştirin](machine-learning-udf.md)

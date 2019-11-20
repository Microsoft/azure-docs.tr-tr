---
title: Azure Izleyici 'de verimli günlük sorguları yazma | Microsoft Docs
description: Log Analytics sorguları yazmayı öğrenmek için kaynaklara başvurular.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: a5ee03f6c42f076549856161a6ebe0b1888fe4aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894126"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Azure Izleyici 'de verimli günlük sorguları yazma
Bu makalede, Azure Izleyici 'de etkili günlük sorguları yazma önerileri sağlanır. Bu stratejileri kullanarak, sorgularınızın hızlı bir şekilde ve en düşük fazla Heard ile çalışacağını sağlayabilirsiniz.

## <a name="scope-your-query"></a>Sorgunuzun kapsamını
Sorgunuzun gerçekten ihtiyaç duyduğundan daha fazla veri işleminizin olması, uzun süreli bir sorguya yol açabilir ve genellikle sonuçlarınızda çok fazla verinin etkin bir şekilde analiz edilmesine neden olur. Olağanüstü durumlarda sorgu zaman aşımına uğrar ve başarısız olabilir.

### <a name="specify-your-data-source"></a>Veri kaynağınızı belirtin
Etkili bir sorgu yazarken ilk adım kapsamını gereken veri kaynaklarıyla sınırlandırırsınız. Bir tablo belirtme, `search *`gibi geniş bir metin araması çalıştırılarak her zaman tercih edilir. Belirli bir tabloyu sorgulamak için aşağıdaki gibi tablo adıyla sorgunuzu başlatın:

``` Kusto
requests | ...
```

Aşağıdaki gibi bir sorgu kullanarak belirli tablolardaki birden çok sütunda bir değer aramak için [aramayı](/azure/kusto/query/searchoperator) kullanabilirsiniz:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Aşağıdaki gibi çeşitli tabloları sorgulamak için [UNION](/azure/kusto/query/unionoperator) kullanın:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Bir zaman aralığı belirtin
Ayrıca sorgunuzu, ihtiyacınız olan veri aralığı ile sınırlandırmalısınız. Varsayılan olarak, sorgunuz son 24 saat içinde toplanan verileri dahil eder. [Zaman aralığı seçicisindeki](get-started-portal.md#select-a-time-range) bu seçeneği değiştirebilir veya doğrudan sorgunuza ekleyebilirsiniz. Sorgunuzun geri kalanı yalnızca söz konusu aralıktaki verileri işleyerek, tablo adından hemen sonra zaman filtresini eklemek en iyisidir:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Yalnızca en son kayıtları al

Yalnızca en son kayıtları döndürmek için, *izleme* tablosunda günlüğe kaydedilen en son 10 kaydı döndüren aşağıdaki sorguda *en üstteki* işleci kullanın:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Kayıtları filtrele
Yalnızca belirli bir koşulla eşleşen günlükleri gözden geçirmek için, aşağıdaki sorguda *olduğu* gibi, yalnızca _daitylevel_ değerinin 0 ' dan yüksek olduğu kayıtları döndüren WHERE işlecini kullanın:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Dize karşılaştırmaları
[Dizeleri değerlendirirken](/azure/kusto/query/datatypes-string-operators), genellikle tam belirteçleri ararken `contains` yerine `has` kullanmanız gerekir. `has`, alt dizeler için arama yapmak zorunda olmadığından daha etkilidir.

## <a name="returned-columns"></a>Döndürülen sütunlar

İşlenmekte olan sütun kümesini yalnızca ihtiyaç duyduğunuz sütunlara daraltmak için [Proje](/azure/kusto/query/projectoperator) kullanın:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Değerleri hesaplamak ve kendi sütunlarınızı oluşturmak için [Genişlet](/azure/kusto/query/extendoperator) ' i kullanarak, genellikle tablo sütununa filtre uygulamak daha verimli olacaktır.

Örneğin, _işlem\_adı_ üzerinde filtre uygulayan ilk sorgu, yeni bir _abonelik_ sütunu oluşturan ve üzerinde filtre uygulayan ikinciden daha etkili olacaktır:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Birleştirmeleri kullanma
[JOIN](/azure/kusto/query/joinoperator) işlecini kullanırken, sorgunun sol tarafında daha az satır içeren tabloyu seçin.


## <a name="next-steps"></a>Sonraki adımlar
Sorgu en iyi uygulamaları hakkında daha fazla bilgi edinmek için bkz. [sorgu en iyi yöntemleri](/azure/kusto/query/best-practices).
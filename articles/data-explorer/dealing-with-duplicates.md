---
title: Azure Veri Gezgini 'de yinelenen verileri işleme
description: Bu konuda, Azure Veri Gezgini kullanırken yinelenen verilerle uğraşmak için çeşitli yaklaşımlar gösterilecektir.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608236"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Azure Veri Gezgini 'de yinelenen verileri işleme

Buluta veri gönderen cihazlar, verilerin yerel bir önbelleğini korur. Veri boyutuna bağlı olarak, yerel önbellek verileri günler veya hatta aylar için depoluyor olabilir. Ön belleğe alınmış verileri yeniden veren ve analitik veritabanında veri çoğaltmasına neden olan hatalı çalışan cihazlardan analitik veritabanlarınızı korumak istiyorsunuz. Bu konu, bu tür senaryolar için yinelenen verileri işlemeye yönelik en iyi yöntemleri özetler.

Veri çoğaltma için en iyi çözüm yinelemeyi engellemektedir. Mümkünse, veri işlem hattındaki bu sorunu giderip veri işlem hattında veri hareketiyle ilişkili maliyetleri kaydeder ve sistemle birlikte bulunan yinelenen verilerle içeri aktarılmalarını önler. Ancak, kaynak sistemin değiştirilemediği durumlarda, bu senaryoya yönelik çeşitli yollar vardır.

## <a name="understand-the-impact-of-duplicate-data"></a>Yinelenen verilerin etkisini anlayın

Yinelenen verilerin yüzdesini izleyin. Yinelenen verilerin yüzdesi bulunduktan sonra, sorunun kapsamını ve iş etkisini analiz edebilir ve uygun çözümü seçebilirsiniz.

Yinelenen kayıt yüzdesini belirlemek için örnek sorgu:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Yinelenen verileri işlemeye yönelik çözümler

### <a name="solution-1-dont-remove-duplicate-data"></a>Çözüm #1: Yinelenen verileri kaldırma

Yinelenen verilerin iş gereksinimlerinizi ve toleransını anlayın. Bazı veri kümeleri, yinelenen verilerin belirli bir yüzdesi ile yönetebilir. Yinelenen verilerin büyük bir etkisi yoksa, varlığını yoksayabilirsiniz. Yinelenen verileri kaldırmanın avantajı, alma işleminin veya sorgu performansının ek bir yükü olmamasıdır.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Çözüm #2: Sorgu sırasında yinelenen satırları işle

Sorgu sırasında verilerdeki yinelenen satırları filtrelemeniz başka bir seçenektir. [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) Toplu işlev, yinelenen kayıtları filtrelemek ve zaman damgasına (veya başka bir sütuna) göre son kaydı döndürmek için kullanılabilir. Bu yöntemi kullanmanın avantajı, sorgu süresi sırasında aynı zamanda çoğaltılmasından daha hızlı bir şekilde yapılır. Ayrıca, tüm kayıtlar (yinelemeler dahil) denetim ve sorun giderme için kullanılabilir. `arg_max` İşlevi kullanmanın dezavantajı, verilerin her sorgulanışında ek sorgu zamanı ve CPU üzerinde yükleme olur. Sorgulanan verilerin miktarına bağlı olarak, bu çözüm işlevsel olmayan veya bellek kullanan bir duruma gelebilir ve diğer seçeneklere geçiş yapılmasını gerektirir.

Aşağıdaki örnekte, benzersiz kayıtları tespit eden bir sütun kümesi için alınan son kaydı sorgulıyoruz:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Bu sorgu, tabloyu doğrudan sorgulamak yerine bir işlevin içine de yerleştirilebilecek:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Çözüm #3: Alma işlemi sırasında yinelenenleri filtrele

Başka bir çözüm, alma işlemi sırasında yinelenenleri filtrelemeye yönelik bir çözümdür. Sistem, kusto tablolarında alma sırasında yinelenen verileri yoksayar. Veriler hazırlama tablosuna alınır ve yinelenen satırlar kaldırıldıktan sonra başka bir tabloya kopyalanır. Bu çözümün avantajı, önceki çözümle karşılaştırıldığında sorgu performansının önemli ölçüde artmasına olanak sağlar. Dezavantajları, artan alım süresini ve ek veri depolama maliyetlerini içerir. Bu çözüm, yalnızca dupliingin eşzamanlı olarak sunulmadığından işe yarar. Yinelenen kayıtlar içeren birden çok eş zamanlı Alım varsa, yinelenenleri kaldırma işlemi tabloda varolan herhangi bir eşleşen kaydı bulmayacak olduğundan, tümü alınabilir.    

Aşağıdaki örnek bu yöntemi göstermektedir:

1. Aynı şemaya sahip başka bir tablo oluşturun:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Yeni kayıtları daha önce alınan olanlarla birleştirerek yinelenen kayıtları filtrelemek için bir işlev oluşturun.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Birleşimler, CPU 'ya bağlı işlemlerdir ve sisteme ek bir yük ekler.

1. `DeviceEventsUnique` Tablodaki [güncelleştirme ilkesini](/azure/kusto/management/update-policy) ayarla. Yeni veriler `DeviceEventsAll` tabloya geçtiğinde güncelleştirme ilkesi etkinleştirilir. Yeni [kapsamlar](/azure/kusto/management/extents-overview) oluşturulduğunda kusto altyapısı işlevi otomatik olarak yürütür. İşleme, yeni oluşturulan verilerin kapsamına alınır. Aşağıdaki komut, güncelleştirme ilkesini oluşturmak için kaynak tablo`DeviceEventsAll`(), hedef tablo`DeviceEventsUnique`() ve işlevi `RemoveDuplicatesDeviceEvents` birlikte oluşturur.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Güncelleştirme ilkesi, verilerin alma sırasında filtrelenmesini ve iki kez ( `DeviceEventsAll` tabloya ve `DeviceEventsUnique` tabloya) göre filtrelenmesinden itibaren alma süresini uzatır.

1. Seçim Verilerin kopyalarının depolanmasını önlemek için `DeviceEventsAll` tabloda daha düşük bir veri saklama alanı ayarlayın. Veri hacmine ve sorun giderme için verileri bekletmek istediğiniz sürenin uzunluğuna bağlı olarak gün sayısını seçin. Verileri depolama alanına yüklenmediğinden, `0d` SMM 'yi kaydetmek ve performansı artırmak için gün bekletme olarak ayarlayabilirsiniz.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Özet

Veri çoğaltma, birden çok şekilde işlenebilir. İşletmeniz için doğru yöntemi öğrenmek için, seçenekleri dikkatle değerlendirin, hesap fiyatını ve performansını inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Veri Gezgini için sorgu yazma](write-queries.md)

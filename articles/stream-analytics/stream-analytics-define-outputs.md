---
title: Azure Stream Analytics çıkış çıkışları
description: Bu makalede Azure Stream Analytics için kullanılabilen veri çıkışı seçenekleri açıklanmaktadır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 3ce4f673657561e196520466b569d0cf83d75a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019355"
---
# <a name="outputs-from-azure-stream-analytics"></a>Azure Stream Analytics çıkış çıkışları

Azure Stream Analytics iş bir giriş, sorgu ve bir çıktıdan oluşur. Dönüştürülmüş verileri gönderebilmeniz için birkaç çıktı türü vardır. Bu makalede desteklenen Stream Analytics çıkışları listelenmektedir. Stream Analytics sorgunuzu tasarlarken, [from yan tümcesini](/stream-analytics-query/into-azure-stream-analytics)kullanarak çıkışın adına bakın. Sorguya birden çok yan tümce ekleyerek, iş başına tek bir çıktı veya akış işi başına birden fazla çıkış (ihtiyacınız varsa) kullanabilirsiniz.

Stream Analytics iş çıktıları oluşturmak, düzenlemek ve test etmek için [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), [REST API](/rest/api/streamanalytics/)ve [Visual Studio 'yu](stream-analytics-quick-create-vs.md)kullanabilirsiniz.

Bazı çıkış türleri [bölümleme](#partitioning)destekler ve [Çıkış toplu işlem boyutları](#output-batch-size) , üretilen işi iyileştirmek için farklılık gösterir. Aşağıdaki tabloda her çıkış türü için desteklenen özellikler gösterilmektedir:

| Çıkış türü | Bölümleme | Güvenlik | 
|-------------|--------------|----------|
|[Azure Data Lake Storage 1. Nesil](azure-data-lake-storage-gen1-output.md)|Yes|Azure Active Directory Kullanıcı </br> , Yönetilen kimlik|
|[Azure SQL Veritabanı](sql-database-output.md)|Evet, isteğe bağlı.|SQL kullanıcı kimlik doğrulaması, </br> Yönetilen Kimlik (önizleme)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Yes|SQL kullanıcı kimlik doğrulaması, </br> Yönetilen Kimlik (önizleme)|
|[BLOB depolama ve Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Yes|Erişim anahtarı, </br> Yönetilen Kimlik (önizleme)|
|[Azure Event Hubs](event-hubs-output.md)|Evet, çıkış yapılandırmasında bölüm anahtarı sütununu ayarlamanız gerekir.|Erişim anahtarı, </br> Yönetilen Kimlik (önizleme)|
|[Power BI](power-bi-output.md)|No|Azure Active Directory Kullanıcı, </br> Yönetilen Kimlik|
|[Azure Tablo Depolama](table-storage-output.md)|Yes|Hesap anahtarı|
|[Azure Service Bus kuyrukları](service-bus-queues-output.md)|Yes|Erişim anahtarı|
|[Azure Service Bus konuları](service-bus-topics-output.md)|Yes|Erişim anahtarı|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Yes|Erişim anahtarı|
|[Azure İşlevleri](azure-functions-output.md)|Yes|Erişim anahtarı|

## <a name="partitioning"></a>Bölümleme

Stream Analytics, Power BI hariç tüm çıkışlara yönelik bölümleri destekler. Bölüm anahtarları ve çıkış yazıcısı sayısı hakkında daha fazla bilgi için ilgilendiğiniz belirli çıktı türü için makaleye bakın. Tüm çıkış makaleleri önceki bölüme bağlanır.  

Ayrıca, bölümlerin daha gelişmiş olarak ayarlanması için, çıktı yazıcılarının sayısı `INTO <partition count>` sorgunuzda bir (bkz.) yan tümcesi kullanılarak denetlenebilir [](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)ve bu da istenen iş topolojisini elde etmek için yararlı olabilir. Çıkış bağdaştırıcınız bölümlendirilmemişse, bir giriş bölümünde verilerin bulunmaması, geç varış süresinin sonuna kadar gecikmeye neden olur. Bu gibi durumlarda, çıkış tek bir yazıcı ile birleştirilir ve bu da işlem hattınızda performans sorunlarına neden olabilir. Geç alma ilkesi hakkında daha fazla bilgi için bkz. [Azure Stream Analytics olay sırası konuları](./stream-analytics-time-handling.md).

## <a name="output-batch-size"></a>Çıkış toplu iş boyutu

Tüm çıktılar toplu işlemeyi destekler, ancak yalnızca bazı toplu iş boyutunu açıkça destekler. Azure Stream Analytics, olayları işlemek ve çıkışlara yazmak için değişken boyutlu toplu işler kullanır. Genellikle Stream Analytics altyapısı tek seferde bir ileti yazmaz ve verimlilik için toplu işler kullanır. Hem gelen hem de giden olayların oranı yüksek olduğunda Stream Analytics daha büyük toplu işler kullanır. Çıkış hızı düşük olduğunda, gecikme süresini düşük tutmak için daha küçük toplu işler kullanır.

## <a name="parquet-output-batching-window-properties"></a>Parquet çıkış toplu işleme penceresi özellikleri

Azure Resource Manager şablon dağıtımı veya REST API kullanılırken, iki toplu işlem penceresi özelliği şunlardır:

1. *timeWindow*

   Toplu iş başına en fazla bekleme süresi. Değer bir TimeSpan dizesi olmalıdır. Örneğin, iki dakika için "00:02:00". Bu süreden sonra, en düşük satır gereksinimi karşılanmasa bile toplu iş çıktıya yazılır. Varsayılan değer 1 dakikadır ve izin verilen en fazla 2 saattir. Blob çıktınızdan yol deseninin sıklığı varsa, bekleme süresi bölüm saat aralığından daha yüksek olamaz.

2. *Boyut penceresi*

   Toplu iş başına en az satır sayısı. Parquet için her Batch yeni bir dosya oluşturur. Geçerli varsayılan değer 2.000 satırdır ve izin verilen en fazla 10.000 satır olur.

Bu toplu işlem penceresi özellikleri yalnızca API sürüm **2017-04-01-önizleme** tarafından desteklenir. REST API çağrısının JSON yükünün bir örneği aşağıda verilmiştir:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>
> [Hızlı başlangıç: Azure portalını kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/

---
title: Desteklenen veri türleri-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 'de desteklenen veri türleri hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 8e8e6f007e19d2514a5f75dbdda7da13e1f8be3c
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505187"
---
# <a name="supported-data-types"></a>Desteklenen veri türleri

Aşağıdaki tabloda Azure Time Series Insights Gen2 tarafından desteklenen veri türleri listelenmektedir

| Veri türü | Açıklama | Örnek | [Zaman serisi Ifadesi söz dizimi](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet içindeki özellik sütunu adı
|---|---|---|---|---|
| **bool** | İki durumdan birine sahip bir veri türü: `true` veya `false` . | `"isQuestionable" : true` | `$event.isQuestionable.Bool` veya `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | Genellikle günün tarih ve saati olarak ifade edilen bir anlık zamanı temsil eder. [Iso 8601](https://www.iso.org/iso-8601-date-and-time-format.html) biçiminde ifade edilir. DateTime özellikleri her zaman UTC biçiminde depolanır. Doğru biçimlendirildiyse, saat dilimi uzaklıkları uygulanır ve ardından UTC 'de depolanır. Ortam zaman damgası özelliği ve tarih saat uzaklıkları hakkında daha fazla bilgi için [Bu](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) bölüme bakın | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  "EventProcessedLocalTime" olay kaynak zaman damgasıdır: `$event.$ts` . Başka bir JSON özelliği ise: `$event.eventProcessedLocalTime.DateTime` veya `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | Çift duyarlıklı 64 bit sayı  | `"value": 31.0482941` | `$event.value.Double` veya `$event['value'].Double` |  `value_double`
| **long** | İmzalı 64 bitlik bir tamsayı  | `"value" : 31` | `$event.value.Long` veya `$event['value'].Long` |  `value_long`
| **string** | Metin değerleri geçerli UTF-8 içermelidir. Null ve boş dizeler aynı şekilde işlenir. |  `"site": "DIM_MLGGG"`| `$event.site.String` veya `$event['site'].String`| `site_string`
| **dynamic** | Dizi veya özellik çantasından (sözlük) oluşan karmaşık (basit olmayan) bir tür. Şu anda yalnızca, TS ID veya TimeStamp Özelliği (ies) içermeyen nesnelerin basit veya dizi dizilerinin strıngımı JSON dizileri dinamik olarak depolanacak. Nesnelerin düzleştirilmesini ve dizilerin nasıl toplanacağını anlamak için bu [makaleyi](./concepts-json-flattening-escaping-rules.md) okuyun. Bu tür olarak depolanan yük özelliklerine yalnızca `Explore Events` Ham olayları görüntülemek için Time Series Insights Gezgini ' nde seçerek veya [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) istemci tarafı ayrıştırma için sorgu API 'si aracılığıyla erişilebilir. |  `"values": "[197, 194, 189, 188]"` | Zaman serisi Ifadesinde dinamik türlerin başvurulması henüz desteklenmiyor | `values_dynamic`

> [!NOTE]
> 64 bit tam sayı değerleri desteklenir, ancak Azure Time Series Insights gezgin tarafından güvenli bir şekilde ifade edilebilecek en büyük sayı, JavaScript sınırlamaları nedeniyle 9.007.199.254.740.991 ' tür (2 ^ 53-1). Bunun üzerinde veri modelinizdeki sayılarla çalışıyorsanız, [zaman serisi model değişkeni](./concepts-variables.md#numeric-variables) oluşturup değeri [dönüştürerek](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) boyutu azaltabilirsiniz.

> [!NOTE]
> **Dize** türü null yapılabilir değil:
>
> * Boş dizenin (**' '**) değerini **null** Ile karşılaştıran [zaman serisi sorgusunda](/rest/api/time-series-insights/reference-query-apis) ifade edilen [zaman serisi ifadesi (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) aynı şekilde davranır: `$event.siteid.String = NULL` ile eşdeğerdir `$event.siteid.String = ''` .
> * Özgün olaylar boş dizeler içerse bile, API **null** değerler döndürebilir.
> * Karşılaştırma veya değerlendirme yapmak için **dize** sütunlarında **null** değerler bağımlılığı almaz, bunları boş dizeler gibi aynı şekilde değerlendirin.

## <a name="sending-mixed-data-types"></a>Karışık veri türleri gönderme

Azure Time Series Insights Gen2 ortamınız kesin bir şekilde türdedir. Cihazlar veya Etiketler bir cihaz özelliği için farklı türlerdeki verileri gönderse, değerler iki ayrı sütunda depolanır ve API çağrılarında zaman serisi model değişkeni ifadelerinizi tanımlarken [birleşim () işlevi](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) kullanılmalıdır.

Azure Time Series Insights Gezgini, aynı cihaz özelliğinin ayrı sütunlarını otomatik olarak eklemenin bir yolunu sunar. Aşağıdaki örnekte, algılayıcı `PresentValue` hem Long hem de Double olabilecek bir özellik gönderir. Özelliğin tüm depolanan değerlerle (veri türünden bağımsız olarak) `PresentValue` sorgulanmak için, öğesini seçin `PresentValue (Double | Long)` ve sütunlar sizin için birleştirme yapılır.

[![Gezgin otomatik birleşim](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Nesneler ve diziler

Olay yüklerinizin bir parçası olarak nesneler ve diziler gibi karmaşık türler gönderebilirsiniz. İç içe geçmiş nesneler düzleştirilir ve diziler, `dynamic` ortam yapılandırmanıza ve JSON şekline bağlı olarak birden çok olay oluşturmak üzere olarak depolanır veya düzleştirilir. [JSON düzleştirme ve kaçış kuralları](./concepts-json-flattening-escaping-rules.md) hakkında daha fazla bilgi edinmek için

## <a name="next-steps"></a>Sonraki adımlar

* Olayların nasıl depolanacağını anlamak için [JSON düzleştirme ve kaçış kurallarını](./concepts-json-flattening-escaping-rules.md) okuyun.

* Ortamınızın [verimlilik sınırlamalarını](./concepts-streaming-ingress-throughput-limits.md) anlayın

* Akış verilerini almak için [olay kaynakları](concepts-streaming-ingestion-event-sources.md) hakkında bilgi edinin.

---
title: Azure Time Series Insights Gen2 ' deki giriş ve düzleştirme kurallarında yaklaşan değişiklikler | Microsoft Docs
description: Giriş kuralı değişiklikleri
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 44553e2a57f796dcf48561ff3b273e25514247ae
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504541"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Yeni ortamlar için JSON düzleştirme ve kaçış kurallarında yaklaşan değişiklikler

> [!IMPORTANT]
> Bu değişiklikler yalnızca *Yeni oluşturulan* Microsoft Azure Time Series Insights Gen2 ortamları için uygulanır. Değişiklikler Gen1 ortamları için uygulanmaz.

Azure Time Series Insights Gen2 ortamınız, belirli bir adlandırma kuralları kümesini izleyerek depolama sütunlarınızı dinamik olarak oluşturur. Bir olay yapıldığında Time Series Insights JSON yükünün ve özellik adlarının bir kural kümesini uygular. JSON verilerinin düzleştirildiğinde ve depolama, 2020 Temmuz 'da yeni Azure Time Series Insights Gen2 ortamları için geçerli olan değişiklikler. Bu değişiklik, aşağıdaki durumlarda sizi etkiler:

* JSON yükünüzü iç içe geçmiş nesneler içeriyor.
* JSON yükünüzü diziler içeriyor.
* JSON özellik adında aşağıdaki dört özel karakterden birini kullanın: `[` `\` `.``'`
* Zaman serisi (TS) KIMLIĞI özelliklerinden biri veya birkaçı iç içe geçmiş bir nesne içinde.

Yeni bir ortam oluşturursanız ve bu durumların bir veya daha fazlası olay yükünüzü geçerliyse, verileriniz düzleştirilmez ve farklı şekilde depolanır. Aşağıdaki tabloda değişiklikler özetlenmektedir:

| Geçerli kural | Yeni kural | Örnek JSON | Önceki sütun adı | Yeni sütun adı
|---|---| ---| ---|  ---|
| İç içe geçmiş JSON, ayırıcı olarak alt çizgi kullanılarak düzleştirilir. |İç içe geçmiş JSON, ayırıcı olarak bir nokta kullanılarak düzleştirilir.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Özel karakterler atlanmaz. | Özel karakterleri içeren `.` `[`   `\` ve `'` ve kullanarak kaçan JSON `['` özellik adları `']` . `['`Ve içinde `']` , tek tırnak ve ters eğik çizgi ek kaçışı vardır. Tek tırnak işareti olarak `\'` ve ters eğik çizgi olarak yazılır `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Temel elemanlar dizileri bir dize olarak depolanır. | Temel türlerin dizileri dinamik bir tür olarak depolanır.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Nesne dizileri her zaman düzleştirilir ve birden çok olay üretir. | Bir dizideki nesneler TS ID veya timestamp özelliklerine sahip değilse, nesne dizisi dinamik bir tür olarak tamamen saklanır. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Yeni ortamlar için önerilen değişiklikler

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>TS KIMLIĞINIZ ve/veya timestamp özelliği bir nesne içinde iç içe ise

Yeni dağıtım kurallarından her türlü yeni dağıtımın eşleşmesi gerekir. Örneğin, TS KIMLIĞINIZ ise, `telemetry_tagId` `telemetry.tagId` ortam TS kimliği olarak yapılandırmak için tüm Azure Resource Manager şablonlarını veya Otomatik Dağıtım betiklerini güncelleştirmeniz gerekir. Ayrıca, iç içe geçmiş JSON 'daki olay kaynağı zaman damgaları için de bu değişikliği yapmanız gerekir

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Yükleriniz iç içe geçmiş JSON veya özel karakterler içeriyorsa ve yazma [zaman serisi model](./concepts-model-overview.md) değişkeni ifadelerini otomatikleştirin

Yeni giriş kurallarını eşleştirmek için [Typesbatchput](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) çalıştıran istemci kodunuzu güncelleştirin. Örneğin, önceki bir [zaman serisi ifadesini](/rest/api/time-series-insights/reference-time-series-expression-syntax) `"value": {"tsx": "$event.series_value.Double"}` aşağıdaki seçeneklerden birine güncelleştirmeniz gerekir:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Time Series Insights Gen2 Storage ve ınress](./concepts-ingestion-overview.md)hakkında bilgi edinin.

* [Zaman serisi sorgu API 'lerini](./concepts-query-overview.md)kullanarak verilerinizi sorgulamayı öğrenin.

* [Yeni zaman serisi ifade sözdizimi](/rest/api/time-series-insights/reference-time-series-expression-syntax)hakkında daha fazla bilgi edinin.
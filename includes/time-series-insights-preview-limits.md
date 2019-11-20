---
title: include dosyası
description: include dosyası
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/16/2019
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: dfe16b4e965670d115cfa92f1cb3ca812d6375ad
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990854"
---
### <a name="general-availability-and-preview-comparison"></a>Genel kullanılabilirlik ve önizleme karşılaştırması

Aşağıdaki tabloda Azure Time Series Insights genel kullanım (GA) ve önizleme örnekleri arasındaki birçok önemli fark özetlenmektedir.

| | Genel Kullanım | Önizleme |
| --- | --- | ---|
| Birinci sınıf vatandaşlık | Olay merkezli | Zaman serisi merkezli |
| Anlam mantık yürütme | Alt düzey (başvuru verileri) | Üst düzey (modeller) |
| Veri bağlamı | Cihaz dışı düzeyi | Cihaz ve cihaz dışı düzeyi |
| İşlem mantığı depolaması | Hayır | Modelin tür değişkenlerinde depolanan bölümü |
| Depolama ve erişim denetimi | Hayır | Model aracılığıyla etkinleştirildi |
| Toplamalar/örnekleme | Hayır | Olay ağırlıklı ve zaman ağırlıklı |
| Sinyal yeniden oluşturma | Hayır | İlişkilendirme |
| Türetilmiş zaman serisinin üretimi | Hayır | Evet, birleştirmeler ve birleşimler |
| Dil esnekliği | Birleştirilemeyen | Birleştirilebilir |
| İfade dili | Koşul dizesi | Zaman serisi ifadeleri (koşul dizeleri, değerler, ifadeler ve işlevler) |

### <a name="property-limits"></a>Özellik sınırları

Time Series Insights Özellik sınırları, GA 'de en fazla 800 ' den 1.000 ' e artmıştır. Sağlanan olay özelliklerinin karşılık gelen JSON, CSV ve grafik sütunları [Time Series Insights önizleme Gezgini](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)içinde görüntüleyebilmeniz gerekir.

| SKU | En yüksek özellikler |
| --- | --- |
| Önizleme PAYG | 1\.000 Özellikleri (sütun) |
| GA S1 | 600 özellikleri (sütun) |
| GA S2 | 800 Özellikleri (sütun) |

### <a name="event-sources"></a>Olay kaynakları

Örnek başına en fazla iki olay kaynağı desteklenir. 

* [Bir olay hub 'ı kaynağı eklemeyi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)öğrenin.
* [IoT Hub 'ı kaynağı](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)yapılandırın.

### <a name="api-limits"></a>API sınırları

Time Series Insights önizlemesi için REST API sınırları [REST API başvuru belgelerinde](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)belirtilmiştir.
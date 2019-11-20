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
ms.openlocfilehash: e8c040bc186932680ce7eb4418a787dc061d02ce
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991163"
---
Genel kullanıma yönelik anahtar sınırları aşağıda özetlenmiştir.

### <a name="sku-ingress-rates-and-capacities"></a>SKU giriş fiyatları ve kapasiteleri

S1 ve S2 SKU giriş fiyatları ve kapasiteleri, yeni bir Time Series Insights ortamı yapılandırılırken esneklik sağlar.

| S1 SKU kapasitesi | Giriş oranı | Maksimum depolama kapasitesi
| --- | --- | --- |
| 1 | 1 GB (1.000.000 olay) | ayda 30 GB (30.000.000 olay) |
| 10 | 10 GB (10.000.000 olay) | ayda 300 GB (300.000.000 olay) |

| S2 SKU kapasitesi | Giriş oranı | Maksimum depolama kapasitesi
| --- | --- | --- |
| 1 | 10 GB (10.000.000 olay) | ayda 300 GB (300.000.000 olay) |
| 10 | 100 GB (100.000.000 olay) | ayda 3 TB (3.000.000.000 olay) |

> [!NOTE]
> Kapasitelerin ölçeği doğrusal olarak, kapasite 2 ' ye sahip bir S1 SKU 'SU her gün giriş oranı ve 60 GB (60.000.000 olay) için 2 GB (2.000.000) olay destekler.

S2 SKU ortamları ayda önemli ölçüde daha fazla olay destekler ve önemli ölçüde daha yüksek bir giriş kapasitesine sahiptir.

| SKU  | Aylık olay sayısı  | Aylık olay boyutu  | Dakika başına olay sayısı | Dakika başına olay boyutu  |
|---------|---------|---------|---------|---------|
| S1     |   30.000.000     |  30 GB     |  720    |  720 KB   |
 |S2     |   300.000.000    |   300 GB   | 7\.200   | 7\.200 KB  |

### <a name="property-limits"></a>Özellik sınırları

GA Özellik sınırları, seçilen SKU ortamına bağlıdır. Sağlanan olay özelliklerinin karşılık gelen JSON, CSV ve grafik sütunları [Time Series Insights Gezgini](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)içinde görüntülenebilir.

| SKU | En yüksek özellikler |
| --- | --- |
| S1 | 600 özellikleri (sütun) |
| S2 | 800 Özellikleri (sütun) |

### <a name="event-sources"></a>Olay kaynakları

Örnek başına en fazla iki olay kaynağı desteklenir. 

* [Bir olay hub 'ı kaynağı eklemeyi](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)öğrenin.
* [IoT Hub 'ı kaynağı](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)yapılandırın.

### <a name="api-limits"></a>API sınırları

Time Series Insights genel kullanım için REST API sınırları [REST API başvuru belgelerinde](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)belirtilmiştir.
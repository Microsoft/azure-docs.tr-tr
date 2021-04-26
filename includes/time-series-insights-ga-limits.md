---
title: include dosyası
description: include dosyası
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: e584b6eff16636f0657c586f6c630dbf8bbb99b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026960"
---
Aşağıda Azure Time Series Insights Gen1 ' deki anahtar sınırları özetlenmektedir.

### <a name="sku-ingress-rates-and-capacities"></a>SKU giriş fiyatları ve kapasiteleri

S1 ve S2 SKU giriş fiyatları ve kapasiteleri, yeni bir Azure Time Series Insights ortamı yapılandırılırken esneklik sağlar. SKU kapasiteniz, ne olursa değil, depolanan olay veya bayt sayısına göre günlük giriş hızınızı gösterir. Giriş, *dakika başına* ölçülür ve **kısıtlama** , belirteç demeti algoritması kullanılarak uygulanır. Giriş, 1 KB 'lik bloklar halinde ölçülür. Örneğin, 0,8 KB 'lik gerçek olay bir olay olarak ölçülür ve 2,6 KB 'lik bir olay üç olay olarak sayılır.

| S1 SKU kapasitesi | Giriş oranı | Maksimum depolama kapasitesi
| --- | --- | --- |
| 1 | 1 GB (1.000.000 olay)/gün | ayda 30 GB (30.000.000 olay) |
| 10 | 10 GB (10.000.000 olay)/gün | ayda 300 GB (300.000.000 olay) |

| S2 SKU kapasitesi | Giriş oranı | Maksimum depolama kapasitesi
| --- | --- | --- |
| 1 | 10 GB (10.000.000 olay)/gün | ayda 300 GB (300.000.000 olay) |
| 10 | 100 GB (100.000.000 olay)/gün | ayda 3 TB (3.000.000.000 olay) |

> [!NOTE]
> Kapasitelerin ölçeği doğrusal olarak, kapasite 2 ' ye sahip bir S1 SKU 'SU her gün giriş oranı ve 60 GB (60.000.000 olay) için 2 GB (2.000.000) olay destekler.

S2 SKU ortamları ayda önemli ölçüde daha fazla olay destekler ve önemli ölçüde daha yüksek bir giriş kapasitesine sahiptir.

| SKU  | Aylık olay sayısı  | Dakika başına olay sayısı | Dakika başına olay boyutu  |
|---------|---------|---------|---------|---------|
| S1     |   30.000.000   |  720    |  720 KB   |
 |S2     |   300.000.000   | 7.200   | 7.200 KB  |

### <a name="property-limits"></a>Özellik sınırları

Gen1 Özellik sınırları, seçilen SKU ortamına bağlıdır. Sağlanan olay özelliklerinin karşılık gelen JSON, CSV ve grafik sütunları [Azure Time Series Insights Gezgini](../articles/time-series-insights/time-series-quickstart.md)içinde görüntülenebilir.

| SKU | En yüksek özellikler |
| --- | --- |
| S1 | 600 özellikleri (sütun) |
| S2 | 800 Özellikleri (sütun) |

### <a name="event-sources"></a>Olay kaynakları

Örnek başına en fazla iki olay kaynağı desteklenir.

* [Bir olay hub 'ı kaynağı eklemeyi](../articles/time-series-insights/how-to-ingest-data-event-hub.md)öğrenin.
* [IoT Hub 'ı kaynağı](../articles/time-series-insights/how-to-ingest-data-iot-hub.md)yapılandırın.

### <a name="api-limits"></a>API sınırları

Azure Time Series Insights Gen1 için REST API sınırları [REST API başvuru belgelerinde](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)belirtilir.
---
title: Önizlemede veri sorgulama-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Preview veri sorgulama hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: f44aa5c0a412dc53f4b1a5f127887257a1b0b550
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006304"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Azure Time Series Insights önizlemede veri sorgulama

Azure Time Series Insights önizleme, genel yüzey API 'Leri aracılığıyla ortamda depolanan olaylar ve meta veriler üzerinde veri sorgulama imkanı sunar. Bu API 'Ler [Time Series Insights önizleme Gezgininde](./time-series-insights-update-explorer.md)de kullanılır.

Time Series Insights ' de üç birincil API kategorisi mevcuttur:

* **Ortam API 'leri**: Bu apı 'ler Time Series Insights ortamındaki sorguları etkinleştirir. Sorgu örnekleri, çağıranın erişimi olan ortamların ve ortam meta verilerinin listesidir.
* **Zaman serisi modeli-sorgu (TSM-Q) API 'leri**: zaman serisi modelinin ortam bölümünde depolanan meta verilerde oluşturma, okuma, güncelleştirme ve SILME (CRUD) işlemlerine izin vermez. Örnekler, türler ve Hiyerarşiler örnektir.
* **Zaman serisi sorgusu (TSQ) API 'leri**: kaynak sağlayıcıdan kaydedildiğinden veya değişkenlerin bir parçası olan skaler ve toplama işlevleri kullanılarak verileri azaltarak telemetri veya olay verilerinin alınmasına izin vermez. Bu API 'Ler zaman serisi verilerinde hesaplamaları dönüştürmek, birleştirmek ve uygulamak için işlemler gerçekleştirebilir.

Time Series Insights hesaplamaları ifade etmek için zengin bir dize tabanlı ifade dili, [zaman serisi ifadesi (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)kullanır.

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure Time Series Insights Preview Core API 'Leri

Aşağıdaki temel API 'Ler desteklenir.

[![zaman serisi sorgusuna genel bakış](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Ortam API 'Leri

Aşağıdaki ortam API 'Leri kullanılabilir:

* [Ortam API 'Si al](/rest/api/time-series-insights/management/environments/get): çağıranın erişim yetkisine sahip olduğu ortamların listesini döndürür.
* [Ortam kullanılabilirliği API 'si](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): olay zaman damgası `$ts`üzerinden olay sayısı dağılımını döndürür. Bu API, varsa olay sayısını döndürerek zaman damgasında herhangi bir olay olup olmadığını belirlemenize yardımcı olur.
* [Olay şeması API 'Si al](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): belirli bir arama yayılması için olay şeması meta verilerini döndürür. Bu API, belirtilen arama alanı için şemada bulunan tüm meta verileri ve özellikleri almaya yardımcı olur.

## <a name="time-series-model-query-tsm-q-apis"></a>Zaman serisi modeli-sorgu (TSD-Q) API 'Leri

Aşağıdaki zaman serisi modeli sorgu API 'Leri kullanılabilir. Bu API 'lerin çoğu, birden çok zaman serisi model varlığı üzerinde toplu CRUD işlemlerini etkinleştirmek için toplu yürütme işlemini destekler:

* [Model ayarları API 'si](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): varsayılan tür ve ortamın model adı üzerinde *Get* ve *Patch* seçeneklerini sunar.
* [Türler API 'si](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): zaman serisi türlerinde ve bunlarla ilişkili değişkenlerde CRUD 'yi mümkün.
* [Hiyerarşiler API 'si](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): zaman serisi hiyerarşilerinde ve bunlarla ilişkili alan yollarında CRUD 'yi mümkün.
* [Örnek API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): zaman serisi örneklerine ve bunlarla ilişkili örnek alanlarına CRUD 'yi sunar. Ayrıca, örnekleri API 'SI aşağıdaki işlemleri destekler:
  * [Arama](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): örnek özniteliklerine göre zaman serisi örnekleri aramasında bir isabetlerin kısmi listesini alır.
  * [Öneri](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest):, örnek özniteliklerine göre zaman serisi örnekleri aramasında arama sırasında oluşan kısmi bir liste arar ve bu noktaları önerir.

## <a name="time-series-query-tsq-apis"></a>Zaman serisi sorgusu (TSQ) API 'Leri

Aşağıdaki zaman serisi sorgu API 'Leri kullanılabilir. Bu API 'Ler Time Series Insights tüm desteklenen çok katmanlı depolama alanları üzerinde kullanılabilir. Sorgu URL 'SI parametreleri sorgunun yürütülmesi gereken [Depo türünü](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) belirtmek için kullanılır:

* [Olayları al API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): kaynak sağlayıcıdan Time Series Insights kaydedildiğinden Time Series Insights verilerinin olayları sorgulamasını ve alınmasını mümkün. Bu API, belirli bir zaman serisi KIMLIĞI ve arama kapsamı için ham olayların alınmasına izin verir. Bu API, seçilen giriş için tüm veri kümesini almak üzere sayfalandırmayı destekler. 

* [Seri API 'Si al](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): hatta, hattaki verileri kullanarak yakalanan olaylardan Time Series Insights verilerinin sorgu ve alınmasına izin vermez. Döndürülen değerler modelde tanımlanan veya satır içi olarak verilen değişkenlere göre yapılır. Bu API, seçilen giriş için tüm veri kümesini almak üzere sayfalandırmayı destekler. Bu API, hesaplanan özellikleri veya sütunları tanımlamaya yardımcı olur.

    >[!NOTE]
    > Toplama yan tümcesi, bir modelde belirtilse veya satır içi sağlanmış olsa bile yok sayılır.

  Get Series API 'SI her Aralık için her değişken için bir zaman serisi değeri döndürür. Bir zaman serisi değeri, Time Series Insights bir sorgudan çıkış JSON için kullanılan bir biçimdir. Döndürülen değerler, zaman serisi KIMLIĞI ve belirtilen değişken kümesi temel alınarak hesaplanır.

* [Toplama serisi API 'si](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): kaydedilen verileri örnekleyerek ve toplayarak yakalanan olaylardan Time Series Insights verilerinin sorgu ve alınmasına izin vermez. Bu API, [devamlılık belirteçlerini](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage)kullanarak devamlılığın yürütülmesini destekler.

  Toplama serisi API 'SI her Aralık için her değişken için bir zaman serisi değeri döndürür. Değerler, zaman serisi KIMLIĞINI ve sağlanmış olan değişken kümesini temel alır. Toplama serisi API 'SI, zaman serisi modelinde depolanan veya toplu olarak veya örnek veriler için satır içi olarak belirtilen değişkenleri kullanarak azalmaya erişir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Time Series Insights önizlemede [depolama ve](./time-series-insights-update-storage-ingress.md) giriş hakkında daha fazla bilgi edinin.
- Time Series Insights Preview [veri modelleme](./time-series-insights-update-tsm.md) makalesini okuyun.
- [Zaman SERISI kimliğini seçmek için en iyi yöntemleri](./time-series-insights-update-how-to-id.md)bulun.

---
title: Azure Time Series Insights veri başvurusunu izleme | Microsoft Docs
description: İzleme Azure Time Series Insights için başvuru belgeleri.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 20d376ee501edf16711617847c685dfd531f2c80
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504592"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Azure Time Series Insights veri başvurusunu izleme

Azure Time Series Insights ortamınızdan Azure Izleyici tarafından toplanan veriler ve kaynaklar hakkında bilgi edinin. İzleme verilerini toplama ve çözümleme hakkında ayrıntılar için bkz. [izleme Time Series Insights]( ./how-to-monitor-tsi.md) .

## <a name="metrics"></a>Ölçümler

Bu bölümde, Azure Time Series Insights için toplanan otomatik olarak toplanan tüm platform ölçümleri listelenmektedir. Tüm Azure Izleyici desteği ölçümlerinin bir listesi (Azure Time Series Insights dahil) için bkz. [Azure izleyici desteklenen ölçümler](../azure-monitor/essentials/metrics-supported.md).
Bu ölçümler için kaynak sağlayıcısı [Microsoft. Timeseriesınsights/ortamlar/EventSources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) ve [Microsoft. timeseriesınsights/ortamlarıdır](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Giriş

|Metric|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|
|---|---|---|---|---|
|Alınan baytlar|Alınan bayt sayısı|Bayt|Toplam|Olay kaynağından okunan bayt sayısı|
|Inressreceiveınvalidmessages|Giriş geçersiz Iletiler aldı|Count|Toplam|Olay kaynağından okunan geçersiz ileti sayısı|
|Inressreceived Iletileri|Giriş alınan Iletiler|Count|Toplam|Olay kaynağından okunan ileti sayısı|
|Inressreceived Iletisayaçgecikmesi|Alınan Ileti sayısı gecikmesi|Count|Ortalama|Olay kaynak bölümünde en son sıraya alınmış iletinin sıra numarası ve giriş bölümünde işlenmekte olan iletilerin sıra numarası arasındaki fark|
|Inressreceived Iletizamangecikmesi|Alınan Ileti zaman gecikmesi|Saniye|Maksimum|İleti olay kaynağında sıraya alındığı zaman ve giriş sırasında işlendiği zaman arasındaki fark|
|IngressStoredBytes|Giriş depolanan baytlar|Bayt|Toplam|Başarılı bir şekilde işlenen ve sorgu için kullanılabilir olayların toplam boyutu|
|Inressstoo Vents|Giriş saklı olayları|Count|Toplam|Sorgu için başarıyla işlenen ve kullanılabilir düzleştirilmiş olay sayısı|

### <a name="storage"></a>Depolama

|Metric|Ölçüm görünen adı|Birim|Toplama Türü|Açıklama|
|---|---|---|---|---|
|WarmStorageMaxProperties|Isınma depolama en yüksek özellikleri|Count|Maksimum|S1/S2 SKU 'SU için ortam tarafından izin verilen en fazla özellik sayısı ve PAYG SKU 'SU için ısınma tarafından izin verilen en fazla özellik sayısı|
|WarmStorageUsedProperties|Sıcak depolama kullanılan özellikler |Count|Maksimum|S1/S2 SKU 'SU için ortam tarafından kullanılan özellik sayısı ve PAYG SKU 'SU için sıcak mağaza tarafından kullanılan özellik sayısı|

## <a name="resource-logs"></a>Kaynak günlükleri

Bu bölümde, Azure Time Series Insights ortamınız için toplayacağınız kaynak günlüklerinin türleri listelenir.

| Kategori | Görünen Ad | Açıklama |
|----- |----- |----- |
| Giriş | Tsıingress | Giriş kategorisi, giriş ardışık düzeninde oluşan hataları izler. Bu kategori olayları alırken meydana gelen hataları (örneğin, bir olay kaynağına bağlanma hataları) ve olayları (bir olay yükünü ayrıştırırken hatalar gibi) işlemeyi içerir. |

## <a name="schemas"></a>Şemalar
Aşağıdaki şemalar Azure Time Series Insights tarafından kullanılıyor

### <a name="tsiingress-table"></a>Tsıingress tablosu

| Özellik | Açıklama |
|----- |----- |
| TimeGenerated | Bu olayın oluşturulduğu saat (UTC). |
| Konum | Kaynağın konumu. |
| Kategori | Günlük olayının kategorisi. |
| OperationName | Etkinliğin işlem adı. |
| CorrelationId | İsteğin bağıntı KIMLIĞI. |
| Level | Etkinliğin önem düzeyi. |
| ResultDescription | İşlem sonucunun açıklaması; Örneğin, ' alınan yasak hata '. |
| İleti | Hatayla ilişkili ileti. Neyin yanlış gittiğini ve hatanın nasıl azaltılacağını gösteren ayrıntıları içerir. |
| ErrorCode | Hatayla ilişkili kod |
| EventSourceType | Olay kaynağının türü. Bu, Olay Hub 'ı veya IoT Hub 'ı olabilir. |
| EventSourceProperties | Olay kaynağınıza özgü özellikler koleksiyonu. Tüketici grubu ve erişim anahtarı adı gibi ayrıntıları içerir. |

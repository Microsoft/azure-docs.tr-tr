---
title: Azure Tablo depolama izleme verileri başvurusu | Microsoft Docs
description: Azure Tablo depolamadaki verileri izlemek için günlük ve ölçüm başvurusu.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: ad56b6af9a9071812ad6fa581954010df3b6b5d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574843"
---
# <a name="azure-table-storage-monitoring-data-reference"></a>Azure Tablo depolama izleme verileri başvurusu

Azure depolama için izleme verilerini toplama ve çözümleme hakkında ayrıntılı bilgi için bkz. [Azure Storage 'ı izleme](monitor-table-storage.md) .

## <a name="metrics"></a>Ölçümler

Aşağıdaki tablolarda, Azure depolama için toplanan platform ölçümleri listelenmektedir. 

### <a name="capacity-metrics"></a>Kapasite ölçümleri

Kapasite ölçümleri değerleri her saat Azure Izleyici 'ye gönderilir. Değerler günlük olarak yenilenir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm kapasite ölçümleri için desteklenen zaman dilimi bir saattir (PT1H).

Azure depolama, Azure Izleyici 'de aşağıdaki kapasite ölçümlerini sağlar.

#### <a name="account-level"></a>Hesap düzeyi

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="table-storage"></a>Tablo depolama

Bu tabloda [Tablo depolama ölçümleri](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices)gösterilmektedir.

| Metric | Açıklama |
| ------------------- | ----------------- |
| Tablokapasitesi | Depolama hesabı tarafından kullanılan tablo depolaması miktarı. <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |
| Tablosayısı   | Depolama hesabındaki tablo sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |
| TableEntityCount | Depolama hesabındaki tablo varlıklarının sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |

### <a name="transaction-metrics"></a>İşlem ölçümleri

İşlem ölçümleri, Azure depolama 'daki bir depolama hesabına yönelik her istek için Azure Izleyici 'ye dağıtılır. Depolama hesabınızda hiçbir etkinlik olmaması durumunda, dönem içinde işlem ölçümlerinde hiç veri olmayacaktır. Tüm işlem ölçümleri hem hesap hem de tablo depolama hizmeti düzeyinde kullanılabilir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm işlem ölçümleri için desteklenen saat graıns PT1H ve PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Ölçüm boyutları

Azure depolama, Azure Izleyici 'de ölçümler için aşağıdaki boyutları destekler.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Kaynak günlükleri (Önizleme)

> [!NOTE]
> Azure Izleyici 'de Azure depolama günlükleri genel önizlemede bulunur ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, genel amaçlı v1 ve genel amaçlı v2 depolama hesaplarında blob 'lar (Azure Data Lake Storage 2.), dosyalar, kuyruklar, tablolar, Premium Depolama hesapları için Günlükler sunar. Klasik depolama hesapları desteklenmez.

Aşağıdaki tabloda Azure Izleyici günlüklerinde veya Azure Storage 'da toplandıklarında Azure depolama kaynak günlüklerinin özellikleri listelenmektedir. Özellikler işlemi, hizmeti ve işlemi gerçekleştirmek için kullanılan yetkilendirme türünü anlatmaktadır.

### <a name="fields-that-describe-the-operation"></a>İşlemi tanımlayan alanlar

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>İşlemin nasıl doğrulandığını tanımlayan alanlar

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Hizmeti tanımlayan alanlar

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Ayrıca bkz.

- Bkz. Azure Storage izleme açıklaması için bkz. [Azure Tablo depolamayı izleme](monitor-table-storage.md) .
- Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md) .
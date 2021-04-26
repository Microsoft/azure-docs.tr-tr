---
title: Azure dosyaları izleme veri başvurusu | Microsoft Docs
description: Azure dosyalarından verileri izlemek için günlük ve ölçüm başvurusu.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 4575709a4fa7067b02228036fb2e2b4a60844e21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592311"
---
# <a name="azure-files-monitoring-data-reference"></a>Azure dosyaları izleme veri başvurusu

Azure dosyaları için izleme verilerini toplama ve çözümleme hakkında ayrıntılı bilgi için bkz. [Azure dosyalarını izleme](storage-files-monitoring.md) .

## <a name="metrics"></a>Ölçümler

Aşağıdaki tablolarda Azure dosyaları için toplanan platform ölçümleri listelenmektedir. 

### <a name="capacity-metrics"></a>Kapasite ölçümleri

Kapasite ölçümleri değerleri günlük olarak (24 saate kadar) yenilenir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm kapasite ölçümleri için desteklenen zaman dilimi bir saattir (PT1H).

Azure dosyaları, Azure Izleyici 'de aşağıdaki kapasite ölçümlerini sağlar.

#### <a name="account-level"></a>Hesap düzeyi

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Dosyaları

Bu tabloda [Azure dosya ölçümleri](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)gösterilmektedir.

| Metric | Açıklama |
| ------------------- | ----------------- |
| Dosya kapasitesi | Depolama hesabı tarafından kullanılan dosya depolama alanı miktarı. <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |
| FileCount   | Depolama hesabındaki dosyaların sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |
| Filesharecapacitykotası | Azure dosyaları hizmeti tarafından bayt olarak kullanılabilecek depolama miktarının üst sınırı. <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024|
| Dosya ShareCount | Depolama hesabındaki dosya paylaşımlarının sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Ortalama <br/> Değer örneği: 1024 |
| FileShareProvisionedIOPS | Bir dosya paylaşımında sağlanan ıOPS sayısı. Bu ölçüm yalnızca Premium dosya depolaması için geçerlidir. <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama |
| FileShareSnapshotCount | Depolama hesabının Azure dosyaları hizmetindeki paylaşımda bulunan anlık görüntü sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Ortalama | 
|FileShareSnapshotSize|Depolama hesabının Azure dosyaları hizmetindeki anlık görüntüler tarafından kullanılan depolama miktarı. <br/><br/> Birim: bayt <br/> Toplama türü: Ortalama|

### <a name="transaction-metrics"></a>İşlem ölçümleri

İşlem ölçümleri, Azure depolama 'daki bir depolama hesabına yönelik her istek için Azure Izleyici 'ye dağıtılır. Depolama hesabınızda hiçbir etkinlik olmaması durumunda, dönem içinde işlem ölçümlerinde hiç veri olmayacaktır. Tüm işlem ölçümleri hem hesapta hem de Azure dosyaları hizmet düzeyinde kullanılabilir. Zaman dilimi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm işlem ölçümleri için desteklenen saat graıns PT1H ve PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Ölçüm boyutları

Azure dosyaları, Azure Izleyici 'de ölçümler için aşağıdaki boyutları destekler.

> [!NOTE] 
> Dosya paylaşımı boyutu standart dosya paylaşımları için kullanılamaz (yalnızca Premium dosya paylaşımları). Standart dosya paylaşımları kullanılırken, belirtilen ölçümler depolama hesabındaki tüm dosya paylaşımları içindir. Standart dosya paylaşımları için paylaşım başına ölçümleri almak için, depolama hesabı başına bir dosya paylaşımı oluşturun.

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

- Bkz. Azure Storage izleme açıklaması için [Azure dosyalarını izleme](storage-files-monitoring-reference.md) .
- Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](../../azure-monitor/essentials/monitor-azure-resource.md) .
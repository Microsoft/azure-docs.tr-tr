---
title: Duraklatma, devam etmeyi, REST API 'lerle ölçeklendirme
description: REST API 'Leri aracılığıyla Azure SQL veri ambarı 'nda işlem gücünü yönetin.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f72b3fd1024a68a6f48d2e9e676fc7ca23bf2a4f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686046"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Azure SQL veri ambarı için REST API 'Leri
Azure SQL veri ambarı 'nda işlem yönetimi için REST API 'Leri.

## <a name="scale-compute"></a>Hesaplamayı ölçeklendirme
Veri ambarı birimlerini değiştirmek için [Create veya Update Database](/rest/api/sql/databases/createorupdate) REST API kullanın. Aşağıdaki örnek, sunucu sunucum üzerinde barındırılan MySQLDW veritabanı için veri ambarı birimlerini DW1000 olarak ayarlar. Sunucu, ResourceGroup1 adlı bir Azure Kaynak grubunda bulunur.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>İşlem Duraklat

Bir veritabanını duraklatmak için [duraklatma veritabanını](/rest/api/sql/databases/pause) REST API kullanın. Aşağıdaki örnek, server01 adlı bir sunucuda barındırılan Database02 adlı bir veritabanını duraklatır. Sunucu, ResourceGroup1 adlı bir Azure Kaynak grubunda bulunur.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>İşlem işlemine geri dön

Bir veritabanını başlatmak için REST API [özgeçmişi veritabanını](/rest/api/sql/databases/resume) kullanın. Aşağıdaki örnek, server01 adlı bir sunucuda barındırılan Database02 adlı bir veritabanını başlatır. Sunucu, ResourceGroup1 adlı bir Azure Kaynak grubunda bulunur. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Veritabanı durumunu denetle

> [!NOTE]
> Veritabanı çevrimiçi iş akışını tamamlarken, bağlantı hatalarıyla sonuçlarken veritabanı durumu şu anda ÇEVRIMIÇI olarak dönebilir. Bağlantı girişimlerini tetiklemek için bu API çağrısını kullanıyorsanız, uygulama kodunuzda 2 ila 3 dakika gecikme eklemeniz gerekebilir.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Bakım zamanlaması al
Bir veri ambarı için ayarlanmış bakım zamanlamasını denetleyin. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Bakım zamanlamasını ayarla
Mevcut bir veri ambarında maintnenance zamanlamasını ayarlamak ve güncelleştirmek için.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [Işlem yönetimi](sql-data-warehouse-manage-compute-overview.md).


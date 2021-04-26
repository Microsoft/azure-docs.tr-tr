---
title: Ayrılmış SQL Havuzu (eskiden SQL DW) için REST API 'lerle duraklatın, devam edin, ölçeklendirin
description: REST API 'Leri aracılığıyla Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için işlem gücünü yönetin.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/29/2019
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c04f61aaef5f5072ce0fb39ff111ba07ee151700
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375910"
---
# <a name="rest-apis-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için REST API 'Leri

Azure SYNAPSE Analytics 'te adanmış SQL Havuzu (eski adıyla SQL DW) için işlem yönetimi için REST API 'Leri.

## <a name="scale-compute"></a>Hesaplamayı ölçeklendirme

Veri ambarı birimlerini değiştirmek için [Create veya Update Database](/rest/api/sql/databases/createorupdate) REST API kullanın. Aşağıdaki örnek, sunucu sunucum üzerinde barındırılan MySQLDW veritabanı için veri ambarı birimlerini DW1000 olarak ayarlar. Sunucu, ResourceGroup1 adlı bir Azure Kaynak grubunda bulunur.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2020-08-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    location: "West Central US",
    "sku": {
    "name": "DW200c"
    }
}
```

## <a name="pause-compute"></a>İşlem Duraklat

Bir veritabanını duraklatmak için [duraklatma veritabanını](/rest/api/sql/databases/pause) REST API kullanın. Aşağıdaki örnek, server01 adlı bir sunucuda barındırılan Database02 adlı bir veritabanını duraklatır. Sunucu, ResourceGroup1 adlı bir Azure Kaynak grubunda bulunur.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>İşlem işlemine geri dön

Bir veritabanını başlatmak için REST API [özgeçmişi veritabanını](/rest/api/sql/databases/resume) kullanın. Aşağıdaki örnek, server01 adlı bir sunucuda barındırılan Database02 adlı bir veritabanını başlatır. Sunucu, ResourceGroup1 adlı bir Azure Kaynak grubunda bulunur.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2020-08-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Veritabanı durumunu denetle

> [!NOTE]
> Veritabanı çevrimiçi iş akışını tamamlarken, bağlantı hatalarıyla sonuçlarken veritabanı durumu şu anda ÇEVRIMIÇI olarak dönebilir. Bağlantı girişimlerini tetiklemek için bu API çağrısını kullanıyorsanız, uygulama kodunuzda 2 ila 3 dakika gecikme eklemeniz gerekebilir.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/servers/{serverName}/databases/{databaseName}?api-version=2020-08-01-preview
```

## <a name="get-maintenance-schedule"></a>Bakım zamanlaması al

Adanmış bir SQL Havuzu (eski adıyla SQL DW) için ayarlanmış bakım zamanlamasını denetleyin.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Bakım zamanlamasını ayarla

Mevcut ayrılmış bir SQL havuzunda (eski adıyla SQL DW) bir bakım zamanlaması ayarlamak ve güncelleştirmek için.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": "Saturday",
                                "startTime": "00:00",
                                "duration": "08:00",
                },
                {
                                "dayOfWeek": "Wednesday",
                                "startTime": "00:00",
                                "duration": "08:00",
                }
                ]
    }
}

```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Işlem yönetimi](sql-data-warehouse-manage-compute-overview.md).

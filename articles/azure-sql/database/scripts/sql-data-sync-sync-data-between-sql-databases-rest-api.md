---
title: 'REST API: birden çok veritabanı arasında eşitleme'
description: Birden çok veritabanı arasında eşitleme yapmak için REST API örnek betiği kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d3ff8114c11b224a0bdbb0bd2d0e5686a7e57b55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565920"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Birden çok veritabanı arasında veri eşitlemek için REST API kullanma 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Bu REST API örnek, verileri birden çok veritabanı arasında eşitlemek için SQL Data Sync yapılandırır.

SQL Data Sync genel bir bakış için bkz. [Azure 'da SQL Data Sync ile birden çok bulutta ve şirket içi veritabanlarında veri eşitleme](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL Data Sync Şu anda Azure SQL yönetilen örneğini desteklemez.

## <a name="create-sync-group"></a>Eşitleme grubu oluştur

Bir eşitleme grubu oluşturmak için [Oluştur veya Güncelleştir](/rest/api/sql/syncgroups/createorupdate) şablonunu kullanın.
 
Eşitleme grubu oluştururken, eşitleme şemasında (table\column) geçiş yapmaz ve masterSyncMemberName içinde geçirmeyin çünkü bu zaman eşitleme grubunda henüz table\column bilgileri yok.

Eşitleme grubu oluşturmak için örnek istek: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Eşitleme grubu oluşturmak için örnek yanıt:

Durum kodu: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Durum kodu: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Eşitleme üyesi oluştur

Bir eşitleme üyesi oluşturmak için [Oluştur veya Güncelleştir](/rest/api/sql/syncmembers/createorupdate) şablonunu kullanın.

Eşitleme üyesi oluşturmak için örnek istek:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Eşitleme üyesi oluşturmak için örnek yanıt:

Durum kodu: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Durum kodu: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Şemayı Yenile

Eşitleme grubunuz başarıyla oluşturulduktan sonra, aşağıdaki şablonları kullanarak şemayı yenileyin.

Hub veritabanı şemasını yenilemek için [hub şemasını Yenile](/rest/api/sql/syncgroups/refreshhubschema)  şablonunu kullanın. 

Bir hub veritabanı şemasını yenilemeye yönelik örnek istek: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Bir hub veritabanı şemasını yenilemek için örnek yanıt: 

Durum kodu: 200

Durum kodu: 202

Merkez veritabanı şemasını listelemek için [liste Merkezi şemaları](/rest/api/sql/syncgroups/listhubschemas) şablonunu kullanın. 

Üye veritabanı şemasını yenilemek için [üye şemasını Yenile](/rest/api/sql/syncmembers/refreshmemberschema) şablonunu kullanın. 

Üye veritabanı şemasını listelemek için [liste üyesi şema](/rest/api/sql/syncmembers/listmemberschemas) şablonunu kullanın. 

Yalnızca şemanız başarıyla yenilendiğinde bir sonraki adıma geçin. 

## <a name="update-sync-group"></a>Eşitleme grubunu Güncelleştir 

Eşitleme grubunuzu güncelleştirmek için [Oluştur veya Güncelleştir](/rest/api/sql/syncgroups/createorupdate) şablonunu kullanın.

Eşitleme şemasını belirterek eşitleme grubunu güncelleştirin. Kullanmak istediğiniz şemayı tutan ad olan şemanızı ve masterSyncMemberName 'i ekleyin. 

Eşitleme grubunu güncelleştirmek için örnek istek: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Eşitleme grubunu güncelleştirmek için örnek yanıt: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Eşitleme üyesini Güncelleştir

Eşitleme üyesini güncelleştirmek için [Oluştur veya Güncelleştir](/rest/api/sql/syncmembers/createorupdate) şablonunu kullanın.

Bir eşitleme üyesini güncelleştirmek için örnek istek: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Bir eşitleme üyesini güncelleştirmek için örnek yanıt: 

Durum kodu: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Durum kodu: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Eşitlemeyi Tetikle

Eşitleme işlemi tetiklemek için [tetikleyici eşitleme](/rest/api/sql/syncgroups/triggersync) şablonunu kullanın.

Eşitleme işleminin tetiklenmesi için örnek istek: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Eşitleme işleminin tetiklenmesi için örnek yanıt: 

Durum kodu: 200

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).

Ek SQL Veritabanı PowerShell betiği örnekleri [Azure SQL Veritabanı PowerShell betikleri](../powershell-script-content-guide.md) içinde bulunabilir.

SQL Data Sync hakkında daha fazla bilgi için bkz.:

- Genel Bakış- [Azure 'da SQL Data Sync birden çok bulutta ve şirket içi veritabanlarında veri eşitleme](../sql-data-sync-data-sql-server-sql-database.md)
- Veri eşitlemesini ayarlama
    - Azure portal- [öğretici: Azure SQL veritabanı ile verileri eşitlemek için SQL Data Sync ayarlama SQL Server](../sql-data-sync-sql-server-configure.md)
    - PowerShell kullanma- [Azure SQL veritabanı ve SQL Server bir veritabanı arasında veri eşitlemek Için PowerShell kullanın](sql-data-sync-sync-data-between-azure-onprem.md)
- Veri eşitleme Aracısı- [Azure 'da SQL Data Sync Için veri eşitleme Aracısı](../sql-data-sync-agent-overview.md)
- En iyi uygulamalar- [Azure 'da SQL Data Sync Için en iyi yöntemler](../sql-data-sync-best-practices.md)
- İzleyici- [Azure izleyici günlükleri ile izleyici SQL Data Sync](../monitor-tune-overview.md)
- Sorun giderme- [Azure 'da SQL Data Sync sorunlarını giderme](../sql-data-sync-troubleshoot.md)
- Eşitleme şemasını güncelleştirme
    - [Azure 'da SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatik hale getirmek Için](../sql-data-sync-update-sync-schema.md) Transact-SQL-otomatikleştir kullanma
    - PowerShell kullanma- [mevcut bir eşitleme grubundaki eşitleme şemasını güncelleştirmek Için PowerShell kullanın](update-sync-schema-in-sync-group.md)

SQL veritabanı hakkında daha fazla bilgi için bkz.

- [SQL veritabanına genel bakış](../sql-database-paas-overview.md)
- [Veritabanı Yaşam Döngüsü Yönetimi](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
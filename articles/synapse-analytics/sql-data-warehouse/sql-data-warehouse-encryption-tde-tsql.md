---
title: Saydam veri şifrelemesi (T-SQL)
description: Azure SYNAPSE Analytics 'te (T-SQL) saydam veri şifrelemesi (TDE)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 80e5da6bb281806afe6bc980e35d70732bcd609c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676281"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Saydam Veri Şifrelemesi ile çalışmaya başlama (TDE)

> [!div class="op_single_selector"]
>
> * [Güvenliğe genel bakış](sql-data-warehouse-overview-manage-security.md)
> * [Kimlik Doğrulaması](sql-data-warehouse-authentication.md)
> * [Şifreleme (portal)](sql-data-warehouse-encryption-tde.md)
> * [Şifreleme (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Gerekli İzinler

Saydam Veri Şifrelemesi (TDE) etkinleştirmek için, bir yönetici veya dbmanager rolünün bir üyesi olmanız gerekir.

## <a name="enabling-encryption"></a>Şifrelemeyi etkinleştirme

TDE ' i etkinleştirmek için şu adımları izleyin:

1. Yönetici veya ana veritabanında **DBManager** rolünün bir üyesi kullanarak veritabanını barındıran sunucudaki *ana* veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Şifrelemeyi devre dışı bırakma

TDE devre dışı bırakmak için şu adımları izleyin:

1. Ana veritabanında yönetici veya **DBManager** rolünün üyesi olan bir oturum kullanarak *ana* veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Duraklatılmış bir adanmış SQL havuzunun, TDE ayarlarında değişiklik yapılmadan önce sürdürülmelidir.

## <a name="verifying-encryption"></a>Şifreleme doğrulanıyor

Şifreleme durumunu doğrulamak için aşağıdaki adımları izleyin:

1. Ana veritabanında yönetici veya **DBManager** rolünün üyesi olan bir oturum kullanarak *ana* veya örnek veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Sonucu şifreli bir ```1``` veritabanını gösterir, ```0``` şifreli olmayan bir veritabanını gösterir.

## <a name="encryption-dmvs"></a>Şifreleme DMVs

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

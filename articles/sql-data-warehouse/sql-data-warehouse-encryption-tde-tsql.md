---
title: Saydam veri şifrelemesi (T-SQL)
description: SQL veri ambarı 'nda (T-SQL) saydam veri şifrelemesi (TDE)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 4e7f4f31cd8b899e3fcf79568ea62830313936b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822600"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Saydam Veri Şifrelemesi ile çalışmaya başlama (TDE)
> [!div class="op_single_selector"]
> * [Güvenliğe genel bakış](sql-data-warehouse-overview-manage-security.md)
> * [Kimlik doğrulaması](sql-data-warehouse-authentication.md)
> * [Şifreleme (portal)](sql-data-warehouse-encryption-tde.md)
> * [Şifreleme (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Gerekli Izinler
Saydam Veri Şifrelemesi (TDE) etkinleştirmek için, bir yönetici veya dbmanager rolünün bir üyesi olmanız gerekir.

## <a name="enabling-encryption"></a>Şifrelemeyi etkinleştirme
SQL veri ambarı için TDE 'ı etkinleştirmek üzere aşağıdaki adımları izleyin:

1. Yönetici veya ana veritabanında **DBManager** rolünün bir üyesi kullanarak veritabanını barındıran sunucudaki *ana* veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Şifrelemeyi devre dışı bırakma
SQL veri ambarı için TDE 'ı devre dışı bırakmak için aşağıdaki adımları izleyin:

1. Ana veritabanında yönetici veya **DBManager** rolünün üyesi olan bir oturum kullanarak *ana* veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Duraklatılmış bir SQL veri ambarı, TDE ayarlarında değişiklik yapılmadan önce devam etmelidir.
> 
> 

## <a name="verifying-encryption"></a>Şifreleme doğrulanıyor
Bir SQL veri ambarı 'nın şifreleme durumunu doğrulamak için aşağıdaki adımları izleyin:

1. Ana veritabanında yönetici veya **DBManager** rolünün üyesi olan bir oturum kullanarak *ana* veya örnek veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

```1``` sonucu şifreli bir veritabanını gösterir ```0``` şifreli olmayan bir veritabanını gösterir.

## <a name="encryption-dmvs"></a>Şifreleme DMVs
* [sys. databases][sys.databases] 
* [sys. dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->

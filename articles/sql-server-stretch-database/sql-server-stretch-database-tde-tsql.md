---
title: Stretch Database için Saydam Veri Şifrelemesi etkinleştir (T-SQL)
description: Azure TSQL 'de SQL Server Stretch Database için Saydam Veri Şifrelemesi (TDE) etkinleştirme
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033964"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Azure 'da Stretch Database için Saydam Veri Şifrelemesi (TDE) etkinleştirme (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Saydam Veri Şifrelemesi (TDE), uygulamada değişiklik yapılmasına gerek kalmadan veritabanı, ilişkili yedeklemeler ve bekleyen işlem günlüğü dosyalarının gerçek zamanlı şifrelemesini ve şifre çözmeyi gerçekleştirerek kötü amaçlı etkinlik tehditlerine karşı korunmaya yardımcı olur.

TDE, veritabanı şifreleme anahtarı adlı bir simetrik anahtar kullanarak veritabanının tamamının depolanmasını şifreler. Veritabanı şifreleme anahtarı yerleşik bir sunucu sertifikası tarafından korunur. Yerleşik sunucu sertifikası her bir Azure sunucusu için benzersizdir. Microsoft bu sertifikaları en az 90 günde bir otomatik olarak döndürür. TDE genel bir açıklaması için, bkz. [Saydam veri şifrelemesi (tde)].

## <a name="enabling-encryption"></a>Şifrelemeyi etkinleştirme
Esnetme özellikli bir SQL Server veritabanından geçirilen verileri depolayan bir Azure veritabanı için TDE 'yı etkinleştirmek üzere aşağıdaki işlemleri yapın:

1. Yönetici veya ana veritabanında **DBManager** rolünün üyesi olan bir oturum açma kullanarak veritabanını barındıran Azure sunucusundaki *ana* veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Şifrelemeyi devre dışı bırakma
Esnetme özellikli bir SQL Server veritabanından geçirilen verileri depolayan bir Azure veritabanı için TDE ' ı devre dışı bırakmak için aşağıdaki işlemleri yapın:

1. Ana veritabanında yönetici veya **DBManager** rolünün üyesi olan bir oturum kullanarak *ana* veritabanına bağlanın
2. Veritabanını şifrelemek için aşağıdaki ifadeyi yürütün.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Şifreleme doğrulanıyor
Esnetme özellikli bir SQL Server veritabanından geçirilmiş verileri depolayan bir Azure veritabanı için şifreleme durumunu doğrulamak üzere şunları yapın:

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

<!--Anchors-->
[Saydam Veri Şifrelemesi (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

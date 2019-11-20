---
title: Değişken atama
description: Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik T-SQL değişkenlerini atamaya yönelik ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1ae5285a8d1cf6fa391c082d0196b213e6b6a9c5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692764"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda değişken atama

Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik T-SQL değişkenlerini atamaya yönelik ipuçları.

## <a name="setting-variables-with-declare"></a>DECLARE ile değişkenleri ayarlama

SQL veri ambarı 'ndaki değişkenler, `DECLARE` ifadesiyle veya `SET` ifadesiyle ayarlanır. Değişkenleri DECLARE ile başlatmak, SQL veri ambarı 'nda değişken değer ayarlamak için en esnek yollarla biridir.

```sql
DECLARE @v  int = 0
;
```

Aynı anda birden fazla değişken ayarlamak için BILDIR ' i de kullanabilirsiniz. Şunları yapmak için SELECT veya UPDATE kullanamazsınız:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Aynı DECLARE ifadesinde bir değişkeni başlatamıyor ve kullanamazsınız. Bu noktayı göstermek için, @p1 hem başlatılmış hem de aynı DECLARE ifadesinde kullanıldığı için aşağıdaki **örneğe izin verilmez** . Aşağıdaki örnek bir hata verir.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Değerleri ayarla olarak ayarlama

SET, tek bir değişken ayarlamak için yaygın bir yöntemdir.

Aşağıdaki deyimler, SET ile bir değişken ayarlamaya yönelik tüm geçerli yollardır:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Tek seferde yalnızca bir değişken ayarlayabilirsiniz. Ancak, bileşik işleçlere izin verilir.

## <a name="limitations"></a>Sınırlamalar

Değişken atama için GÜNCELLEŞTIRME kullanamazsınız.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

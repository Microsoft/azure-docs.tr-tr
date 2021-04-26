---
title: Değişken atama
description: Bu makalede, Azure SYNAPSE Analytics 'te adanmış SQL havuzları için T-SQL değişkenleri atamaya yönelik gerekli ipuçları bulacaksınız.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459225"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzları için değişkenler atama

Bu makalede, adanmış SQL havuzunda T-SQL değişkenlerini atamaya yönelik gerekli ipuçları bulacaksınız.

## <a name="set-variables-with-declare"></a>DECLARE ile değişkenleri ayarla

Adanmış SQL havuzundaki değişkenler, veya ifadesini kullanarak ayarlanır `DECLARE` `SET` . Değişkenleri DECLARE ile başlatmak, SQL havuzunda değişken değer ayarlamak için en esnek yollarla biridir.

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

Aynı DECLARE ifadesinde bir değişkeni başlatamıyor ve kullanamazsınız. Bu noktayı göstermek için,  @p1 aynı Declare ifadesinde hem başlatılmış hem de kullanıldığı için aşağıdaki örneğe izin verilmez. Bu nedenle, aşağıdaki örnek bir hata verir:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Değerleri ayarla ayarla

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

Değişken atama için UPDATE kullanamazsınız.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

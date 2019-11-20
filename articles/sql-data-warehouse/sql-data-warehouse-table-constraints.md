---
title: Birincil, yabancı ve benzersiz anahtarlar
description: Azure SQL veri ambarı 'nda tablo kısıtlamaları desteği
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 8f3102425c6f984df0f50bc05eeb6f9a5e66d3dd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685485"
---
# <a name="primary-key-foreign-key-and-unique-key-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda birincil anahtar, yabancı anahtar ve benzersiz anahtar

Azure SQL veri ambarı 'nda birincil anahtar, yabancı anahtar ve benzersiz anahtar dahil tablo kısıtlamaları hakkında bilgi edinin.

## <a name="table-constraints"></a>Tablo düzeyinde kısıtlamalar 
Azure SQL veri ambarı, bu tablo kısıtlamalarını destekler: 
- BIRINCIL anahtar yalnızca KÜMELENMEMIŞ ve zorunlu KıLıNMAYAN her ikisi de kullanıldığında desteklenir.    
- UNIQUE kısıtlaması yalnızca ZORLANMAMıŞ ile desteklenir.   

YABANCı anahtar kısıtlaması Azure SQL veri ambarı 'nda desteklenmiyor.  

## <a name="remarks"></a>Açıklamalar
Birincil anahtar ve/veya benzersiz anahtar olması, veri ambarı altyapısının bir sorgu için en uygun yürütme planını oluşturmasına izin verir.  Birincil anahtar sütunundaki veya benzersiz bir kısıtlama sütunundaki tüm değerler benzersiz olmalıdır. 

Azure veri ambarı 'nda birincil anahtar veya benzersiz kısıtlama içeren bir tablo oluşturduktan sonra, kullanıcıların bu sütunlardaki tüm değerlerin benzersiz olduğundan emin olması gerekir.  Bunun ihlali sorgunun yanlış sonuç döndürmesine neden olabilir.  Bu örnek, birincil anahtar veya benzersiz kısıtlama sütunu yinelenen değerler içeriyorsa bir sorgunun yanlış bir sonuç döndürmesine neden olabileceğini gösterir.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Örnekler
Birincil anahtarla bir veri ambarı tablosu oluşturun: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Benzersiz kısıtlaması olan bir veri ambarı tablosu oluşturun:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Sonraki adımlar

Veri Ambarınızla ilgili tabloları oluşturduktan sonra, bir sonraki adım tabloya veri yüklemek olur. Yükleme öğreticisi için bkz. [SQL veri ambarı 'na veri yükleme](load-data-wideworldimportersdw.md).

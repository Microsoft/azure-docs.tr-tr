---
title: T-SQL döngüleri kullanma
description: Çözüm geliştirmek için T-SQL döngülerini kullanma ve Azure SQL veri ambarı 'nda imleçler değiştirme ipuçları.
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
ms.openlocfilehash: b57358e32bda83ef51fe67aa1057411d51773fa6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685834"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>SQL Data Warehouse 'da T-SQL döngüleri kullanma
Çözüm geliştirmek için T-SQL döngülerini kullanma ve Azure SQL veri ambarı 'nda imleçler değiştirme ipuçları.

## <a name="purpose-of-while-loops"></a>WHILE döngülerinin amacı

SQL veri ambarı, sürekli olarak ekstre blokları yürütmeye yönelik [while](/sql/t-sql/language-elements/while-transact-sql) döngüsünü destekler. Bu WHILE döngüsü, belirtilen koşullar doğru olduğu sürece veya kod BREAK anahtar sözcüğünü kullanarak döngüyü özel olarak sonlandırana kadar devam eder. Döngüler SQL kodunda tanımlanan imleçleri değiştirmek için kullanışlıdır. Neyse ki SQL Code 'da yazılan neredeyse tüm imleçler hızlı ileri, salt okunurdur. Bu nedenle, [WHILE] döngüleri, imleçleri değiştirmek için harika bir alternatiftir.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>SQL veri ambarı 'ndaki imleçleri değiştirme
Bununla birlikte, önce baş olarak kullanmadan önce şu soruyu sormanız gerekir: "Bu imleç, küme tabanlı işlemleri kullanmak için yeniden yazılabilir mi?." Çoğu durumda, yanıt Evet 'tir ve genellikle en iyi yaklaşım olur. Küme temelli bir işlem, genellikle yinelemeli, satır satır yaklaşımına göre daha hızlı gerçekleştirilir.

İleri sarma salt okuma imleçleri, bir döngü yapısıyla kolayca değiştirilebilir. Aşağıda basit bir örnek verilmiştir. Bu kod örneği, veritabanındaki her tablo için istatistikleri günceller. Döngüdeki tabloları yineleerek her komut sırayla yürütülür.

İlk olarak, ayrı ayrı deyimleri tanımlamak için kullanılan benzersiz bir satır numarası içeren geçici bir tablo oluşturun:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

İkinci olarak, döngüyü gerçekleştirmek için gereken değişkenleri başlatın:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Şimdi onları birer birer yürüten deyimler üzerinde döngü gerçekleştirin:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Son olarak, ilk adımda oluşturulan geçici tabloyu bırakın

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).


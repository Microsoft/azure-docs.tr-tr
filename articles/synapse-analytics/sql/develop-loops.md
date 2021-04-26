---
title: T-SQL döngüleri kullanma
description: T-SQL döngülerini kullanma, imleçleri değiştirme ve Azure SYNAPSE Analytics 'te SYNAPSE SQL ile ilgili çözümleri geliştirmeyle ilgili ipuçları.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120895"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te SYNAPSE SQL ile T-SQL döngüleri kullanma

Bu makalede T-SQL döngülerini kullanma, imleçler değiştirme ve SYNAPSE SQL ile ilgili çözümleri geliştirme konusunda temel ipuçları sunulmaktadır.

## <a name="purpose-of-while-loops"></a>WHILE döngülerinin amacı

SYNAPSE SQL, sürekli olarak ekstre blokları yürütmeye yönelik [while](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) döngüsünü destekler. Bu WHILE döngüsü, belirtilen koşullar doğru olduğu sürece veya kod BREAK anahtar sözcüğünü kullanarak döngüyü özel olarak sonlandırana kadar devam eder. 

SYNAPSE SQL içindeki döngüler, SQL kodunda tanımlanan imleçleri değiştirmek için kullanışlıdır. Neyse ki SQL Code 'da yazılan neredeyse tüm imleçler hızlı ileri, salt okunurdur. Bu nedenle, döngüler değiştirmek için harika bir alternatiftir.

## <a name="replace-cursors-in-synapse-sql"></a>SYNAPSE SQL 'de imleçleri değiştirme

' De kullanılmadan önce, aşağıdaki soru göz önünde bulundurulmalıdır: "Bu imleç, küme tabanlı işlemleri kullanmak için yeniden yazılabilir mi?" Çoğu durumda, yanıt Evet 'tir ve sıklıkla en iyi yaklaşım olur. Küme temelli bir işlem, genellikle yinelemeli, satır satır yaklaşımına göre daha hızlı yürütülür.

İleri sarma salt okuma imleçler kolayca bir döngü yapısıyla değiştirilmiştir. Aşağıdaki kod basit bir örnektir. Bu kod örneği, veritabanındaki her tablo için istatistikleri günceller. Döngüdeki tabloları yineleerek her komut sırayla yürütülür.

İlk olarak, ayrı ayrı deyimleri tanımlamak için kullanılan benzersiz bir satır numarası içeren geçici bir tablo oluşturun:

```sql
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

İkinci olarak, döngüyü yürütmek için gereken değişkenleri başlatın:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Şimdi onları birer birer yürüten deyimler üzerinde döngü gerçekleştirin:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Son olarak, ilk adımda oluşturulan geçici tabloyu bırakın

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](develop-overview.md).
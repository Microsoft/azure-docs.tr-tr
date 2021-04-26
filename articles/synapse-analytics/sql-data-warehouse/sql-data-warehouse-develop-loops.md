---
title: T-SQL döngüleri kullanma
description: T-SQL döngüleri kullanılarak çözüm geliştirmeye yönelik ipuçları ve Azure SYNAPSE Analytics 'te adanmış SQL havuzları için imleçleri değiştirme.
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
ms.openlocfilehash: a6c4eb98d77ece6e6ae130fd57cc263ee7e5ca64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683231"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzları için T-SQL döngüleri kullanma

Bu makalede, T-SQL döngüleri kullanılarak adanmış SQL havuzu çözümü geliştirme ve imleçleri değiştirme ipuçları yer almaktadır.

## <a name="purpose-of-while-loops"></a>WHILE döngülerinin amacı

Azure 'daki adanmış SQL havuzları, sürekli olarak bildirim blokları yürütmeye yönelik [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) döngüsünü destekler. Bu WHILE döngüsü, belirtilen koşullar doğru olduğu sürece veya kod BREAK anahtar sözcüğünü kullanarak döngüyü özel olarak sonlandırana kadar devam eder.

Döngüler SQL kodunda tanımlanan imleçleri değiştirmek için kullanışlıdır. Neyse ki SQL Code 'da yazılan neredeyse tüm imleçler hızlı ileri, salt okunurdur. Bu nedenle, döngüler değiştirmek için harika bir alternatiftir.

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>Adanmış SQL havuzundaki imleçleri değiştirme

Bununla birlikte, önce baş olarak kullanmadan önce şu soruyu sormanız gerekir: "Bu imleç, küme tabanlı işlemleri kullanmak için yeniden yazılabilir mi?"

Çoğu durumda, yanıt Evet 'tir ve sıklıkla en iyi yaklaşım olur. Küme temelli bir işlem, genellikle yinelemeli, satır satır yaklaşımına göre daha hızlı gerçekleştirilir.

İleri sarma salt okuma imleçleri, bir döngü yapısıyla kolayca değiştirilebilir. Aşağıdaki örnek basit bir örnektir. Bu kod örneği, veritabanındaki her tablo için istatistikleri günceller. Döngüdeki tabloları yineleerek her komut sırayla yürütülür.

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

İkinci olarak, döngüyü gerçekleştirmek için gereken değişkenleri başlatın:

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

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

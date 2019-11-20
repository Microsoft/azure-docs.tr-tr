---
title: Vekil anahtarlar oluşturmak için KIMLIK kullanma
description: Azure SQL veri ambarı 'nda tablolar üzerinde vekil anahtarlar oluşturmak için KIMLIK özelliğinin kullanılmasına yönelik öneriler ve örnekler.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0ee15b975b5513077b26cceeb80ea3fb8c02456b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692476"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda vekil anahtarlar oluşturmak için KIMLIK kullanma

Azure SQL veri ambarı 'nda tablolar üzerinde vekil anahtarlar oluşturmak için KIMLIK özelliğinin kullanılmasına yönelik öneriler ve örnekler.

## <a name="what-is-a-surrogate-key"></a>Vekil anahtar nedir?

Tablodaki bir vekil anahtar, her satır için benzersiz bir tanımlayıcıya sahip bir sütundur. Anahtar Tablo verilerinden oluşturulmaz. Veri ambarı modellerini tasarlarken, tablolarında vekil anahtarlar oluşturmak gibi veri modelleri. KIMLIK özelliğini, yük performansını etkilemeden, bu hedefe basitçe ulaşmak için kullanabilirsiniz.  

## <a name="creating-a-table-with-an-identity-column"></a>KIMLIK sütunuyla tablo oluşturma

IDENTITY özelliği, yük performansını etkilemeden veri ambarındaki tüm dağıtımların ölçeğini genişletmek için tasarlanmıştır. Bu nedenle, KIMLIK uygulanması, bu hedeflere ulaşılmaya yönelik olarak tasarlanmıştır.

Aşağıdaki ifadeye benzer bir sözdizimi kullanarak tabloyu ilk oluşturduğunuzda, KIMLIK özelliğine sahip olarak bir tablo tanımlayabilirsiniz:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Daha sonra tabloyu doldurmak için `INSERT..SELECT` kullanabilirsiniz.

Bu bölümün geri kalanında, bunları daha fazla anlamanıza yardımcı olmak için uygulamanın nuslarını vurgular.  

### <a name="allocation-of-values"></a>Değerlerin ayrılması

IDENTITY özelliği, SQL Server ve Azure SQL veritabanı 'nın davranışını yansıtan vekil değerlerinin ayrıldığı sırayı garanti etmez. Bununla birlikte, Azure SQL veri ambarı 'nda garanti olmaması daha fazla önemlidir.

Aşağıdaki örnek bir çizimde verilmiştir:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Yukarıdaki örnekte, dağıtım 1 ' de iki satır oluşturulmuş. İlk satırda, `C1`sütununda 1 vekil değeri bulunur ve ikinci satırda 61 yedek değeri bulunur. Bu değerlerin her ikisi de ıDENTITY özelliği tarafından oluşturulmuştur. Ancak, değerlerin ayrılması bitişik değildir. Bu davranış tasarım gereğidir.

### <a name="skewed-data"></a>Eğilmiş veriler

Veri türü için değer aralığı, dağıtımlar arasında eşit olarak yayılır. Dağıtılmış bir tablo, asimetrik verilerden bulunursa, veri türü için kullanılabilir olan değer aralığı zamanından önce tükenebilir. Örneğin, tüm veriler tek bir dağıtım içinde sona ererse, etkin şekilde tablo, veri türü değerlerinin yalnızca tek altılarına erişim sağlar. Bu nedenle, ıDENTITY özelliği yalnızca `INT` ve `BIGINT` veri türleriyle sınırlıdır.

### <a name="selectinto"></a>Seç.. BIRLEŞTIRIN

Varolan bir KIMLIK sütunu yeni bir tabloya seçildiğinde, aşağıdaki koşullardan biri doğru değilse yeni sütun KIMLIK özelliğini devralır:

- SELECT ifadesinde bir JOIN var.
- Çoklu SELECT deyimleri UNıON kullanılarak birleştirilir.
- KIMLIK sütunu, SEÇIM listesinde birden çok kez listelendi.
- KIMLIK sütunu bir ifadenin parçasıdır.

Bu koşullardan herhangi biri doğru ise, sütun KIMLIK özelliğini devralma yerine NULL DEĞIL olarak oluşturulur.

### <a name="create-table-as-select"></a>SEÇIM OLARAK CREATE TABLE

SELECT (CTAS) olarak CREATE TABLE, SELECT için belgelenen aynı SQL Server davranışını izler. Birleştirin. Ancak, deyimin `CREATE TABLE` bölümünün sütun tanımında bir ıDENTITY özelliği belirtemezsiniz. CTAS 'nin `SELECT` bölümünde de ıDENTITY işlevini kullanamazsınız. Bir tabloyu doldurmak için `CREATE TABLE` kullanarak tabloyu ve ardından `INSERT..SELECT` doldurmanız gerekir.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Bir KIMLIK sütununa açıkça değer ekleme

SQL veri ambarı `SET IDENTITY_INSERT <your table> ON|OFF` sözdizimini destekler. KIMLIK sütununa açıkça değer eklemek için bu sözdizimini kullanabilirsiniz.

Birçok veri modu, boyutları içindeki belirli satırlar için önceden tanımlanmış negatif değerler kullanmak gibidir. Örnek,-1 veya "bilinmeyen üye" satırıdır.

Sonraki komut dosyası, SET IDENTITY_INSERT kullanarak bu satırın açıkça nasıl ekleneceğini gösterir:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Veri yükleme

IDENTITY özelliğinin varlığı, veri yükleme kodunuzda bazı etkileri vardır. Bu bölümde, KIMLIK kullanarak tablolara veri yüklemeye yönelik bazı temel desenler vurgulanmıştır.

Bir tabloya veri yüklemek ve KIMLIK kullanarak bir yedek anahtar oluşturmak için tabloyu oluşturun ve ardından Ekle ' yi kullanın. Seç veya Ekle... Yüklemeyi gerçekleştirecek değerler.

Aşağıdaki örnekte temel desenler vurgulanmıştır:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> KIMLIK sütunu olan bir tabloya veri yüklerken `CREATE TABLE AS SELECT` kullanmak mümkün değildir.
>

Veri yükleme hakkında daha fazla bilgi için bkz. [Azure SQL veri ambarı Için ayıklama, yükleme ve dönüştürme (ELT) tasarlama](design-elt-data-loading.md) ve [en iyi uygulamalar yükleme](guidance-for-loading-data.md).

## <a name="system-views"></a>Sistem görünümleri

IDENTITY özelliği olan bir sütunu tanımlamak için [sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalog görünümünü kullanabilirsiniz.

Veritabanı şemasını daha iyi anlamanıza yardımcı olmak için bu örnek, sys. identity_column ' nin diğer sistem Kataloğu görünümleriyle nasıl tümleştirileceğini gösterir:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Sınırlamalar

IDENTITY özelliği kullanılamaz:

- Sütun veri türü INT veya BIGINT olmadığında
- Sütun aynı zamanda dağıtım anahtarı olduğunda
- Tablo bir dış tablo olduğunda

Aşağıdaki ilgili işlevler SQL veri ambarı 'nda desteklenmez:

- [IDENTITY ()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Genel görevler

Bu bölümde, KIMLIK sütunlarıyla çalışırken ortak görevleri gerçekleştirmek için kullanabileceğiniz bazı örnek kodlar sağlanmaktadır.

C1 sütunu, aşağıdaki tüm görevlerdeki KIMLIKTIR.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Bir tablo için ayrılan en yüksek değeri bulma

Dağıtılmış bir tablo için ayrılan en yüksek değeri öğrenmek için `MAX()` işlevini kullanın:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>IDENTITY özelliği için çekirdek ve artışı bulma

Aşağıdaki sorguyu kullanarak bir tablonun kimlik artışı ve çekirdek yapılandırma değerlerini öğrenmek için katalog görünümlerini kullanabilirsiniz:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Sonraki adımlar

- [Tabloya genel bakış](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [CREATE TABLE (Transact-SQL) KIMLIĞI (özellik)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKGIRINTILE](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

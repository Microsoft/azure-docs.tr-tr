---
title: Vekil anahtarlar oluşturmak için KIMLIK kullanma
description: Adanmış SQL havuzundaki tablolarda yedek anahtarlar oluşturmak için KIMLIK özelliğinin kullanılmasına yönelik öneriler ve örnekler.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/20/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5c620aa60e134379614a905226caa4a66d179fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673544"
---
# <a name="using-identity-to-create-surrogate-keys-using-dedicated-sql-pool-in-azuresynapse-analytics"></a>AzureSynapse Analytics 'te adanmış SQL havuzu kullanarak yedek anahtarlar oluşturmak için KIMLIK kullanma

Bu makalede, adanmış SQL havuzundaki tablolarda vekil anahtarlar oluşturmak için KIMLIK özelliğini kullanmaya yönelik öneriler ve örnekler bulacaksınız.

## <a name="what-is-a-surrogate-key"></a>Vekil anahtar nedir?

Tablodaki bir vekil anahtar, her satır için benzersiz bir tanımlayıcıya sahip bir sütundur. Anahtar Tablo verilerinden oluşturulmaz. Veri ambarı modellerini tasarlarken, tablolarında vekil anahtarlar oluşturmak gibi veri modelleri. KIMLIK özelliğini, yük performansını etkilemeden, bu hedefe basitçe ulaşmak için kullanabilirsiniz.
> [!NOTE]
> Azure SYNAPSE Analytics 'te, KIMLIK değeri her dağıtımda kendi kendine artar ve diğer dağıtımların KIMLIK değerleriyle çakışmaz.  Kullanıcı açıkça "SET IDENTITY_INSERT ON" veya reseeds KIMLIĞIYLE yinelenen bir değer eklediğinde, SYNAPSE içindeki KIMLIK değerinin benzersiz olması garanti edilmez. Ayrıntılar için bkz. [Create Table (Transact-SQL) Identity (özellik)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest&preserve-view=true). 


## <a name="creating-a-table-with-an-identity-column"></a>KIMLIK sütunuyla tablo oluşturma

IDENTITY özelliği, yük performansını etkilemeden adanmış SQL havuzundaki tüm dağıtımların ölçeğini genişletmek için tasarlanmıştır. Bu nedenle, KIMLIK uygulanması, bu hedeflere ulaşılmaya yönelik olarak tasarlanmıştır.

Aşağıdaki ifadeye benzer bir sözdizimi kullanarak tabloyu ilk oluşturduğunuzda, KIMLIK özelliğine sahip olarak bir tablo tanımlayabilirsiniz:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Daha sonra `INSERT..SELECT` tabloyu doldurmak için öğesini kullanabilirsiniz.

Bu bölümün geri kalanında, bunları daha fazla anlamanıza yardımcı olmak için uygulamanın nuslarını vurgular.  

### <a name="allocation-of-values"></a>Değerlerin ayrılması

IDENTITY özelliği, veri ambarının dağıtılmış mimarisi nedeniyle vekil değerlerinin ayrıldığı sırayı garanti etmez. IDENTITY özelliği, yük performansını etkilemeden adanmış SQL havuzundaki tüm dağıtımların ölçeğini genişletmek için tasarlanmıştır. 

Aşağıdaki örnek bir çizimde verilmiştir:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
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

Yukarıdaki örnekte, dağıtım 1 ' de iki satır oluşturulmuş. İlk satırda, sütununda 1 vekil değeri bulunur `C1` ve ikinci satırda 61 yedek değeri bulunur. Bu değerlerin her ikisi de ıDENTITY özelliği tarafından oluşturulmuştur. Ancak, değerlerin ayrılması bitişik değildir. Bu davranış tasarım gereğidir.

### <a name="skewed-data"></a>Eğilmiş veriler

Veri türü için değer aralığı, dağıtımlar arasında eşit olarak yayılır. Dağıtılmış bir tablo, asimetrik verilerden bulunursa, veri türü için kullanılabilir olan değer aralığı zamanından önce tükenebilir. Örneğin, tüm veriler tek bir dağıtım içinde sona ererse, etkin şekilde tablo, veri türü değerlerinin yalnızca tek altılarına erişim sağlar. Bu nedenle, ıDENTITY özelliği `INT` `BIGINT` yalnızca ve veri türleriyle sınırlıdır.

### <a name="selectinto"></a>Seç.. BIRLEŞTIRIN

Varolan bir KIMLIK sütunu yeni bir tabloya seçildiğinde, aşağıdaki koşullardan biri doğru değilse yeni sütun KIMLIK özelliğini devralır:

- SELECT ifadesinde bir JOIN var.
- Çoklu SELECT deyimleri UNıON kullanılarak birleştirilir.
- KIMLIK sütunu, SEÇIM listesinde birden çok kez listelendi.
- KIMLIK sütunu bir ifadenin parçasıdır.

Bu koşullardan herhangi biri doğru ise, sütun KIMLIK özelliğini devralma yerine NULL DEĞIL olarak oluşturulur.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

SELECT (CTAS) olarak CREATE TABLE, SELECT için belgelenen aynı SQL Server davranışını izler. Birleştirin. Ancak, deyimin bölümünün sütun tanımında bir ıDENTITY özelliği belirtemezsiniz `CREATE TABLE` . `SELECT`CTAS 'nin bölümünde de Identity işlevini kullanamazsınız. Bir tabloyu doldurmak için, `CREATE TABLE` tabloyu ve ardından doldurmak üzere öğesini tanımlamak için kullanmanız gerekir `INSERT..SELECT` .

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Bir KIMLIK sütununa açıkça değer ekleme

Adanmış SQL havuzu `SET IDENTITY_INSERT <your table> ON|OFF` sözdizimini destekler. KIMLIK sütununa açıkça değer eklemek için bu sözdizimini kullanabilirsiniz.

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

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Verileri yükleme

IDENTITY özelliğinin varlığı, veri yükleme kodunuzda bazı etkileri vardır. Bu bölümde, KIMLIK kullanarak tablolara veri yüklemeye yönelik bazı temel desenler vurgulanmıştır.

Bir tabloya veri yüklemek ve KIMLIK kullanarak bir yedek anahtar oluşturmak için tabloyu oluşturun ve ardından Ekle ' yi kullanın. Seç veya Ekle... Yüklemeyi gerçekleştirecek değerler.

Aşağıdaki örnekte temel desenler vurgulanmıştır:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> `CREATE TABLE AS SELECT`Kimlik sütunu olan bir tabloya veri yüklerken şu anda kullanılamaz.
>

Verileri yükleme hakkında daha fazla bilgi için bkz. [Özel SQL havuzu Için ayıklama, yükleme ve dönüştürme (ELT) tasarlama](design-elt-data-loading.md) ve  [en iyi uygulamalar yükleme](guidance-for-loading-data.md).

## <a name="system-views"></a>Sistem görünümleri

IDENTITY özelliği olan bir sütunu belirlemek için [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) katalog görünümünü kullanabilirsiniz.

Veritabanı şemasını daha iyi anlamanıza yardımcı olmak için bu örnek sys.identity_column ' öğesini diğer sistem Kataloğu görünümleriyle nasıl tümleştirileceğini gösterir:

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

Aşağıdaki ilgili işlevler adanmış SQL havuzunda desteklenmez:

- [IDENTITY ()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="common-tasks"></a>Genel görevler

Bu bölümde, KIMLIK sütunlarıyla çalışırken ortak görevleri gerçekleştirmek için kullanabileceğiniz bazı örnek kodlar sağlanmaktadır.

C1 sütunu, aşağıdaki tüm görevlerdeki KIMLIKTIR.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Bir tablo için ayrılan en yüksek değeri bulma

`MAX()`Dağıtılmış bir tablo için ayrılan en yüksek değeri öğrenmek için işlevini kullanın:

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

- [Tabloya genel bakış](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE (Transact-SQL) KIMLIĞI (özellik)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [DBCC CHECKGIRINTILE](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

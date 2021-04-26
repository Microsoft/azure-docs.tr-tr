---
title: Saklı yordamları kullanma
description: Çözüm geliştirme için Azure SYNAPSE Analytics 'te SYNAPSE SQL kullanarak saklı yordamları uygulamaya yönelik ipuçları.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674151"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te SYNAPSE SQL kullanan saklı yordamlar

SYNAPSE SQL tarafından sağlanan ve sunucusuz havuzlar, karmaşık veri işleme mantığını SQL saklı yordamlarına yerleştirmenizi sağlar. Saklı yordamlar, SQL kodunuzu kapsüllemek ve veri ambarındaki verilerinize yakın bir şekilde depolamak için harika bir yoldur. Saklı yordamlar, kodu yönetilebilir birimlere kapsülleyerek ve kodun daha fazla yeniden kullanılabilirliğini kolaylaştırarak geliştiricilerin çözümlerini modüler hale getirmenize yardımcı olur. Her saklı yordam aynı zamanda parametreleri daha da esnek hale getirmek için kabul edebilir.
Bu makalede, çözüm geliştirmeye yönelik SYNAPSE SQL havuzunda saklı yordamları uygulamaya yönelik bazı ipuçları bulacaksınız.

## <a name="what-to-expect"></a>Beklentiler

SYNAPSE SQL SQL Server ' de kullanılan T-SQL özelliklerinin çoğunu destekler. Daha da önemlisi, çözümünüzün performansını en üst düzeye çıkarmak için kullanabileceğiniz genişleme özel özellikleri vardır. Bu makalede, saklı yordamlarda yerleştirebileceğiniz özellikler hakkında bilgi edineceksiniz.

> [!NOTE]
> Yordam gövdesinde yalnızca SYNAPSE SQL Surface alanında desteklenen özellikleri kullanabilirsiniz. Saklı yordamlarda kullanılabilecek nesneleri ve deyimleri belirlemek için [Bu makaleyi](overview-features.md) gözden geçirin. Bu makalelerdeki örneklerde, hem sunucusuz hem de adanmış yüzey alanında kullanılabilen genel Özellikler kullanılır. Bu makalenin sonundaki [sağlanan ve sunucusuz SYNAPSE SQL havuzlarındaki ek sınırlamalara](#limitations) bakın.

SQL havuzunun ölçeğini ve performansını korumak için, davranış farklılıkları ve bazıları desteklenmeyen bazı özellikler ve işlevler de vardır.

## <a name="stored-procedures-in-synapse-sql"></a>SYNAPSE SQL 'de saklı yordamlar

Aşağıdaki örnekte, veritabanında varsa dış nesneleri bırakma yordamlarını görebilirsiniz:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Bu yordamlar `EXEC` , yordam adı ve parametrelerini belirtebileceğiniz bir ifade kullanılarak yürütülebilir:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

SYNAPSE SQL, Basitleştirilmiş ve kolaylaştırılmış bir saklı yordam uygulamasına sahiptir. SQL Server kıyasla en büyük fark, saklı yordamın önceden derlenmiş kod olmaması gerektiğidir. Veri ambarlarında, derleme süresi, büyük veri birimlerine karşı sorgu çalıştırmak için gereken zamana kıyasla küçüktür. Saklı yordam kodunun büyük sorgular için doğru iyileştirildiğinden emin olmak daha önemlidir. Amaç, milisaniye değil, saat, dakika ve saniyeyi tasarrufu sağlamaktır. Bu nedenle, saklı yordamları SQL Logic Container olarak düşünmek daha yararlıdır.

SYNAPSE SQL, saklı yordamlarınızı yürüttüğünde, SQL deyimleri ayrıştırılır, çevrilir ve çalışma zamanında iyileştirilir. Bu işlem sırasında her bir ifade dağıtılmış sorgulara dönüştürülür. Verilere karşı yürütülen SQL kodu, gönderilen sorgudan farklı.

## <a name="encapsulate-validation-rules"></a>Doğrulama kurallarını yalıtma

Saklı yordamlar, doğrulama mantığını SQL veritabanında depolanan tek bir modülde bulmanızı sağlar. Aşağıdaki örnekte, parametrelerin değerlerinin nasıl doğrulanacağı ve varsayılan değerlerini nasıl değiştirebileceğiniz hakkında bilgi alabilirsiniz.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

Yordam çağrıldığında SQL yordamının mantığı giriş parametrelerini doğrular.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Saklı yordamları iç içe geçirme

Saklı yordamlar diğer saklı yordamları çağırdığında veya dinamik SQL yürütürde, iç saklı yordam veya kod çağırma iç içe geçmiş olarak kabul edilir.
Aşağıdaki kodda, iç içe yordam örneği gösterilmektedir:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Bu yordam, bazı adları temsil eden bir parametreyi kabul eder ve ardından bu adı taşıyan nesneleri bırakmak için diğer yordamları çağırır.
SYNAPSE SQL havuzu, en fazla sekiz iç içe geçme düzeyini destekler. Bu özellik SQL Server kıyasla biraz farklıdır. SQL Server iç içe geçme düzeyi 32 ' dir.

Üst düzey saklı yordam çağrısı, iç içe düzey 1 ' e karşılık gelir.

```sql
EXEC clean_up 'mytest'
```

Saklı yordam aynı zamanda başka bir EXEC çağrısı de yapıyorsa, iç içe düzey iki olarak artar.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

İkinci yordam daha sonra bazı dinamik SQL çalıştırırsa, iç içe düzeyi üç olarak artar.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> SYNAPSE SQL Şu anda [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true)öğesini desteklemiyor. İç içe düzeyi izlemeniz gerekir. Sekiz iç içe düzey sınırı aşmanız çok düşüktür, ancak bunu yaparsanız, iç içe geçme düzeylerine bu sınırın sığması için kodunuzu yeniden güncelleştirmeniz gerekir.

## <a name="insertexecute"></a>INSERT..EXECUTE

Sağlanan SYNAPSE SQL havuzu, bir INSERT ifadesiyle saklı yordamın sonuç kümesini kullanmanıza izin vermez. Kullanabileceğiniz alternatif bir yaklaşım vardır. Örnek için, sağlanan SYNAPSE SQL havuzu için [geçici tablolarda](develop-tables-temporary.md) bulunan makaleye bakın.

## <a name="limitations"></a>Sınırlamalar

SYNAPSE SQL 'de uygulanmayan Transact-SQL saklı yordamlarının bazı yönleri vardır; örneğin:

| Özellik/seçenek | Sağlanan | Sunucusuz |
| --- | --- |
| Geçici saklı yordamlar | Hayır | Yes |
| Numaralandırılmış saklı yordamlar | Hayır | Hayır |
| Genişletilmiş saklı yordamlar | Hayır | Hayır |
| CLR saklı yordamları | Hayır | Hayır |
| Şifreleme seçeneği | Hayır | Yes |
| Çoğaltma seçeneği | Hayır | Hayır |
| Tablo değerli parametreler | Hayır | Hayır |
| Salt okuma parametreleri | Hayır | Hayır |
| Varsayılan parametreler | Hayır | Yes |
| Yürütme bağlamları | Hayır | Hayır |
| Return ekstresi | Hayır | Yes |
| IÇINE EKLE.. EXEC | Hayır | Evet |

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](develop-overview.md).

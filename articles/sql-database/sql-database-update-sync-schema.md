---
title: SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin
description: Azure SQL Data Sync şema değişikliklerinin çoğaltılmasını nasıl otomatikleştirebileceğinizi öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/14/2018
ms.openlocfilehash: 639901975bbb66b9f410bea297d9e48cd96d6d1b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822441"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Azure SQL Data Sync şema değişikliklerinin çoğaltılmasını otomatikleştirin

SQL Data Sync, kullanıcıların verileri Azure SQL veritabanları ve şirket içi SQL Server arasında tek bir yönde veya her iki yönde eşitlemesine olanak tanır. SQL Data Sync geçerli sınırlamalarından biri, şema değişikliklerinin çoğaltılmasıyla ilgili desteğin olmamasıdır. Tablo şemasını her değiştirdiğiniz zaman, hub ve tüm Üyeler dahil olmak üzere tüm uç noktalarda değişiklikleri el ile uygulamanız gerekir ve ardından eşitleme şemasını güncelleştirebilirsiniz.

Bu makalede, şema değişikliklerinin tüm SQL Data Sync uç noktalara otomatik olarak çoğaltılması için bir çözüm sunulmaktadır.
1. Bu çözüm, şema değişikliklerini izlemek için bir DDL tetikleyicisi kullanır.
1. Tetikleyici, şema değiştirme komutlarını bir izleme tablosuna ekler.
1. Bu izleme tablosu, veri eşitleme hizmeti kullanılarak tüm uç noktalarla eşitlenir.
1. Diğer uç noktalara şema değişikliklerini uygulamak için ekleme yapıldıktan sonra DML Tetikleyicileri.

Bu makale, şema değişikliğine örnek olarak ALTER TABLE kullanır, ancak bu çözüm diğer şema değişikliği türleri için de geçerlidir.

> [!IMPORTANT]
> Eşitleme ortamınızda otomatik şema değişiklik çoğaltmasını uygulamaya başlamadan önce bu makaleyi dikkatle, özellikle [sorun giderme](#troubleshoot) ve [diğer hususlar](#other)hakkındaki bölümleri dikkatle okumanızı öneririz. Ayrıca, [SQL Data Sync ile birden çok bulut ve şirket içi veritabanı arasında eşitleme verilerini](sql-database-sync-data.md)okumanızı öneririz. Bazı veritabanı işlemleri bu makalede açıklanan çözümü bozabilir. Bu sorunları gidermek için SQL Server ve Transact-SQL ' e ait Ek etki alanı bilgileri gerekebilir.

![Şema değişikliklerinin çoğaltılmasını otomatikleştirme](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Otomatik şema değişiklik çoğaltmasını ayarlama

### <a name="create-a-table-to-track-schema-changes"></a>Şema değişikliklerini izlemek için tablo oluşturma

Eşitleme grubundaki tüm veritabanlarında şema değişikliklerini izlemek için bir tablo oluşturun:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Bu tabloda, şema değişikliklerinin sırasını izlemek için bir kimlik sütunu bulunur. Gerekirse daha fazla bilgi günlüğe kaydetmek için daha fazla alan ekleyebilirsiniz.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Şema değişikliklerinin geçmişini izlemek için bir tablo oluşturma

Tüm uç noktalarında, en son uygulanan şema değiştirme komutunun KIMLIĞINI izlemek için bir tablo oluşturun.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Şema değişikliklerinin yapıldığı veritabanında ALTER TABLE DDL tetikleyicisi oluşturma

ALTER TABLE işlemleri için bir DDL tetikleyicisi oluşturun. Bu tetikleyiciyi yalnızca şema değişikliklerinin yapıldığı veritabanında oluşturmanız gerekir. Çakışmaları önlemek için, yalnızca bir eşitleme grubundaki bir veritabanında şema değişikliklerine izin verin.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Tetikleyici, her ALTER TABLE komutu için şema değişiklik izleme tablosuna bir kayıt ekler. Bu örnek, veri eşitleme hizmeti tarafından büyük olasılıkla yapıldığından, şema **DataSync**altında yapılan şema değişikliklerinin çoğaltılmasını önlemek için bir filtre ekler. Yalnızca belirli şema değişiklik türlerini çoğaltmak istiyorsanız daha fazla filtre ekleyin.

Diğer şema değişikliği türlerini çoğaltmak için daha fazla tetikleyici de ekleyebilirsiniz. Örneğin, değişiklikleri saklı yordamlara çoğaltmak için CREATE_PROCEDURE, ALTER_PROCEDURE ve DROP_PROCEDURE Tetikleyicileri oluşturun.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Ekleme sırasında şema değişikliklerini uygulamak için diğer uç noktalarda Tetikleyici oluşturma

Bu tetikleyici, diğer uç noktalarla eşitlendiğinde şema değiştirme komutunu yürütür. Şema değişikliklerinin yapıldığı (yani, DDL tetikleyicisi `AlterTableDDLTrigger` önceki adımda oluşturulduğu veritabanında) hariç, bu tetikleyiciyi tüm uç noktalarında oluşturmanız gerekir.

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Bu tetikleyici, ekleme işleminden sonra çalışır ve geçerli komutun bir sonraki çalıştırılıp çalıştırılmayacağını denetler. Kod mantığı, hiçbir şema değişikliği ifadesinin atlanmamasını sağlar ve ekleme sırası dışında olsa bile tüm değişiklikler uygulanır.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Şema değişiklik izleme tablosunu tüm uç noktalarla Eşitle

Şema değişiklik izleme tablosunu, var olan eşitleme grubunu veya yeni bir eşitleme grubunu kullanarak tüm uç noktalarla eşitleyebilirsiniz. İzleme tablosundaki değişikliklerin, özellikle de tek yönlü bir eşitleme kullanırken tüm uç noktalarla eşitlendiğinden emin olun.

Şema değişiklik geçmişi tablosunu eşitleme, bu tablo farklı uç noktalarda farklı bir durum sakladığı için.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Şema değişikliklerini bir eşitleme grubuna Uygula

Yalnızca DDL tetikleyicisinin oluşturulduğu veritabanında yapılan şema değişiklikleri çoğaltılır. Diğer veritabanlarında yapılan şema değişiklikleri çoğaltılmaz.

Şema değişiklikleri tüm uç noktalara çoğaltıldıktan sonra, yeni sütunları eşitlemeyi başlatmak veya durdurmak için eşitleme şemasını güncelleştirmek üzere ek adımlar gerçekleştirmeniz gerekir.

#### <a name="add-new-columns"></a>Yeni sütun Ekle

1.  Şema değişikliğini yapın.

1.  Tetikleyiciyi oluşturan adımı tamamlayana kadar yeni sütunların dahil edildiği herhangi bir veri değişikliğini önleyin.

1.  Şema değişiklikleri tüm uç noktalara uygulanana kadar bekleyin.

1.  Veritabanı şemasını yenileyin ve yeni sütunu eşitleme şemasına ekleyin.

1.  Yeni sütundaki veriler bir sonraki eşitleme işlemi sırasında eşitlenir.

#### <a name="remove-columns"></a>Sütunları kaldırma

1.  Eşitleme şemasından sütunları kaldırın. Veri eşitleme, bu sütunlardaki verileri eşitlemeyi durduruyor.

1.  Şema değişikliğini yapın.

1.  Veritabanı şemasını yenileyin.

#### <a name="update-data-types"></a>Veri türlerini güncelleştirme

1.  Şema değişikliğini yapın.

1.  Şema değişiklikleri tüm uç noktalara uygulanana kadar bekleyin.

1.  Veritabanı şemasını yenileyin.

1.  Yeni ve eski veri türleri tamamen uyumlu değilse-Örneğin, `int` 'den `bigint` olarak değiştirirseniz, Tetikleyicileri oluşturan adımlar tamamlanmadan önce eşitleme başarısız olabilir. Eşitleme, yeniden denemeden sonra başarılı oldu.

#### <a name="rename-columns-or-tables"></a>Sütunları veya tabloları yeniden adlandırma

Sütunları veya tabloları yeniden adlandırmak, veri eşitlemenin çalışmayı durdurmasına neden olur. Yeni bir tablo veya sütun oluşturun, verileri geri girin ve ardından eski tabloyu veya sütunu yeniden adlandırmak yerine silin.

#### <a name="other-types-of-schema-changes"></a>Diğer şema değişikliği türleri

Diğer şema değişikliği türleri için (örneğin, saklı yordamlar oluşturma veya bir dizini bırakma), eşitleme şemasının güncelleştirilmesi gerekli değildir.

## <a name="troubleshoot"></a>Otomatik şema değişikliği çoğaltma sorunlarını giderme

Bu makalede açıklanan çoğaltma mantığı bazı durumlarda çalışmayı durduruyor. Örneğin, Azure SQL veritabanı 'nda desteklenmeyen bir şirket içi veritabanında bir şema değişikliği yaptıysanız. Bu durumda, şema değişiklik izleme tablosunu eşitleme başarısız olur. Bu sorunu el ile çözmeniz gerekir:

1.  DDL tetikleyicisini devre dışı bırakın ve sorun düzeltilinceye kadar başka şema değişikliklerinden kaçının.

1.  Sorunun gerçekleştiği uç nokta veritabanında, şema değişikliğinin gerçekleşebileceği uç noktada ekleme tetikleyicisi ' ni devre dışı bırakın. Bu eylem, şema değiştirme komutunun eşitlenmesini sağlar.

1.  Şema değişiklik izleme tablosunu eşitlemek için eşitlemeyi tetikle.

1.  Sorunun gerçekleştiği uç nokta veritabanında, en son uygulanan şema değiştirme komutunun KIMLIĞINI almak için şema değişiklik geçmişi tablosunu sorgulayın.

1.  Önceki adımda aldığınız KIMLIK değerinden daha büyük bir KIMLIĞE sahip tüm komutları listelemek için şema değişiklik izleme tablosunu sorgulayın.

    a.  Uç nokta veritabanında yürütülemeyen komutları yoksayın. Şema tutarsızlığı ile uğraşmanız gerekir. Tutarsızlık uygulamanızı etkirse, özgün şema değişikliklerini dönün.

    b.  Uygulanması gereken bu komutları el ile uygulayın.

1.  Şema değişiklik geçmişi tablosunu güncelleştirin ve en son uygulanan KIMLIĞI doğru değere ayarlayın.

1.  Şemanın güncel olup olmadığını iki kez kontrol edin.

1.  İkinci adımda INSERT tetikleyicisi devre dışı bırakıldıktan sonra yeniden etkinleştirin.

1.  İlk adımda DDL tetikleyicisini devre dışı olarak yeniden etkinleştirin.

Şema değişiklik izleme tablosunda kayıtları temizlemek istiyorsanız, kes yerine DELETE kullanın. Şema değişiklik izleme tablosundaki kimlik sütunu DBCC CHECKIDENT kullanarak hiçbir şekilde yeniden sıfırlanıyor. Yeniden dağıtım gerekliyse, yeni şema değişiklik izleme tabloları oluşturabilir ve DDL tetikleyicisinde tablo adını güncelleştirebilirsiniz.

## <a name="other"></a>Diğer konular

-   Hub ve üye veritabanlarını yapılandıran veritabanı kullanıcılarının, şema değiştirme komutlarını yürütmek için yeterli izni olması gerekir.

-   Yalnızca seçili tablolardaki veya işlemlerdeki şema değişikliğini çoğaltmak için DDL tetikleyicisine daha fazla filtre ekleyebilirsiniz.

-   Şema değişikliklerini yalnızca DDL tetikleyicisinin oluşturulduğu veritabanında yapabilirsiniz.

-   Şirket içi SQL Server veritabanında değişiklik yapıyorsanız, şema değişikliğinin Azure SQL veritabanı 'nda desteklendiğinden emin olun.

-   Şema değişiklikleri DDL tetikleyicisinin oluşturulduğu veritabanı dışındaki veritabanlarında yapılırsa, değişiklikler çoğaltılmaz. Bu sorundan kaçınmak için, diğer uç noktalarda değişiklikleri engellemek üzere DDL Tetikleyicileri oluşturabilirsiniz.

-   Şema değişiklik izleme tablosunun şemasını değiştirmeniz gerekiyorsa, değişikliği yapmadan önce DDL tetikleyicisini devre dışı bırakın ve ardından değişikliği tüm uç noktalara el ile uygulayın. Aynı tablodaki bir INSERT tetikleyicisinden sonra şemanın güncelleştirilmesi çalışmaz.

-   DBCC CHECKIDENT kullanarak kimlik sütununu yeniden mühürmeyin.

-   Şema değişiklik izleme tablosundaki verileri temizlemek için TRUNCATE kullanmayın.

## <a name="next-steps"></a>Sonraki adımlar

SQL Data Sync hakkında daha fazla bilgi için bkz.:

-   Genel Bakış- [Azure SQL Data Sync ile birden çok bulut ve şirket içi veritabanı arasında veri eşitleme](sql-database-sync-data.md)
-   Veri eşitlemesini ayarlama
    - Portalda- [öğreticide, Azure SQL veritabanı ve şirket içi SQL Server arasında veri eşitlemek için SQL Data Sync ayarlama](sql-database-get-started-sql-data-sync.md)
    - PowerShell ile
        -  [PowerShell kullanarak birden çok Azure SQL veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell kullanarak bir Azure SQL Veritabanı ile SQL Server şirket içi veritabanı arasında eşitleme](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Veri eşitleme Aracısı- [Azure SQL Data Sync Için veri eşitleme Aracısı](sql-database-data-sync-agent.md)
-   En iyi uygulamalar- [Azure SQL Data Sync Için en iyi yöntemler](sql-database-best-practices-data-sync.md)
-   İzleyici- [Azure izleyici günlükleri ile izleyici SQL Data Sync](sql-database-sync-monitor-oms.md)
-   Sorun giderme- [Azure SQL Data Sync sorunlarını giderme](sql-database-troubleshoot-data-sync.md)
-   Eşitleme şemasını güncelleştirme
    -   PowerShell ile- [varolan bir eşitleme grubundaki eşitleme şemasını güncelleştirmek Için PowerShell kullanın](scripts/sql-database-sync-update-schema.md)

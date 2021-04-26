---
title: İşlemleri kullanma
description: Çözümleri geliştirmek için Azure SYNAPSE Analytics 'te adanmış SQL havuzu ile işlem uygulama ipuçları.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8af6802f785718ca6064a34c98d9f6dafc046a2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594776"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzu ile işlemleri kullanma

Çözümleri geliştirmek için Azure SYNAPSE Analytics 'te adanmış SQL havuzu ile işlem uygulama ipuçları.

## <a name="what-to-expect"></a>Beklentiler

Tahmin ettiğiniz gibi, adanmış SQL havuzu veri ambarı iş yükünün parçası olarak işlemleri destekler. Ancak, adanmış SQL havuzunun performansının büyük ölçekli olarak korunduğundan emin olmak için bazı özellikler SQL Server ile karşılaştırıldığında sınırlı olur. Bu makalede farklılıklar vurgulanmıştır ve diğerleri listelenmiştir.

## <a name="transaction-isolation-levels"></a>İşlem yalıtım düzeyleri

Adanmış SQL havuzu, ACID işlemlerini uygular. İşlem desteğinin yalıtım düzeyi, KAYDEDILMEYEN okuma için varsayılan değer olarak kullanılır.  Ana veritabanına bağlıyken Kullanıcı veritabanı için READ_COMMITTED_SNAPSHOT veritabanı seçeneğini açarak, KAYDEDILMIŞ anlık görüntü YALıTıMıNı okumak için bunu değiştirebilirsiniz.  

Etkinleştirildikten sonra, bu veritabanındaki tüm işlemler okuma tarafından yürütülen anlık görüntü YALıTıMı altında yürütülür ve oturum düzeyinde READ UNCOMMıTTED ayarı dikkate alınmayacak. Ayrıntılar için [alter database set Options (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?preserve-view=true&view=azure-sqldw-latest) ' i işaretleyin.

## <a name="transaction-size"></a>İşlem boyutu
Tek bir veri değişikliği işleminin boyutu sınırlıdır. Sınır, dağıtım başına uygulanır. Bu nedenle, toplam ayırma, sınırı dağıtım sayısıyla çarpılarak hesaplanabilir. 

İşlemdeki en fazla satır sayısını tahmin etmek için dağıtım ucunu her satırın toplam boyutuna böler. Değişken uzunluğu sütunlarında, en büyük boyutu kullanmak yerine ortalama bir sütun uzunluğu almayı düşünün.

Aşağıdaki varsayımlar aşağıda verilmiştir:

* Verilerin eşit bir şekilde dağıtılması gerçekleşti
* Ortalama satır uzunluğu 250 bayttır

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Dağıtım başına sınır (GB) | Dağıtım sayısı | En fazla işlem boyutu (GB) | Dağıtım başına satır sayısı | İşlem başına en fazla satır |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4.000.000 |240.000.000 |
| DW200c |1,5 |60 |90 |6,000,000 |360.000.000 |
| DW300c |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c'yi seçin |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7,5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW2500c |18,75 |60 |1125 |75.000.000 |4.500.000.000 |
| DW3000c |22.5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW5000c |37.5 |60 |2.250 |150.000.000 |9.000.000.000 |
| DW6000c |45 |60 |2.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3.375 |225.000.000 |13.500.000.000 |
| DW10000c |75 |60 |4.500 |300,000,000 |18.000.000.000 |
| DW15000c |112.5 |60 |6,750 |450.000.000 |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900.000.000 |54.000.000.000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Dağıtım başına sınır (GB) | Dağıtım sayısı | En fazla işlem boyutu (GB) | Dağıtım başına satır sayısı | İşlem başına en fazla satır |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4.000.000 |240.000.000 |
| DW200 |1,5 |60 |90 |6,000,000 |360.000.000 |
| DW300 |2.25 |60 |135 |9.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4,5 |60 |270 |18.000.000 |1.080.000.000 |
| DW1000 |7,5 |60 |450 |30.000.000 |1.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3.600.000.000 |
| DW3000 |22.5 |60 |1.350 |90.000.000 |5.400.000.000 |
| DW6000 |45 |60 |2.700 |180.000.000 |10.800.000.000 |

İşlem boyut sınırı işlem veya işlem başına uygulandı. Tüm eşzamanlı işlemler arasında uygulanmaz. Bu nedenle, her bir işlemin günlüğe bu miktarda veri yazmasına izin verilir.

Günlüğe yazılan veri miktarını iyileştirmek ve en aza indirmek için [işlem en iyi yöntemler](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) makalesine başvurun.

> [!WARNING]
> En büyük işlem boyutu yalnızca, verilerin yayılmasının eşit olduğu karma veya ROUND_ROBIN dağıtılmış tablolar için elde edilebilir. İşlem, dağıtımlarla çarpıtılmış bir şekilde veri yazıyor, en yüksek işlem boyutundan önce sınıra ulaşılması olasıdır.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>İşlem durumu

Adanmış SQL havuzu,-2 değerini kullanarak başarısız bir işlemi raporlamak için XACT_STATE () işlevini kullanır. Bu değer, işlemin başarısız olduğu ve yalnızca geri alma için işaretlenen anlamına gelir.

> [!NOTE]
> Başarısız bir işlemi göstermek için XACT_STATE işlevi tarafından-2 kullanılması SQL Server farklı davranışları temsil eder. SQL Server, bir uncommittable işlemini göstermek için-1 değerini kullanır. SQL Server, bir işlem içindeki bazı hatalara, uncommittable olarak işaretlenmesi gerekmeden tolerans sağlayabilir. Örneğin, bir `SELECT 1/0` hataya neden olur, ancak bir işlemi committable durumuna zorlamaz. SQL Server ayrıca, komuntable işleminde okuma izni verir. Ancak adanmış SQL havuzu bunu yapmanızı sağlar. Adanmış bir SQL havuzu işleminde hata oluşursa, otomatik olarak-2 durumunu girer ve deyim geri alınana kadar başka bir SELECT deyimi de yapamazsınız. Bu nedenle, kod değişiklikleri yapmanız gerekebilmeniz için uygulama kodunuzun XACT_STATE () kullanıp kullanmadığını denetlemek önemlidir.

Örneğin SQL Server, aşağıdaki gibi görünen bir işlem görebilirsiniz:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Yukarıdaki kod aşağıdaki hata iletisini verir:

Msg 111233, düzey 16, durum 1, satır 1 111233; Geçerli işlem iptal edildi ve bekleyen tüm değişiklikler geri alındı. Neden: yalnızca geri alma durumundaki bir işlem, bir DDL, DML veya SELECT ifadesiyle önce açık bir şekilde geri alınmadı.

ERROR_ * işlevlerinin çıktısını almazsınız.

Adanmış SQL havuzunda kodun biraz değiştirilmesi gerekir:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Beklenen davranış artık gözlemlenmiştir. İşlemdeki hata yönetilir ve ERROR_ * işlevleri değerleri beklenen şekilde sağlar.

Tüm değiştirilen işlem GERI ALMANıN, CATCH bloğundaki hata bilgilerinin okunmasından önce gerçekleşmesi gerekiyordu.

## <a name="error_line-function"></a>Error_Line () işlevi

Ayrıca, adanmış SQL havuzunun ERROR_LINE () işlevini uygulamadığından veya desteklemediği bir değer de vardır. Kodunuzda bu işleve sahipseniz, adanmış SQL havuzuyla uyumlu olacak şekilde kaldırmanız gerekir. Eşdeğer işlevselliği uygulamak için kodunuzda sorgu etiketleri kullanın. Daha fazla bilgi için bkz. [etiket](develop-label.md) makalesi.

## <a name="use-of-throw-and-raiserror"></a>THROW ve RAERROR kullanımı

THROW, adanmış SQL havuzunda özel durumları oluşturmak için daha modern bir uygulama, ancak RAERROR da desteklenir. Bununla ilgili dikkat edilmesi gereken birkaç fark vardır.

* Kullanıcı tanımlı hata iletileri numaraları, THROW için 100.000-150.000 aralığında olamaz
* RAERROR hata iletileri 50.000 ' de düzeltildi
* Sys. messages kullanımı desteklenmiyor

## <a name="limitations"></a>Sınırlamalar

Adanmış SQL havuzu, işlemlerle ilgili birkaç başka kısıtlama sağlar. Bunlar şu şekildedir:

* Dağıtılmış işlem yok
* İç içe işlem yapılmasına izin verilmez
* Kaydetme noktasına izin verilmez
* Adlandırılmış işlem yok
* İşaretlenmiş işlem yok
* Kullanıcı tanımlı işlem içinde CREATE TABLE gibi DDL desteği yoktur

## <a name="next-steps"></a>Sonraki adımlar

İşlemleri iyileştirme hakkında daha fazla bilgi edinmek için bkz. [işlem en iyi uygulamaları](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). [ADANMıŞ SQL havuzu](best-practices-dedicated-sql-pool.md) ve [sunucusuz SQL havuzu](best-practices-serverless-sql-pool.md)için ek en iyi yöntemler kılavuzları da sağlanır.
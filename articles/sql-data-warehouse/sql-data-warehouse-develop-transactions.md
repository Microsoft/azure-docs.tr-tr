---
title: İşlemleri kullanma
description: Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik işlemler uygulama ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 376b7b8a734e5064713237e9250542a4c5cc18f1
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903083"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>SQL veri ambarı 'nda işlemleri kullanma
Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik işlemler uygulama ipuçları.

## <a name="what-to-expect"></a>Sizi neler bekliyor
Bekleneceğiniz gibi SQL veri ambarı, veri ambarı iş yükünün parçası olarak işlemleri destekler. Ancak, SQL veri ambarı 'nın performansının ölçekte korunduğundan emin olmak için bazı özellikler SQL Server karşılaştırıldığında sınırlı olur. Bu makalede farklılıklar vurgulanmıştır ve diğerleri listelenmiştir. 

## <a name="transaction-isolation-levels"></a>İşlem yalıtım düzeyleri
SQL veri ambarı, ACID işlemlerini uygular. Ancak, işlem desteğinin yalıtım düzeyi READ UNCOMMıTTED ile sınırlıdır; Bu düzey değiştirilemez. READ UNCOMMıTTED bir sorun oluşturacaksa, verilerin kirli olarak okunmasını engellemek için bir dizi kodlama yöntemi uygulayabilirsiniz. En popüler Yöntemler, kullanıcıların hala hazırlanmakta olan verileri sorgulamasını engellemek için hem CTAS hem de tablo bölüm değiştirme (genellikle kayan pencere stili olarak bilinir) kullanır. Verilerin ön filtreleneceği görünümler de popüler bir yaklaşımdır.  

## <a name="transaction-size"></a>İşlem boyutu
Tek bir veri değişikliği işleminin boyutu sınırlıdır. Sınır, dağıtım başına uygulanır. Bu nedenle, toplam ayırma, sınırı dağıtım sayısıyla çarpılarak hesaplanabilir. İşlemdeki en fazla satır sayısını tahmin etmek için dağıtım ucunu her satırın toplam boyutuna böler. Değişken uzunluğu sütunlarında, en büyük boyutu kullanmak yerine ortalama bir sütun uzunluğu almayı düşünün.

Aşağıdaki varsayımlar aşağıda verilmiştir:

* Verilerin eşit bir şekilde dağıtılması gerçekleşti 
* Ortalama satır uzunluğu 250 bayttır

## <a name="gen2"></a>Gen2

| [DWU](sql-data-warehouse-overview-what-is.md) | Dağıtım başına sınır (GB) | Dağıtım sayısı | En fazla işlem boyutu (GB) | Dağıtım başına satır sayısı | İşlem başına en fazla satır |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200c |1,5 |60 |90 |6,000,000 |360.000.000 |
| DW300c |2,25 |60 |135 |9\.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7,5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW2500c |18,75 |60 |1125 |75.000.000 |4\.500.000.000 |
| DW3000c |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW5000c |37,5 |60 |2\.250 |150.000.000 |9\.000.000.000 |
| DW6000c |45 |60 |2\.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3\.375 |225.000.000 |13.500.000.000 |
| DW10000c |75 |60 |4\.500 |300,000,000 |18.000.000.000 |
| DW15000c |112,5 |60 |6\.750 |450.000.000 |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900.000.000 |54.000.000.000 |

## <a name="gen1"></a>Gen1

| [DWU](sql-data-warehouse-overview-what-is.md) | Dağıtım başına sınır (GB) | Dağıtım sayısı | En fazla işlem boyutu (GB) | Dağıtım başına satır sayısı | İşlem başına en fazla satır |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200 |1,5 |60 |90 |6,000,000 |360.000.000 |
| DW300 |2,25 |60 |135 |9\.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4,5 |60 |270 |18.000.000 |1\.080.000.000 |
| DW1000 |7,5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2\.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW3000 |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW6000 |45 |60 |2\.700 |180.000.000 |10.800.000.000 |

İşlem boyut sınırı işlem veya işlem başına uygulandı. Tüm eşzamanlı işlemler arasında uygulanmaz. Bu nedenle, her bir işlemin günlüğe bu miktarda veri yazmasına izin verilir. 

Günlüğe yazılan veri miktarını iyileştirmek ve en aza indirmek için lütfen [En Iyi işlemler uygulamalar](sql-data-warehouse-develop-best-practices-transactions.md) makalesine başvurun.

> [!WARNING]
> En büyük işlem boyutu yalnızca, verilerin yayılmasının eşit olduğu karma veya ROUND_ROBIN dağıtılmış tablolar için elde edilebilir. İşlem, dağıtımlarla çarpıtılmış bir şekilde veri yazıyor, en yüksek işlem boyutundan önce sınıra ulaşılması olasıdır.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>İşlem durumu
SQL veri ambarı,-2 değerini kullanarak başarısız bir işlemi raporlamak için XACT_STATE () işlevini kullanır. Bu değer, işlemin başarısız olduğu ve yalnızca geri alma için işaretlenen anlamına gelir.

> [!NOTE]
> Başarısız bir işlemi göstermek için XACT_STATE işlevi tarafından-2 kullanılması SQL Server farklı davranışları temsil eder. SQL Server, bir uncommittable işlemini göstermek için-1 değerini kullanır. SQL Server, bir işlem içindeki bazı hatalara, uncommittable olarak işaretlenmesi gerekmeden tolerans sağlayabilir. Örneğin `SELECT 1/0` bir hataya neden olur, ancak bir işlemi committable durumuna zorlamaz. SQL Server ayrıca, komuntable işleminde okuma izni verir. Ancak, SQL veri ambarı bunu yapmanızı sağlar. Bir SQL veri ambarı işleminin içinde bir hata oluşursa, otomatik olarak-2 durumunu girer ve deyim geri alınana kadar başka bir SELECT deyimi de yapamazsınız. Bu nedenle, kod değişiklikleri yapmanız gerekebilmeniz için uygulama kodunuzun XACT_STATE () kullanıp kullanmadığını denetlemek önemlidir.
> 
> 

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

Msg 111233, düzey 16, durum 1, satır 1 111233; Geçerli işlem iptal edildi ve bekleyen tüm değişiklikler geri alındı. Neden: yalnızca geri alma durumundaki bir işlem, bir DDL, DML veya SELECT ifadesiyle önce açıkça geri alınmadı.

ERROR_ * işlevlerinin çıktısını almazsınız.

SQL veri ambarı 'nda kodun biraz değiştirilmesi gerekir:

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
Ayrıca, SQL veri ambarı 'nın ERROR_LINE () işlevini uygulamamayı veya desteklemediğini de unutmayın. Kodunuzda bu varsa, SQL veri ambarı ile uyumlu olacak şekilde kaldırmanız gerekir. Eşdeğer işlevselliği uygulamak için kodunuzda sorgu etiketleri kullanın. Daha fazla ayrıntı için bkz. [etiket](sql-data-warehouse-develop-label.md) makalesi.

## <a name="using-throw-and-raiserror"></a>THROW ve RAERROR kullanma
THROW, SQL veri ambarı 'nda özel durumlar oluşturmak için daha modern bir uygulama, ancak RAERROR da desteklenir. Bununla ilgili dikkat edilmesi gereken birkaç fark vardır.

* Kullanıcı tanımlı hata iletileri numaraları, THROW için 100.000-150.000 aralığında olamaz
* RAERROR hata iletileri 50.000 ' de düzeltildi
* Sys. messages kullanımı desteklenmiyor

## <a name="limitations"></a>Sınırlamalar
SQL veri ambarı, işlemlerle ilgili birkaç farklı kısıtlama sağlar.

Bunlar aşağıdaki gibidir:

* Dağıtılmış işlem yok
* İç içe işlem yapılmasına izin verilmez
* Kaydetme noktasına izin verilmez
* Adlandırılmış işlem yok
* İşaretlenmiş işlem yok
* Kullanıcı tanımlı işlem içinde CREATE TABLE gibi DDL desteği yoktur

## <a name="next-steps"></a>Sonraki adımlar
İşlemleri iyileştirme hakkında daha fazla bilgi edinmek için bkz. [işlem en iyi uygulamaları](sql-data-warehouse-develop-best-practices-transactions.md). Diğer SQL veri ambarı en iyi uygulamaları hakkında bilgi edinmek için bkz. [SQL veri ambarı en iyi uygulamaları](sql-data-warehouse-best-practices.md).


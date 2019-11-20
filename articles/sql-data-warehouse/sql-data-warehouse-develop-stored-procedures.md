---
title: Saklı yordamları kullanma
description: Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik saklı yordamları uygulamaya yönelik ipuçları.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e6e1144043cbbbc8124785351e1e56a776b84527
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692816"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>SQL veri ambarı 'nda saklı yordamları kullanma
Azure SQL veri ambarı 'nda çözüm geliştirmeye yönelik saklı yordamları uygulamaya yönelik ipuçları.

## <a name="what-to-expect"></a>Sizi neler bekliyor

SQL veri ambarı SQL Server ' de kullanılan T-SQL özelliklerinin çoğunu destekler. Daha da önemlisi, çözümünüzün performansını en üst düzeye çıkarmak için kullanabileceğiniz genişleme özel özellikleri vardır.

Bununla birlikte, SQL veri ambarı 'nın ölçeğini ve performansını korumak için, davranış farklılıkları ve bazıları desteklenmeyen bazı özellikler ve işlevler de vardır.


## <a name="introducing-stored-procedures"></a>Saklı yordamları tanıtma
Saklı yordamlar, SQL kodunuzu kapsüllemek için harika bir yoldur; veri ambarındaki verilerinize yakın bir şekilde depolanın. Saklı yordamlar, geliştiricilerin kodu yönetilebilir birimlere kapsülleyerek çözümlerini modüler hale getirmenize yardımcı olur; kodun daha fazla yeniden kullanılabilirliğini kolaylaştırın. Her saklı yordam aynı zamanda parametreleri daha da esnek hale getirmek için kabul edebilir.

SQL veri ambarı, Basitleştirilmiş ve kolaylaştırılmış bir saklı yordam uygulamasına sahiptir. SQL Server kıyasla en büyük fark, saklı yordamın önceden derlenmiş kod olmaması gerektiğidir. Veri ambarlarında, derleme süresi, büyük veri birimlerine karşı sorgu çalıştırmak için gereken zamana kıyasla küçüktür. Saklı yordam kodunun büyük sorgular için doğru iyileştirildiğinden emin olmak daha önemlidir. Amaç, milisaniye değil, saat, dakika ve saniyeyi tasarrufu sağlamaktır. Bu nedenle, saklı yordamları SQL Logic Container olarak düşünmek daha yararlıdır.     

SQL veri ambarı saklı yordamınızda yürütüldüğünde, SQL deyimleri ayrıştırılır, çevrilir ve çalışma zamanında iyileştirilir. Bu işlem sırasında her bir ifade dağıtılmış sorgulara dönüştürülür. Verilere karşı yürütülen SQL kodu, gönderilen sorgudan farklı.

## <a name="nesting-stored-procedures"></a>Saklı yordamları iç içe geçirme
Saklı yordamlar diğer saklı yordamları çağırdığında veya dinamik SQL yürütürde, iç saklı yordam veya kod çağırma iç içe geçmiş olarak kabul edilir.

SQL veri ambarı, en fazla sekiz iç içe geçme düzeyini destekler. Bu SQL Server biraz farklıdır. SQL Server iç içe geçme düzeyi 32 ' dir.

Üst düzey saklı yordam çağrısı, iç içe düzey 1 ' e karşılık gelir.

```sql
EXEC prc_nesting
```
Saklı yordam aynı zamanda başka bir EXEC çağrısı de yapıyorsa, iç içe düzey iki olarak artar.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
İkinci yordam daha sonra bazı dinamik SQL çalıştırırsa, iç içe düzeyi üç olarak artar.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Not, SQL veri ambarı Şu anda [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql)desteklememektedir. İç içe düzeyi izlemeniz gerekir. Sekiz iç içe düzey sınırı aşmanız çok düşüktür, ancak bunu yaparsanız, iç içe geçme düzeylerine bu sınırın sığması için kodunuzu yeniden güncelleştirmeniz gerekir.

## <a name="insertexecute"></a>Ekle.. YÜRÜTME
SQL veri ambarı, bir INSERT ifadesiyle saklı yordamın sonuç kümesini kullanmanıza izin vermez. Ancak, kullanabileceğiniz alternatif bir yaklaşım vardır. Bir örnek için [geçici tablolardaki](sql-data-warehouse-tables-temporary.md)makaleye bakın. 

## <a name="limitations"></a>Sınırlamalar
SQL veri ambarı 'nda uygulanmayan Transact-SQL saklı yordamlarının bazı yönleri vardır.

Bunlar:

* geçici saklı yordamlar
* numaralandırılmış saklı yordamlar
* Genişletilmiş saklı yordamlar
* CLR saklı yordamları
* şifreleme seçeneği
* çoğaltma seçeneği
* tablo değerli parametreler
* salt okuma parametreleri
* Varsayılan parametreler
* Yürütme bağlamları
* Return ekstresi

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).


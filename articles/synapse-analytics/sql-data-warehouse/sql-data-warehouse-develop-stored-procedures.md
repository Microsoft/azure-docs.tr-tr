---
title: Saklı yordamları kullanma
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzları için saklı yordamları uygulayarak çözüm geliştirmeye yönelik ipuçları.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673595"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzları için saklı yordamları kullanma

Bu makalede, saklı yordamları uygulayarak adanmış SQL havuzu çözümlerini geliştirmeye yönelik ipuçları sunulmaktadır.

## <a name="what-to-expect"></a>Beklentiler

Adanmış SQL havuzu SQL Server ' de kullanılan T-SQL özelliklerinin çoğunu destekler. Daha da önemlisi, çözümünüzün performansını en üst düzeye çıkarmak için kullanabileceğiniz genişleme özel özellikleri vardır.

Ayrıca, adanmış SQL havuzunun ölçeğini ve performansını korumanıza yardımcı olmak için davranış farklılıklarına sahip ek özellikler ve işlevler vardır.

## <a name="introducing-stored-procedures"></a>Saklı yordamları tanıtma

Saklı yordamlar, SQL kodunuzu kapsüllemek için harika bir yoldur ve bu, adanmış SQL havuzu verilerinize yakın bir şekilde depolanır. Saklı yordamlar, geliştiricilerin kodu yönetilebilir birimlere kapsülleyerek ve daha fazla kod yeniden kullanılabilirliğini kolaylaştırarak çözümlerini modüler olarak ayarlamasını de yardımcı olur. Her saklı yordam aynı zamanda parametreleri daha da esnek hale getirmek için kabul edebilir.

Adanmış SQL havuzu Basitleştirilmiş ve kolaylaştırılmış bir saklı yordam uygulamasını sağlar. SQL Server kıyasla en büyük fark, saklı yordamın önceden derlenmiş kod olmaması gerektiğidir.

Genel olarak, veri ambarlarında, derleme süresi, büyük veri birimlerine karşı sorgu çalıştırmak için gereken zamana kıyasla küçüktür. Saklı yordam kodunun büyük sorgular için doğru iyileştirildiğinden emin olmak daha önemlidir.

> [!TIP]
> Amaç, milisaniye değil, saat, dakika ve saniyeyi tasarrufu sağlamaktır. Bu nedenle, saklı yordamları SQL Logic Container olarak düşünmek yararlı olur.

Ayrılmış bir SQL havuzu saklı yordamınız yürütüldüğünde, SQL deyimleri ayrıştırılır, çevrilir ve çalışma zamanında iyileştirilir. Bu işlem sırasında her bir ifade dağıtılmış sorgulara dönüştürülür. Verilere karşı yürütülen SQL kodu, gönderilen sorgudan farklı.

## <a name="nesting-stored-procedures"></a>Saklı yordamları iç içe geçirme

Saklı yordamlar diğer saklı yordamları çağırdığında veya dinamik SQL yürütürde, iç saklı yordam veya kod çağırma iç içe geçmiş olarak kabul edilir.

Adanmış SQL havuzu, en fazla sekiz iç içe geçme düzeyini destekler. Buna karşılık, SQL Server iç içe geçme düzeyi 32 ' dir.

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

Adanmış SQL havuzu şu anda [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)öğesini desteklemiyor. Bu nedenle, iç içe düzeyi izlemeniz gerekir. Sekiz iç içe düzey sınırı aşmanız çok düşüktür. Ancak bunu yaparsanız, kodunuzu bu sınırın içinde iç içe geçmiş düzeylerine uyacak şekilde yeniden güncelleştirmeniz gerekir.

## <a name="insertexecute"></a>INSERT..EXECUTE

Adanmış SQL havuzu, bir INSERT ifadesiyle saklı yordamın sonuç kümesini kullanmanıza izin vermez. Bununla birlikte, kullanabileceğiniz alternatif bir yaklaşım vardır. Bir örnek için [geçici tablolardaki](sql-data-warehouse-tables-temporary.md)makaleye bakın.

## <a name="limitations"></a>Sınırlamalar

Özel SQL havuzunda uygulanmayan Transact-SQL saklı yordamlarının bazı yönleri şunlardır:

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
* Return deyimi

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla geliştirme ipucu için bkz. [geliştirmeye genel bakış](sql-data-warehouse-overview-develop.md).

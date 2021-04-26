---
title: Adanmış SQL havuzları için veri yükleme en iyi uygulamaları
description: Azure SYNAPSE Analytics 'te adanmış SQL havuzları kullanarak veri yüklemeye yönelik öneriler ve performans iyileştirmeleri.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 294b2a5188f0dfd8cb29f21bdbb29236b1740231
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565874"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te adanmış SQL havuzları kullanarak veri yüklemeye yönelik en iyi uygulamalar

Bu makalede adanmış SQL havuzu kullanarak verileri yüklemeye yönelik öneriler ve performans iyileştirmeleri öğreneceksiniz.

## <a name="preparing-data-in-azure-storage"></a>Azure Depolama’da verileri hazırlama

Gecikme süresini en aza indirmek için depolama katmanınızı ve adanmış SQL havuzunuzu birlikte bulundurma.

Verileri ORC Dosya Biçimi’ne aktarırken, verilerde büyük metin sütunları varsa Java belleği yetersiz hatası gibi hatalar alabilirsiniz. Bu sınırlama için bir geçici çözüm olarak, sütunların yalnızca bir alt kümesini dışarı aktarın.

Tüm dosya biçimleri farklı performans özelliklerine sahiptir. En hızlı yükleme için, sıkıştırılan sınırlandırılmış metin dosyaları kullanın. UTF-8 ve UTF-16 arasındaki performans farkı azdır.

Büyük sıkıştırılmış dosyaları daha küçük sıkıştırılmış dosyalara bölün.

## <a name="running-loads-with-enough-compute"></a>Yükleri yeterli işlemle çalıştırma

En yüksek yükleme hızı için aynı anda yalnızca bir yük işi çalıştırın. Bu uygun değilse, eşzamanlı olarak en az sayıda yük çalıştırın. Büyük bir yükleme işi bekleliyorsanız, yüklemeden önce adanmış SQL havuzunuzu ölçeklendirmeniz gerekir.

Yükleri uygun işlem kaynaklarıyla çalıştırmak için, yükleri çalıştırmaya ayrılmış yükleme kullanıcıları oluşturun. Her yükleme kullanıcısını belirli bir iş yükü grubuna sınıflandırın. Yük çalıştırmak için, yükleme kullanıcılarından biri olarak oturum açın ve sonra yükü çalıştırın. Yük, kullanıcının iş yükü grubuyla çalışır.  

### <a name="example-of-creating-a-loading-user"></a>Yükleme kullanıcısı oluşturmayla ilgili örnek

Bu örnek, belirli bir iş yükü grubuna sınıflandırılmış bir yükleme kullanıcısı oluşturur. İlk adım, **ana öğeye bağlanmak** ve oturum açma bilgisi oluşturmaktır.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Adanmış SQL havuzuna bağlanın ve bir kullanıcı oluşturun. Aşağıdaki kod, mySampleDataWarehouse adlı veritabanına bağlı olduğunuzu varsayar. Yükleyici adlı bir kullanıcının nasıl oluşturulacağını gösterir ve [Copy ifadesini](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)kullanarak tablo oluşturma ve yükleme izinleri verir. Daha sonra kullanıcıyı en fazla kaynakla birlikte DataLoads iş yükü grubuna sınıflandırır. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Bu, SQL havuzunun %100 kaynaklarını tek bir yüklemeye ayırmanın çok büyük bir örneğidir. Bu, size en fazla 1 eşzamanlılık sağlar. Bunun yalnızca kendi iş yüklerinizde kaynakları balanace için kendi yapılandırmalarına sahip ek iş yükü grupları oluşturmanız gerektiği ilk yükleme için kullanılması gerektiğini unutmayın. 

Yükleme iş yükü grubu için kaynaklarla bir yük çalıştırmak için, yükleyici olarak oturum açın ve yükü çalıştırın.

## <a name="allowing-multiple-users-to-load-polybase"></a>Birden çok kullanıcının yüklenmesine izin verme (PolyBase)

Genellikle birden çok kullanıcının adanmış bir SQL havuzuna veri yüklemesi gerekir. [Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (POLYBASE) olarak Create Table ile yükleme, veritabanının denetim izinlerini gerektirir.  CONTROL izinleri tüm şemalara denetim erişimi verir.

Tüm yükleme kullanıcılarının tüm şemalarda denetim erişimine sahip olmasını istemeyebilirsiniz. İzinleri sınırlandırmak için, DENY CONTROL deyimini kullanabilirsiniz.

Örneğin, A departmanı için schema_A ve B departmanı için schema_B adında veritabanı şemaları olduğunu düşünelim. user_A ve user_B adlı veritabanı kullanıcıları sırayla A ve B departmanları için PolyBase yükleme kullanıcıları olsun. Her ikisine de CONTROL veritabanı izinleri verilmiştir. A ve B şemalarını oluşturanlar DENY kullanarak bu şemaları kilitler:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A ve user_B artık diğer bölüm şemasından kilitlidir.

## <a name="loading-to-a-staging-table"></a>Hazırlama tablosuna yükleme

Verileri ayrılmış bir SQL havuzu tablosuna taşımak için en hızlı yükleme hızına ulaşmak üzere verileri bir hazırlama tablosuna yükleyin.  Hazırlama tablosunu bir yığın olarak tanımlayın ve dağıtım seçeneği için hepsine bir kez yöntemini kullanın.

Yükleme işleminin genellikle ilk olarak bir hazırlama tablosuna yüklediğiniz ve ardından verileri bir üretime adanmış SQL havuzu tablosuna ekleyen iki adımlı bir işlem olduğunu düşünün. Üretim tablosu bir karma dağıtım kullanıyorsa, hazırlama tablosunu karma dağıtımla tanımlamanız durumunda toplam yükleme ve ekleme süresi daha hızlı olabilir.

Hazırlama tablosuna yükleme işlemi daha uzun sürer, ancak satırları üretim tablosuna eklemeyi içeren ikinci adım, dağıtımlar arasında veri hareketi oluşturmaz.

## <a name="loading-to-a-columnstore-index"></a>Bir columnstore dizinine yükleme

Columnstore dizinleri, verileri yüksek kaliteli satır grupları olarak sıkıştırmak için yüksek miktarlarda bellek gerektirir. En iyi sıkıştırma ve dizin verimliliği için, columnstore dizininin her satır grubunda en fazla 1.048.576 satırı sıkıştırması gerekir.

Bellek baskısı olduğunda, columnstore dizini en yüksek sıkıştırma oranlarına ulaşamayabilir. Bu senaryo, sırasıyla sorgu performansını etkiler. Derinlemesine bir bakış için, bkz. [Columnstore bellek iyileştirmeleri](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Yükleme kullanıcısının en yüksek sıkıştırma oranlarına ulaşmak için yeterli belleğe sahip olduğundan emin olmak için, orta veya büyük bir kaynak sınıfının üyesi olan yükleme kullanıcılarını kullanın.
- Yeni satır gruplarını tamamen doldurmak için yeterli satır yükleyin. Bir toplu yükleme sırasında her 1.048.576 satır, tam bir satır grubu olarak doğrudan columnstore’da sıkıştırılır. 102.400’den daha az satır içeren yükler, satırları bir b ağacı dizininde tutulduğu deltastore’a gönderir.

> [!NOTE]
> Çok az sayıda satır yüklerseniz, bu, tüm deltasme 'ye yol açabilir ve doğrudan columnstore biçiminde sıkıştırılmaz.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>SqLBulkCopy API veya bcp kullanırken toplu iş boyutunu artır

COPY ifadesiyle yükleme, adanmış SQL havuzlarıyla en yüksek performansı sağlar. Yüklemek için KOPYAYı kullanamaz ve [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) veya [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)kullanması gerekiyorsa, daha iyi aktarım hızı için toplu iş boyutunu artırmayı göz önünde bulundurmanız gerekir.

> [!TIP]
> 100 K ila 1M satır arasında bir toplu iş boyutu en iyi toplu iş boyutu kapasitesini belirlemek için önerilen temeldir.

## <a name="handling-loading-failures"></a>Yükleme hatalarını işleme

Bir dış tablo kullanan bir yük *"Sorgu iptal edildi-- dış bir kaynaktan okunurken en yüksek reddedilme sayısına ulaşıldı"* hatasıyla başarısız olabilir. Bu ileti, dış verilerinizin kirli kayıtlar içerdiğini gösterir.

Bir veri kaydı, aşağıdaki koşullardan birini karşılıyorsa kirli olarak kabul edilir:

- Veri türleri ve sütun sayısı dış tablonun sütun tanımlarına uymuyor.
- Veriler, belirtilen dış dosya biçimine uymuyor.

Kirli kayıtları düzeltmek için dış tablo ve dış dosya biçimlerinizin doğru olduğundan ve dış verilerinizin bu tanımlara uyduğundan emin olun.

Dış veri kayıtlarının bir alt kümesi kirli ise, [dış tablo oluşturma (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)içindeki reddetme seçeneklerini kullanarak sorgularınız için bu kayıtları reddetmeyi seçebilirsiniz.

## <a name="inserting-data-into-a-production-table"></a>Üretim tablosuna veri ekleme

Küçük bir tabloya bir [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) deyimiyle tek seferlik yükleme yapmak veya `INSERT INTO MyLookup VALUES (1, 'Type 1')` gibi bir deyimle bir aramanın düzenli aralıklarla yeniden yüklenmesi yeterlidir.  Ancak, tekli ton eklemeleri toplu yükleme gerçekleştirmek kadar verimli değildir.

Gün boyunca binlerce ekleme yapmanız gerekiyorsa, eklemeleri toplu olarak yüklemek için toplu iş haline getirin.  Bir dosyaya tekli eklemeleri eklemek için işlemlerinizi geliştirin ve ardından dosyayı düzenli olarak yükleyen başka bir işlem oluşturun.

## <a name="creating-statistics-after-the-load"></a>Yüklemeden sonra istatistik oluşturma

Sorgu performansını geliştirmek için ilk yüklemeden veya verilerdeki önemli değişikliklerden sonra istatistiklerin tüm sütunlarda oluşturulması önemlidir. İstatistiklerin oluşturulması el ile yapılabilir veya [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)etkinleştirebilirsiniz.

İstatistiklerin ayrıntılı bir açıklaması için bkz. [İstatistikler](sql-data-warehouse-tables-statistics.md). Aşağıdaki örnek, Customer_Speed tablonun beş sütununda nasıl el ile istatistik oluşturulacağını gösterir.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Depolama anahtarlarını Döndür (PolyBase)

Blob depolamanızın erişim anahtarlarını düzenli olarak değiştirmek iyi bir güvenlik uygulamasıdır. Blob depolama hesabınız için anahtarları geçirmenizi sağlayan iki depolama anahtarınız bulunur.

Azure Depolama hesabı anahtarlarını döndürmek için:

Anahtarı değişen her depolama hesabı için, [VERİTABANI KAPSAMLI KİMLİK BİLGİSİNİ DEĞİŞTİR](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) yazın.

Örnek:

Özgün anahtar oluşturuldu

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

1. anahtardan 2. anahtara geçin

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Temel dış veri kaynaklarında başka bir değişiklik yapılması gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

- Ayıklama, yükleme ve dönüştürme (ELT) işlemi tasarlarken COPY veya PolyBase hakkında daha fazla bilgi edinmek için bkz. [Azure SYNAPSE Analytics Için TASARıM ELT](design-elt-data-loading.md).
- Yükleme öğreticisi için, [Azure Blob depolama 'Dan SYNAPSE SQL 'e veri yüklemek üzere Copy Ifadesini kullanın](./load-data-from-azure-blob-storage-using-copy.md).
- Veri yüklerini izlemek için bkz. [DMV’leri kullanarak iş yükünüzü izleme](sql-data-warehouse-manage-monitor.md).
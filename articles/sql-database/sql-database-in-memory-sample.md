---
title: Bellek içi örnek
description: OLTP ve columnstore örneği ile Azure SQL veritabanı 'nı bellek Içi teknolojilerle deneyin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: e7e7fc44d5f8b46a66c698d3a33ceeab5b8625c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810337"
---
# <a name="in-memory-sample"></a>Bellek içi örnek

Azure SQL veritabanı 'nda bellek içi teknolojiler uygulamanızın performansını iyileştirebilmeniz ve veritabanınızın maliyetini büyük olasılıkla azaltmanızı sağlar. Azure SQL veritabanı 'nda bellek Içi teknolojileri kullanarak, çeşitli iş yükleriyle performans iyileştirmeleri elde edebilirsiniz.

Bu makalede, Azure SQL veritabanı 'nda Ayrıca, bellek Içi OLTP ve columnstore dizinlerinin kullanımını gösteren iki örnek görürsünüz.

Daha fazla bilgi için bkz.
- [Bellek ıçı OLTP genel bakış ve kullanım senaryoları](https://msdn.microsoft.com/library/mt774593.aspx) (müşteri örnek olay incelemeleri ve kullanmaya başlamak için bilgiler içerir)
- [Bellek Içi OLTP için belgeler](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore dizinleri Kılavuzu](https://msdn.microsoft.com/library/gg492088.aspx)
- [Gerçek zamanlı işlemsel analiz](https://msdn.microsoft.com/library/dn817827.aspx) olarak da bilinen karma işlem/analitik Işleme (htap)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. bellek Içi OLTP örneğini yüklemeyi

AdventureWorksLT örnek veritabanını [Azure Portal](https://portal.azure.com/)birkaç tıklamayla oluşturabilirsiniz. Bu bölümdeki adımlarda, AdventureWorksLT veritabanınızı bellek Içi OLTP nesneleriyle nasıl zenginleştirebilirsiniz ve performans avantajları gösterilmektedir.

Daha fazla uyarlaması için, ancak bellek Içi OLTP için daha görsel açıdan çarpıcı performans tanıtımı için bkz.:

- Yayın: [bellek içi-OLTP-demo-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Kaynak kodu: [bellek içi-OLTP-demo-kaynak kodu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Yükleme adımları

1. [Azure Portal](https://portal.azure.com/)sunucuda bir Premium veya iş açısından kritik veritabanı oluşturun. **Kaynağı** AdventureWorksLT örnek veritabanına ayarlayın. Ayrıntılı yönergeler için bkz. [Ilk Azure SQL veritabanınızı oluşturma](sql-database-single-database-get-started.md).

2. Veritabanına SQL Server Management Studio [(SSMS. exe)](https://msdn.microsoft.com/library/mt238290.aspx)ile bağlanın.

3. [Bellek ıçı OLTP Transact-SQL betiğini](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) panonuza kopyalayın. T-SQL betiği, 1. adımda oluşturduğunuz AdventureWorksLT örnek veritabanında gerekli bellek Içi nesneleri oluşturur.

4. T-SQL betiğini SSMS 'ye yapıştırın ve betiği yürütün. `MEMORY_OPTIMIZED = ON` yan tümcesi CREATE TABLE deyimleri önemli. Örneğin:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Hata 40536


T-SQL betiğini çalıştırdığınızda 40536 hatası alırsanız, veritabanının bellek Içi destekleyip desteklemediğini doğrulamak için aşağıdaki T-SQL betiğini çalıştırın:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


**0** sonucu, bellekteki desteklenmez ve **1** ' in desteklendiği anlamına gelir. Sorunu tanılamak için veritabanının Premium hizmet katmanında olduğundan emin olun.


#### <a name="about-the-created-memory-optimized-items"></a>Oluşturulan bellek için iyileştirilmiş öğeler hakkında

**Tablolar**: örnek, bellek için iyileştirilmiş aşağıdaki tabloları içerir:

- SalesLT. Product_inmem
- SalesLT. SalesOrderHeader_inmem
- SalesLT. SalesOrderDetail_inmem
- Demo. DemoSalesOrderHeaderSeed
- Demo. DemoSalesOrderDetailSeed


Bellek için iyileştirilmiş tabloları SSMS içindeki **Nesne Gezgini** aracılığıyla inceleyebilirsiniz. **Tablo** ** > filtre** > **filtre ayarlarını** > **bellek için iyileştirilmiş**' a sağ tıklayın. Değer 1 ' e eşittir.


Ya da katalog görünümlerini sorgulayabilirsiniz, örneğin:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Yerel koda derlenmiş saklı yordam**: bir Katalog görünümü sorgusu aracılığıyla saleslt. usp_InsertSalesOrder_inmem inceleyebilirsiniz:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Örnek OLTP iş yükünü çalıştırın

Aşağıdaki iki *saklı yordam* arasındaki tek fark, ilk yordamın tabloları en iyi duruma getirilmiş sürümlerini kullandığından, ikinci yordam ise normal disk üzerinde tablo kullanır:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


Bu bölümde, yararlı **ostres. exe** yardımcı programını kullanarak bu iki saklı yordamı stressellevels üzerinde nasıl yürütekullanacağınızı görürsünüz. İki stres çalıştırmanın tamamlanmasının ne kadar sürdüğünü karşılaştırabilirsiniz.


Ostres. exe dosyasını çalıştırdığınızda, aşağıdakilerin her ikisi için tasarlanan parametre değerlerini geçirmeniz önerilir:

- -N100 kullanarak çok sayıda eşzamanlı bağlantı çalıştırın.
- Her bağlantı döngüsünü-R500 kullanarak yüzlerce kez yapın.


Ancak, her şeyin çalıştığından emin olmak için-N10 ve-R50 gibi çok daha küçük değerler ile başlamak isteyebilirsiniz.


### <a name="script-for-ostressexe"></a>Ostres. exe betiği


Bu bölümde, ostres. exe komut satırımızda gömülü olan T-SQL betiği görüntülenir. Betik, daha önce yüklediğiniz T-SQL betiği tarafından oluşturulan öğeleri kullanır.


Aşağıdaki betik, aşağıdaki bellek için iyileştirilmiş *tablolara*beş satır içeren bir örnek satış siparişi ekler:

- SalesLT. SalesOrderHeader_inmem
- SalesLT. SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Önceki T-SQL betiğinin, ostres. exe için *_ondisk* sürümünü yapmak için, *_inmem* alt dizenin her iki tekrarı da *_ondisk*ile değiştirmelisiniz. Bu değişiklikler, tabloların ve saklı yordamların adlarını etkiler.


### <a name="install-rml-utilities-and-ostress"></a>RML yardımcı programlarını ve `ostress` yükleyip


İdeal olarak, ostres. exe ' yi bir Azure sanal makinesinde (VM) çalıştırmayı planlarsınız. AdventureWorksLT veritabanınızın bulunduğu aynı Azure coğrafi bölgesinde bir [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/) oluşturursunuz. Ancak bunun yerine, dizüstü bilgisayarınızda ostres. exe dosyasını çalıştırabilirsiniz.


VM 'de veya seçtiğiniz herhangi bir konakta, yeniden yürütme biçimlendirme dili (RML) yardımcı programlarını yükleyebilirsiniz. Yardımcı programlar ostres. exe ' yi içerir.

Daha fazla bilgi için bkz.
- [Bellek ıçı OLTP Için örnek veritabanında](https://msdn.microsoft.com/library/mt465764.aspx)ostres. exe tartışması.
- [Bellek ıçı OLTP Için örnek veritabanı](https://msdn.microsoft.com/library/mt465764.aspx).
- [Ostres. exe ' yi yüklemeye yönelik blog](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Önce *_inmem* stres iş yükünü çalıştırın


Ostres. exe komut satırımuzu çalıştırmak için bir *RML komut istemi* penceresi kullanabilirsiniz. Komut satırı parametreleri doğrudan `ostress`:

- 100 bağlantıyı eşzamanlı olarak çalıştırın (-N100).
- Her bağlantının T-SQL betiğini 50 kez (-R50) çalıştırmasını sağlayabilirsiniz.


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Önceki ostres. exe komut satırını çalıştırmak için:


1. Tüm önceki çalıştırmalar tarafından eklenen tüm verileri silmek için SSMS 'de aşağıdaki komutu çalıştırarak veritabanı veri içeriğini sıfırlayın:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Önceki ostres. exe komut satırının metnini panonuza kopyalayın.

3. -S-U-P-d parametrelerinin `<placeholders>` doğru gerçek değerlerle değiştirin.

4. Düzenlenen Komut satırlarınızı bir RML cmd penceresinde çalıştırın.


#### <a name="result-is-a-duration"></a>Sonuç bir süre


`ostress.exe` tamamlandığında, RML cmd penceresinde çalışma süresini son çıkış satırı olarak yazar. Örneğin, daha kısa bir test çalışması yaklaşık 1,5 dakika boyunca çalışır:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>*_Ondisk*için sıfırlayın, düzenleyin ve yeniden çalıştırın


*_İnmem* çalıştırıldıktan sonra, *_ondisk* çalıştırmak için aşağıdaki adımları gerçekleştirin:


1. Önceki çalıştırma tarafından eklenen tüm verileri silmek için SSMS 'de aşağıdaki komutu çalıştırarak veritabanını sıfırlayın:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Tüm *_inmem* *_ondisk*olacak şekilde değiştirmek için ostres. exe komut satırını düzenleyin.

3. Ostres. exe dosyasını ikinci kez yeniden çalıştırın ve süre sonucunu yakalayın.

4. Daha sonra, veritabanını sıfırlayın (büyük miktarda test verisi olabilecek bir şeyi silmek için).


#### <a name="expected-comparison-results"></a>Beklenen karşılaştırma sonuçları

Bellek Içi testleriniz, bu uyarlaması iş yükü için, veritabanı ile aynı Azure bölgesindeki bir Azure VM 'de çalışan `ostress` ile bu performansı **dokuz kez** artmış olduğunu göstermiştir.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. bellek Içi analiz örneğini yükler


Bu bölümde, bir columnstore dizini kullanırken geleneksel b-ağaç dizinine karşı GÇ ve istatistik sonuçlarını karşılaştırırsınız.


OLTP iş yükünde gerçek zamanlı analizler için, kümelenmemiş bir columnstore dizini kullanmak genellikle en iyisidir. Ayrıntılar için bkz. [açıklanan columnstore dizinleri](https://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Columnstore Analytics testini hazırlama


1. Örnekten yeni bir AdventureWorksLT veritabanı oluşturmak için Azure portal kullanın.
   - Bu tam adı kullanın.
   - Herhangi bir Premium hizmet katmanını seçin.

2. [Sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) panonuza kopyalayın.
   - T-SQL betiği, 1. adımda oluşturduğunuz AdventureWorksLT örnek veritabanında gerekli bellek Içi nesneleri oluşturur.
   - Betik, boyut tablosu ve iki olgu tablosu oluşturur. Olgu tabloları her biri 3.500.000 satır ile doldurulur.
   - Betiğin tamamlanması 15 dakika sürebilir.

3. T-SQL betiğini SSMS 'ye yapıştırın ve betiği yürütün. **Create INDEX** deyimindeki **columnstore** anahtar sözcüğü, içinde olduğu gibi önemlidir:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. AdventureWorksLT 'yi uyumluluk düzeyi 130 olarak ayarlayın:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Düzey 130, bellek Içi özelliklerle doğrudan ilgili değildir. Ancak düzey 130, genellikle 120 ' den daha hızlı sorgu performansı sağlar.


#### <a name="key-tables-and-columnstore-indexes"></a>Anahtar tablolar ve columnstore dizinleri


- dbo. FactResellerSalesXL_CCI, *veri* düzeyinde gelişmiş sıkıştırmaya sahip olan, kümelenmiş bir columnstore dizini olan bir tablodur.

- dbo. FactResellerSalesXL_PageCompressed, yalnızca *sayfa* düzeyinde sıkıştırılan, benzer bir normal kümelenmiş dizini olan bir tablodur.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Columnstore dizinini karşılaştırmak için anahtar sorguları


Performans geliştirmelerini görmek için [çalıştırabileceğiniz birkaç T-SQL sorgu türü](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) vardır. T-SQL betikteki adım 2 ' de, bu sorgu çiftine dikkat edin. Bunlar yalnızca bir satıra göre farklılık gösterir:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Kümelenmiş bir columnstore dizini FactResellerSalesXL\_CCı tablosudur.

Aşağıdaki T-SQL komut dosyası alıntısı, her tablo sorgusunun GÇ ve zaman istatistiklerini yazdırır.


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

P2 fiyatlandırma katmanının bulunduğu bir veritabanında, geleneksel dizin ile karşılaştırıldığında kümelenmiş columnstore dizinini kullanarak bu sorgu için yaklaşık dokuz kat performans artışı elde edebilirsiniz. P15 ile, columnstore dizinini kullanarak performans kazancını yaklaşık 57 kez bekleyebilir.



## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı Başlangıç 1: daha hızlı T-SQL performansı için bellek Içi OLTP teknolojileri](https://msdn.microsoft.com/library/mt694156.aspx)

- [Mevcut bir Azure SQL uygulamasında bellek Içi OLTP kullanın](sql-database-in-memory-oltp-migration.md)

- Bellek içi OLTP için [bellek ıçı OLTP depolama alanını izleme](sql-database-in-memory-oltp-monitoring.md)


## <a name="additional-resources"></a>Ek kaynaklar

#### <a name="deeper-information"></a>Daha derin bilgi

- [SQL veritabanı 'nda bellek Içi OLTP ile DTU 'yu %70 oranında düşürürken, çekirdeğin anahtar veritabanı iş yükünü nasıl çift katına kullandığını öğrenin](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Azure SQL veritabanı blog gönderisine bellek içi OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Bellek Içi OLTP hakkında bilgi edinin](https://msdn.microsoft.com/library/dn133186.aspx)

- [Columnstore dizinleri hakkında bilgi edinin](https://msdn.microsoft.com/library/gg492088.aspx)

- [Gerçek zamanlı operasyonel çözümlemeler hakkında bilgi edinin](https://msdn.microsoft.com/library/dn817827.aspx)

- Bkz. [ortak Iş yükü desenleri ve geçiş hususları](https://msdn.microsoft.com/library/dn673538.aspx) (bellek içi OLTP yaygın olarak önemli performans kazancı sağlayan iş yükü düzenlerini açıklar)

#### <a name="application-design"></a>Uygulama tasarımı

- [Bellek içi OLTP (bellek Içi Iyileştirme)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Mevcut bir Azure SQL uygulamasında bellek Içi OLTP kullanın](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Araçlar

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Veri Araçları (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)

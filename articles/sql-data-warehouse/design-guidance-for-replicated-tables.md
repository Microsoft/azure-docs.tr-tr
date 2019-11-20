---
title: Çoğaltılan tablolar için tasarım kılavuzu
description: Azure SQL veri ambarı şemanızda çoğaltılan tabloları tasarlamaya yönelik öneriler. 
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 18577cb729c9f17a112979cd1ebb763af38b9ca2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693052"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda çoğaltılan tabloları kullanmaya yönelik tasarım kılavuzu
Bu makale, SQL veri ambarı şemanızda çoğaltılan tabloları tasarlamaya yönelik öneriler sağlar. Veri hareketini ve sorgu karmaşıklığını azaltarak sorgu performansını artırmak için bu önerileri kullanın.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Ön koşullar
Bu makalede, SQL veri ambarı 'nda veri dağıtımı ve veri taşıma kavramlarıyla ilgili bilgi sahibi olduğunuz varsayılır.  Daha fazla bilgi için bkz. [mimari](massively-parallel-processing-mpp-architecture.md) makalesi. 

Tablo tasarımının bir parçası olarak, verileriniz ve verilerin nasıl sorgulandığı hakkında mümkün olduğunca fazla bilgi edinin.  Örneğin, bu soruları göz önünde bulundurun:

- Tablo ne kadar büyük?   
- Tablo ne sıklıkta yenilenir?   
- Bir veri ambarında olgu ve boyut tabloları var mı?   

## <a name="what-is-a-replicated-table"></a>Çoğaltılan tablo nedir?
Çoğaltılan bir tablo, her Işlem düğümü üzerinde erişilebilir olan tablonun tam kopyasına sahiptir. Bir tablonun çoğaltılması, bir JOIN veya toplanmadan önce Işlem düğümleri arasında veri aktarımı gereksinimini ortadan kaldırır. Tabloda birden fazla kopya olduğundan, çoğaltılan tablolar tablo boyutu 2 GB 'tan az sıkıştırıldığında en iyi şekilde çalışır.  2 GB sabit bir sınır değildir.  Veriler statikse ve değişmezse, daha büyük tabloları çoğaltabilirsiniz.

Aşağıdaki diyagramda, her bir Işlem düğümündeki erişilebilir bir çoğaltılan tablo gösterilmektedir. SQL veri ambarı 'nda, çoğaltılan tablo her Işlem düğümündeki bir dağıtım veritabanına tamamen kopyalanır. 

![Çoğaltılan tablo](media/guidance-for-using-replicated-tables/replicated-table.png "Çoğaltılan tablo")  

Çoğaltılan tablolar, bir yıldız şemasında boyut tabloları için iyi çalışır. Boyut tabloları genellikle Boyut tablosundan farklı şekilde dağıtılan olgu tablolarına birleştirilir.  Boyutlar genellikle birden çok kopyanın depolanmasını ve korunmasını olanaklı kılan bir boyutlardır. Boyutlar, müşteri adı ve adresi ve ürün ayrıntıları gibi yavaş değişen açıklayıcı verileri depolar. Verilerin yavaş değişen doğası, çoğaltılan tablonun daha az bakımda olmasına yol açar. 

Şu durumlarda çoğaltılan bir tablo kullanmayı göz önünde bulundurun:

- Disk üzerindeki tablo boyutu, satır sayısından bağımsız olarak 2 GB 'tan az. Bir tablonun boyutunu bulmak için [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) komutunu kullanabilirsiniz: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- Tablo, aksi takdirde veri hareketine gerek duyduğu birleşimlerde kullanılır. Karma olarak dağıtılan tablo gibi aynı sütunda dağıtılmamış tablolar, hepsini bir kez deneme tablosuna katılırken, sorguyu tamamlaması için veri taşıma gerekir.  Tablolardan biri küçükse, çoğaltılan bir tabloyu düşünün. Çoğu durumda, hepsini bir kez deneme tablosu yerine çoğaltılan tabloları kullanmanızı öneririz. Sorgu planlarında veri taşıma işlemlerini görüntülemek için [sys. DM _pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)kullanın.  Yayınmoveişlem, çoğaltılan bir tablo kullanılarak ortadan kaldırılabilir olan tipik veri taşıma işlemidir.  
 
Çoğaltılan tablolar şu durumlarda en iyi sorgu performansını vermeyebilir:

- Tabloda, sık kullanılan ekleme, güncelleştirme ve silme işlemleri bulunur. Bu veri işleme dili (DML) işlemleri, çoğaltılan tablonun yeniden derlenmesini gerektirir. Sık sık yeniden oluşturmak daha yavaş performansa neden olabilir.
- Veri ambarı sıklıkla ölçeklenir. Bir veri ambarının ölçeklendirilmesi, çoğaltılan tabloyu yeniden inşa eden Işlem düğümlerinin sayısını değiştirir.
- Tabloda çok sayıda sütun bulunur, ancak veri işlemleri genellikle yalnızca az sayıda sütuna erişir. Bu senaryoda, tüm tabloyu çoğaltmak yerine tabloyu dağıtmak daha etkili olabilir ve ardından sık erişilen sütunlarda bir dizin oluşturabilirsiniz. Bir sorgu veri hareketi gerektirdiğinde, SQL veri ambarı yalnızca istenen sütunlar için verileri taşılar. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Basit sorgu koşullarına sahip çoğaltılan tabloları kullanma
Bir tabloyu dağıtmayı veya çoğaltmayı seçmeden önce, tabloya karşı çalıştırmayı planladığınız sorgu türlerini düşünün. Mümkün olduğunda,

- Eşitlik veya eşitsizlik gibi basit sorgu koşullarına sahip sorgular için çoğaltılan tabloları kullanın.
- Gibi karmaşık sorgu koşullarına sahip sorgular için dağıtılmış tabloları kullanın.

CPU yoğun sorgular, iş tüm Işlem düğümlerine dağıtıldığında en iyi şekilde gerçekleştirilir. Örneğin, bir tablonun her satırında hesaplamalar çalıştıran sorgular, çoğaltılan tablolardaki dağıtılmış tablolar üzerinde daha iyi gerçekleştirilir. Çoğaltılan bir tablo her bir Işlem düğümünde tam olarak depolandığından, çoğaltılan bir tabloya karşı CPU yoğunluklu bir sorgu, her Işlem düğümündeki tüm tabloya karşı çalışır. Ek hesaplama sorgu performansını yavaşlatabilir.

Örneğin, bu sorgunun karmaşık bir koşulu vardır.  Veriler, çoğaltılan tablo yerine dağıtılmış bir tabloda olduğunda daha hızlı çalışır. Bu örnekte, verilerin hepsini bir kez deneme dağıtımı yapılabilir.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Mevcut hepsini bir kez deneme tablolarını çoğaltılan tablolara Dönüştür
Daha önce hepsini bir kez deneme tablonuz varsa, bu makalede özetlenen kriterleri karşılıyorsa onları çoğaltılan tablolara dönüştürmeyi öneririz. Çoğaltılan tablolar, veri taşıma gereksinimini ortadan kaldırdıklarından, hepsini bir kez deneme tablolarında performansı geliştirir.  Hepsini bir kez deneme tablosu her zaman birleşimler için veri hareketini gerektirir. 

Bu örnek, DimSalesTerritory tablosunu çoğaltılan bir tabloyla değiştirmek için [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) kullanır. Bu örnek, DimSalesTerritory 'in karma dağıtılan veya hepsini bir kez deneme olmasına bakılmaksızın işe yarar.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Hepsini bir kez deneme için sorgu performansı örneği yinelenmiş 

Çoğaltılan bir tablo, tüm tablo bir Işlem düğümünde zaten mevcut olduğundan, birleştirmeler için herhangi bir veri taşıması gerektirmez. Boyut tabloları hepsini bir kez daha kez dağıtılmışsa, bir JOIN boyut tablosunu her bir Işlem düğümüne tam olarak kopyalar. Verileri taşımak için, sorgu planı, Yayınmoveoperation adlı bir işlem içerir. Bu tür veri taşıma işlemi sorgu performansını yavaşlatır ve çoğaltılan tablolar kullanılarak ortadan kalkar. Sorgu planı adımlarını görüntülemek için [sys. DM _pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) sistem katalog görünümünü kullanın. 

Örneğin, AdventureWorks şemasına karşı aşağıdaki sorguda `FactInternetSales` tablosu karma olarak dağıtılır. `DimDate` ve `DimSalesTerritory` tabloları daha küçük boyut tablolarıdır. Bu sorgu, 2004 mali yılı için Kuzey Amerika toplam satışları döndürür:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
`DimDate` ve `DimSalesTerritory` hepsini bir kez deneme tablosu olarak yeniden oluşturdunuz. Sonuç olarak, sorgu birden çok yayın taşıma işlemine sahip olan aşağıdaki sorgu planını gösterdi: 
 
![Hepsini bir kez deneme sorgu planı](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

`DimDate` ve `DimSalesTerritory` çoğaltılan tablolar olarak yeniden oluşturdunuz ve sorguyu yeniden çalıştırdık. Elde edilen sorgu planı çok daha kısadır ve herhangi bir yayın hareketi yoktur.

![Çoğaltılan sorgu planı](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Çoğaltılan tabloları değiştirmeye yönelik performans konuları
SQL veri ambarı, tablonun ana sürümünü tutarak çoğaltılan bir tablo uygular. Her Işlem düğümündeki ana sürümü bir dağıtım veritabanına kopyalar. Bir değişiklik olduğunda, SQL veri ambarı ilk olarak ana tabloyu güncelleştirir. Ardından, her Işlem düğümündeki tabloları yeniden oluşturur. Çoğaltılan bir tablonun yeniden oluşturulması, tablonun her bir Işlem düğümüne kopyalanmasını ve sonra dizinlerin oluşturulmasını içerir.  Örneğin, DW400 üzerindeki çoğaltılan bir tablo, verilerin 5 kopyasını içerir.  Her Işlem düğümündeki bir ana kopya ve tam kopya.  Tüm veriler dağıtım veritabanlarında depolanır. SQL veri ambarı daha hızlı veri değiştirme deyimlerini ve esnek ölçeklendirme işlemlerini desteklemek için bu modeli kullanır. 

Sonrasında yeniden, gereklidir:
- Veriler yüklendi veya değiştirildi
- Veri ambarı farklı bir düzeye ölçeklendirilir
- Tablo tanımı güncelleştirildi

Yeniden oluşturulduktan sonra gerekli değildir:
- İşlemi Duraklat
- İşlemi sürdürür

Yeniden oluşturma, veriler değiştirildikten hemen sonra gerçekleşmez. Bunun yerine, bir sorgu tablodan ilk kez seçerken yeniden oluşturma tetiklenir.  Yeniden oluşturma işlemini tetikleyen sorgu, verilerin zaman uyumsuz olarak her bir Işlem düğümüne kopyalandığı sırada tablonun ana sürümünden hemen okur. Veri kopyalama işlemi tamamlanana kadar, sonraki sorgular tablonun ana sürümünü kullanmaya devam edecektir.  Başka bir yeniden derlemeyi zorlayan çoğaltılmış tabloya karşı herhangi bir etkinlik olursa, veri kopyalama geçersiz kılınır ve sonraki select deyimleri verilerin yeniden kopyalanmasını tetikler. 

### <a name="use-indexes-conservatively"></a>Dizinleri koruma kullanma
Standart dizin oluşturma uygulamaları çoğaltılan tablolar için geçerlidir. SQL veri ambarı, her çoğaltılan tablo dizinini yeniden oluşturma 'nın bir parçası olarak yeniden oluşturur. Yalnızca performans kazancı, dizinleri yeniden oluşturma maliyetini aşarsa dizinleri kullanın.  
 
### <a name="batch-data-loads"></a>Toplu veri yükleri
Çoğaltılan tablolara veri yüklerken, yükleri bir araya getirerek yeniden oluşturmayı en aza indirmenize çalışın. Select deyimlerini çalıştırmadan önce tüm toplu yüklemeleri gerçekleştirin.

Örneğin, bu yük deseninin verileri dört kaynaktan yüklenir ve dört yeniden yüklemeyi çağırır. 

- Kaynak 1 ' den yükleme.
- Select deyimleri yeniden oluşturma Tetikleyicileri 1.
- Kaynak 2 ' den yükleme.
- Select bildiri Tetikleyicileri yeniden oluştur 2.
- Kaynak 3 ' ten yükleyin.
- Select deyimin tetikleyicisi yeniden oluştur 3.
- Kaynak 4 ' ten yükleyin.
- Select deyimleri yeniden derleme Tetikleyicileri 4.

Örneğin, bu yük deseninin verileri dört kaynaktan yüklenir, ancak yalnızca bir yeniden derlemeyi çağırır.

- Kaynak 1 ' den yükleme.
- Kaynak 2 ' den yükleme.
- Kaynak 3 ' ten yükleyin.
- Kaynak 4 ' ten yükleyin.
- SELECT ifadesinin yeniden derlendiğini seçin.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Bir toplu yükleme sonrasında çoğaltılan bir tabloyu yeniden oluşturma
Tutarlı sorgu yürütme süreleri sağlamak için, bir toplu yükleme sonrasında çoğaltılan tabloların derlemesini zorunlu tutmayı düşünün. Aksi halde, ilk sorgu sorguyu tamamlayacak veri hareketini kullanmaya devam eder. 

Bu sorgu, değiştirilmiş ancak yeniden derlenmemişse çoğaltılan tabloları listelemek için [sys. PDW _replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV öğesini kullanır.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Yeniden derlemeyi tetiklemek için, önceki çıktıda bulunan her tabloda aşağıdaki ifadeyi çalıştırın. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Sonraki adımlar 
Çoğaltılan bir tablo oluşturmak için aşağıdaki deyimlerden birini kullanın:

- [CREATE TABLE (Azure SQL veri ambarı)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [SELECT olarak CREATE TABLE (Azure SQL veri ambarı)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Dağıtılmış tablolara genel bakış için bkz. [Dağıtılmış tablolar](sql-data-warehouse-tables-distribute.md).




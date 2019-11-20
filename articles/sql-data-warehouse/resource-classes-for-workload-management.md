---
title: İş yükü yönetimi için kaynak sınıfları
description: Azure SQL veri ambarı 'ndaki sorgulara yönelik eşzamanlılık ve işlem kaynaklarını yönetmek için kaynak sınıflarını kullanma kılavuzu.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/04/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 558a6e3faa207e15000657a17bec99a7b1ac99e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685932"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Azure SQL veri ambarı 'nda kaynak sınıflarıyla iş yükü yönetimi

Azure SQL veri ambarınızdaki sorgulara yönelik belleği ve eşzamanlılık yönetimini yönetmek için kaynak sınıflarını kullanma kılavuzu.  

## <a name="what-are-resource-classes"></a>Kaynak sınıfları nedir?

Bir sorgunun performans kapasitesi kullanıcının kaynak sınıfına göre belirlenir.  Kaynak sınıfları, Azure SQL veri ambarı 'nda, işlem kaynaklarını yöneten ve sorgu yürütme için eşzamanlılık olan önceden belirlenen kaynak sınırlamalarıdır. Kaynak sınıfları, eşzamanlı olarak çalışan sorgu sayısı ve her sorguya atanan işlem kaynakları için sınırlar ayarlayarak iş yükünüzü yönetmenize yardımcı olabilir.  Bellek ve eşzamanlılık arasında bir denge vardır.

- Daha küçük kaynak sınıfları sorgu başına en fazla bellek miktarını azaltır, ancak eşzamanlılık düzeyini artırır.
- Daha büyük kaynak sınıfları sorgu başına maksimum belleği artırır, ancak eşzamanlılık düzeyini azaltır.

İki tür kaynak sınıfı vardır:

- Sabit bir veri kümesi boyutu üzerinde artan eşzamanlılık için uygun olan statik kaynak sınıfları.
- Boyut olarak büyüyen ve hizmet düzeyi ölçeği arttığı için performansın artması gereken veri kümelerine uygun olan dinamik kaynak sınıfları.

Kaynak sınıfları, kaynak tüketimini ölçmek için eşzamanlılık yuvaları kullanır.  [Eşzamanlılık yuvaları](#concurrency-slots) , bu makalenin ilerleyen kısımlarında açıklanmıştır.

- Kaynak sınıflarının kaynak kullanımını görüntülemek için bkz. [bellek ve eşzamanlılık sınırları] bellek-eşzamanlılık-limits.md).
- Kaynak sınıfını ayarlamak için sorguyu farklı bir kullanıcı altında çalıştırabilir veya [geçerli kullanıcının kaynak sınıfı üyeliğini değiştirebilirsiniz](#change-a-users-resource-class) .

### <a name="static-resource-classes"></a>Statik kaynak sınıfları

Statik kaynak sınıfları, [veri ambarı birimlerinde](what-is-a-data-warehouse-unit-dwu-cdwu.md)ölçülen geçerli performans düzeyinden bağımsız olarak aynı miktarda bellek ayırır. Sorgular performans düzeyinden bağımsız olarak aynı bellek ayırmasını kullandığından, [veri ambarının ölçeklendirilmesi](quickstart-scale-compute-portal.md) , bir kaynak sınıfı içinde daha fazla sorgunun çalışmasına izin verir.  Statik kaynak sınıfları, veri hacmi biliniyorsa ve sabit ise idealdir.

Statik kaynak sınıfları, önceden tanımlanmış bu veritabanı rolleriyle uygulanır:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- kullanıcıyı staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dinamik kaynak sınıfları

Dinamik kaynak sınıfları, geçerli hizmet düzeyine bağlı olarak değişken miktarda bellek ayırır. Statik kaynak sınıfları daha yüksek eşzamanlılık ve statik veri birimleri için faydalıdır, ancak dinamik kaynak sınıfları, büyüyen veya değişken veri miktarı için daha uygundur.  Daha büyük bir hizmet düzeyine kadar ölçeklendirirseniz, sorgularınız otomatik olarak daha fazla bellek alır.  

Dinamik kaynak sınıfları, önceden tanımlanmış bu veritabanı rolleriyle uygulanır:

- smallrc
- düz RC
- largerc
- xlargerc

Her kaynak sınıfı için bellek ayırma, **hizmet düzeyinden bağımsız**olarak aşağıdaki gibidir.  En düşük eşzamanlılık sorguları da listelenir.  Bazı hizmet düzeylerinde, en düşük eşzamanlılık elde edilebilir.

| Kaynak Sınıfı | Bellek yüzdesi | En az eşzamanlı sorgular |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 03                | 32                     |
| düz RC       | %10               | 10                     |
| largerc        | #               | 4                      |
| xlargerc       | %70               | 1                      |

### <a name="default-resource-class"></a>Varsayılan kaynak sınıfı

Varsayılan olarak, her Kullanıcı, **smallrc**dinamik kaynak sınıfının bir üyesidir.

Hizmet yöneticisinin kaynak sınıfı smallrc 'de düzeltildi ve değiştirilemez.  Hizmet Yöneticisi, sağlama işlemi sırasında oluşturulan kullanıcı.  Yeni bir sunucu ile yeni bir SQL veri ambarı örneği oluşturulurken, bu bağlamdaki Hizmet Yöneticisi "Sunucu Yöneticisi oturum açma" için belirtilen oturum açdır.

> [!NOTE]
> Active Directory yöneticisi olarak tanımlanan kullanıcılar veya gruplar da hizmet yöneticileridir.
>
>

## <a name="resource-class-operations"></a>Kaynak sınıfı işlemleri

Kaynak sınıfları, veri yönetimi ve işleme etkinlikleri için performansı geliştirmek üzere tasarlanmıştır. Karmaşık sorgular, büyük bir kaynak sınıfı altında çalıştırılmasından de yararlanabilir. Örneğin, büyük birleşimler ve sıralamalar için sorgu performansı, kaynak sınıfı sorguyu bellekte yürütmeye olanak tanımak için yeterince büyükse iyileştirebilirler.

### <a name="operations-governed-by-resource-classes"></a>Kaynak sınıfları tarafından yönetilen işlemler

Bu işlemler, kaynak sınıflarına göre yönetilir:

- ıNSERT-SELECT, UPDATE, DELETE
- Seç (Kullanıcı tablolarını sorgularken)
- ALTER INDEX-REBUILD veya YENIDEN düzenleme
- TABLO YENIDEN OLUŞTURMAYı DEĞIŞTIR
- CREATE INDEX
- KÜMELENMIŞ COLUMNSTORE DIZINI OLUŞTUR
- SELECT OLARAK CREATE TABLE (CTAS)
- Veri yükleme
- Veri taşıma hizmeti (DMS) tarafından gerçekleştirilen veri taşıma işlemleri

> [!NOTE]  
> Dinamik yönetim görünümlerinde (DMVs) veya diğer sistem görünümlerinde SELECT deyimleri eşzamanlılık limitlerinin herhangi birine tabidir. Üzerinde yürütülen sorguların sayısından bağımsız olarak sistemi izleyebilirsiniz.

### <a name="operations-not-governed-by-resource-classes"></a>Kaynak sınıfları tarafından yönetilmeyen işlemler

Kullanıcı daha büyük bir kaynak sınıfının üyesi olsa da, bazı sorgular her zaman smallrc kaynak sınıfında çalışır. Bu dışlama sorguları eşzamanlılık sınırına doğru sayılmaz. Örneğin, eşzamanlılık sınırı 16 ise, birçok kullanıcı kullanılabilir eşzamanlılık yuvalarını etkilemeden sistem görünümlerinden seçim yapabilir.

Aşağıdaki deyimler kaynak sınıflarından muaf tutulur ve her zaman smallrc 'de çalıştırılır:

- TABLO oluştur veya bırak
- TABLO DEĞIŞTIR... Bölüm DEĞIŞTIRME, bölme veya BIRLEŞTIRME
- ALTER ıNDEX DISABLE
- DROP INDEX
- ISTATISTIKLERI oluşturma, GÜNCELLEŞTIRME veya BıRAKMA
- TRUNCATE TABLE
- DEĞIŞTIRME YETKILENDIRMESI
- OTURUM AÇMA OLUŞTUR
- Kullanıcı oluşturma, DEĞIŞTIRME veya BıRAKMA
- OLUŞTURMA, DEĞIŞTIRME veya BıRAKMA yordamı
- Görünüm Oluştur veya bırak
- DEĞER EKLE
- Sistem görünümleri ve DMVs 'ler arasından SEÇIM yapın
- AÇıKLAMAK
- DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Eşzamanlılık Yuvaları

Eşzamanlılık yuvaları, sorgu yürütme için kullanılabilir kaynakları izlemenin kolay bir yoludur. Bunlar, bir yarışmaya, bir konser sınırlı olduğundan, bir yarışmaya bilgisayar ayırmak için satın aldığınız biletler gibidir. Veri ambarı başına toplam eşzamanlılık yuvası sayısı hizmet düzeyine göre belirlenir. Bir sorgunun yürütülmeye başlayabilmesi için yeterli eşzamanlılık yuvası ayırabilmelidir. Bir sorgu tamamlandığında eşzamanlılık yuvalarını serbest bırakır.  

- 10 eşzamanlılık yuvalarla çalışan bir sorgu, 2 eşzamanlılık yuvalarında çalışan bir sorgudan 5 kat daha fazla işlem kaynağına erişebilir.
- Her sorgu 10 eşzamanlılık yuvası gerektiriyorsa ve 40 eşzamanlılık yuvası varsa, aynı anda yalnızca 4 sorgu çalıştırılabilir.

Yalnızca kaynak yönetilen sorgular eşzamanlılık yuvalarını kullanır. Sistem sorguları ve bazı önemsiz sorgular herhangi bir yuva tüketmez. Tüketilen eşzamanlılık yuvasının tam sayısı sorgunun kaynak sınıfına göre belirlenir.

## <a name="view-the-resource-classes"></a>Kaynak sınıflarını görüntüleme

Kaynak sınıfları, önceden tanımlanmış veritabanı rolleri olarak uygulanır. İki tür kaynak sınıfı vardır: dinamik ve statik. Kaynak sınıflarının bir listesini görüntülemek için aşağıdaki sorguyu kullanın:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Kullanıcının kaynak sınıfını değiştirme

Kaynak sınıfları, kullanıcılar veritabanı rollerine atanarak uygulanır. Bir Kullanıcı bir sorgu çalıştırdığında, sorgu kullanıcının kaynak sınıfıyla çalışır. Örneğin, bir Kullanıcı staticrc10 veritabanı rolünün üyesiyse, sorguları az miktarda bellekle çalışır. Bir veritabanı kullanıcısı xlargerc veya staticrc80 veritabanı rollerinin üyesiyse, sorguları büyük miktarda bellekle çalışır.

Bir kullanıcının kaynak sınıfını artırmak için, [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) kullanarak kullanıcıyı büyük bir kaynak sınıfının veritabanı rolüne ekleyin.  Aşağıdaki kod, bir kullanıcıyı largerc veritabanı rolüne ekler.  Her istek, sistem belleğinin %22 ' i alır.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Kaynak sınıfını azaltmak için [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)kullanın.  ' Loaduser ' bir üye veya başka herhangi bir kaynak sınıfı değilse, varsayılan smallrc kaynak sınıfına, %3 bellek izni ile gitirler.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Kaynak sınıfı önceliği

Kullanıcılar birden çok kaynak sınıfının üyesi olabilir. Bir Kullanıcı birden fazla kaynak sınıfına ait olduğunda:

- Dinamik kaynak sınıfları statik kaynak sınıflarından önceliklidir. Örneğin, bir kullanıcı hem düz RC (dinamik) hem de staticrc80 (statik) üyesiyse, sorgular düz RC ile çalışır.
- Daha büyük kaynak sınıfları, daha küçük kaynak sınıflarından önceliklidir. Örneğin, bir Kullanıcı bir düz RC ve largerc üyesiyse, sorgular largerc ile çalışır. Benzer şekilde, bir kullanıcı hem staticrc20 hem de statirc80 'ın üyesiyse, sorgular staticrc80 kaynak ayırmaları ile çalışır.

## <a name="recommendations"></a>Öneriler

Belirli bir sorgu veya yükleme işlemi türünü çalıştırmaya ayrılmış bir Kullanıcı oluşturmanızı öneririz. Kaynak sınıfını sıklıkla değiştirmek yerine bu kullanıcıya kalıcı bir kaynak sınıfı verin. Statik kaynak sınıfları iş yükünde daha büyük genel denetime sahiptir, bu nedenle dinamik kaynak sınıflarını düşünmeden önce statik kaynak sınıflarını kullanmanızı öneririz.

### <a name="resource-classes-for-load-users"></a>Yükleme kullanıcıları için kaynak sınıfları

`CREATE TABLE`, varsayılan olarak kümelenmiş columnstore dizinleri kullanır. Verileri bir columnstore dizinine sıkıştırmak bellek yoğun bir işlemdir ve bellek baskısı Dizin kalitesini azaltabilir. Bellek baskısı, verileri yüklerken daha yüksek bir kaynak sınıfına ihtiyaç duymasına yol açabilir. Yüklerin yeterli belleğe sahip olduğundan emin olmak için, yükleri çalıştırmak için tasarlanmış bir kullanıcı oluşturabilir ve bu kullanıcıyı daha yüksek bir kaynak sınıfına atayabilirsiniz.

Yüklemeleri verimli bir şekilde işlemek için gereken bellek, yüklenen tablonun yapısına ve veri boyutuna bağlıdır. Bellek gereksinimleri hakkında daha fazla bilgi için bkz. [satır grubu kalitesini en üst düzeye çıkarma](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Bellek gereksinimini belirledikten sonra, yükleme kullanıcısının statik veya dinamik kaynak sınıfına mi atanacağını seçin.

- Tablo belleği gereksinimleri belirli bir aralığa denk geldiğinde statik kaynak sınıfı kullanın. Çalışır duruma uygun bellekle yükler. Veri ambarını ölçeklendirirseniz, yüklerin daha fazla belleğe ihtiyacı yoktur. Statik bir kaynak sınıfı kullanarak, bellek ayırmaları sabit kalır. Bu tutarlılık bellek tasarrufu sağlar ve daha fazla sorgunun aynı anda çalışmasına izin verir. Yeni çözümlerin, daha fazla denetim sağladığı için öncelikle statik kaynak sınıflarını kullanmasını öneririz.
- Tablo belleği gereksinimleri çok farklılık gösterdiğinde dinamik bir kaynak sınıfı kullanın. Yükler geçerli DWU veya cDWU düzeyinden daha fazla bellek gerektirebilir. Veri ambarının ölçeklendirilmesi, yükleme işlemlerine daha fazla bellek ekleyerek yükün daha hızlı çalışmasına izin verir.

### <a name="resource-classes-for-queries"></a>Sorgular için kaynak sınıfları

Bazı sorgular yoğun işlem yoğunluğu ve bazıları değildir.  

- Sorgular karmaşıksa, ancak yüksek eşzamanlılık gerektirmeyen bir dinamik kaynak sınıfı seçin.  Örneğin, günlük veya haftalık raporlar oluşturmak, kaynakların ara sıra gereksinimdir. Raporlar büyük miktarlarda veriyi işlerken, veri ambarını ölçeklendirmek kullanıcının var olan kaynak sınıfına daha fazla bellek sağlar.
- Kaynak beklentileri gün boyunca farklılık gösterdiğinden bir statik kaynak sınıfı seçin. Örneğin, bir statik kaynak sınıfı, veri ambarının birçok kişi tarafından sorgulandığı zaman iyi bir şekilde çalışacaktır. Veri ambarını ölçeklendirirken, kullanıcıya ayrılan bellek miktarı değişmez. Sonuç olarak, sistemde paralel olarak daha fazla sorgu yürütülenebilir.

Uygun bellek, sorgulanan veri miktarı, tablo şemaları doğası ve çeşitli birleşimler, seçme ve grup koşulları gibi birçok etkene bağlıdır. Genel olarak, daha fazla bellek ayırmak sorguların daha hızlı tamamlanmasını sağlar, ancak genel eşzamanlılık miktarını azaltır. Eşzamanlılık bir sorun değilse, bellek ayırma, işleme zarar vermez.

Performansı ayarlamak için farklı kaynak sınıfları kullanın. Sonraki bölüm, en iyi kaynak sınıfını bulmanıza yardımcı olan bir saklı yordam sağlar.

## <a name="example-code-for-finding-the-best-resource-class"></a>En iyi kaynak sınıfını bulmak için örnek kod

Belirli bir SLO 'da kaynak sınıfına göre eşzamanlılık ve bellek iznini anlamak için aşağıdaki belirtilen saklı yordamı ve belirli bir kaynak sınıfındaki bölümlenmemiş CCı tablosunda bellek kullanımı yoğun CCı işlemleri için en iyi kaynak sınıfını kullanabilirsiniz:

Bu saklı yordamın amacı aşağıda verilmiştir:

1. Belirli bir SLO 'da kaynak sınıfına göre eşzamanlılık ve bellek iznini görmek için. Bu örnekte gösterildiği gibi, kullanıcının hem şema hem de TableName için NULL sağlaması gerekir.  
2. Belirli bir kaynak sınıfındaki bölümlenmemiş CCı tablosu üzerinde bellek yoğunluğu olan CCı işlemleri için en iyi kaynak sınıfını (yükleme, kopyalama tablosu, yeniden oluşturma dizini vb.) görmek için. Saklı yordam, gerekli bellek iznini bulmak için tablo şemasını kullanır.

### <a name="dependencies--restrictions"></a>Bağımlılıklar & kısıtlamaları

- Bu saklı yordam, bölümlenmiş bir CCI tablosu için bellek gereksinimini hesaplamak üzere tasarlanmamıştır.
- Bu saklı yordam, CTAS/INSERT-SELECT bölümünün SELECT bölümü için bellek gereksinimlerini hesaba almaz ve bir SEÇIM olduğunu varsayar.
- Bu saklı yordam, bu saklı yordamın oluşturulduğu oturumda kullanılabilen geçici bir tablo kullanır.
- Bu saklı yordam, geçerli tekliflere (örneğin, donanım yapılandırması, DMS config) bağlıdır ve bu değişikliklerden herhangi biri doğru şekilde çalışmaz.  
- Bu saklı yordam, mevcut eşzamanlılık sınırı tekliflerine bağlıdır ve bu değişiklikler bu saklı yordam doğru şekilde çalışmaz.  
- Bu saklı yordam mevcut kaynak sınıfı tekliflerine bağlıdır ve bu değişiklikler bu saklı yordam doğru şekilde çalışmaz.  

>[!NOTE]  
>Saklı yordamı belirtilen parametrelerle yürüttükten sonra çıkış almıyorsanız, iki durum olabilir.
>
>1. DW parametresi geçersiz bir SLO değeri içeriyor
>2. Veya, tablodaki CCı işlemi için eşleşen bir kaynak sınıfı yok.
>
>Örneğin, DW100c adresinde en yüksek bellek verme 1 GB 'tır ve tablo şeması, 1 GB gereksinimini ortadan yatabilecek kadar genişse.

### <a name="usage-example"></a>Kullanım örneği

Sözdizimi  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:, DW DB 'den geçerli DWU 'yi ayıklamak için NULL bir parametre sağlayın ya da ' DW100c ' biçiminde desteklenen DWU 'yi sağlayın
2. @SCHEMA_NAME: tablonun şema adını sağlama
3. @TABLE_NAME: ilgilendiğiniz tablo adını sağlayın

Bu saklı proc yürütülen örnekler:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Aşağıdaki ifade, önceki örneklerde kullanılan Table1 oluşturur.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Saklı yordam tanımı

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500 
  ELSE Mem*100 
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200c', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300c', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400c', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500c', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000c', 32, 40, 1, 4, 8, 28, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500c', 32, 60, 1, 6, 13, 42, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000c', 48, 80, 2, 8, 17, 56, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW2500c', 48, 100, 3, 10, 22, 70, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000c', 64, 120, 3, 12, 26, 84, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW5000c', 64, 200, 6, 20, 44, 140, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW6000c', 128, 240, 7, 24, 52, 168, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW7500c', 128, 300, 9, 30, 66, 210, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW10000c', 128, 400, 12, 40, 88, 280, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW15000c', 128, 600, 18, 60, 132, 420, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW30000c', 128, 1200, 36, 120, 264, 840, 1, 2, 4, 8, 16, 32, 64, 128 
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Sonraki adımlar

Veritabanı kullanıcılarını ve güvenliğini yönetme hakkında daha fazla bilgi için bkz. [SQL veri ambarı 'nda veritabanını güvenli hale getirme][Secure a database in SQL Data Warehouse]. Daha büyük kaynak sınıflarının kümelenmiş columnstore dizini kalitesini nasıl iyileştirebilecek hakkında daha fazla bilgi için bkz. [columnstore sıkıştırması Için bellek iyileştirmeleri](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

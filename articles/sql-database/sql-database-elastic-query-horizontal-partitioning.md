---
title: Ölçekli bulut veritabanları genelinde raporlama
description: yatay bölümler üzerinde esnek sorgular ayarlama
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823754"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Ölçekli bulut veritabanları genelinde raporlama (Önizleme)

![Parçalar arasında sorgu][1]

Parçalı veritabanları, ölçeği genişletilmiş bir veri katmanında dağıtır. Şema, yatay bölümlendirme olarak da bilinen tüm katılan veritabanlarında aynıdır. Elastik bir sorgu kullanarak, parçaları oluşturulmuş bir veritabanındaki tüm veritabanlarına yayılan raporlar oluşturabilirsiniz.

Hızlı başlangıç için bkz. [ölçekli bulut veritabanları genelinde raporlama](sql-database-elastic-query-getting-started.md).

Parçalı olmayan veritabanları için bkz. [farklı şemalarla bulut veritabanları genelinde sorgulama](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Ön koşullar

* Elastik veritabanı istemci kitaplığını kullanarak bir parça haritası oluşturun. bkz. parça [eşleme yönetimi](sql-database-elastic-scale-shard-map-management.md). Ya da [elastik veritabanı araçlarını kullanmaya başlama](sql-database-elastic-scale-get-started.md)bölümünde örnek uygulamayı kullanın.
* Alternatif olarak, bkz. [var olan veritabanlarını ölçekli veritabanlarına geçirme](sql-database-elastic-convert-to-use-elastic-tools.md).
* Kullanıcı herhangi bir dış VERI kaynağı iznine sahip olmalıdır. Bu izin ALTER DATABASE iznine dahildir.
* Temel alınan veri kaynağına başvurmak için herhangi bir dış VERI kaynağı izinlerini DEĞIŞTIRME gerekir.

## <a name="overview"></a>Genel Bakış

Bu deyimler, esnek sorgu veritabanında bulunan, parçalı veri katmanınızın meta veri temsilini oluşturur.

1. [ANA ANAHTAR OLUŞTUR](https://msdn.microsoft.com/library/ms174382.aspx)
2. [VERITABANı KAPSAMLı KIMLIK BILGISI OLUŞTUR](https://msdn.microsoft.com/library/mt270260.aspx)
3. [DıŞ VERI KAYNAĞı OLUŞTUR](https://msdn.microsoft.com/library/dn935022.aspx)
4. [DıŞ TABLO OLUŞTUR](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1,1 veritabanı kapsamlı ana anahtar ve kimlik bilgileri oluşturma

Kimlik bilgisi, uzak veritabanlarınıza bağlanmak için elastik sorgu tarafından kullanılır.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> *"\<Kullanıcı adı\>"* ' nın herhangi bir *"\@ServerName"* sonekini içermediğinden emin olun.

## <a name="12-create-external-data-sources"></a>1,2 dış veri kaynakları oluşturma

Sözdizimi

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>,
            SHARD_MAP_NAME = ‘<shardmapname>’
                   ) [;]

### <a name="example"></a>Örnek

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net',
        DATABASE_NAME='ShardMapDatabase',
        CREDENTIAL= SMMUser,
        SHARD_MAP_NAME='ShardMap'
    );

Geçerli dış veri kaynaklarının listesini alın:

    select * from sys.external_data_sources;

Dış veri kaynağı, parça haritanızda başvuru yapıyor. Daha sonra elastik bir sorgu, veri katmanına katılan veritabanlarını numaralandırmak için dış veri kaynağını ve temel alınan parça haritasını kullanır.
Aynı kimlik bilgileri, parça haritasını okumak ve elastik bir sorgunun işlenmesi sırasında parçaları üzerindeki verilere erişmek için kullanılır.

## <a name="13-create-external-tables"></a>1,3 dış tablo oluşturma

Sözdizimi  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::=
      DATA_SOURCE = <External_Data_Source>,
      [ SCHEMA_NAME = N'nonescaped_schema_name',]
      [ OBJECT_NAME = N'nonescaped_object_name',]
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Örnek**

    CREATE EXTERNAL TABLE [dbo].[order_line](
         [ol_o_id] int NOT NULL,
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL,
         [ol_number] tinyint NOT NULL,
         [ol_i_id] int NOT NULL,
         [ol_delivery_d] datetime NOT NULL,
         [ol_amount] smallmoney NOT NULL,
         [ol_supply_w_id] int NOT NULL,
         [ol_quantity] smallint NOT NULL,
         [ol_dist_info] char(24) NOT NULL
    )

    WITH
    (
        DATA_SOURCE = MyExtSrc,
         SCHEMA_NAME = 'orders',
         OBJECT_NAME = 'order_details',
        DISTRIBUTION=SHARDED(ol_w_id)
    );

Geçerli veritabanından dış tablo listesini alın:

    SELECT * from sys.external_tables;

Dış tabloları bırakmak için:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Açıklamalar

DATA\_SOURCE yan tümcesi dış tablo için kullanılan dış veri kaynağını (bir parça eşlemesi) tanımlar.  

ŞEMA\_adı ve nesne\_adı yan tümceleri, dış tablo tanımını farklı bir şemadaki bir tabloyla eşleştirir. Atlanırsa, uzak nesne şeması "dbo" olarak kabul edilir ve adı tanımlanmakta olan dış tablo adıyla aynı kabul edilir. Bu, uzak tablonuzun adı, dış tabloyu oluşturmak istediğiniz veritabanında zaten alınmış ise yararlıdır. Örneğin, genişleme veri katmanınızda katalog görünümlerinin veya DMV 'nin toplam görünümünü almak için bir dış tablo tanımlamak istiyorsunuz. Katalog görünümleri ve DMVs yerel olarak zaten mevcut olduğundan, dış tablo tanımı için adlarını kullanamazsınız. Bunun yerine, farklı bir ad kullanın ve şema\_adı ve/veya nesne\_adı yan tümcelerinde katalog görünümünün veya DMV 'nin adını kullanın. (Aşağıdaki örneğe bakın.)

DAĞıTıM yan tümcesi, bu tablo için kullanılan veri dağılımını belirtir. Sorgu işlemcisi, en verimli sorgu planlarını oluşturmak için DAĞıTıM yan tümcesinde belirtilen bilgileri kullanır.

1. Parçalama, verilerin veritabanları arasında yatay olarak bölümlenmesi **anlamına gelir** . Veri dağıtımına ilişkin bölümlendirme anahtarı, **< sharding_column_name >** parametresidir.
2. **Çoğaltılan** , tablonun aynı kopyalarının her veritabanında bulunduğu anlamına gelir. Çoğaltmalarınızın veritabanları genelinde özdeş olmasını sağlamak sizin sorumluluğunuzdadır.
3. Hepsini bir **kez deneme\_** , tablonun bir uygulamaya bağımlı dağıtım yöntemi kullanılarak yatay olarak bölümlenmesi anlamına gelir.

**Veri katmanı başvurusu**: dış tablo DDL, bir dış veri kaynağını ifade eder. Dış veri kaynağı, dış tabloya, veri katmanınızda tüm veritabanlarını bulmak için gereken bilgileri sağlayan bir parça Haritası belirtir.

### <a name="security-considerations"></a>Güvenlikle ilgili dikkat edilmesi gerekenler

Dış tabloya erişimi olan kullanıcılar, dış veri kaynağı tanımında verilen kimlik bilgileri altındaki temeldeki uzak tablolara otomatik olarak erişim elde edebilir. Dış veri kaynağının kimlik bilgileri aracılığıyla, ayrıcalıkların istenmeyen ayrıcalıklarından kaçının. Bir dış tablo için, normal bir tablo gibi verme veya Iptal etme kullanın.  

Dış veri kaynağınızı ve dış tablolarınızı tanımladıktan sonra, dış Tablolarınızda artık tam T-SQL kullanabilirsiniz.

## <a name="example-querying-horizontal-partitioned-databases"></a>Örnek: yatay bölümlenmiş veritabanlarını sorgulama

Aşağıdaki sorgu, ambarlar, siparişler ve sipariş satırları arasında üç yönlü bir JOIN gerçekleştirir ve çeşitli toplamalar ve seçmeli bir filtre kullanır. (1) yatay bölümleme (parçalama) ve (2) ambarların, siparişlerin ve sipariş satırlarının ambar kimliği sütunuyla birlikte bulunduğunu ve elastik sorgunun parçalar üzerinde birleştirmeleri birlikte bulabildiğinizi ve içindeki parçaların maliyetli bir kısmını işlemesini kabul eder. dir.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Uzak T-SQL yürütmesi için saklı yordam: SP\_execute_remote

Elastik sorgu Ayrıca, parçalara doğrudan erişim sağlayan bir saklı yordam de sunar. Saklı yordama [\_uzaktan çalıştır\_](https://msdn.microsoft.com/library/mt703714) ve uzak veritabanlarında uzak saklı yordamları veya t-SQL kodu yürütmek için kullanılabilir. Aşağıdaki parametreleri alır:

* Veri kaynağı adı (nvarchar): RDBMS türünde dış veri kaynağının adı.
* Sorgu (nvarchar): her parça üzerinde yürütülecek T-SQL sorgusu.
* Parametre bildirimi (nvarchar)-isteğe bağlı: sorgu parametresinde kullanılan parametreler için veri türü tanımlarına sahip dize (sp_executesql gibi).
* Parametre değeri listesi-isteğe bağlı: parametre değerlerinin virgülle ayrılmış listesi (sp_executesql gibi).

SP\_Execute\_uzak veritabanlarında verilen T-SQL ifadesini yürütmek için çağırma parametrelerinde sağlanan dış veri kaynağını kullanır. Bu, shardmap Manager veritabanına ve uzak veritabanlarına bağlanmak için dış veri kaynağının kimlik bilgilerini kullanır.  

Örnek:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Araçlar için bağlantı

Uygulamanızı, bı ve veri tümleştirme araçlarınızı, dış tablo tanımlarınızla veritabanına bağlamak için normal SQL Server bağlantı dizelerini kullanın. SQL Server, aracınız için bir veri kaynağı olarak desteklendiğinden emin olun. Ardından, araçla bağlantılı diğer SQL Server veritabanları gibi elastik sorgu veritabanına başvurun ve araç veya uygulamanızdaki dış tabloları yerel tablolar gibi kullanın.

## <a name="best-practices"></a>En iyi uygulamalar

* Elastik sorgu uç noktası veritabanına, shardmap veritabanına ve SQL DB güvenlik duvarları üzerinden tüm parçalara erişim verildiğinden emin olun.  
* Dış tablo tarafından tanımlanan veri dağıtımını doğrulayın veya zorunlu tutun. Gerçek veri dağıtım tablonuz, tablo tanımınızda belirtilen dağıtıma farklıysa, sorgunuz beklenmedik sonuçlara neden olabilir.
* Elastik sorgu şu anda parça olan anahtar üzerinde koşullar, belirli parçaları işlemeden güvenle hariç tutmasına imkan tanır.
* Elastik sorgu en iyi şekilde, çoğu hesaplamanın parçalar halinde yapılabildiği sorgular için geçerlidir. Genellikle, parçalar üzerinde değerlendirilebilecek seçmeli filtre koşullarına sahip en iyi sorgu performansını elde edersiniz veya tüm parçalar üzerinde bölüm hizalanmış bir şekilde gerçekleştirilebilecek bölümlendirme anahtarları üzerinde birleşimler vardır. Diğer sorgu desenlerinin, parçalardan baş düğüme büyük miktarlarda veri yüklemesi gerekebilir ve kötü bir şekilde çalışabilir

## <a name="next-steps"></a>Sonraki adımlar

* Elastik sorguya genel bakış için bkz. [elastik sorguya genel bakış](sql-database-elastic-query-overview.md).
* Dikey bölümleme öğreticisi için bkz. [çapraz veritabanı sorgusuna Başlarken (dikey bölümlendirme)](sql-database-elastic-query-getting-started-vertical.md).
* Dikey olarak bölümlenmiş verilere yönelik sözdizimi ve örnek sorgular için bkz. [dikey olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-vertical-partitioning.md)
* Yatay bölümleme (parçalama) öğreticisi için bkz. [Yatay bölümleme (parçalama) için elastik sorgu ile çalışmaya](sql-database-elastic-query-getting-started.md)başlama.
* Tek bir uzak Azure SQL veritabanı üzerinde Transact-SQL ifadesini yürüten saklı yordam için bkz. [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) , yatay bölümleme düzeninde parçalar olarak hizmet veren veritabanları kümesi.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

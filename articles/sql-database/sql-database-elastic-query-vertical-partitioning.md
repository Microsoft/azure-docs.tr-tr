---
title: Farklı şemalı bulut veritabanlarında sorgulama yapma
description: dikey bölümler üzerinde çapraz veritabanı sorguları ayarlama
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d5983d25685242a696300f293231bbf987e8442d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823725"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Farklı şemalarla bulut veritabanları genelinde sorgulama (Önizleme)

![Farklı veritabanlarındaki tablolar arasında sorgu][1]

Dikey olarak bölümlenmiş veritabanları, farklı veritabanlarındaki farklı tablo kümelerini kullanır. Diğer bir deyişle, şema farklı veritabanlarında farklı olur. Örneğin, tüm envanter tabloları, tüm muhasebe ile ilgili tablolar ikinci bir veritabanı üzerinde olduğunda tek bir veritabanıdır. 

## <a name="prerequisites"></a>Ön koşullar

* Kullanıcı herhangi bir dış VERI kaynağı iznine sahip olmalıdır. Bu izin ALTER DATABASE iznine dahildir.
* Temel alınan veri kaynağına başvurmak için herhangi bir dış VERI kaynağı izinlerini DEĞIŞTIRME gerekir.

## <a name="overview"></a>Genel Bakış

> [!NOTE]
> Yatay bölümlemeden farklı olarak, bu DDL deyimleri elastik veritabanı istemci kitaplığı aracılığıyla bir parça haritası ile veri katmanı tanımlamaya bağlı değildir.
>

1. [ANA ANAHTAR OLUŞTUR](https://msdn.microsoft.com/library/ms174382.aspx)
2. [VERITABANı KAPSAMLı KIMLIK BILGISI OLUŞTUR](https://msdn.microsoft.com/library/mt270260.aspx)
3. [DıŞ VERI KAYNAĞı OLUŞTUR](https://msdn.microsoft.com/library/dn935022.aspx)
4. [DıŞ TABLO OLUŞTUR](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Veritabanı kapsamlı ana anahtar ve kimlik bilgileri oluşturma

Kimlik bilgisi, uzak veritabanlarınıza bağlanmak için elastik sorgu tarafından kullanılır.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> `<username>` hiçbir **"\@ServerName"** sonekini içermediğinden emin olun. 
>

## <a name="create-external-data-sources"></a>Dış veri kaynakları oluşturma

Sözdizimi

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> TÜR parametresi **RDBMS**olarak ayarlanmalıdır. 
>

### <a name="example"></a>Örnek

Aşağıdaki örnek dış veri kaynakları için CREATE ifadesinin kullanımını gösterir. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Geçerli dış veri kaynaklarının listesini almak için: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Dış tablolar

Sözdizimi

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Örnek

```sql
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 
```

Aşağıdaki örnek, geçerli veritabanından dış Tablo listesinin nasıl alınacağını gösterir: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Açıklamalar

Elastik sorgu, mevcut dış tablo söz dizimini genişleterek, RDBMS türünde dış veri kaynaklarını kullanan dış tabloları tanımlar. Dikey bölümlendirme için bir dış tablo tanımı aşağıdaki özellikleri içerir: 

* **Şema**: dış tablo DDL, sorgularınızın kullanabileceği bir şemayı tanımlar. Dış tablo tanımınızda belirtilen şemanın, asıl verilerin depolandığı uzak veritabanındaki tabloların şemasıyla eşleşmesi gerekir. 
* **Uzak veritabanı başvurusu**: dış tablo DDL, bir dış veri kaynağını ifade eder. Dış veri kaynağı, gerçek tablo verilerinin depolandığı uzak veritabanının SQL veritabanı sunucu adını ve veritabanı adını belirtir. 

Bir dış veri kaynağını önceki bölümde özetlenen şekilde kullanarak, dış tablo oluşturma sözdizimi aşağıdaki gibidir: 

DATA_SOURCE yan tümcesi dış tablo için kullanılan dış veri kaynağını (dikey bölümlendirme durumunda uzak veritabanı) tanımlar.  

SCHEMA_NAME ve OBJECT_NAME yan tümceleri, dış tablo tanımını uzak veritabanındaki farklı bir şemadaki bir tabloya veya sırasıyla farklı bir ada sahip bir tabloya eşleyebilme olanağı sağlar. Bu, uzak veritabanınızdaki bir Katalog görünümü veya DMV için bir dış tablo tanımlamak istiyorsanız veya uzak tablo adının zaten yerel olarak alındığı başka herhangi bir durum için yararlıdır.  

Aşağıdaki DDL ekstresi, mevcut bir dış tablo tanımını yerel katalogdan bırakır. Uzak veritabanını etkilemez. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Dış tablo oluşturma/bırakma izinleri**: Ayrıca, temel alınan veri kaynağına başvurmak için gereken dış tablo DDL için HERHANGI BIR dış veri kaynağı izinlerini değiştirme gerekir.  

## <a name="security-considerations"></a>Güvenlikle ilgili dikkat edilmesi gerekenler

Dış tabloya erişimi olan kullanıcılar, dış veri kaynağı tanımında verilen kimlik bilgileri altındaki temeldeki uzak tablolara otomatik olarak erişim elde edebilir. Dış veri kaynağının kimlik bilgisi aracılığıyla ayrıcalıkların istenmeyen olarak yükseltilmesini önlemek için dış tabloya erişimi dikkatle yönetmeniz gerekir. Normal SQL izinleri, bir dış tabloya normal bir tablo gibi erişim vermek veya erişimi Iptal etmek için kullanılabilir.  

## <a name="example-querying-vertically-partitioned-databases"></a>Örnek: dikey olarak bölümlenmiş veritabanlarını sorgulama

Aşağıdaki sorgu, siparişler ve sipariş satırları için iki yerel tablo ve müşteriler için uzak tablo arasında üç yönlü bir JOIN gerçekleştirir. Bu, elastik sorgu için başvuru verileri kullanım örneğine bir örnektir: 

```sql
    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Uzak T-SQL yürütmesi için saklı yordam: SP\_execute_remote

Elastik sorgu, uzak veritabanına doğrudan erişim sağlayan bir saklı yordam de sunar. Saklı yordama [\_uzaktan çalıştır\_](https://msdn.microsoft.com/library/mt703714) , uzak saklı yordamları veya t-SQL kodu uzak veritabanında yürütmek için kullanılabilir. Aşağıdaki parametreleri alır: 

* Veri kaynağı adı (nvarchar): RDBMS türünde dış veri kaynağının adı. 
* Sorgu (nvarchar): uzak veritabanında yürütülecek T-SQL sorgusu. 
* Parametre bildirimi (nvarchar)-isteğe bağlı: sorgu parametresinde kullanılan parametreler için veri türü tanımlarına sahip dize (sp_executesql gibi). 
* Parametre değeri listesi-isteğe bağlı: parametre değerlerinin virgülle ayrılmış listesi (sp_executesql gibi).

SP\_Execute\_uzak veritabanında verilen T-SQL ifadesini yürütmek için çağırma parametrelerinde sağlanan dış veri kaynağını kullanır. Uzak veritabanına bağlanmak için dış veri kaynağının kimlik bilgilerini kullanır.  

Örnek: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Araçlar için bağlantı

Bı ve veri tümleştirme araçlarınızı, elastik sorgu özellikli ve dış tablolar tanımlanmış SQL DB sunucusundaki veritabanlarına bağlamak için normal SQL Server bağlantı dizelerini kullanabilirsiniz. SQL Server, aracınız için bir veri kaynağı olarak desteklendiğinden emin olun. Daha sonra, diğer tüm SQL Server veritabanıyla aynı şekilde, bu şekilde elastik sorgu veritabanına ve dış tablolarına başvurun. 

## <a name="best-practices"></a>En iyi uygulamalar

* Azure hizmetleri 'nin SQL DB güvenlik duvarı yapılandırmasındaki erişimi etkinleştirerek, elastik sorgu uç noktası veritabanına uzak veritabanına erişim verildiğinden emin olun. Ayrıca, dış veri kaynağı tanımında belirtilen kimlik bilgisinin uzak veritabanında başarıyla oturum açabildiğinden ve uzak tabloya erişim izinlerine sahip olduğundan emin olun.  
* Elastik sorgu en iyi şekilde, hesaplama işlemlerinin büyük bir kısmının uzak veritabanlarında yapılabildiği sorgular için geçerlidir. Genellikle uzak veritabanlarında veya yalnızca uzak veritabanında gerçekleştirilebilecek birleşimlerde değerlendirilebilecek seçmeli filtre koşullarına sahip en iyi sorgu performansını elde edersiniz. Diğer sorgu desenlerinin, uzak veritabanından büyük miktarlarda veri yüklemesi gerekebilir ve kötü bir şekilde çalışabilir. 

## <a name="next-steps"></a>Sonraki adımlar

* Elastik sorguya genel bakış için bkz. [elastik sorguya genel bakış](sql-database-elastic-query-overview.md).
* Dikey bölümleme öğreticisi için bkz. [çapraz veritabanı sorgusuna Başlarken (dikey bölümlendirme)](sql-database-elastic-query-getting-started-vertical.md).
* Yatay bölümleme (parçalama) öğreticisi için bkz. [Yatay bölümleme (parçalama) için elastik sorgu ile çalışmaya](sql-database-elastic-query-getting-started.md)başlama.
* Yatay olarak bölümlenmiş veriler için sözdizimi ve örnek sorgular için bkz. [yatay olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-horizontal-partitioning.md)
* Tek bir uzak Azure SQL veritabanı üzerinde Transact-SQL ifadesini yürüten saklı yordam için bkz. [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) , yatay bölümleme düzeninde parçalar olarak hizmet veren veritabanları kümesi.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->

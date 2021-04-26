---
title: Maksimum paralellik derecesini yapılandırın (MAXDOP)
titleSuffix: Azure SQL Database
description: En Yüksek paralellik derecesi (MAXDOP) hakkında bilgi edinin.
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536911"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>Azure SQL Veritabanı’nda en yüksek paralellik derecesini (MAXDOP) yapılandırma
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Bu makalede, Azure SQL veritabanı 'nda **maksimum paralellik derecesi (MAXDOP)** yapılandırma ayarı açıklanmaktadır. 

> [!NOTE]
> **Bu içerik Azure SQL veritabanı ' na odaklanılmıştır.** Azure SQL veritabanı, Microsoft SQL Server veritabanı altyapısının en son kararlı sürümünü temel alır. bu nedenle, sorun giderme ve yapılandırma seçenekleri farklı olmasına rağmen içeriğin büyük bölümü benzerdir. SQL Server için MAXDOP hakkında daha fazla bilgi için, bkz. [en yüksek paralellik sunucu yapılandırma seçeneğini yapılandırma](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Genel Bakış
  MAXDOP, veritabanı altyapısında sorgu içi paralellik denetimini denetler. Daha yüksek MAXDOP değerleri genellikle sorgu başına daha paralel iş parçacığı ve daha hızlı sorgu yürütme ile sonuçlanır. 

  Azure SQL veritabanı 'nda, her yeni tek veritabanı ve elastik havuz veritabanı için varsayılan MAXDOP ayarı 8 ' dir. Bu varsayılan, gereksiz kaynak kullanımını engeller, yine de veritabanı altyapısının paralel iş parçacıklarını kullanarak sorguları daha hızlı yürütmesine olanak sağlar. Genellikle Azure SQL veritabanı iş yükleri için MAXDOP 'nin daha fazla yapılandırılması gerekmez, ancak gelişmiş bir performans ayarlama alıştırması olarak avantajlara olanak sağlayabilir.

> [!Note]
>   Eylül 2020 ' de, Azure SQL veritabanı hizmetindeki telemetri yıllarına bağlı olarak, en çok çeşitli müşteri iş yükleri için en uygun değer olarak, [yeni veritabanları için varsayılan](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)olarak MAXDOP 8. Bu varsayılan, aşırı paralellik nedeniyle performans sorunlarını önlemeye yardımcı oldu. Bundan önce, yeni veritabanları için varsayılan ayar MAXDOP 0 idi. MAXDOP, 2020 Eylül 'den önce oluşturulan mevcut veritabanları için otomatik olarak değiştirilmedi.

  Genel olarak, veritabanı altyapısı paralellik kullanarak bir sorgu yürütmeyi seçerse, yürütme süresi daha hızlıdır. Ancak, aşırı paralellik, sorgu performansını iyileştirmeden ek işlemci kaynakları kullanabilir. Ölçekte, fazla paralellik, aynı veritabanı altyapısı örneğinde çalışan tüm sorguların sorgu performansını olumsuz etkileyebilir. Geleneksel olarak, paralellik için üst sınır ayarlamak SQL Server iş yüklerinde yaygın bir performans ayarlama uygulamadır.

  Aşağıdaki tabloda, farklı MAXDOP değerleriyle sorgu yürütürken veritabanı altyapısı davranışı açıklanmaktadır:

| MAXDOP | Davranış | 
|--|--|
| = 1 | Veritabanı altyapısı sorguları yürütmek için tek bir seri iş parçacığı kullanır. Paralel iş parçacıkları kullanılmaz. | 
| > 1 | Veritabanı altyapısı, paralel iş parçacıklarında kullanılacak ek [zamanlayıcılar](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) sayısını MAXDOP değerine veya mantıksal işlemcilerin toplam sayısını (hangisi daha küçükse) ayarlar. |
| = 0 | Veritabanı altyapısı, paralel iş parçacıkları tarafından kullanılacak ek [zamanlayıcılar](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) sayısını, hangisi daha küçükse, mantıksal işlemcilerin veya 64 toplam sayısına ayarlar. | 
| | |

> [!Note]
> Her sorgu en az bir Zamanlayıcı ve bu Zamanlayıcı üzerinde bir çalışan iş parçacığı ile yürütülür.
>
> Paralellik ile yürütülen bir sorgu ek zamanlayıcılar ve ek paralel iş parçacıkları kullanır. Aynı Zamanlayıcı üzerinde birden çok paralel iş parçacığı yürütebileceğinden, bir sorguyu yürütmek için kullanılan toplam iş parçacığı sayısı belirtilen MAXDOP değerinden yüksek olabilir veya toplam mantıksal işlemci sayısıdır. Daha fazla bilgi için bkz. [paralel görevleri zamanlama](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks).

##  <a name="considerations"></a><a name="Considerations"></a> Konuları  

-   Azure SQL veritabanı 'nda varsayılan MAXDOP değerini değiştirebilirsiniz:
    -   Sorgu düzeyinde, **MAXDOP** [sorgu ipucunu](/sql/t-sql/queries/hints-transact-sql-query)kullanarak.     
    -   Veritabanı düzeyinde, **MAXDOP** [veritabanı kapsamlı yapılandırmasını](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)kullanarak.

-   Uzun sürme SQL Server MAXDOP konuları ve [önerileri](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) Azure SQL veritabanı için geçerlidir. 

-   Bir dizin oluşturan veya yeniden oluşturan ya da kümelenmiş bir dizini bırakan dizin işlemleri kaynak kullanımı yoğun olabilir. Veya deyimindeki MAXDOP dizin seçeneğini belirterek dizin işlemleri için veritabanı MAXDOP değerini geçersiz kılabilirsiniz `CREATE INDEX` `ALTER INDEX` . MAXDOP değeri, yürütme zamanında ifadeye uygulanır ve dizin meta verilerinde depolanmaz. Daha fazla bilgi için bkz. [paralel Dizin Işlemlerini yapılandırma](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   Sorgu ve Dizin işlemlerine ek olarak, MAXDOP için veritabanı kapsamlı yapılandırma seçeneği Ayrıca, DBCC CHECKTABLE, DBCC CHECKDB ve DBCC CHECKFıLEGROUP gibi paralel yürütme kullanan diğer deyimlerin paralelliğini denetler. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Öneri  

  Veritabanı için MAXDOP ' nin değiştirilmesi, sorgu performansı ve kaynak kullanımı için hem pozitif hem de negatif üzerinde önemli bir etkiye sahip olabilir. Ancak, tüm iş yükleri için en uygun olan tek bir MAXDOP değeri yoktur. MAXDOP ayarı için [öneriler](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) nanced ve birçok faktöre bağlıdır. 

  Bazı yoğun eşzamanlı iş yükleri diğerlerinden farklı bir MAXDOP ile daha iyi çalışabilir. Doğru şekilde yapılandırılmış bir MAXDOP, performans ve kullanılabilirlik olayları riskini azaltmalıdır ve bazı durumlarda gereksiz kaynak kullanımından kaçınmak için maliyetleri azaltabilir ve bu nedenle daha düşük bir hizmet hedefine ölçeklendirebilirsiniz.

### <a name="excessive-parallelism"></a>Aşırı paralellik

  Daha yüksek bir MAXDOP, genellikle CPU yoğun sorguların süresini azaltır. Ancak aşırı paralellik, diğer CPU ve çalışan iş parçacığı kaynakları sorgularını gerçekleştirerek diğer eş zamanlı iş yükü performansını daha da kullanabilir. Olağanüstü durumlarda aşırı paralellik, tüm veritabanı veya elastik havuz kaynaklarını tüketebilir, sorgu zaman aşımları, hatalar ve uygulama kesintilerine neden olabilir. 

> [!Tip]
> Müşterilerin, şu anda sorunlara yol açmamasına rağmen, MAXDOP 'yi 0 olarak ayarlamaktan kaçınmanıza önerilir.

  Aşırı paralellik, hizmet hedefi tarafından verilen CPU ve çalışan iş parçacığı kaynakları tarafından desteklenenden daha fazla eşzamanlı istek olduğunda soruna neden olur. Bir veritabanının ölçeği azaltılması durumunda aşırı paralellik olmaması veya Azure SQL veritabanı 'nda gelecek donanım nesilleri aynı veritabanı hizmeti hedefi için daha fazla çekirdek sunmanız nedeniyle, gelecekteki olası sorunlar riskini azaltmak için MAXDOP 0 ' ı önleyin.

### <a name="modifying-maxdop"></a>MAXDOP 'yi değiştirme 

  Azure SQL veritabanı iş yükünüz için varsayılandan farklı bir MAXDOP ayarının en iyi olduğunu belirlerseniz, `ALTER DATABASE SCOPED CONFIGURATION` T-SQL ifadesini kullanabilirsiniz. Örnekler için aşağıdaki [Transact-SQL bölümünün kullanıldığı örneklere](#examples) bakın. MAXDOP 'yi oluşturduğunuz her yeni veritabanı için varsayılan olmayan bir değere değiştirmek için, bu adımı veritabanı dağıtım sürecinizi ekleyin.

  Varsayılan olmayan MAXDOP, iş yükünde yalnızca küçük bir sorgu alt kümesini avantajlarsa, seçenek (MAXDOP) ipucu ekleyerek sorgu düzeyinde MAXDOP 'yi geçersiz kılabilirsiniz. Örnekler için aşağıdaki [Transact-SQL bölümünün kullanıldığı örneklere](#examples) bakın. 

  Gerçekçi eşzamanlı sorgu yüklerini içeren yük testi ile MAXDOP yapılandırma değişikliklerinizi kapsamlı olarak test edin. 

  Birincil ve ikincil çoğaltmalar için MAXDOP, farklı MAXDOP ayarları, okuma/yazma ve salt okuma iş yükleriniz için en uygun olduğunda bağımsız olarak yapılandırılabilir. Bu, Azure SQL veritabanı [okuma ölçeği](read-scale-out.md)genişletme, [coğrafi çoğaltma](active-geo-replication-overview.md)ve [hiper ölçekli](service-tier-hyperscale.md) ikincil çoğaltmalar için geçerlidir. Varsayılan olarak, tüm ikincil çoğaltmalarda birincil çoğaltmanın MAXDOP yapılandırması devralınır.

## <a name="security"></a><a name="Security"></a> Güven  
  
###  <a name="permissions"></a><a name="Permissions"></a> İzinleri  
  `ALTER DATABASE SCOPED CONFIGURATION`Deyimin, veritabanı rolünün bir üyesi olarak `db_owner` veya izin verilen bir kullanıcı olarak sunucu yöneticisi olarak yürütülmesi gerekir `ALTER ANY DATABASE SCOPED CONFIGURATION` .
 
## <a name="examples"></a>Örnekler   

  Bu örnekler,  `SAMPLE` Azure SQL veritabanı 'nın yeni tek bir veritabanı için seçildiğinde, en son AdventureWorksLT örnek veritabanını kullanır.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP veritabanı kapsamlı yapılandırması   

  Bu örnek, yapılandırmayı olarak ayarlamak için [alter database KAPSAMSıZ yapılandırma](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) deyimini nasıl kullanacağınızı gösterir `MAXDOP` `2` . Ayar, yeni sorgular için hemen geçerli olur. [Invoke-sqlcmd](/powershell/module/sqlserver/invoke-sqlcmd) PowerShell cmdlet 'ı ayarlanacak T-SQL sorgularını yürütür ve MAXDOP veritabanı kapsamlı yapılandırmasını döndürür. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Bu örnek, [okuma ölçeği genişletme çoğaltmaları etkin](read-scale-out.md), [coğrafi çoğaltma](active-geo-replication-overview.md)ve [Azure SQL veritabanı hiper ölçek Ikincil çoğaltmalarıyla](service-tier-hyperscale.md)Azure SQL veritabanları ile kullanım içindir. Örnek olarak, birincil çoğaltma ikincil çoğaltma olarak farklı bir varsayılan MAXDOP olarak ayarlanır, benimsemeyi bekleme bir okuma-yazma ve salt okunurdur iş yükü arasında farklılık gösterebilir.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  Azure SQL veritabanınızda T-SQL sorgularını yürütmek için [Azure Portal sorgu düzenleyicisini](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)veya [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) kullanabilirsiniz.

1.  Yeni bir sorgu penceresi açın.

2.  MAXDOP 'yi değiştirmek istediğiniz veritabanına bağlanın. Ana veritabanında veritabanı kapsamlı yapılandırma ayarları değiştirilemez.
  
3.  Aşağıdaki örneği kopyalayıp sorgu penceresine yapıştırın ve **Yürüt**' ü seçin. 

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP veritabanı kapsamlı yapılandırması

  Bu örnek, [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) Sistem kataloğu görünümü kullanılarak geçerli veritabanı MAXDOP veritabanı kapsamlı yapılandırmasının nasıl belirleneceğini gösterir.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Bu örnek, yapılandırmayı olarak ayarlamak için [alter database KAPSAMSıZ yapılandırma](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) deyimini nasıl kullanacağınızı gösterir `MAXDOP` `8` . Ayar hemen yürürlüğe girer.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Bu örnek, [okuma ölçeği genişletme çoğaltmaları etkin](read-scale-out.md), [coğrafi çoğaltma](active-geo-replication-overview.md)ve [hiper ÖLÇEKLI](service-tier-hyperscale.md) ikincil çoğaltmalarla Azure SQL veritabanları ile kullanım içindir. Örnek olarak, birincil çoğaltma ikincil çoğaltmadan farklı bir MAXDOP olarak ayarlanır. Bu, okuma-yazma ve salt okuma iş yükleri arasında farklılıklar olabileceğini benimsemeyi bekleme. Tüm deyimler birincil çoğaltmada yürütülür. `value_for_secondary`Öğesinin sütunu, `sys.database_scoped_configurations` İkincil çoğaltmanın ayarlarını içerir.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP sorgu ipucu

  Bu örnek, ' a zorlamak için sorgu ipucunu kullanarak bir sorgunun nasıl yürütüleceğini gösterir `max degree of parallelism` `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP dizin seçeneği

  Bu örnek, öğesini öğesine zorlamak için dizin seçeneği kullanılarak bir dizinin nasıl yeniden oluşturulduğunu `max degree of parallelism` gösterir `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Ayrıca bkz.  
* [ALTER DATABASE KAPSAMSıZ yapılandırma &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Paralel dizin Işlemlerini yapılandırma](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Sorgu Ipuçları &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Dizin seçeneklerini ayarla](/sql/relational-databases/indexes/set-index-options)     
* [Azure SQL veritabanı engelleme sorunlarını anlama ve çözme](understand-resolve-blocking.md)

## <a name="next-steps"></a>Sonraki adımlar

* [Performansı izleme ve ayarlama](/sql/relational-databases/performance/monitor-and-tune-for-performance)

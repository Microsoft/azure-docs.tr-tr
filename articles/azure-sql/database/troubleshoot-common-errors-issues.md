---
title: Yaygın olarak karşılaşılan Azure SQL Veritabanı bağlantı sorunlarını giderme
description: Azure SQL veritabanı bağlantı sorunlarını giderme ve diğer Azure SQL veritabanı veya Azure SQL yönetilen örnek özel sorunlarını çözme adımlarını sağlar
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: ec61f2c67576d6e144d8d4bb7e8ecaaa157db0a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233381"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği ile bağlantı sorunlarını ve diğer hataları giderme
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı veya Azure SQL yönetilen örneği bağlantısı başarısız olduğunda hata iletileri alırsınız. Bu bağlantı sorunlarına yeniden yapılandırma, güvenlik duvarı ayarları, bir bağlantı zaman aşımı, yanlış oturum açma bilgileri veya [uygulama tasarım](develop-overview.md) sürecinde en iyi yöntemler ve tasarım kılavuzları uygulanamaması neden olabilir. Ayrıca, bazı Azure SQL veritabanı veya SQL yönetilen örnek kaynaklarında maksimum sınıra ulaşıldığında artık bağlanamazsınız.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Geçici hata hata iletileri (40197, 40613 ve diğerleri)

Azure altyapısının SQL Veritabanı hizmetinde ağır iş yükleri ortaya çıktığında sunucuları dinamik olarak yeniden yapılandırabilme özelliği vardır.  Bu dinamik davranış, istemci programınızın veritabanı veya örnekle olan bağlantısını kaybetmesine neden olabilir. Bu tür bir hata koşuluna *geçici* bir hata denir. Planlanmış bir olay (örneğin, yazılım yükseltmesi) veya plansız bir olay (örneğin, bir işlem kilitlenmesi veya yük dengeleme) nedeniyle veritabanı yeniden yapılandırma olayları meydana gelir. En yeniden yapılandırma olaylarının çoğu genellikle kısa süreli olur ve en çok 60 saniyeden kısa bir süre içinde tamamlanmalıdır. Ancak, büyük bir işlemin uzun süre çalışan bir kurtarmaya neden olduğu gibi, bu olayların zaman zaman tamamlanması daha uzun sürebilir. Aşağıdaki tabloda, SQL veritabanına bağlanırken uygulamaların alabileceği çeşitli geçici hatalar listelenmektedir

### <a name="list-of-transient-fault-error-codes"></a>Geçici hata hata kodları listesi

| Hata kodu | Önem Derecesi | Açıklama |
| ---:| ---:|:--- |
| 4060 |16 |Oturum açma tarafından istenen "%. &#x2a;ls" veritabanı açılamıyor. Oturum açılamadı. Daha fazla bilgi için bkz. [hatalar 4000-4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Hizmet, isteğinizi işlerken bir hatayla karşılaştı. Lütfen tekrar deneyin. Hata kodu% d.<br/><br/>Yazılım veya donanım yükseltmeleri, donanım hataları veya diğer yük devretme sorunları nedeniyle bu hatayı alırsınız. 40197 hatası iletisi içinde gömülü hata kodu (% d), hata veya yük devretme türü hakkında ek bilgiler sağlar. Hata kodlarının bazı örnekleri 40020 40197, 40143, 40166 ve 40540 hata koduna katıştırılır.<br/><br/>Yeniden bağlanma, sizi veritabanınızın sağlıklı bir kopyasına otomatik olarak bağlar. Uygulamanız hata 40197 ' i yakalamalı, sorun giderme için ileti içinde katıştırılmış hata kodunu (% d) günlüğe kaydedin ve kaynaklar kullanılabilir olana kadar SQL veritabanı 'na yeniden bağlanmayı deneyin ve bağlantınız yeniden oluşturulur. Daha fazla bilgi için bkz. [Geçici hatalar](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Hizmet şu an meşgul. 10 saniye sonra isteği yeniden deneyin. Olay KIMLIĞI:% ls. Kod:% d. Daha fazla bilgi için bkz. <br/>&bull;&nbsp; [Mantıksal SQL Server Kaynak sınırları](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Tek veritabanları için DTU tabanlı sınırlar](service-tiers-dtu.md)<br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [tek veritabanları için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md).|
| 40613 |17 |'%. &#x2a;ls ' sunucusundaki '%. &#x2a;ls ' veritabanı şu anda kullanılamıyor. Lütfen bağlantıyı daha sonra yeniden deneyin. Sorun devam ederse, müşteri desteğine başvurun ve '%. &#x2a;ls ' öğesinin oturum izleme KIMLIĞINI sağlayın.<br/><br/> Bu hata, veritabanında zaten var olan bir ayrılmış yönetici bağlantısı (DAC) varsa meydana gelebilir. Daha fazla bilgi için bkz. [Geçici hatalar](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |İsteği işlenemiyor. İsteği işlemek için yeterli kaynak yok.<br/><br/>Hizmet şu an meşgul. Lütfen isteği daha sonra yeniden deneyin. Daha fazla bilgi için bkz. <br/>&bull;&nbsp; [Mantıksal SQL Server Kaynak sınırları](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Tek veritabanları için DTU tabanlı sınırlar](service-tiers-dtu.md)<br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [tek veritabanları için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md). |
| 49919 |16 |Oluşturma veya güncelleştirme isteği işlenemiyor. "% Ld" aboneliği için çok fazla sayıda oluşturma veya güncelleştirme işlemi sürüyor.<br/><br/>Hizmet, aboneliğiniz veya sunucunuz için birden çok oluşturma veya güncelleştirme isteğini işlemekle meşgul. İstekler Şu anda kaynak iyileştirmesi için engelleniyor. Bekleyen işlemler için sorgu [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Bekleyen oluşturma veya güncelleştirme isteklerinin tamamlanmasını bekleyin veya bekleyen isteklerinizin birini silip isteğinizi daha sonra yeniden deneyin. Daha fazla bilgi için bkz. <br/>&bull;&nbsp; [Mantıksal SQL Server Kaynak sınırları](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Tek veritabanları için DTU tabanlı sınırlar](service-tiers-dtu.md)<br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [tek veritabanları için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md). |
| 49920 |16 |İsteği işlenemiyor. "% Ld" aboneliği için çok fazla işlem devam ediyor.<br/><br/>Hizmet, bu abonelik için birden çok isteği işlemekle meşgul. İstekler Şu anda kaynak iyileştirmesi için engelleniyor. İşlem durumu için sorgu [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Bekleyen istekler tamamlanana kadar bekleyin veya bekleyen isteklerinizin birini silip isteğinizi daha sonra yeniden deneyin. Daha fazla bilgi için bkz. <br/>&bull;&nbsp; [Mantıksal SQL Server Kaynak sınırları](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Tek veritabanları için DTU tabanlı sınırlar](service-tiers-dtu.md)<br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [tek veritabanları için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md). |
| 4221 |16 |' HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING ' üzerinde uzun bekleme nedeniyle okuma-ikincil için oturum açma başarısız oldu. Çoğaltma geri dönüştürüldüğünde, uçuşdaki işlemler için satır sürümleri eksik olduğundan, çoğaltma oturum açma için kullanılamıyor. Bu sorun, birincil çoğaltmadaki etkin işlemler geri alınarak veya uygulanırken çözülebilir. Bu koşulun oluşumları, birincil üzerinde uzun yazma işlemlerinden kaçınılarak en aza indirgenebilir. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Geçici bağlantı sorunlarını giderme adımları

1. Uygulama tarafından hataların bildirildiği süre boyunca oluşan bilinen kesintiler için [Microsoft Azure hizmet panosunu](https://azure.microsoft.com/status) denetleyin.
2. Azure SQL veritabanı gibi bir bulut hizmetine bağlanan uygulamalar, düzenli olarak yeniden yapılandırma olayları ve kullanıcılara açık uygulama hataları yerine bu hataları işlemek için yeniden deneme mantığı uygulamalıdır.
3. Bir veritabanı kaynak sınırlarına yaklaşıyorsa, geçici bir bağlantı sorunu olabilir. Bkz. [kaynak sınırları](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Bağlantı sorunları devam ederse veya uygulamanızın hatayla karşılaştığı süre 60 saniye değerini aşarsa veya hatanın belirli bir gün içinde birden çok kez yinelendiğini görürseniz, [Azure](https://azure.microsoft.com/support/options) destek sitesinde **Destek Al** ' ı seçerek bir Azure destek isteği dosyası sağlayın.

#### <a name="implementing-retry-logic"></a>Yeniden deneme mantığını uygulama

İstemci programınızın kendisini düzeltmek için geçici hata süresi verdikten sonra bir bağlantıyı yeniden oluşturabilmesi için yeniden deneme mantığı olması önemle önerilir.  İlk yeniden denemeden önce 5 saniye gecikme yapmanızı öneririz. 5 saniyelik risklerden daha kısa bir gecikme sonrasında bulut hizmeti 'nin yeniden denenmesi. Sonraki her yeniden deneme için gecikme, en fazla 60 saniyeye kadar üstel olarak artar.

Yeniden deneme mantığına ait kod örnekleri için bkz.:

- [Dayanıklı bağlantısı 'i ADO.NET ile SQL 'e bağlama](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [PHP ile dayanıklı bağlantısı 'i SQL 'e bağlama](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Uygulamanızda geçici hataları işleme hakkında daha fazla bilgi için, [SQL veritabanı 'na geçici bağlantı hatalarını giderme](troubleshoot-common-connectivity-issues.md) makalesini inceleyin

ADO.NET kullanan istemciler için *engelleme süresi* hakkında bir tartışma, [bağlantı havuzu 'nda (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling)kullanılabilir.

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Sunucunuza bağlantı kurulurken ağla ilgili veya örneğe özgü bir hata oluştu

Bu sorun, uygulamanın sunucuya bağlanamamasından kaynaklanır.

Bu sorunu çözmek için, [yaygın bağlantı sorunlarını giderme adımları](#steps-to-fix-common-connection-issues) bölümündeki adımları (gösterilen sırayla) deneyin.

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Sunucu/örnek bulunamadı veya erişilebilir değil (hata 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Hata 26: belirtilen sunucu bulunurken hata oluştu

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Hata 40: sunucuya bir bağlantı açılamadı

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Hata 10053: sunucudan sonuçlar alınırken aktarım düzeyi hatası oluştu

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Bu sorunlar, uygulamanın sunucuya bağlanamamesi durumunda meydana gelir.

Bu sorunları çözmek için, [yaygın bağlantı sorunlarını giderme adımları](#steps-to-fix-common-connection-issues) bölümündeki adımları (gösterilen sırayla) deneyin.

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Güvenlik Duvarı sorunları nedeniyle sunucuya bağlanılamıyor

### <a name="error-40615-cannot-connect-to--servername-"></a>Hata 40615: < ServerName > bağlanılamıyor

Bu sorunu çözmek için [Azure Portal aracılığıyla SQL veritabanında güvenlik duvarı ayarlarını yapılandırın](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Hata 5: < ServerName > bağlanılamıyor

Bu sorunu çözmek için, bağlantı noktası 1433 ' nin, istemci ve internet arasındaki tüm güvenlik duvarlarındaki giden bağlantılar için açık olduğundan emin olun.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Sunucuda oturum açılamıyor (hatalar 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Kullanıcı ' < Kullanıcı adı > ' için oturum açma başarısız

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Bu sorunu çözmek için, geçerli bir Kullanıcı adı ve parola sağlamak üzere hizmet yöneticinize başvurun.

Genellikle, hizmet Yöneticisi oturum açma kimlik bilgilerini eklemek için aşağıdaki adımları kullanabilir:

1. SQL Server Management Studio (SSMS) kullanarak sunucuda oturum açın.
2. Oturum açma adının devre dışı olup olmadığını denetlemek için ana veritabanında aşağıdaki SQL sorgusunu çalıştırın:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins;
   ```

3. Karşılık gelen ad devre dışıysa, aşağıdaki deyimi kullanarak etkinleştirin:

   ```sql
   ALTER LOGIN <User name> ENABLE;
   ```

4. SQL oturum açma Kullanıcı adı yoksa, yeni bir SQL oturumu oluşturmak için aşağıdaki SQL sorgusunu düzenleyin ve çalıştırın:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>';
   GO
   ```

5. SSMS Nesne Gezgini 'de **veritabanları**' nı genişletin.
6. Kullanıcı iznini vermek istediğiniz veritabanını seçin.
7. **Güvenlik**' e sağ tıklayın ve ardından **Yeni**, **Kullanıcı**' yı seçin.
8. Oluşturulan betikte yer tutucuları olan aşağıdaki SQL sorgusunu düzenleyin ve çalıştırın:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>;
   GO

   -- Add user to the database owner role
   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>';
   GO
   ```

   Belirli `sp_addrolemember` kullanıcıları belirli veritabanı rollerine eşlemek için de kullanabilirsiniz.

   > [!NOTE]
   > Azure SQL veritabanı 'nda, veritabanı rolü üyeliğini yönetmek için daha yeni [alter role](/sql/t-sql/statements/alter-role-transact-sql) sözdizimini göz önünde bulundurun.  

Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda veritabanlarını ve oturum açma Işlemlerini yönetme](./logins-create-manage.md).

## <a name="connection-timeout-expired-errors"></a>Bağlantı zaman aşımı süresi doldu hataları

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): bağlantı zaman aşımı süresi doldu

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): zaman aşımı süresi doldu

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. EntityException: temeldeki sağlayıcı açık bir şekilde başarısız oldu

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>< sunucu adına bağlanılamıyor >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Bu özel durumlar, bağlantı ya da sorgu sorunları nedeniyle oluşabilir. Bu hatanın bağlantı sorunlarından kaynaklandığını onaylamak için bkz. bir hata, bir [bağlantı sorunundan kaynaklanıp kaynaklanmadığını onaylayın](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Uygulamanın sunucuya bağlanamadığı için bağlantı zaman aşımları meydana gelir. Bu sorunu çözmek için, [yaygın bağlantı sorunlarını giderme adımları](#steps-to-fix-common-connection-issues) bölümündeki adımları (gösterilen sırayla) deneyin.

## <a name="resource-governance-errors"></a>Kaynak idare hataları

### <a name="error-10928-resource-id-d"></a>Hata 10928: kaynak KIMLIĞI:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Bu sorunu geçici olarak çözmek için aşağıdaki yöntemlerden birini deneyin:

- Uzun süre çalışan sorgular olup olmadığını doğrulayın.

  > [!NOTE]
  > Bu, sorunu çözemeyebilir bir en az aList yaklaşımdır. Uzun süre çalışan veya engelleyen sorguları giderme hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı engelleme sorunlarını anlama ve çözme](understand-resolve-blocking.md).

1. Tüm engelleyici istekleri görmek için [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) görünümünü denetlemek üzere aşağıdaki SQL sorgusunu çalıştırın:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

1. [Sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dinamik yönetim işlevini ve soruna neden olan sorgunun session_id kullanarak baş engelleyicisini yönelik **giriş arabelleğini** belirleme, örneğin:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

1. Baş engelleyici sorgusunu ayarlayın.

Veritabanı, adresleme engellenme ve uzun süre çalışan sorgulara rağmen sürekli olarak sınırına ulaşırsa, daha fazla kaynak [sürümü](https://azure.microsoft.com/pricing/details/sql-database/)olan bir sürüme yükseltmeyi göz önünde bulundurun.

Veritabanı limitleri hakkında daha fazla bilgi için bkz.  [sunucular Için SQL veritabanı kaynak sınırları](./resource-limits-logical-server.md).

### <a name="error-10929-resource-id-1"></a>Hata 10929: kaynak KIMLIĞI: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Hata 40501: hizmet şu anda meşgul

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Bu bir altyapı azaltma hatası, kaynak limitlerinin aşıldığını belirten bir gösterge.

Kaynak limitleri hakkında daha fazla bilgi için bkz. [MANTıKSAL SQL Server Kaynak sınırları](./resource-limits-logical-server.md).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Hata 40544: veritabanı boyut kotasına ulaştı

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Bu hata, veritabanı boyut kotasına ulaştığında oluşur.

Aşağıdaki adımlar, sorunu geçici olarak gidermenize veya size ek seçenekler sağlamanıza yardımcı olabilir:

1. Azure portal panosunu kullanarak veritabanının geçerli boyutunu denetleyin.

   > [!NOTE]
   > Hangi tabloların en fazla alanı tükettiğini ve bu nedenle Temizleme için olası adayların olduğunu belirlemek için aşağıdaki SQL sorgusunu çalıştırın:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. Geçerli boyut, sürümünüz için desteklenen en büyük boyutu aşmazsa, MAXSıZE ayarını artırmak için ALTER DATABASE ' i kullanabilirsiniz.
3. Veritabanı, sürümünüz için desteklenen en büyük boyutu zaten aşdurmamışsa aşağıdaki adımlardan birini veya birkaçını deneyin:

   - Normal veritabanı temizleme etkinliklerini gerçekleştirin. Örneğin, kesme/silme veya SQL Server Integration Services (SSIS) veya toplu kopyalama programı (bcp) yardımcı programını kullanarak istenmeyen verileri temizleyin.
   - Verileri bölümleyin veya silin, dizinleri bırakın veya olası çözümler için belgelere başvurun.
   - Veritabanı Ölçeklendirme için bkz. [tek veritabanı kaynaklarını ölçeklendirme](./single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](./elastic-pool-scale.md).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Hata 40549: uzun süre çalışan bir işlem olduğu için oturum sonlandırıldı

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Bu hatayla sürekli olarak karşılaşırsanız, bu adımları izleyerek sorunu çözmeyi deneyin:

1. Total_elapsed_time sütunu için yüksek değere sahip tüm açık oturumları görmek için sys.dm_exec_requests görünümünü denetleyin. Aşağıdaki SQL betiğini çalıştırarak bu denetimi gerçekleştirin:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. [Sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dinamik yönetim işlevini ve soruna neden olan sorgunun session_id kullanarak baş engelleyicisini yönelik **giriş arabelleğini** belirleme, örneğin:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

3. Sorguyu ayarlayın.

    > [!Note]
    > Azure SQL veritabanı 'nda engelleme sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı engelleme sorunlarını anlama ve çözme](understand-resolve-blocking.md).

Ayrıca, sorgularınızı toplu olarak da düşünün. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](../performance-improve-use-batching.md).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Hata 40551: çok sayıda TEMPDB kullanımı nedeniyle oturum sonlandırıldı

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Bu sorunu geçici olarak çözmek için şu adımları izleyin:

1. Geçici tablo alanı kullanımını azaltmak için sorguları değiştirin.
2. Artık gerekli olmadıklarında geçici nesneleri bırakın.
3. Tabloları Kes veya kullanılmayan tabloları Kaldır.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Hata 40552: oturum, aşırı işlem günlüğü alanı kullanımı nedeniyle sonlandırıldı

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Bu sorunu çözmek için aşağıdaki yöntemleri deneyin:

- Bu sorun, ekleme, güncelleştirme veya silme işlemleri nedeniyle oluşabilir.
Toplu işlem veya birden çok daha küçük işleme bölme uygulayarak hemen üzerinde çalıştırılan satır sayısını azaltmayı deneyin.
- Bu sorun, dizin yeniden oluşturma işlemleri nedeniyle oluşabilir. Bu sorunu geçici olarak çözmek için, tabloda etkilenen satır sayısının (bayt + 80 ' de güncellenen alanın ortalama boyutu) 2 gigabayt (GB) < emin olun.

  > [!NOTE]
  > Dizin yeniden oluşturmak için, güncellenen alanın ortalama boyutu ortalama Dizin boyutu ile değiştirilmelidir.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Hata 40553: aşırı bellek kullanımı nedeniyle oturum sonlandırıldı

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Bu sorunu geçici olarak çözmek için sorguyu iyileştirmeden çalışın.

Derinlemesine bir sorun giderme yordamı için bkz. [My Query, bulutta güzel çalışıyor mu?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>Ek kaynak idare hata iletileri tablosu

| Hata kodu | Önem Derecesi | Açıklama |
| ---:| ---:|:--- |
| 10928 |20 |Kaynak KIMLIĞI:% d. Veritabanı için% s sınırı% d ve bu sınıra ulaşıldı. Daha fazla bilgi için bkz. [tek ve havuza alınmış veritabanları Için SQL veritabanı kaynak sınırları](resource-limits-logical-server.md).<br/><br/>Kaynak KIMLIĞI, sınıra ulaşan kaynağı gösterir. Çalışan iş parçacıkları için kaynak KIMLIĞI = 1. Oturumlar için kaynak KIMLIĞI = 2.<br/><br/>Bu hata ve nasıl çözüleceği hakkında daha fazla bilgi için bkz.: <br/>&bull;&nbsp; [Mantıksal SQL Server Kaynak sınırları](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Tek veritabanları için DTU tabanlı sınırlar](service-tiers-dtu.md)<br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [tek veritabanları için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md). |
| 10929 |20 |Kaynak KIMLIĞI:% d. % S en düşük garanti% d, maksimum sınır% d ve veritabanı için geçerli kullanım% d. Ancak, sunucu şu anda bu veritabanı için% d değerinden büyük istekleri desteklemeye yönelik çok meşgul. Kaynak KIMLIĞI, sınıra ulaşan kaynağı gösterir. Çalışan iş parçacıkları için kaynak KIMLIĞI = 1. Oturumlar için kaynak KIMLIĞI = 2. Daha fazla bilgi için bkz. <br/>&bull;&nbsp; [Mantıksal SQL Server Kaynak sınırları](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Tek veritabanları için DTU tabanlı sınırlar](service-tiers-dtu.md)<br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [tek veritabanları için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md). <br/>Aksi takdirde, daha sonra yeniden deneyin. |
| 40544 |20 |Veritabanı boyut kotasına ulaştı. Verileri bölümleyin veya silin, dizinleri bırakın veya olası çözümler için belgelere başvurun. Veritabanı Ölçeklendirme için bkz. [tek veritabanı kaynaklarını ölçeklendirme](single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](elastic-pool-scale.md).|
| 40549 |16 |Uzun süre çalışan bir işlem olduğu için oturum sonlandırıldı. İşleminizi kısaltmayı deneyin. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](../performance-improve-use-batching.md).|
| 40550 |16 |Oturum çok fazla kilit elde ettiğinden sonlandırıldı. Tek bir işlemde daha az sayıda satır okumayı veya değiştirmeyi deneyin. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](../performance-improve-use-batching.md).|
| 40551 |16 |Aşırı kullanım nedeniyle oturum sonlandırıldı `TEMPDB` . Geçici tablo alanı kullanımını azaltmak için sorgunuzu değiştirmeyi deneyin.<br/><br/>Geçici nesneler kullanıyorsanız, `TEMPDB` geçici nesneleri oturum için artık gerekli olmadıklarında bırakarak veritabanında alandan tasarruf edin. SQL veritabanı 'nda tempdb kullanımı hakkında daha fazla bilgi için bkz. [SQL veritabanı 'Nda tempdb veritabanı](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Aşırı işlem günlüğü alanı kullanımı nedeniyle oturum sonlandırıldı. Tek bir işlemde daha az sayıda satır değiştirmeyi deneyin. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](../performance-improve-use-batching.md).<br/><br/>Yardımcı programını veya sınıfını kullanarak toplu eklemeler yaparsanız, `bcp.exe` `System.Data.SqlClient.SqlBulkCopy` her bir `-b batchsize` `BatchSize` işlemde sunucuya kopyalanmış satır sayısını sınırlamak için veya seçeneklerini kullanmayı deneyin. İfadesiyle bir dizini `ALTER INDEX` yeniden oluşturuyorsanız seçeneğini kullanmayı deneyin `REBUILD WITH ONLINE = ON` . Sanal çekirdek satın alma modeli için işlem günlüğü boyutları hakkında bilgi için bkz.: <br/>&bull;&nbsp; [tek veritabanları için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL yönetilen örnek kaynak sınırları](../managed-instance/resource-limits.md).|
| 40553 |16 |Aşırı bellek kullanımı nedeniyle oturum sonlandırıldı. Daha az satır işlemek için sorgunuzu değiştirmeyi deneyin.<br/><br/>`ORDER BY` `GROUP BY` Transact-SQL kodunuzda ve işlem sayısını azaltmak sorgunuzun bellek gereksinimlerini azaltır. Veritabanı Ölçeklendirme için bkz. [tek veritabanı kaynaklarını ölçeklendirme](single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Elastik havuz hataları

Aşağıdaki hatalar elastik havuzlar oluşturma ve kullanmayla ilgilidir:

| Hata kodu | Önem Derecesi | Açıklama | Düzeltici eylem |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Elastik havuz, depolama sınırına ulaştı. Elastik havuzun depolama alanı kullanımı (% d) MB/s değerini aşamaz. Elastik havuzun depolama sınırına ulaşıldığında veritabanına veri yazmaya çalışılıyor. Kaynak limitleri hakkında bilgi için bkz.: <br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md). <br/> |Depolama sınırını artırmak, elastik havuzdaki ayrı veritabanları tarafından kullanılan depolamayı azaltmak veya elastik havuzdan veritabanlarını kaldırmak için mümkünse, depolama alanı sayısını ve/veya depolama alanını esnek havuza eklemeyi düşünün. Elastik havuz ölçekleme için bkz. [elastik havuz kaynaklarını ölçeklendirme](elastic-pool-scale.md). Kullanılmayan alanı veritabanlarından kaldırma hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda veritabanları için dosya alanını yönetme](file-space-manage.md).|
| 10929 | 16 |% S en düşük garanti% d, maksimum sınır% d ve veritabanı için geçerli kullanım% d. Ancak, sunucu şu anda bu veritabanı için% d değerinden büyük istekleri desteklemeye yönelik çok meşgul. Kaynak limitleri hakkında bilgi için bkz.: <br/>&bull;&nbsp; [Elastik havuzlar için DTU tabanlı sınırlar](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [elastik havuzlar için sanal çekirdek tabanlı sınırlar](resource-limits-vcore-elastic-pools.md). <br/> Aksi takdirde, daha sonra yeniden deneyin. Veritabanı başına DTU/sanal çekirdek en az; Veritabanı başına DTU/sanal çekirdek maks. Elastik havuzdaki tüm veritabanları genelinde eş zamanlı çalışan (istek) toplam sayısı havuz sınırını aşmaya çalıştı. |Çalışan sınırını artırmak veya elastik havuzdan veritabanlarını kaldırmak için mümkünse, elastik havuzun DTU 'ları veya sanal çekirdekleri artırmayı düşünün. |
| 40844 | 16 |'% Ls ' sunucusundaki '% ls ' veritabanı, elastik havuzdaki bir '% ls ' sürüm veritabanıdır ve sürekli bir kopyalama ilişkisine sahip olamaz.  |Yok |
| 40857 | 16 |Sunucu: '% ls ', elastik havuz adı: '% ls ' için elastik havuz bulunamadı. Belirtilen elastik havuz belirtilen sunucuda yok. | Geçerli bir elastik havuz adı sağlayın. |
| 40858 | 16 |'% Ls ' esnek havuzu sunucuda zaten var: '% ls '. Belirtilen elastik havuz belirtilen sunucuda zaten var. | Yeni elastik havuz adı sağlayın. |
| 40859 | 16 |Elastik havuz, '% ls ' hizmet katmanını desteklemiyor. Belirtilen hizmet katmanı elastik havuz sağlama için desteklenmiyor. |Varsayılan hizmet katmanını kullanmak için doğru sürümü sağlayın veya hizmet katmanını boş bırakın. |
| 40860 | 16 |Elastik havuz '% ls ' ve hizmet hedefi '% ls ' birleşimi geçersiz. Elastik havuz ve hizmet katmanı yalnızca kaynak türü ' Elakpool ' olarak belirtilmişse birlikte belirtilebilir. |Elastik havuz ve hizmet katmanının doğru birleşimini belirtin. |
| 40861 | 16 |Veritabanı sürümü '%. *ls ', '% ' olan esnek havuz hizmeti katmanından farklı olamaz.* ls '. Veritabanı sürümü elastik havuz hizmeti katmanından farklı. |Elastik havuz hizmeti katmanından farklı bir veritabanı sürümü belirtmeyin.  Veritabanı sürümünün belirtilmesi gerekmediğini unutmayın. |
| 40862 | 16 |Elastik havuz hizmeti hedefi belirtilmişse elastik havuz adı belirtilmelidir. Elastik havuz hizmeti hedefi, elastik havuzu benzersiz bir şekilde tanımlamaz. |Elastik havuz hizmeti hedefini kullanıyorsanız elastik havuz adını belirtin. |
| 40864 | 16 |Elastik havuz için DTU 'Lar '%. * ls ' hizmet katmanı için en az (% d) DTU olmalıdır. Elastik havuz için en düşük sınırın altındaki DTU 'Lar ayarlamaya çalışılıyor. |Elastik havuz için DTU 'ları en azından minimum sınıra ayarlamayı yeniden deneyin. |
| 40865 | 16 |Elastik havuz için DTU 'Lar, '%. * ls ' hizmet katmanının DTU değerini (% d) aşamaz. En yüksek sınırın üzerinde esnek havuz için DTU 'Lar ayarlamaya çalışılıyor. |Elastik havuz için DTU 'ları en yüksek sınırdan büyük olmayacak şekilde ayarlamayı yeniden deneyin. |
| 40867 | 16 |Veritabanı başına DTU Max, hizmet katmanı '%. * ls ' için en az (% d) olmalıdır. Desteklenen sınırın altında veritabanı başına DTU en fazla ayarı yapılmaya çalışılıyor. | İstenen ayarı destekleyen elastik havuz hizmet katmanını kullanmayı düşünün. |
| 40868 | 16 |Veritabanı başına DTU en yüksek değeri, '%. * ls ' hizmet katmanı için (% d) değerini aşamaz. Desteklenen sınırın ötesinde veritabanı başına DTU en fazla ayarı yapılmaya çalışılıyor. | İstenen ayarı destekleyen elastik havuz hizmet katmanını kullanmayı düşünün. |
| 40870 | 16 |Veritabanı başına DTU en düşük değeri, '%. * ls ' hizmet katmanı için (% d) değerini aşamaz. Veritabanı başına desteklenen sınırın ötesinde DTU en düşük değeri ayarlamaya çalışılıyor. | İstenen ayarı destekleyen elastik havuz hizmet katmanını kullanmayı düşünün. |
| 40873 | 16 |Veritabanı başına veritabanı (% d) ve DTU min sayısı (% d), elastik havuzun (% d) DTU sayısını aşamaz. Esnek havuzdaki veritabanları için en düşük değeri belirleme girişimi, elastik havuzun DTU 'ları aşıyor. | Elastik havuzun DTU 'larının arttırmasını veya veritabanı başına DTU boyutunu azaltmayı veya elastik havuzdaki veritabanlarının sayısını azaltmayı düşünün. |
| 40877 | 16 |Elastik havuz herhangi bir veritabanı içermediği takdirde silinemez. Elastik havuz bir veya daha fazla veritabanı içeriyor ve bu nedenle silinemez. |Veritabanlarını silmek için elastik havuzdan kaldırın. |
| 40881 | 16 |'%. * Ls ' esnek havuzu, veritabanı sayısı sınırına ulaştı.  Elastik havuz için veritabanı sayısı sınırı (% d) DTU 'lar (% d) ile esnek havuz için (% d) değerini aşamaz. Elastik havuzun veritabanı sayısı sınırına ulaşıldığında, elastik havuzda veritabanı oluşturulmaya veya eklemeye çalışılıyor. | Veritabanı sınırını artırmak veya elastik havuzdan veritabanlarını kaldırmak için mümkünse elastik havuzun DTU 'larının artırılmasını göz önünde bulundurun. |
| 40889 | 16 |'%. * Ls ' esnek havuzu için DTU 'Lar veya depolama sınırı, veritabanları için yeterli depolama alanı sağlamayacağından azaltılabilir. Elastik havuzun depolama sınırının altında depolama alanı kullanımını azaltma girişimi. | Esnek havuzda ayrı veritabanlarının depolama kullanımını azaltmayı düşünün veya DTU 'ları veya depolama sınırını azaltmak için havuzdan veritabanlarını kaldırın. |
| 40891 | 16 |Veritabanı başına DTU en az (% d), veritabanı başına en fazla DTU sayısını (% d) aşamaz. Veritabanı başına DTU en yüksek değerini veritabanı başına ayarlamaya çalışılıyor. |Veritabanları başına DTU en düşük değeri, veritabanı başına en fazla DTU sayısını aşmadığından emin olun. |
| TBD | 16 |Elastik havuzdaki tek bir veritabanı için depolama boyutu, '%. * ls ' hizmet katmanı elastik havuzu tarafından izin verilen en büyük boyutu aşamaz. Veritabanı için en büyük boyut, elastik havuz hizmeti katmanının izin verdiği en büyük boyutu aşıyor. |En büyük veritabanı boyutunu elastik havuz hizmeti katmanının izin verdiği en büyük boyut limitlerinin sınırları içinde ayarlayın. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Oturum açma tarafından istenen "ana" veritabanı açılamıyor. Oturum açılamadı

Bu sorun, hesabın ana veritabanına erişim izni olmadığı için oluşur. Ancak, SQL Server Management Studio (SSMS), ana veritabanına bağlanmayı dener.

Bu sorunu çözmek için şu adımları izleyin:

1. SSMS oturum açma ekranında **Seçenekler**' i ve ardından **bağlantı özellikleri**' ni seçin.
2. **Veritabanına Bağlan** alanına, kullanıcının varsayılan veritabanı adını varsayılan oturum açma veritabanı olarak girin ve sonra **Bağlan**' ı seçin.

   ![Bağlantı özellikleri](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Bir bağlantı sorunundan kaynaklanan bir hata olup olmadığını onaylayın

Bir bağlantı sorunundan kaynaklanan bir hata olup olmadığını doğrulamak için, aşağıdaki gibi bir bağlantıyı açmaya yönelik çağrıları gösteren çerçeveler için yığın izlemesini gözden geçirin ( **SqlConnection** sınıfına yönelik başvuruya göz önünde bulunur):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Özel durum sorgu sorunları tarafından tetiklendiğinde, aşağıdakine benzer bir çağrı yığını görürsünüz ( **SqlCommand** sınıfının başvurusunu unutmayın). Bu durumda, [sorgularınızı ayarlayın](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

İnce ayar performansı hakkında ek yönergeler için aşağıdaki kaynaklara bakın:

- [Azure SQL dizinlerini ve istatistiklerini koruma](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Azure SQL veritabanı 'nda el ile ayarlama sorgusu performansı](./performance-guidance.md)
- [Dinamik yönetim görünümlerini kullanarak performans Azure SQL veritabanı 'nı izleme](./monitoring-with-dmvs.md)
- [Azure SQL veritabanı 'nda sorgu deposunu çalıştırma](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>Yaygın bağlantı sorunlarını giderme adımları

1. TCP/IP 'nin uygulama sunucusunda bir istemci protokolü olarak etkinleştirildiğinden emin olun. Daha fazla bilgi için bkz. [istemci protokollerini yapılandırma](/sql/database-engine/configure-windows/configure-client-protocols). SQL araçları yüklü olmayan uygulama sunucularında, **cliconfg.exe** (SQL Server istemci ağ yardımcı programı) çalıştırarak TCP/IP 'nin etkinleştirildiğini doğrulayın.
2. Doğru yapılandırıldığından emin olmak için uygulamanın bağlantı dizesini denetleyin. Örneğin, bağlantı dizesinin doğru bağlantı noktasını (1433) ve tam sunucu adını belirttiğinden emin olun.
Bkz. [bağlantı bilgilerini al](./connect-query-ssms.md#get-server-connection-information).
3. Bağlantı zaman aşımı değerini artırmayı deneyin. En az 30 saniyelik bir bağlantı zaman aşımı kullanmanızı öneririz.
4. [SQL Server Management Studio (SSMS)](./connect-query-ssms.md), bir UDL dosyası, ping veya Telnet kullanarak uygulama sunucusu Ile Azure SQL veritabanı arasındaki bağlantıyı test edin. Daha fazla bilgi için bkz. bağlantı sorunlarını [giderme](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) ve [bağlantı sorunları için tanılama](./troubleshoot-common-connectivity-issues.md#diagnostics).

   > [!NOTE]
   > Sorun giderme adımı olarak, farklı bir istemci bilgisayardaki bağlantıyı da sınayabilirsiniz.

5. En iyi uygulama olarak, yeniden deneme mantığının yerinde olduğundan emin olun. Yeniden deneme mantığı hakkında daha fazla bilgi için bkz. [SQL veritabanında geçici hata ve bağlantı hatalarını giderme](./troubleshoot-common-connectivity-issues.md).

Bu adımlar sorununuzu gidermezse daha fazla veri toplamayı deneyin ve desteğe başvurun. Uygulamanız bir bulut hizmeti ise günlüğe kaydetmeyi etkinleştirin. Bu adım, hatanın UTC zaman damgasını döndürür. Ayrıca, SQL veritabanı izleme KIMLIĞINI döndürür. [Microsoft Müşteri Destek Hizmetleri](https://azure.microsoft.com/support/options/) , bu bilgileri kullanabilir.

Günlüğe kaydetmenin nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Azure App Service uygulamalar için tanılama günlüğünü etkinleştirme](../../app-service/troubleshoot-diagnostic-logs.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL Veritabanı bağlantı mimarisi](./connectivity-architecture.md)
- [Azure SQL veritabanı ve Azure SYNAPSE Analytics ağ erişim denetimleri](./network-access-controls-overview.md)
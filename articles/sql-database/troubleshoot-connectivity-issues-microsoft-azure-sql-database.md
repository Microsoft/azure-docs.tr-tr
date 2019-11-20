---
title: Azure SQL veritabanı 'na bağlanma ve kullanma sorunları
description: Azure SQL veritabanı bağlantı sorunlarını giderme ve diğer SQL veritabanına özel sorunları çözme adımlarını sağlar
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: v-miegge
ms.author: ramakoni
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 0bd018d90f4ca2c64df56d27eebdc6c9160309ac
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082417"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Microsoft Azure SQL Veritabanı bağlantı sorunlarını ve diğer hataları giderme

Azure SQL Veritabanına bağlantı başarısız olduğunda hata iletileri alırsınız. Bu bağlantı sorunlarına Azure SQL veritabanı yeniden yapılandırması, güvenlik duvarı ayarları, bağlantı zaman aşımı veya yanlış oturum açma bilgileri neden olabilir. Ayrıca, bazı Azure SQL veritabanı kaynaklarına yönelik maksimum sınıra ulaşıldığında Azure SQL veritabanı 'na bağlanamazsınız.

## <a name="transient-fault-error-messages"></a>Geçici hata hatası iletileri

Azure altyapısının SQL Veritabanı hizmetinde ağır iş yükleri ortaya çıktığında sunucuları dinamik olarak yeniden yapılandırabilme özelliği vardır.  Bu dinamik davranış istemci programınızın SQL Veritabanı bağlantısını kaybetmesine neden olabilir. Bu tür bir hata koşuluna *geçici*bir hata denir. İstemci programınızın kendisini düzeltmek için geçici hata süresi verdikten sonra bir bağlantıyı yeniden oluşturabilmesi için yeniden deneme mantığı olması önemle önerilir.  İlk yeniden denemeden önce 5 saniye gecikme yapmanızı öneririz. 5 saniyelik riskden daha kısa bir gecikme sonrasında bulut hizmeti 'nin yeniden denenmesi. Sonraki her yeniden deneme için gecikme, en fazla 60 saniyeye kadar üstel olarak artar.

Yeniden deneme mantığına ait kod örnekleri için bkz.:

* [SQL veritabanı ve SQL Server için bağlantı kitaplıkları](sql-database-libraries.md)
* [SQL veritabanı 'nda bağlantı hatalarını ve geçici hataları gidermeye yönelik eylemler](sql-database-connectivity-issues.md)

> [!TIP]
> Aşağıdaki bölümlerde ele alınan sorunları çözmek için, [genel bağlantı sorunlarını giderme adımları](#steps-to-fix-common-connection-issues) bölümündeki adımları (gösterilen sırada) deneyin.

### <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Hata 40613: sunucu < y > üzerinde < x > veritabanı şu anda kullanılamıyor

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

Bu sorunu çözmek için:

1. Bilinen kesintiler için [Microsoft Azure hizmeti panosunu](https://status.azure.com/status) denetleyin.
2. Bilinen kesintiler yoksa, destek talebi açmak için [Microsoft Azure Destek Web sitesine](https://azure.microsoft.com/support/options) gidin.

Daha fazla bilgi için bkz. ["sunucudaki veritabanı şu anda kullanılamıyor" hatası](sql-database-troubleshoot-common-connection-issues.md#troubleshoot-transient-errors).

### <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>SQL veritabanı sunucusu bağlantısı kurulurken ağla ilgili veya örneğe özgü bir hata oluştu

Bu sorun, uygulamanın sunucuya bağlanamamasından kaynaklanır.

Bu sorunu çözmek için, [yaygın bağlantı sorunlarını giderme adımları](#steps-to-fix-common-connection-issues) bölümündeki adımları (gösterilen sırayla) deneyin.

### <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Sunucu/örnek bulunamadı veya erişilebilir değil (hata 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Hata 26: belirtilen sunucu bulunurken hata oluştu

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Hata 40: sunucuya bir bağlantı açılamadı

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Hata 10053: sunucudan sonuçlar alınırken aktarım düzeyi hatası oluştu

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

#### <a name="cannot-connect-to-a-secondary-database"></a>İkincil veritabanına bağlanılamıyor

Veritabanı yeniden yapılandırma sürecinde olduğundan ve birincil veritabanındaki etkin bir işlemin ortasında yeni sayfalar uygulanırken, ikincil veritabanına bağlantı girişimi başarısız oldu.

#### <a name="adonet-and-blocking-period"></a>ADO.NET ve engelleme süresi

[SQL Server bağlantı havuzda (ADO.net)](https://msdn.microsoft.com/library/8xx3tyca.aspx), ADO.NET kullanan istemciler için *engelleme süresi* hakkında bir tartışma vardır.

### <a name="list-of-transient-fault-error-codes"></a>Geçici hata hata kodları listesi

Aşağıdaki hatalar geçicidir ve uygulama mantığındaki yeniden denenmelidir:

| Hata kodu | Severity | Açıklama |
| ---:| ---:|:--- |
| 4060 |16 |Veritabanı açılamıyor. "%.&#x2a;ls" oturum açma tarafından istenen. Oturum açılamadı. Daha fazla bilgi için bkz. [hatalar 4000-4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Hizmet, isteğinizi işlerken bir hatayla karşılaştı. Lütfen yeniden deneyin. Hata kodu% d.<br/><br/>Yazılım veya donanım yükseltmeleri, donanım hataları veya diğer yük devretme sorunları nedeniyle bu hatayı alırsınız. # 40197 hatası iletisi içinde gömülü hata kodu (% d), hata veya yük devretme türü hakkında ek bilgiler sağlar. Hata kodlarının bazı örnekleri 40020 40197, 40143, 40166 ve 40540 hata koduna katıştırılır.<br/><br/>SQL veritabanı sunucunuza yeniden bağlanmak veritabanınızın sağlıklı bir kopyasına otomatik olarak bağlanır. Uygulamanız hata 40197 ' i yakalamalı, sorun giderme için ileti içinde katıştırılmış hata kodunu (% d) günlüğe kaydedin ve kaynaklar kullanılabilir olana kadar SQL veritabanı 'na yeniden bağlanmayı deneyin ve bağlantınız yeniden oluşturulur. Daha fazla bilgi için bkz. [geçici hatalar](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Hizmet şu anda meşgul. 10 saniye sonra isteği yeniden deneyin. Olay KIMLIĞI:% ls. Kod:% d. Daha fazla bilgi için bkz. <br/>&bull; &nbsp;[veritabanı sunucusu kaynak sınırları](sql-database-resource-limits-database-server.md)<br/>[tek veritabanları Için DTU tabanlı limitleri](sql-database-service-tiers-dtu.md) &bull; &nbsp;<br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[tek veritabanları için &nbsp;sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-single-databases.md) &bull;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>&bull; &nbsp;[yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Veritabanı '%.&#x2a;ls' sunucusundaki '%.&#x2a;ls' şu anda kullanılamıyor. Lütfen bağlantıyı daha sonra yeniden deneyin. Sorun devam ederse müşteri desteğine başvurun ve oturum izleme Kimliğini verin '%.&#x2a;ls'.<br/><br/> Bu hata, veritabanında zaten var olan bir ayrılmış yönetici bağlantısı (DAC) varsa meydana gelebilir. Daha fazla bilgi için bkz. [geçici hatalar](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |İsteği işlenemiyor. İsteği işlemek için yeterli kaynak yok.<br/><br/>Hizmet şu anda meşgul. Lütfen isteği daha sonra yeniden deneyin. Daha fazla bilgi için bkz. <br/>&bull; &nbsp;[veritabanı sunucusu kaynak sınırları](sql-database-resource-limits-database-server.md)<br/>[tek veritabanları Için DTU tabanlı limitleri](sql-database-service-tiers-dtu.md) &bull; &nbsp;<br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[tek veritabanları için &nbsp;sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-single-databases.md) &bull;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>&bull; &nbsp;[yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Oluşturma veya güncelleştirme isteği işlenemiyor. "% Ld" aboneliği için çok fazla sayıda oluşturma veya güncelleştirme işlemi sürüyor.<br/><br/>Hizmet, aboneliğiniz veya sunucunuz için birden çok oluşturma veya güncelleştirme isteğini işlemekle meşgul. İstekler Şu anda kaynak iyileştirmesi için engelleniyor. Bekleyen işlemler için [sys. dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) sorgulayın. Bekleyen oluşturma veya güncelleştirme isteklerinin tamamlanmasını bekleyin veya bekleyen isteklerinizin birini silip isteğinizi daha sonra yeniden deneyin. Daha fazla bilgi için bkz. <br/>&bull; &nbsp;[veritabanı sunucusu kaynak sınırları](sql-database-resource-limits-database-server.md)<br/>[tek veritabanları Için DTU tabanlı limitleri](sql-database-service-tiers-dtu.md) &bull; &nbsp;<br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[tek veritabanları için &nbsp;sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-single-databases.md) &bull;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>&bull; &nbsp;[yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |İsteği işlenemiyor. "% Ld" aboneliği için çok fazla işlem devam ediyor.<br/><br/>Hizmet, bu abonelik için birden çok isteği işlemekle meşgul. İstekler Şu anda kaynak iyileştirmesi için engelleniyor. İşlem durumu için [sys. dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) sorgula. Bekleyen istekler tamamlanana kadar bekleyin veya bekleyen isteklerinizin birini silip isteğinizi daha sonra yeniden deneyin. Daha fazla bilgi için bkz. <br/>&bull; &nbsp;[veritabanı sunucusu kaynak sınırları](sql-database-resource-limits-database-server.md)<br/>[tek veritabanları Için DTU tabanlı limitleri](sql-database-service-tiers-dtu.md) &bull; &nbsp;<br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[tek veritabanları için &nbsp;sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-single-databases.md) &bull;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>&bull; &nbsp;[yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |' HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING ' üzerinde uzun bekleme nedeniyle okuma-ikincil için oturum açma başarısız oldu. Çoğaltma geri dönüştürüldüğünde, uçuşdaki işlemler için satır sürümleri eksik olduğundan, çoğaltma oturum açma için kullanılamıyor. Bu sorun, birincil çoğaltmadaki etkin işlemler geri alınarak veya uygulanırken çözülebilir. Bu koşulun oluşumları, birincil üzerinde uzun yazma işlemlerinden kaçınılarak en aza indirgenebilir. |

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Güvenlik Duvarı sorunları nedeniyle sunucuya bağlanılamıyor

### <a name="error-40615-cannot-connect-to--servername-"></a>Hata 40615: < ServerName > bağlanılamıyor

Bu sorunu çözmek için [Azure Portal aracılığıyla SQL veritabanında güvenlik duvarı ayarlarını yapılandırın.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Hata 5: < ServerName > bağlanılamıyor

Bu sorunu çözmek için, bağlantı noktası 1433 ' nin, istemci ve internet arasındaki tüm güvenlik duvarlarındaki giden bağlantılar için açık olduğundan emin olun.

Daha fazla bilgi için bkz. [Windows güvenlik duvarını SQL Server erişimine izin verecek şekilde yapılandırma](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Sunucuda oturum açılamıyor (hatalar 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Kullanıcı ' < Kullanıcı adı > ' için oturum açma başarısız

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Bu sorunu çözmek için, geçerli bir SQL Server Kullanıcı adı ve parola sağlamak üzere hizmet yöneticinize başvurun.

Genellikle, hizmet Yöneticisi oturum açma kimlik bilgilerini eklemek için aşağıdaki adımları kullanabilir:

1. SQL Server Management Studio (SSMS) kullanarak sunucuda oturum açın.
2. Oturum açma adının devre dışı olup olmadığını denetlemek için aşağıdaki SQL sorgusunu çalıştırın:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Karşılık gelen ad devre dışıysa, aşağıdaki deyimi kullanarak etkinleştirin:

   ```sql
   Alter login <User name> enable
   ```

4. SQL oturum açma Kullanıcı adı yoksa, aşağıdaki adımları izleyerek oluşturun:

   1. SSMS 'de **güvenlik** ' e çift tıklayarak genişletin.
   2. **Oturumlar**’a sağ tıklayıp **Yeni oturum açma**’yı seçin.
   3. Oluşturulan betikte yer tutucuları olan aşağıdaki SQL sorgusunu düzenleyin ve çalıştırın:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. **Veritabanı**’na çift tıklayın.
6. Kullanıcı iznini vermek istediğiniz veritabanını seçin.
7. **Güvenlik**’e çift tıklayın.
8. **Kullanıcılar**’a sağ tıklayıp **Yeni Kullanıcı**’yı seçin.
9. Oluşturulan betikte yer tutucuları olan aşağıdaki SQL sorgusunu düzenleyin ve çalıştırın:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Belirli kullanıcıları belirli veritabanı rollerine eşlemek için `sp_addrolemember` de kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure SQL veritabanı 'nda veritabanlarını ve oturum açma Işlemlerini yönetme](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Bağlantı zaman aşımı süresi doldu hataları

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): bağlantı zaman aşımı süresi doldu

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): zaman aşımı süresi doldu

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. EntityException: temeldeki sağlayıcı açık bir şekilde başarısız oldu

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>< Sunucu adına bağlanılamıyor >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Bu özel durumlar, bağlantı ya da sorgu sorunları nedeniyle oluşabilir. Bu hatanın bağlantı sorunlarından kaynaklandığını onaylamak için bkz. bir hata, bir [bağlantı sorunundan kaynaklanıp kaynaklanmadığını onaylayın](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Uygulamanın sunucuya bağlanamadığı için bağlantı zaman aşımları meydana gelir. Bu sorunu çözmek için, [yaygın bağlantı sorunlarını giderme adımları](#steps-to-fix-common-connection-issues) bölümündeki adımları (gösterilen sırayla) deneyin.

## <a name="transient-errors-errors-40197-40545"></a>Geçici hatalar (hatalar 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Hata 40197: hizmet isteğinizi işlerken bir hatayla karşılaştı. Lütfen yeniden deneyin. Kod < hata kodu >

Arka uçta yeniden yapılandırma veya yük devretme sırasında karşılaşılan geçici bir hata nedeniyle bu sorun oluşur.

Bu sorunu çözmek için kısa bir süre bekleyip yeniden deneyin. Sorun devam etmediği takdirde destek çalışması gerekmez.

En iyi uygulama olarak, yeniden deneme mantığının yerinde olduğundan emin olun. Yeniden deneme mantığı hakkında daha fazla bilgi için bkz. [SQL veritabanında geçici hata ve bağlantı hatalarını giderme](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="resource-governance-errors"></a>Kaynak idare hataları

### <a name="error-10928-resource-id-d"></a>Hata 10928: kaynak KIMLIĞI:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Bu sorunu geçici olarak çözmek için aşağıdaki yöntemlerden birini deneyin:

* Uzun süre çalışan sorgular olup olmadığını doğrulayın.

  > [!NOTE]
  > Bu, sorunu çözemeyebilir bir en az aList yaklaşımdır.

1. Tüm engelleyici istekleri görmek için [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) görünümünü denetlemek IÇIN aşağıdaki SQL sorgusunu çalıştırın:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Baş engelleyicisinin **giriş arabelleğini** belirleme.
3. Baş engelleyici sorgusunu ayarlayın.

   Derinlemesine bir sorun giderme yordamı için bkz. [My Query, bulutta güzel çalışıyor mu?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

Veritabanı, adresleme ve uzun süre çalışan sorgulara rağmen sürekli olarak sınırına ulaşırsa, daha fazla kaynak [sürümü](https://azure.microsoft.com/pricing/details/sql-database/)olan bir sürüme yükseltmeyi göz önünde bulundurun.

Dinamik yönetim görünümleri hakkında daha fazla bilgi için bkz. [sistem dinamik yönetim görünümleri](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Veritabanı limitleri hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı sunucusu Için SQL veritabanı kaynak sınırları](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Hata 10929: kaynak KIMLIĞI: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Hata 40501: hizmet şu anda meşgul

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Bu bir altyapı azaltma hatası, kaynak limitlerinin aşıldığını belirten bir gösterge.

Kaynak limitleri hakkında daha fazla bilgi için bkz. [veritabanı sunucusu kaynak sınırları](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Hata 40544: veritabanı boyut kotasına ulaştı

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Bu hata, veritabanı boyut kotasına ulaştığında oluşur.

Aşağıdaki adımlar, sorunu geçici olarak gidermenize veya size ek seçenekler sağlamanıza yardımcı olabilir:

1. Azure portal panosunu kullanarak veritabanının geçerli boyutunu denetleyin.

   > [!NOTE]
   > Hangi tabloların en fazla alanı tükettiğini ve bu nedenle Temizleme için olası adayların olduğunu belirlemek için aşağıdaki SQL sorgusunu çalıştırın:

   ```sql
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Geçerli boyut, sürümünüz için desteklenen en büyük boyutu aşmazsa, MAXSıZE ayarını artırmak için ALTER DATABASE ' i kullanabilirsiniz.
3. Veritabanı, sürümünüz için desteklenen en büyük boyutu zaten aşdurmamışsa aşağıdaki adımlardan birini veya birkaçını deneyin:

   * Normal veritabanı temizleme etkinliklerini gerçekleştirin. Örneğin, kesme/silme veya SQL Server Integration Services (SSIS) veya toplu kopyalama programı (bcp) yardımcı programını kullanarak istenmeyen verileri temizleyin.
   * Verileri bölümleyin veya silin, dizinleri bırakın veya olası çözümler için belgelere başvurun.
   * Veritabanı Ölçeklendirme için bkz. [tek veritabanı kaynaklarını ölçeklendirme](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) ve [elastik havuz kaynaklarını ölçeklendirme](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Hata 40549: uzun süre çalışan bir işlem olduğu için oturum sonlandırıldı

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Bu hatayla sürekli olarak karşılaşırsanız, bu adımları izleyerek sorunu çözmeyi deneyin:

1. Total_elapsed_time sütunu için yüksek değere sahip tüm açık oturumları görmek için sys. dm_exec_requests görünümünü denetleyin. Aşağıdaki SQL betiğini çalıştırarak bu denetimi gerçekleştirin:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Uzun süre çalışan sorgunun giriş arabelleğini belirleme.
3. Sorguyu ayarlayın.

Ayrıca, sorgularınızı toplu olarak da düşünün. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Derinlemesine bir sorun giderme yordamı için bkz. [My Query, bulutta güzel çalışıyor mu?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Hata 40551: çok sayıda TEMPDB kullanımı nedeniyle oturum sonlandırıldı

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Bu sorunu geçici olarak çözmek için şu adımları izleyin:

1. Geçici tablo alanı kullanımını azaltmak için sorguları değiştirin.
2. Artık gerekli olmadıklarında geçici nesneleri bırakın.
3. Tabloları Kes veya kullanılmayan tabloları Kaldır.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Hata 40552: oturum, aşırı işlem günlüğü alanı kullanımı nedeniyle sonlandırıldı

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Bu sorunu çözmek için aşağıdaki yöntemleri deneyin:

* Bu sorun, ekleme, güncelleştirme veya silme işlemleri nedeniyle oluşabilir.
Toplu işlem veya birden çok daha küçük işleme bölme uygulayarak hemen üzerinde çalıştırılan satır sayısını azaltmayı deneyin.
* Bu sorun, dizin yeniden oluşturma işlemleri nedeniyle oluşabilir. Bu sorunu geçici olarak çözmek için, tabloda etkilenen satır sayısının (bayt + 80 ' de güncellenen alanın ortalama boyutu) 2 gigabayt (GB) < emin olun.

  > [!NOTE]
  > Dizin yeniden oluşturmak için, güncellenen alanın ortalama boyutu ortalama Dizin boyutu ile değiştirilmelidir.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Hata 40553: aşırı bellek kullanımı nedeniyle oturum sonlandırıldı

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Bu sorunu geçici olarak çözmek için sorguyu iyileştirmeden çalışın.

Derinlemesine bir sorun giderme yordamı için bkz. [My Query, bulutta güzel çalışıyor mu?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="table-of-additional-resource-governance-error-messages"></a>Ek kaynak idare hata iletileri tablosu

| Hata kodu | Severity | Açıklama |
| ---:| ---:|:--- |
| 10928 |20 |Kaynak KIMLIĞI:% d. Veritabanı için% s sınırı% d ve bu sınıra ulaşıldı. Daha fazla bilgi için bkz. [tek ve havuza alınmış veritabanları Için SQL veritabanı kaynak sınırları](sql-database-resource-limits-database-server.md).<br/><br/>Kaynak KIMLIĞI, sınıra ulaşan kaynağı gösterir. Çalışan iş parçacıkları için kaynak KIMLIĞI = 1. Oturumlar için kaynak KIMLIĞI = 2.<br/><br/>Bu hata ve nasıl çözüleceği hakkında daha fazla bilgi için bkz.: <br/>&bull; &nbsp;[veritabanı sunucusu kaynak sınırları](sql-database-resource-limits-database-server.md)<br/>[tek veritabanları Için DTU tabanlı limitleri](sql-database-service-tiers-dtu.md) &bull; &nbsp;<br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[tek veritabanları için &nbsp;sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-single-databases.md) &bull;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>&bull; &nbsp;[yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Kaynak KIMLIĞI:% d. % S en düşük garanti% d, maksimum sınır% d ve veritabanı için geçerli kullanım% d. Ancak, sunucu şu anda bu veritabanı için% d değerinden büyük istekleri desteklemeye yönelik çok meşgul. Kaynak KIMLIĞI, sınıra ulaşan kaynağı gösterir. Çalışan iş parçacıkları için kaynak KIMLIĞI = 1. Oturumlar için kaynak KIMLIĞI = 2. Daha fazla bilgi için bkz. <br/>&bull; &nbsp;[veritabanı sunucusu kaynak sınırları](sql-database-resource-limits-database-server.md)<br/>[tek veritabanları Için DTU tabanlı limitleri](sql-database-service-tiers-dtu.md) &bull; &nbsp;<br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[tek veritabanları için &nbsp;sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-single-databases.md) &bull;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>&bull; &nbsp;[yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md). <br/>Aksi takdirde, lütfen daha sonra yeniden deneyin. |
| 40544 |20 |Veritabanı boyut kotasına ulaştı. Verileri bölümleyin veya silin, dizinleri bırakın veya olası çözümler için belgelere başvurun. Veritabanı Ölçeklendirme için bkz. [tek veritabanı kaynaklarını ölçeklendirme](sql-database-single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Uzun süre çalışan bir işlem olduğu için oturum sonlandırıldı. İşleminizi kısaltmayı deneyin. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Oturum çok fazla kilit elde ettiğinden sonlandırıldı. Tek bir işlemde daha az sayıda satır okumayı veya değiştirmeyi deneyin. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Aşırı `TEMPDB` kullanımı nedeniyle oturum sonlandırıldı. Geçici tablo alanı kullanımını azaltmak için sorgunuzu değiştirmeyi deneyin.<br/><br/>Geçici nesneler kullanıyorsanız, geçici nesneleri oturum için artık gerekli olmadıklarında bırakarak `TEMPDB` veritabanında alandan tasarruf edin. SQL veritabanı 'nda tempdb kullanımı hakkında daha fazla bilgi için bkz. [SQL veritabanı 'Nda tempdb veritabanı](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Aşırı işlem günlüğü alanı kullanımı nedeniyle oturum sonlandırıldı. Tek bir işlemde daha az sayıda satır değiştirmeyi deneyin. Toplu işleme hakkında bilgi için bkz. [SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma](sql-database-use-batching-to-improve-performance.md).<br/><br/>`bcp.exe` yardımcı programını veya `System.Data.SqlClient.SqlBulkCopy` sınıfını kullanarak toplu eklemeler yaparsanız, her işlemde sunucuya kopyalanmış satır sayısını sınırlamak için `-b batchsize` veya `BatchSize` seçeneklerini kullanmayı deneyin. `ALTER INDEX` ifadesiyle bir dizini yeniden oluşturuyorsanız `REBUILD WITH ONLINE = ON` seçeneğini kullanmayı deneyin. Sanal çekirdek satın alma modeli için işlem günlüğü boyutları hakkında bilgi için bkz.: <br/>[tek veritabanları için &nbsp;sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-single-databases.md) &bull;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>&bull; &nbsp;[yönetilen örnek kaynak sınırları](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |Aşırı bellek kullanımı nedeniyle oturum sonlandırıldı. Daha az satır işlemek için sorgunuzu değiştirmeyi deneyin.<br/><br/>Transact-SQL kodunuzda `ORDER BY` ve `GROUP BY` işlemlerinin sayısını azaltmak sorgunuzun bellek gereksinimlerini azaltır. Veritabanı Ölçeklendirme için bkz. [tek veritabanı kaynaklarını ölçeklendirme](sql-database-single-database-scale.md) ve [elastik havuz kaynaklarını ölçeklendirme](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Elastik havuz hataları

Aşağıdaki hatalar elastik havuzlar oluşturma ve kullanmayla ilgilidir:

| Hata kodu | Severity | Açıklama | Düzeltici eylem |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Elastik havuz, depolama sınırına ulaştı. Elastik havuzun depolama alanı kullanımı (% d) MB/s değerini aşamaz. Elastik havuzun depolama sınırına ulaşıldığında veritabanına veri yazmaya çalışılıyor. Kaynak limitleri hakkında bilgi için bkz.: <br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md)&bull; &nbsp;. <br/> |Depolama sınırını artırmak, elastik havuzdaki ayrı veritabanları tarafından kullanılan depolamayı azaltmak veya elastik havuzdan veritabanlarını kaldırmak için mümkünse, depolama alanı sayısını ve/veya depolama alanını esnek havuza eklemeyi düşünün. Elastik havuz ölçekleme için bkz. [elastik havuz kaynaklarını ölçeklendirme](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |% S en düşük garanti% d, maksimum sınır% d ve veritabanı için geçerli kullanım% d. Ancak, sunucu şu anda bu veritabanı için% d değerinden büyük istekleri desteklemeye yönelik çok meşgul. Kaynak limitleri hakkında bilgi için bkz.: <br/>[elastik havuzlar Için DTU tabanlı limitleri](sql-database-dtu-resource-limits-elastic-pools.md) &bull; &nbsp;<br/>[elastik havuzlar Için sanal çekirdek tabanlı limitleri](sql-database-vcore-resource-limits-elastic-pools.md)&bull; &nbsp;. <br/> Aksi takdirde, lütfen daha sonra yeniden deneyin. Veritabanı başına DTU/sanal çekirdek en az; Veritabanı başına DTU/sanal çekirdek maks. Elastik havuzdaki tüm veritabanları genelinde eş zamanlı çalışan (istek) toplam sayısı havuz sınırını aşmaya çalıştı. |Çalışan sınırını artırmak veya elastik havuzdan veritabanlarını kaldırmak için mümkünse, elastik havuzun DTU 'ları veya sanal çekirdekleri artırmayı düşünün. |
| 40844 | 16 |'% Ls ' sunucusundaki '% ls ' veritabanı, elastik havuzdaki bir '% ls ' sürüm veritabanıdır ve sürekli bir kopyalama ilişkisine sahip olamaz.  |Yok |
| 40857 | 16 |Sunucu: '% ls ', elastik havuz adı: '% ls ' için elastik havuz bulunamadı. Belirtilen elastik havuz belirtilen sunucuda yok. | Geçerli bir elastik havuz adı sağlayın. |
| 40858 | 16 |'% Ls ' esnek havuzu sunucuda zaten var: '% ls '. Belirtilen elastik havuz belirtilen SQL veritabanı sunucusunda zaten var. | Yeni elastik havuz adı sağlayın. |
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

   ![Bağlantı özellikleri](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Bir bağlantı sorunundan kaynaklanan bir hata olup olmadığını onaylayın

Bir bağlantı sorunundan kaynaklanan bir hata olup olmadığını doğrulamak için, aşağıdaki gibi bir bağlantıyı açmaya yönelik çağrıları gösteren çerçeveler için yığın izlemesini gözden geçirin ( **SqlConnection** sınıfına yönelik başvuruya göz önünde bulunur):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Özel durum sorgu sorunları tarafından tetiklendiğinde, aşağıdakine benzer bir çağrı yığını görürsünüz ( **SqlCommand** sınıfının başvurusunu unutmayın). Bu durumda, [sorgularınızı ayarlayın](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

İnce ayar performansı hakkında ek yönergeler için aşağıdaki kaynaklara bakın:

* [Azure SQL dizinlerini ve istatistiklerini koruma](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Azure SQL veritabanı 'nda el ile ayarlama sorgusu performansı](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Dinamik yönetim görünümlerini kullanarak performans Azure SQL veritabanı 'nı izleme](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Azure SQL veritabanı 'nda sorgu deposunu çalıştırma](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Sık karşılaşılan bağlantı sorunlarını giderme adımları

1. TCP/IP 'nin uygulama sunucusunda bir istemci protokolü olarak etkinleştirildiğinden emin olun. Daha fazla bilgi için bkz. [istemci protokollerini yapılandırma](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). SQL Server araçlarının yüklü olmadığı uygulama sunucularında, TCP/IP 'nin **cliconfg. exe** (SQL Server istemci ağ yardımcı programı) çalıştırarak etkinleştirildiğini doğrulayın.
2. Doğru yapılandırıldığından emin olmak için uygulamanın bağlantı dizesini denetleyin. Örneğin, bağlantı dizesinin doğru bağlantı noktasını (1433) ve tam sunucu adını belirttiğinden emin olun.
Bkz. [SQL Server bağlantı bilgilerini alın](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Bağlantı zaman aşımı değerini artırmayı deneyin. En az 30 saniyelik bir bağlantı zaman aşımı kullanmanızı öneririz.
4. [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), bir UDL dosyası, ping veya Telnet kullanarak uygulama sunucusu Ile Azure SQL veritabanı arasındaki bağlantıyı test edin. Daha fazla bilgi için bkz. bağlantı sorunlarını [giderme SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) bağlantı sorunları ve [Tanılama](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Sorun giderme adımı olarak, farklı bir istemci bilgisayardaki bağlantıyı da sınayabilirsiniz.

5. En iyi uygulama olarak, yeniden deneme mantığının yerinde olduğundan emin olun. Yeniden deneme mantığı hakkında daha fazla bilgi için bkz. [SQL veritabanında geçici hata ve bağlantı hatalarını giderme](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Bu adımlar sorununuzu gidermezse daha fazla veri toplamayı deneyin ve desteğe başvurun. Uygulamanız bir bulut hizmeti ise günlüğe kaydetmeyi etkinleştirin. Bu adım, hatanın UTC zaman damgasını döndürür. Ayrıca, SQL Azure izleme KIMLIĞINI döndürür. [Microsoft Müşteri Destek Hizmetleri](https://azure.microsoft.com/support/options/) , bu bilgileri kullanabilir.

Günlüğe kaydetmenin nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Azure App Service uygulamalar için tanılama günlüğünü etkinleştirme](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure SQL bağlantı mimarisi](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Azure SQL veritabanı ve veri ambarı ağ erişim denetimleri](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)

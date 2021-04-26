---
title: Azure SQL yönetilen örneği 'nde SQL Agent işleriyle iş Otomasyonu
description: Azure SQL yönetilen örneği 'nde Transact-SQL (T-SQL) betikleri çalıştırmak için otomasyon seçenekleri
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 3be01c304a40317e0d21baf6789ef1376cd89b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608087"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği 'nde SQL Agent işlerini kullanarak yönetim görevlerini otomatikleştirme
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

SQL Server ve [SQL yönetilen örneği](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)'nde [SQL Server Agent](/sql/ssms/agent/sql-server-agent) kullanarak Transact-SQL (T-SQL) sorgularını çalıştırmak ve bakım görevlerini gerçekleştirmek için bir veya daha fazla veritabanına göre düzenli olarak yürütülen işleri oluşturabilir ve zamanlayabilirsiniz. Bu makalede, SQL yönetilen örneği için SQL Aracısı tanıtılmıştır.

> [!Note]
> SQL Aracısı Azure SQL veritabanı veya Azure SYNAPSE Analytics 'te kullanılamıyor. Bunun yerine, [elastik ışlerle iş Otomasyonu](job-automation-overview.md)önerilir.

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği 'nde SQL Aracısı iş sınırlamaları

SQL Server ve SQL yönetilen örneği 'nin bir parçası olarak, SQL Aracısı arasındaki farklılıkları vurgulamaktadır. SQL Server ile SQL yönetilen örneği arasındaki desteklenen özellik farklılıkları hakkında daha fazla bilgi için, [SQL Server Azure SQL yönetilen örnek T-SQL farklılıkları](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)bölümüne bakın. 

SQL Server ' de kullanılabilen SQL Aracısı özelliklerinden bazıları SQL yönetilen örneği 'nde desteklenmez:

- SQL Aracısı ayarları salt okunurdur. 
    - Sistem saklı yordamı `sp_set_agent_properties` SQL yönetilen örneği 'nde desteklenmiyor.
- SQL aracısının etkinleştirilmesi/devre dışı bırakılması Şu anda SQL yönetilen örneği 'nde desteklenmiyor. SQL Server Agent sürekli çalışır.
- Bildirimler kısmen desteklenir:
  - Sayfalayıcı desteklenmiyor.
  - NetSend desteklenmez.
  - Uyarılar desteklenmez.
- Proxy 'ler desteklenmez.
- EventLog desteklenmiyor.
- Boş bir CPU 'ya dayalı iş zamanlaması tetiklemesi desteklenmiyor.

## <a name="when-to-use-sql-agent-jobs"></a>SQL Aracısı işlerinin ne zaman kullanılacağı 

SQL Agent işlerini kullanabileceğiniz birkaç senaryo vardır:

- Yönetim görevlerini otomatikleştirin ve saatler, saat sonra her hafta içinde çalışacak şekilde zamanlayın.
  - Şema değişiklikleri, kimlik bilgileri yönetimi, performans verisi toplama veya kiracı (müşteri) telemetri verilerini toplama gibi görevleri dağıtın.
  - Başvuru verilerini güncelleştirme (tüm veritabanlarında ortak bilgiler), Azure Blob depolamadan veri yükleme.
  - Sorgu performansını artırmak için veri bütünlüğünü veya dizin bakımını sağlamak üzere DBCC CHECKDB dahil ortak bakım görevleri. İşleri bir veritabanı koleksiyonunda yoğun saatlerin dışında yenilenecek şekilde yapılandırın.
  - Bir veritabanı kümesinden alınan sorgu sonuçlarını düzenli olarak merkezi bir tabloya toplayın. Performans sorguları sürekli yürütülebilir ve yürütülecek ek görevleri tetikleyecek şekilde yapılandırılabilir.
- Raporlama için veri toplama
  - Veritabanları koleksiyonundan tek bir hedef tabloya veri toplama.
  - Çok sayıda veritabanında müşteri telemetri verilerinin toplanması gibi daha uzun süre çalışan veri işleme sorguları çalıştırın. Sonuçlar daha ayrıntılı analiz için tek bir hedef tabloda toplanır.
- Veri taşımaları
  - Veritabanlarınızda yapılan değişiklikleri diğer veritabanlarına çoğaltan veya uzak veritabanlarında yapılan güncelleştirmeleri toplayacak ve değişiklikleri veritabanına uygulayan işler oluşturun.
  - SQL Server Integration Services (SSIS) kullanarak veritabanlarınızdan veri yükleyen işler oluşturun.

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği 'nde SQL Aracısı işleri

SQL Aracısı Işleri, SQL Server ve SQL yönetilen örneği 'nde görev otomasyonu için kullanılmaya devam eden SQL Aracısı hizmeti tarafından yürütülür. 

SQL Aracısı Işleri, veritabanınıza göre belirtilen bir T-SQL komut dosyası dizisidir. İşleri, bir veya daha fazla kez çalıştırılabilen ve başarı veya başarısızlık için izlenen bir yönetim görevi tanımlamak için kullanın. 

Bir iş, bir yerel sunucuda veya birden çok uzak sunucuda çalıştırılabilir. SQL Aracısı Işleri, SQL yönetilen örnek hizmeti içinde yürütülen bir iç veritabanı altyapısı bileşenidir.

SQL Aracısı Işlerinde birkaç temel kavram vardır:

- İş içinde yürütülmesi gereken bir veya daha fazla adımın **iş adımları** kümesi. Her iş adımı için yeniden deneme stratejisi tanımlayabilir ve iş adımının başarılı veya başarısız olması durumunda gerçekleşmesi gereken eylemi belirleyebilirsiniz.
- **Zamanlamalar** , işin ne zaman yürütülmesi gerektiğini tanımlar.
- **Bildirimler** , iş tamamlandıktan sonra işleçlere e-posta aracılığıyla bildirimde bulunan kurallar tanımlamanızı sağlar.

### <a name="sql-agent-job-steps"></a>SQL Aracısı iş adımları

SQL Aracısı Iş adımları SQL aracısının yürütmesi gereken eylem dizileridir. Her adımda, adım başarılı veya başarısız olursa yürütülmesi gereken adım, hata durumunda yeniden deneme sayısı.

SQL Aracısı, veritabanında tek bir Transact-SQL toplu işi çalıştıran Transact-SQL iş adımları veya özel işletim sistemi betiği yürütebilen IS komutu/PowerShell adımları gibi farklı türlerde iş adımları oluşturmanızı sağlar. bu [sayede,](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) SSIS çalışma zamanı 'nı kullanarak veri [yükleyebilir veya değişiklikleri](../managed-instance/replication-transactional-overview.md) veritabanınızdaki diğer veritabanlarına yayımlayabilirsiniz.

> [!Note]
> Azure SQL yönetilen örneği tarafından barındırılan SSSıSDB ile Azure SSIS Integration Runtime kullanımı hakkında daha fazla bilgi için, bkz. [Azure Data Factory 'Da Azure SQL yönetilen örneğini SQL Server Integration Services (SSIS) kullanma](../../data-factory/how-to-use-sql-managed-instance-with-ir.md).

[İşlemsel çoğaltma](../managed-instance/replication-transactional-overview.md) , tablolardaki DEĞIŞIKLIKLERI Azure SQL yönetilen örneği, Azure SQL veritabanı veya SQL Server diğer veritabanlarına çoğaltabilir. Daha fazla bilgi için bkz. [Azure SQL yönetilen örneği 'nde çoğaltmayı yapılandırma](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Diğer iş adımı türleri şu anda SQL yönetilen örneği 'nde desteklenmez:

- Birleştirme çoğaltması işi adımı desteklenmiyor.
- Sıra okuyucusu desteklenmiyor.
- Analysis Services desteklenmez
 
### <a name="sql-agent-job-schedules"></a>SQL Aracısı iş zamanlamaları

Bir zamanlama, bir işin ne zaman çalışacağını belirtir. Aynı zamanlamaya göre birden fazla iş çalışabilir ve aynı iş için birden fazla zamanlama uygulanabilir.

Bir zamanlama, bir işin çalıştığı zaman için aşağıdaki koşulları tanımlayabilir:

- SQL Server Agent her başladığında. İş her yük devretmeden sonra etkinleştirilir.
- Bir kez, belirli bir tarih ve saatte, bazı bir işin gecikmeli yürütülmesi için faydalıdır.
- Yinelenen bir zamanlamaya göre.

> [!Note]
> SQL yönetilen örneği şu anda CPU boştayken bir iş başlatmayı etkinleştirmiyor.

### <a name="sql-agent-job-notifications"></a>SQL Aracısı iş bildirimleri

SQL Aracısı Işleri, iş başarıyla tamamlandığında veya başarısız olduğunda bildirim almanızı sağlar. E-posta aracılığıyla bildirim alabilirsiniz.

Zaten etkin değilse, önce Azure SQL yönetilen örneği 'nde [veritabanı postası özelliğini](/sql/relational-databases/database-mail/database-mail) yapılandırmanız gerekir:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Örnek olarak, e-posta bildirimlerini göndermek için kullanılacak e-posta hesabını ayarlayın. Hesabı adlı e-posta profiline atayın `AzureManagedInstance_dbmail_profile` . SQL yönetilen örneği 'nde SQL Agent işleri kullanarak e-posta göndermek için, çağrılması gereken bir profil olmalıdır `AzureManagedInstance_dbmail_profile` . Aksi halde, SQL yönetilen örneği SQL Aracısı aracılığıyla e-posta gönderemeyecektir. Aşağıdaki örneğe bakın:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

[Sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) sistem saklı yordamını kullanarak T-SQL aracılığıyla veritabanı postası yapılandırmasını test edin:

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

SQL Agent işleriniz ile ilgili bir şeyin olduğunu işlecine bildirebilirsiniz. Bir işleç, SQL yönetilen örneğindeki bir veya daha fazla örneği bakımın bir sorumlusu için kişi bilgilerini tanımlar. Bazen, operatör sorumlulukları tek bir kişiye atanır.

SQL yönetilen örneği veya SQL Server birden çok örneğe sahip sistemlerde birçok kişi işleç sorumluluklarını paylaşabilir. Operatör güvenlik bilgilerini içermez ve bir güvenlik sorumlusu tanımlamaz. İdeal olarak, bir operatör sorumlulukları değiştirebilen bir kişi değildir, ancak e-posta dağıtım grubu.   

Aşağıdaki örnekte gösterilen SQL Server Management Studio (SSMS) veya Transact-SQL komut dosyasını kullanarak [operatörler oluşturabilirsiniz](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) :

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

SSMS 'de [veritabanı postası günlüğü](/sql/relational-databases/database-mail/database-mail-log-and-audits) aracılığıyla e-postanın başarısını veya başarısızlığını onaylayın.

Daha sonra [herhangi BIR SQL Aracısı işini değiştirebilir](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) ve iş tamamlandığında, başarısız olursa veya SSMS veya aşağıdaki Transact-SQL komut dosyası ile başarılı olursa e-posta yoluyla bildirim gönderilecek işleçleri atayabilirsiniz:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>SQL Aracısı iş geçmişi

Azure SQL yönetilen örneği şu anda, temeldeki kayıt defteri değerlerinde depolandığından SQL Agent özelliklerini değiştirmenize izin vermiyor. Bu, iş geçmişi kayıtları için aracı bekletme ilkesini ayarlamaya yönelik seçenekler, her iş için en fazla 1000 Toplam kayıt ve en fazla 100 geçmiş kaydı olarak düzeltilme anlamına gelir.

### <a name="sql-agent-fixed-database-role-membership"></a>SQL Aracısı sabit veritabanı rol üyeliği

Sysadmin olmayan oturum açma bilgilerine bağlanan kullanıcılar msdb sistem veritabanındaki üç SQL Aracısı sabit veritabanı rolünün herhangi birine eklenirse, bu oturum açma işlemleri için ana saklı yordamlara açık yürütme izinlerinin verilmesi gereken bir sorun vardır. Bu sorunla karşılaşılırsa, "nesne üzerinde yürütme izni reddedildi <object_name> (Microsoft SQL Server, hata: 229)" hata mesajını görürsünüz. 

Kullanıcıları, bu rollere eklenen bir SQL Agent sabit veritabanı rolüne (SQLAgentUserRole, SQLAgentReaderRole veya da SQLAgentOperatorRole rolünün) ekledikten sonra, listelenen sistem oturumu açma yordamlarına açıkça yürütme izinleri vermek için aşağıdaki T-SQL betiğini yürütün. Bu örnek, Kullanıcı adı ve oturum açma adının aynı olduğunu varsayar. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Daha fazla bilgi edinin

- [Azure SQL yönetilen örneği nedir?](../managed-instance/sql-managed-instance-paas-overview.md)
- [SQL yönetilen örneği & Azure SQL veritabanı 'ndaki yenilikler nelerdir?](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Azure SQL yönetilen örnek T-SQL Server 'den SQL farklılıkları](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Özellikler karşılaştırması: Azure SQL veritabanı ve Azure SQL yönetilen örneği](../../azure-sql/database/features-comparison.md)

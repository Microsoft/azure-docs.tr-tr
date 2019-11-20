---
title: Yönetilen örnek veritabanında çoğaltmayı yapılandırma
description: Azure SQL veritabanı yönetilen örnek veritabanında işlem çoğaltmasını yapılandırma hakkında bilgi edinin
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: f303a363fd4d42889e7817273be5d5e5440a2293
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822584"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Azure SQL veritabanı yönetilen örnek veritabanında çoğaltmayı yapılandırma

İşlemsel çoğaltma, verileri bir Azure SQL veritabanı yönetilen örnek veritabanına SQL Server veritabanından veya başka bir örnek veritabanından çoğaltmanıza olanak sağlar. 

Azure SQL veritabanı yönetilen örneği 'nde bir örnek veritabanında yapılan değişiklikleri şu şekilde göndermek için işlem çoğaltmayı da kullanabilirsiniz:

- Bir SQL Server veritabanı.
- Azure SQL veritabanı 'nda tek bir veritabanı.
- Azure SQL veritabanı elastik havuzundaki havuza alınmış bir veritabanı.
 
İşlemsel çoğaltma, [Azure SQL veritabanı yönetilen örneği](sql-database-managed-instance.md)'nin Genel önizlemededir. Yönetilen bir örnek, Yayımcı, dağıtıcı ve abone veritabanlarını barındırabilirler. Kullanılabilir konfigürasyonlar için [işlem çoğaltma yapılandırmalarına](sql-database-managed-instance-transactional-replication.md#common-configurations) bakın.

  > [!NOTE]
  > Bu makale, kaynak grubu oluşturmaya başlamadan önce Azure veritabanı yönetilen örneği ile uçtan uca çoğaltma yapılandırma konusunda bir kullanıcıya kılavuzluk amaçlıdır. Zaten dağıtılan yönetilen örneklere sahipseniz, yayımcı veritabanınızı oluşturmak için [4. adıma](#4---create-a-publisher-database) atlayın veya zaten bir yayımcı ve abone veritabanınız varsa ve çoğaltmayı yapılandırmaya başlamaya hazırsanız [6. adıma](#6---configure-distribution) geçin.  

## <a name="requirements"></a>Gereksinimler

Bir yönetilen örneği yayımcı ve/veya bir dağıtıcı olarak çalışacak şekilde yapılandırmak için şunları yapmanız gerekir:

- Yönetilen örnek şu anda bir coğrafi çoğaltma ilişkisine katılmıyor.
- Yayımcı tarafından yönetilen örnek, dağıtıcı ve abone ile aynı sanal ağda, ya da her üç varlığın sanal ağları arasında [vNet eşlemesi](../virtual-network/tutorial-connect-virtual-networks-powershell.md) oluşturulmuştur. 
- Bağlantı, çoğaltma katılımcıları arasında SQL Kimlik Doğrulaması kullanır.
- Çoğaltma çalışma dizini için bir Azure depolama hesabı payı.
- Bağlantı noktası 445 (TCP Giden), yönetilen örneklerin Azure dosya paylaşımında erişmesi için NSG güvenlik kurallarında açıktır.  "Azure Storage \<depolama hesabı adı > OS hatası 53" hatasıyla karşılaşırsanız, uygun SQL yönetilen örnek alt ağının NSG 'ye bir giden kuralı eklemeniz gerekir.


 > [!NOTE]
 > Azure SQL veritabanı 'nda tek veritabanları ve havuza alınmış veritabanları yalnızca aboneler olabilir. 


## <a name="features"></a>Özellikler

Desteklememektedir

- Azure SQL veritabanı 'nda şirket içi SQL Server ve yönetilen örneklerin işlem ve anlık görüntü çoğaltma karışımı.
- Aboneler şirket içi SQL Server veritabanlarında, Azure SQL veritabanı 'nda tek veritabanlarında/yönetilen örneklerde veya Azure SQL veritabanı elastik havuzlarında havuza alınmış veritabanlarında bulunabilir.
- Tek yönlü veya çift yönlü çoğaltma.

Azure SQL veritabanı 'nda yönetilen bir örnekte aşağıdaki özellikler desteklenmez:

- [Güncelleştirilebilir abonelikler](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- Işlemsel çoğaltma ile [etkin coğrafi çoğaltma](sql-database-active-geo-replication.md) . Etkin coğrafi çoğaltma yerine, [otomatik yük devretme grupları](sql-database-auto-failover-group.md)kullanın, ancak yayının birincil yönetilen örnekten [el ile silinmesi](sql-database-managed-instance-transact-sql-information.md#replication) ve yük devretmeden sonra ikincil yönetilen örnek üzerinde yeniden oluşturulması gerektiğini unutmayın.  
 
## <a name="1---create-a-resource-group"></a>1-kaynak grubu oluşturma

`SQLMI-Repl`adlı bir kaynak grubu oluşturmak için [Azure Portal](https://portal.azure.com) kullanın.  

## <a name="2---create-managed-instances"></a>2-yönetilen örnekler oluşturma

Aynı sanal ağ ve alt ağ üzerinde iki [yönetilen örnek](sql-database-managed-instance-create-tutorial-portal.md) oluşturmak için [Azure Portal](https://portal.azure.com) kullanın. İki yönetilen örnek şu şekilde adlandırılmalıdır:

- `sql-mi-pub`
- `sql-mi-sub`

Ayrıca, Azure SQL veritabanı yönetilen örneklerinizi [bağlamak için bir Azure VM yapılandırmanız](sql-database-managed-instance-configure-vm.md) gerekecektir. 

## <a name="3---create-azure-storage-account"></a>3-Azure depolama hesabı oluşturma

Çalışma dizini için [bir Azure depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) ve ardından depolama hesabı içinde bir [dosya paylaşma](../storage/files/storage-how-to-create-file-share.md) oluşturun. 

Dosya paylaşımının yolunu şu biçimde kopyalayın: `\\storage-account-name.file.core.windows.net\file-share-name`

Depolama erişim anahtarlarını şu biçimde kopyalayın: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Daha fazla bilgi için bkz. [Depolama erişim anahtarlarını görüntüleme ve kopyalama](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4-Yayımcı veritabanı oluşturma

SQL Server Management Studio kullanarak `sql-mi-pub` yönetilen örneğinize bağlanın ve yayımcı veritabanınızı oluşturmak için aşağıdaki Transact-SQL (T-SQL) kodunu çalıştırın:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5-abone veritabanı oluşturma

SQL Server Management Studio kullanarak `sql-mi-sub` yönetilen örneğinize bağlanın ve boş abone veritabanınızı oluşturmak için aşağıdaki T-SQL kodunu çalıştırın:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6-dağıtımı yapılandırma

SQL Server Management Studio kullanarak `sql-mi-pub` yönetilen örneğinize bağlanın ve dağıtım veritabanınızı yapılandırmak için aşağıdaki T-SQL kodunu çalıştırın. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-yayımcıyı dağıtıcı kullanacak şekilde yapılandırma 

Yayımcı tarafından yönetilen örnek `sql-mi-pub`, sorgu yürütmesini [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) moduna değiştirin ve yeni dağıtıcıyı yayımcıya kaydetmek için aşağıdaki kodu çalıştırın. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

Bu betik, yönetilen örnekte yerel bir yayımcıyı yapılandırır, bağlantılı bir sunucu ekler ve SQL Server Agent bir dizi iş oluşturur. 

## <a name="8---create-publication-and-subscriber"></a>8-Yayın ve abone oluşturma

[Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) modunu kullanarak, veritabanınız için çoğaltmayı etkinleştirmek ve Yayımcı, dağıtıcı ve abone arasında çoğaltmayı yapılandırmak Için aşağıdaki T-SQL betiğini çalıştırın. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-aracı parametrelerini değiştirme

Azure SQL veritabanı yönetilen örneği şu anda çoğaltma aracılarıyla bağlantı ile bazı arka uç sorunları yaşıyor. Bu soruna değinirken, çoğaltma aracıları için oturum açma zaman aşımı değerini artırmak için geçici çözüm. 

Oturum açma zaman aşımını artırmak için yayımcıda aşağıdaki T-SQL komutunu çalıştırın: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Oturum açma zaman aşımını varsayılan değere geri ayarlamak için aşağıdaki T-SQL komutunu tekrar çalıştırın, bunu yapmanız gerekir:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Bu değişiklikleri uygulamak için üç aracıyı de yeniden başlatın. 

## <a name="10---test-replication"></a>10-test çoğaltma

Çoğaltma yapılandırıldıktan sonra, yayımcıya yeni öğeler ekleyerek ve abonelere yayan değişiklikleri izleyerek test edebilirsiniz. 

Abone üzerindeki satırları görüntülemek için aşağıdaki T-SQL kod parçacığını çalıştırın:

```sql
select * from dbo.ReplTest
```

Yayımcıya ek satırlar eklemek için aşağıdaki T-SQL kod parçacığını çalıştırın ve ardından abonedeki satırları yeniden kontrol edin. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yayını bırakmak için aşağıdaki T-SQL komutunu çalıştırın:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Çoğaltma seçeneğini veritabanından kaldırmak için aşağıdaki T-SQL komutunu çalıştırın:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Yayımlamayı ve dağıtımı devre dışı bırakmak için aşağıdaki T-SQL komutunu çalıştırın:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

[Yönetilen örnek kaynaklarını kaynak grubundan silerek](../azure-resource-manager/manage-resources-portal.md#delete-resources) ve ardından `SQLMI-Repl`kaynak grubunu silerek Azure kaynaklarınızı temizleyebilirsiniz. 

   
## <a name="see-also"></a>Ayrıca Bkz.

- [İşlem çoğaltması](sql-database-managed-instance-transactional-replication.md)
- [Yönetilen örnek nedir?](sql-database-managed-instance.md)

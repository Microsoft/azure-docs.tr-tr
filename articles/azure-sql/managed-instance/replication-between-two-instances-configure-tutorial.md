---
title: Yönetilen örnekler arasında çoğaltmayı yapılandırma
titleSuffix: Azure SQL Managed Instance
description: Bu öğretici, bir Azure SQL yönetilen örneği yayımcısı/dağıtıcı ve bir SQL yönetilen örnek abonesi arasında işlemsel çoğaltmayı yapılandırmanızı öğretir.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690694"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Öğretici: iki yönetilen örnek arasında çoğaltmayı yapılandırma

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

İşlemsel çoğaltma, verileri bir veritabanından SQL Server ya da [Azure SQL yönetilen örneği](sql-managed-instance-paas-overview.md)üzerinde barındırılan bir diğerine çoğaltmanıza olanak sağlar. SQL yönetilen örneği, çoğaltma topolojisinde bir yayımcı, dağıtıcı veya abone olabilir. Kullanılabilir konfigürasyonlar için [işlem çoğaltma yapılandırmalarına](replication-transactional-overview.md#common-configurations) bakın. 

İşlemsel çoğaltma şu anda SQL yönetilen örneği için genel önizlemededir. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> - Yönetilen bir örneği, Çoğaltma yayımcısı ve dağıtıcı olarak yapılandırın.
> - Yönetilen bir örneği, çoğaltma abonesi olarak yapılandırın.

![İki yönetilen örnek arasında Çoğalt](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Bu öğretici, deneyimli bir hedef kitle için tasarlanmıştır ve kullanıcının Azure 'da hem yönetilen örneklere hem de SQL Server sanal makinelere dağıtım ve bağlanma hakkında bilgi sahibi olduğunu varsayar. 


> [!NOTE]
> - Bu makalede, Azure SQL yönetilen örneği 'nde [İşlemsel çoğaltmanın](/sql/relational-databases/replication/transactional/transactional-replication) kullanımı açıklanmaktadır. Her bir örnek için tamamen okunabilir çoğaltmalar oluşturmanıza olanak sağlayan bir Azure SQL yönetilen örnek özelliği olan [Yük devretme grupları](../database/auto-failover-group-overview.md)ile ilgisiz değildir. [Yük devretme gruplarıyla işlemsel çoğaltmayı](replication-transactional-overview.md#with-failover-groups)yapılandırırken ek hususlar vardır.



## <a name="requirements"></a>Gereksinimler

SQL yönetilen örneği yayımcı ve/veya bir dağıtıcı olarak çalışacak şekilde yapılandırmak için şunları yapmanız gerekir:

- Yayımcı tarafından yönetilen örnek, dağıtıcı ve abone ile aynı sanal ağda olduğundan veya üç varlığın sanal ağları arasında [VPN ağ geçitlerinin](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) yapılandırıldığından emin olur. 
- Bağlantı, çoğaltma katılımcıları arasında SQL Kimlik Doğrulaması kullanır.
- Çoğaltma çalışma dizini için bir Azure depolama hesabı payı.
- Bağlantı noktası 445 (TCP Giden), yönetilen örneklerin Azure dosya paylaşımında erişmesi için NSG güvenlik kurallarında açıktır.  Hatayla karşılaşırsanız `failed to connect to azure storage <storage account name> with os error 53` , uygun SQL yönetilen örnek alt ağının NSG 'ye bir giden kuralı eklemeniz gerekir.

## <a name="1---create-a-resource-group"></a>1-kaynak grubu oluşturma

Ada sahip bir kaynak grubu oluşturmak için [Azure Portal](https://portal.azure.com) kullanın `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2-yönetilen örnekler oluşturma

Aynı sanal ağ ve alt ağ üzerinde iki [SQL yönetilen örnek](instance-create-quickstart.md) oluşturmak için [Azure Portal](https://portal.azure.com) kullanın. Örneğin, iki yönetilen örneği adlandırın:

- `sql-mi-pub` (rastgele seçme için bazı karakterlerle birlikte)
- `sql-mi-sub` (rastgele seçme için bazı karakterlerle birlikte)

Ayrıca, yönetilen örneklerinizi [bağlamak için bir Azure VM 'yi yapılandırmanız](connect-vm-instance-configure.md) gerekir. 

## <a name="3---create-an-azure-storage-account"></a>3-Azure depolama hesabı oluşturma

Çalışma dizini için [bir Azure depolama hesabı oluşturun](../../storage/common/storage-account-create.md#create-a-storage-account) ve ardından depolama hesabı içinde bir [dosya paylaşma](../../storage/files/storage-how-to-create-file-share.md) oluşturun. 

Dosya paylaşımının yolunu şu biçimde kopyalayın: `\\storage-account-name.file.core.windows.net\file-share-name`

Örnek: `\\replstorage.file.core.windows.net\replshare`

Depolama erişim anahtarlarını şu biçimde kopyalayın: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Örnek: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4-Yayımcı veritabanı oluşturma

`sql-mi-pub`SQL Server Management Studio kullanarak yönetilen örneğinize bağlanın ve yayımcı veritabanınızı oluşturmak için aşağıdaki Transact-SQL (T-SQL) kodunu çalıştırın:

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

`sql-mi-sub`SQL Server Management Studio kullanarak yönetilen örneğinize bağlanın ve boş abone veritabanınızı oluşturmak için aşağıdaki T-SQL kodunu çalıştırın:

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

`sql-mi-pub`SQL Server Management Studio kullanarak yönetilen örneğinize bağlanın ve dağıtım veritabanınızı yapılandırmak için aşağıdaki T-SQL kodunu çalıştırın.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-yayımcıyı dağıtıcı kullanacak şekilde yapılandırma

Publisher SQL yönetilen örneğiniz üzerinde `sql-mi-pub` sorgu yürütmeyi [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) moduna değiştirin ve yeni dağıtıcıyı yayınınızdan kaydettirmek için aşağıdaki kodu çalıştırın.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

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

   > [!NOTE]
   > File_storage parametresi için yalnızca ters eğik çizgi () kullandığınızdan emin olun `\` . Eğik çizgi () kullanmak `/` dosya paylaşımıyla bağlantı kurulurken hataya neden olabilir.

Bu betik, yönetilen örnekte yerel bir yayımcı yapılandırır, bağlantılı bir sunucu ekler ve SQL Server Aracısı için bir dizi iş oluşturur.

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


-- Configure your log reader agent
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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-aracı parametrelerini değiştirme

Azure SQL yönetilen örneği şu anda çoğaltma aracılarıyla bağlantı ile bazı arka uç sorunları yaşıyor. Bu sorun giderilirken geçici çözüm, çoğaltma aracıları için oturum açma zaman aşımı değerini artırılmamaktadır.

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

[SQL yönetilen örnek kaynaklarını kaynak grubundan silerek](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) ve sonra kaynak grubunu silerek Azure kaynaklarınızı temizleyebilirsiniz `SQLMI-Repl` . 

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, [Azure SQL yönetilen örneği ile işlemsel çoğaltma](replication-transactional-overview.md) hakkında daha fazla bilgi edinebilirsiniz veya bir [SQL yönetilen örneği yayımcısı/DAĞıTıCı ile Azure VM ABONESI üzerinde bir SQL](replication-two-instances-and-sql-server-configure-tutorial.md)arasında çoğaltmayı yapılandırmayı öğrenin.
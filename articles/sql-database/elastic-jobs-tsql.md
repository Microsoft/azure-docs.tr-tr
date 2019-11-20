---
title: Transact-SQL (T-SQL) ile elastik veritabanı Işleri oluşturma ve yönetme
description: Transact-SQL (T-SQL) kullanarak elastik veritabanı Iş aracısına sahip birçok veritabanı arasında komut dosyalarını çalıştırın.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: deefc1cc1d8fe82eab9ec0085b3a11ccd2fe7840
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820593"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Transact-SQL (T-SQL) kullanarak elastik veritabanı Işleri oluşturma ve yönetme

Bu makalede T-SQL kullanarak esnek ışlerle çalışmaya başlamak için birçok örnek senaryo sunulmaktadır.

Örnekler, [*iş veritabanında*](sql-database-job-automation-overview.md#job-database)bulunan [saklı yordamları](#job-stored-procedures) ve [görünümleri](#job-views) kullanır.

Transact-SQL (T-SQL) işleri oluşturmak, yapılandırmak, yürütmek ve yönetmek için kullanılır. Esnek Iş aracısının oluşturulması T-SQL ' de desteklenmez, bu nedenle önce portalı veya [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent)'i kullanarak bir *elastik iş Aracısı* oluşturmanız gerekir.


## <a name="create-a-credential-for-job-execution"></a>İş yürütmesi için kimlik bilgisi oluşturma

Kimlik bilgisi, komut dosyası yürütmeye yönelik hedef veritabanlarınıza bağlanmak için kullanılır. Bu kimlik bilgisi, betiği başarıyla yürütmek için hedef grup tarafından belirtilen veritabanlarında uygun izinlere ihtiyaç duyuyor. Bir sunucu ve/veya havuz hedef grubu üyesi kullanırken, iş yürütme sırasında sunucu ve/veya havuz genişlemesinden önce kimlik bilgisini yenilemek için kullanılmak üzere bir ana kimlik bilgisi oluşturmanız önerilir. Veritabanı kapsamlı kimlik bilgileri iş Aracısı veritabanında oluşturulur. Aynı kimlik bilgileri *, oturum açmak ve oturum* açma Işleminden bir *Kullanıcı oluşturmak* Için, oturum açma veritabanı izinlerini hedef veritabanlarına vermek için kullanılmalıdır.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Hedef grup (sunucular) oluşturma

Aşağıdaki örnek, bir sunucudaki tüm veritabanlarına karşı bir işin nasıl yürütüleceğini göstermektedir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Tek bir veritabanını dışlama

Aşağıdaki örnek, *Mappingdb*adlı veritabanı dışında bir SQL veritabanı sunucusundaki tüm veritabanlarına karşı bir işin nasıl yürütüleceğini gösterir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Hedef grup (havuzlar) oluşturma

Aşağıdaki örnek, bir veya daha fazla elastik havuzdaki tüm veritabanlarının nasıl hedeflenecek gösterilmektedir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Yeni şemayı birçok veritabanına dağıtma

Aşağıdaki örnek, tüm veritabanlarına yeni şemanın nasıl dağıtılacağını göstermektedir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Yerleşik parametreleri kullanarak veri toplama

Birçok veri toplama senaryosunda, işin sonuçlarını işlemeye son vermek için bu komut dosyası değişkenlerinin bazılarını eklemek yararlı olabilir.

- $ (job_name)
- $ (job_id)
- $ (job_version)
- $ (step_id)
- $ (step_name)
- $ (job_execution_id)
- $ (job_execution_create_time)
- $ (target_group_name)

Örneğin, aynı iş yürütmesindeki tüm sonuçları birlikte gruplandırmak için, aşağıdaki komutta gösterildiği gibi *$ (job_execution_id)* kullanın:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Veritabanı performansını izleme

Aşağıdaki örnek, birden çok veritabanından performans verilerini toplamak için yeni bir iş oluşturur.

Varsayılan olarak, İş Aracısı döndürülen sonuçları depolamak için tablo oluşturmayı arayacaktır. Sonuç olarak, çıkış kimlik bilgisi için kullanılan kimlik bilgileriyle ilişkili oturum açmanın bu işlemi gerçekleştirmek için yeterli izinlere sahip olması gerekir. Tabloyu daha önce el ile oluşturmak isterseniz, aşağıdaki özelliklere sahip olması gerekir:
1. Sonuç kümesi için doğru adı ve veri türlerini içeren sütunlar.
2. Uniqueidentifier veri türüyle internal_execution_id için ek sütun.
3. İnternal_execution_id sütununda `IX_<TableName>_Internal_Execution_ID` adlı kümelenmemiş bir dizin.

[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve aşağıdaki komutları çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>İş tanımlarını görüntüle

Aşağıdaki örnek, geçerli iş tanımlarının nasıl görüntüleneceğini gösterir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>İşi geçici olarak yürütmeye başla

Aşağıdaki örnek, bir işin hemen nasıl başlatılacağını göstermektedir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Bir işin yürütülmesini zamanlama

Aşağıdaki örnek, bir işin daha sonra yürütülmesi için nasıl zamanlanalınacağını göstermektedir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>İzleme işi yürütme durumu

Aşağıdaki örnek, tüm işler için yürütme durumu ayrıntılarının nasıl görüntüleneceğini gösterir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>İşi iptal et

Aşağıdaki örnek, bir işin nasıl iptal edildiğini gösterir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Eski iş geçmişini sil

Aşağıdaki örnek, belirli bir tarihten önce iş geçmişinin nasıl silineceğini gösterir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>İşi ve tüm iş geçmişini sil

Aşağıdaki örnek, bir işin ve tüm ilgili iş geçmişinin nasıl silineceğini gösterir.  
[*İş veritabanına*](sql-database-job-automation-overview.md#job-database) bağlanın ve şu komutu çalıştırın:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>İş saklı yordamları

Aşağıdaki saklı yordamlar [işler veritabanında](sql-database-job-automation-overview.md#job-database)bulunur.



|Saklı yordam  |Açıklama  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Yeni bir iş ekler.    |
|[sp_update_job](#sp_update_job)    |      Var olan bir işi güncelleştirir.   |
|[sp_delete_job](#sp_delete_job)     |      Var olan bir işi siler.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    İşe bir adım ekler.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Bir iş adımını güncelleştirir.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Bir iş adımını siler.    |
|[sp_start_job](#sp_start_job)    |  İşi yürütmeye başlar.       |
|[sp_stop_job](#sp_stop_job)     |     Bir iş yürütmesini sonlandırır.   |
|[sp_add_target_group](#sp_add_target_group)    |     Bir hedef grup ekler.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Hedef grubu siler.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Hedef gruba bir veritabanı veya veritabanı grubu ekler.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Hedef grup üyesini hedef gruptan kaldırır.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Bir işin geçmiş kayıtlarını kaldırır.     |





### <a name="sp_add_job"></a>sp_add_job

Yeni bir iş ekler. 
  
#### <a name="syntax"></a>Sözdizimi  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Bağımsız Değişkenler  

[ **\@job_name =** ] ' job_name '  
İşin adı. Ad benzersiz olmalıdır ve yüzdeyi (%) içeremez inde. job_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@Description =** ] açıklaması  
İşin açıklaması. Açıklama nvarchar (512), varsayılan değeri NULL. Açıklama atlanırsa boş bir dize kullanılır.

[ **\@Enabled =** ] etkin  
İşin zamanlamanın etkin olup olmadığı. Etkin, varsayılan değer olan 0 olan bittir (devre dışı). 0 ise, iş etkin değildir ve zamanlamaya göre çalışmaz; Ancak, el ile çalıştırılabilir. 1 ise, iş zamanlamaya göre çalışacaktır ve ayrıca el ile çalıştırılabilir.

[ **\@schedule_interval_type =** ] schedule_interval_type  
Değer, işin ne zaman yürütüleceğini belirtir. schedule_interval_type nvarchar (50), varsayılan bir kez ve aşağıdaki değerlerden biri olabilir:
- ' Bir kez ',
- ' Dakika ',
- ' Saat ',
- ' Days ',
- ' Hafta ',
- Ay

[ **\@schedule_interval_count =** ] schedule_interval_count  
İşin her yürütmesi arasında gerçekleştirilecek schedule_interval_count dönemi sayısı. schedule_interval_count, varsayılan değer olan 1 ' dir. Değer 1 ' e eşit veya daha büyük olmalıdır.

[ **\@schedule_start_time =** ] schedule_start_time  
İş yürütmenin başlayabileceği tarih. schedule_start_time DATETIME2, varsayılan olarak 0001-01-01 00:00:00.0000000.

[ **\@schedule_end_time =** ] schedule_end_time  
İş yürütmenin durulabileceği tarih. schedule_end_time, varsayılan olarak 9999-12-31 11:59:59.0000000 ile DATETIME2. 

[ **\@job_id =** ] job_id çıkışı  
İşe başarıyla oluşturulduysa iş kimlik numarası işe atandı. job_id, uniqueidentifier türünde bir çıkış değişkenidir.

#### <a name="return-code-values"></a>Dönüş kodu değerleri

0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
sp_add_job, İş Aracısı oluşturulurken belirtilen iş Aracısı veritabanından çalıştırılmalıdır.
Sp_add_job bir iş eklemek için yürütüldükten sonra, sp_add_jobstep iş için etkinlikleri gerçekleştiren adımları eklemek için kullanılabilir. İşin ilk sürüm numarası 0 ' dır ve ilk adım eklendiğinde 1 ' e yükseltilir.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:

- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.

### <a name="sp_update_job"></a>sp_update_job

Var olan bir işi güncelleştirir.

#### <a name="syntax"></a>Sözdizimi

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@job_name =** ] ' job_name '  
Güncellenme işinin adı. job_name nvarchar (128).

[ **\@new_name =** ] ' new_name '  
İşin yeni adı. new_name nvarchar (128).

[ **\@Description =** ] açıklaması  
İşin açıklaması. Açıklama nvarchar (512).

[ **\@Enabled =** ] etkin  
İş zamanlamasının etkin olup olmadığını belirtir (1) veya etkin değil (0). Etkin bit.

[ **\@schedule_interval_type =** ] schedule_interval_type  
Değer, işin ne zaman yürütüleceğini belirtir. schedule_interval_type nvarchar (50) ve aşağıdaki değerlerden biri olabilir:

- ' Bir kez ',
- ' Dakika ',
- ' Saat ',
- ' Days ',
- ' Hafta ',
- Ay

[ **\@schedule_interval_count =** ] schedule_interval_count  
İşin her yürütmesi arasında gerçekleştirilecek schedule_interval_count dönemi sayısı. schedule_interval_count, varsayılan değer olan 1 ' dir. Değer 1 ' e eşit veya daha büyük olmalıdır.

[ **\@schedule_start_time =** ] schedule_start_time  
İş yürütmenin başlayabileceği tarih. schedule_start_time DATETIME2, varsayılan olarak 0001-01-01 00:00:00.0000000.

[ **\@schedule_end_time =** ] schedule_end_time  
İş yürütmenin durulabileceği tarih. schedule_end_time, varsayılan olarak 9999-12-31 11:59:59.0000000 ile DATETIME2. 

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Sp_add_job bir iş eklemek için yürütüldükten sonra, sp_add_jobstep iş için etkinlikleri gerçekleştiren adımları eklemek için kullanılabilir. İşin ilk sürüm numarası 0 ' dır ve ilk adım eklendiğinde 1 ' e yükseltilir.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.



### <a name="sp_delete_job"></a>sp_delete_job

Var olan bir işi siler.

#### <a name="syntax"></a>Sözdizimi

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@job_name =** ] ' job_name '  
Silinecek işin adı. job_name nvarchar (128).

[ **\@zorla =** ] zorla  
İşin sürmekte olan yürütmeler varsa silinip oluşturulmayacağını belirtir ve devam eden tüm yürütmeleri (1) iptal edip devam eden herhangi bir iş yürütmesi varsa başarısız olur (0). zorla bit.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
İş geçmişi, bir iş silindiğinde otomatik olarak silinir.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.



### <a name="sp_add_jobstep"></a>sp_add_jobstep

İşe bir adım ekler.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Bağımsız Değişkenler

[ **\@job_name =** ] ' job_name '  
Adımın ekleneceği işin adı. job_name nvarchar (128).

[ **\@step_id =** ] step_id  
İş adımının sıra kimlik numarası. Adım tanımlama numaraları 1 ' de başlar ve boşluklar olmadan artış yapın. Mevcut bir adım zaten bu kimliğe sahipse, bu adım ve tüm adımlar, bu yeni adımın diziye eklenebilmesi için kimliğinin artmasını sağlayacaktır. Belirtilmezse, step_id adım dizisindeki son ' a otomatik olarak atanır. step_id bir int.

[ **\@step_name =** ] step_name  
Adımın adı. Bir işin ilk adımı dışında (kolaylık sağlaması için) ' JobStep ' varsayılan adı olan belirtilmelidir. step_name nvarchar (128).

[ **\@command_type =** ] ' command_type '  
Bu JobStep tarafından yürütülen komutun türü. command_type, TSql parametresinin bir T-SQL betiği olduğu @command_type anlamına gelen varsayılan değeri olan nvarchar (50) değeridir.

Belirtilmişse, değerin TSql olması gerekir.

[ **\@command_source =** ] ' command_source '  
Komutun depolandığı konumun türü. command_source, varsayılan satır Içi değeri olan nvarchar (50), yani @command_source parametresinin değeri komutun değişmez metinidir.

Belirtilmişse, değerin satır Içi olması gerekir.

[ **\@komutu =** ] komutundaki  
Komut geçerli bir T-SQL betiği olmalıdır ve sonra bu iş adımı tarafından yürütülür. komut, varsayılan değeri NULL olan nvarchar (max).

[ **\@credential_name =** ] ' credential_name '  
Bu adım yürütüldüğünde hedef grup içindeki her bir hedef veritabanına bağlanmak için kullanılan, bu iş denetim veritabanında depolanan veritabanı kapsamlı kimlik bilgisinin adı. credential_name nvarchar (128).

[ **\@target_group_name =** ] ' Target-group_name '  
İş adımının üzerinde yürütüleceği hedef veritabanlarını içeren hedef grubun adı. target_group_name nvarchar (128).

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
İlk yürütme denemesinde iş adımı başarısız olursa, ilk yeniden deneme denemesinden önceki gecikme. initial_retry_interval_seconds, varsayılan değer olan 1 ' dir.

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Yeniden deneme girişimleri arasındaki gecikme üst sınırı. Denemeler arasındaki gecikme bu değerden daha büyük büyürken, bunun yerine bu değere eşit hale gelir. maximum_retry_interval_seconds, varsayılan değer olan 120 ' dir.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Birden çok iş adımı yürütme denemesi başarısız olursa yeniden deneme gecikmesine uygulanacak çarpan. Örneğin, ilk yeniden denenmede 5 saniyelik bir gecikme vardı ve geri dönüş çarpanı 2,0 ise ikinci yeniden deneme 10 saniyelik bir gecikme olur ve üçüncü yeniden deneme 20 saniye gecikmeyle oluşur. retry_interval_backoff_multiplier, varsayılan değer olan 2,0 ile gerçek bir değerdir.

[ **\@retry_attempts =** ] retry_attempts  
İlk girişim başarısız olursa yürütme yeniden denenme sayısı. Örneğin, retry_attempts değeri 10 ise, 1 Başlangıç denemesi ve 10 yeniden deneme girişiminde bulunur ve toplam 11 deneme olur. Son yeniden deneme girişimi başarısız olursa, iş yürütmesi başarısız bir yaşam döngüsü ile sonlandırılır. retry_attempts, varsayılan değer 10 ' dur.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
Adımın yürütülmesi için izin verilen en uzun süre. Bu süre aşılırsa, iş yürütmesi zaman aşımı yaşam döngüsü ile sonlandırılır. step_timeout_seconds, varsayılan değer olan 43.200 saniyedir (12 saat).

[ **\@output_type =** ] ' output_type '  
Null değilse, komutun ilk sonuç kümesinin yazıldığı hedefin türü. output_type nvarchar (50), varsayılan değeri NULL.

Belirtilmişse değer SqlDatabase olmalıdır.

[ **\@output_credential_name =** ] ' output_credential_name '  
Null değilse, çıkış hedef veritabanına bağlanmak için kullanılan veritabanı kapsamlı kimlik bilgisinin adı. Output_type SqlDatabase ise belirtilmesi gerekir. output_credential_name, varsayılan değeri NULL olan nvarchar (128) değeridir.

[ **\@output_subscription_id =** ] ' output_subscription_id '  
Açıklama gerekiyor.

[ **\@output_resource_group_name =** ] ' output_resource_group_name '  
Açıklama gerekiyor.

[ **\@output_server_name =** ] ' output_server_name '  
Null değilse, çıkış hedefi veritabanını içeren sunucunun tam DNS adı. Output_type SqlDatabase ise belirtilmesi gerekir. output_server_name nvarchar (256), varsayılan değeri NULL.

[ **\@output_database_name =** ] ' output_database_name '  
Null değilse, çıkış hedefi tablosunu içeren veritabanının adı. Output_type SqlDatabase ise belirtilmesi gerekir. output_database_name nvarchar (128), varsayılan değeri NULL.

[ **\@output_schema_name =** ] ' output_schema_name '  
Null değilse, çıkış hedefi tablosunu içeren SQL şemasının adı. Output_type SqlDatabase öğesine eşitse, varsayılan değer dbo olur. output_schema_name nvarchar (128).

[ **\@output_table_name =** ] ' output_table_name '  
Null değilse, komutun ilk sonuç kümesinin yazılacağı tablonun adı. Tablo önceden yoksa, döndürülen sonuç kümesi şemasına göre oluşturulur. Output_type SqlDatabase ise belirtilmesi gerekir. output_table_name, varsayılan değeri NULL olan nvarchar (128) değeridir.

[ **\@job_version =** ] job_version çıkışı  
Yeni iş sürümü numarasına atanacak çıkış parametresi. job_version int.

[ **\@max_parallelism =** ] max_parallelism çıkışı  
Elastik havuz başına en yüksek paralellik düzeyi. Ayarlanırsa, iş adımı yalnızca elastik havuz başına en fazla veritabanı üzerinde çalışacak şekilde kısıtlanır. Bu, doğrudan hedef gruba dahil edilen veya hedef gruba dahil olan bir sunucu içinde olan her esnek havuz için geçerlidir. max_parallelism int.


#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Sp_add_jobstep başarılı olduğunda, işin geçerli sürüm numarası artırılır. İşin bir sonraki çalıştırılışında yeni sürüm kullanılacaktır. İş şu anda yürütülerek, bu yürütme yeni adımı içermez.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:  

- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.



### <a name="sp_update_jobstep"></a>sp_update_jobstep

Bir iş adımını güncelleştirir.

#### <a name="syntax"></a>Sözdizimi

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@job_name =** ] ' job_name '  
Adımın ait olduğu işin adı. job_name nvarchar (128).

[ **\@step_id =** ] step_id  
Değiştirilecek iş adımının kimlik numarası. Step_id ya da step_name belirtilmelidir. step_id bir int.

[ **\@step_name =** ] ' step_name '  
Değiştirilecek adımın adı. Step_id ya da step_name belirtilmelidir. step_name nvarchar (128).

[ **\@new_id =** ] new_id  
İş adımının yeni sıra kimlik numarası. Adım tanımlama numaraları 1 ' de başlar ve boşluklar olmadan artış yapın. Bir adım yeniden düzenlendiğinde, diğer adımlar otomatik olarak yeniden numaralandırılır.

[ **\@new_name =** ] ' new_name '  
Adımın yeni adı. new_name nvarchar (128).

[ **\@command_type =** ] ' command_type '  
Bu JobStep tarafından yürütülen komutun türü. command_type, TSql parametresinin bir T-SQL betiği olduğu @command_type anlamına gelen varsayılan değeri olan nvarchar (50) değeridir.

Belirtilmişse, değerin TSql olması gerekir.

[ **\@command_source =** ] ' command_source '  
Komutun depolandığı konumun türü. command_source, varsayılan satır Içi değeri olan nvarchar (50), yani @command_source parametresinin değeri komutun değişmez metinidir.

Belirtilmişse, değerin satır Içi olması gerekir.

[ **\@komutu =** ] komutundaki  
Komut (ler) geçerli bir T-SQL betiği olmalıdır ve sonra bu iş adımı tarafından yürütülür. komut, varsayılan değeri NULL olan nvarchar (max).

[ **\@credential_name =** ] ' credential_name '  
Bu adım yürütüldüğünde hedef grup içindeki her bir hedef veritabanına bağlanmak için kullanılan, bu iş denetim veritabanında depolanan veritabanı kapsamlı kimlik bilgisinin adı. credential_name nvarchar (128).

[ **\@target_group_name =** ] ' Target-group_name '  
İş adımının üzerinde yürütüleceği hedef veritabanlarını içeren hedef grubun adı. target_group_name nvarchar (128).

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
İlk yürütme denemesinde iş adımı başarısız olursa, ilk yeniden deneme denemesinden önceki gecikme. initial_retry_interval_seconds, varsayılan değer olan 1 ' dir.

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Yeniden deneme girişimleri arasındaki gecikme üst sınırı. Denemeler arasındaki gecikme bu değerden daha büyük büyürken, bunun yerine bu değere eşit hale gelir. maximum_retry_interval_seconds, varsayılan değer olan 120 ' dir.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Birden çok iş adımı yürütme denemesi başarısız olursa yeniden deneme gecikmesine uygulanacak çarpan. Örneğin, ilk yeniden denenmede 5 saniyelik bir gecikme vardı ve geri dönüş çarpanı 2,0 ise ikinci yeniden deneme 10 saniyelik bir gecikme olur ve üçüncü yeniden deneme 20 saniye gecikmeyle oluşur. retry_interval_backoff_multiplier, varsayılan değer olan 2,0 ile gerçek bir değerdir.

[ **\@retry_attempts =** ] retry_attempts  
İlk girişim başarısız olursa yürütme yeniden denenme sayısı. Örneğin, retry_attempts değeri 10 ise, 1 Başlangıç denemesi ve 10 yeniden deneme girişiminde bulunur ve toplam 11 deneme olur. Son yeniden deneme girişimi başarısız olursa, iş yürütmesi başarısız bir yaşam döngüsü ile sonlandırılır. retry_attempts, varsayılan değer 10 ' dur.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
Adımın yürütülmesi için izin verilen en uzun süre. Bu süre aşılırsa, iş yürütmesi zaman aşımı yaşam döngüsü ile sonlandırılır. step_timeout_seconds, varsayılan değer olan 43.200 saniyedir (12 saat).

[ **\@output_type =** ] ' output_type '  
Null değilse, komutun ilk sonuç kümesinin yazıldığı hedefin türü. Output_type değerini NULL olarak sıfırlamak için, bu parametrenin değerini ' ' olarak ayarlayın (boş dize). output_type nvarchar (50), varsayılan değeri NULL.

Belirtilmişse değer SqlDatabase olmalıdır.

[ **\@output_credential_name =** ] ' output_credential_name '  
Null değilse, çıkış hedef veritabanına bağlanmak için kullanılan veritabanı kapsamlı kimlik bilgisinin adı. Output_type SqlDatabase ise belirtilmesi gerekir. Output_credential_name değerini NULL olarak sıfırlamak için, bu parametrenin değerini ' ' olarak ayarlayın (boş dize). output_credential_name, varsayılan değeri NULL olan nvarchar (128) değeridir.

[ **\@output_server_name =** ] ' output_server_name '  
Null değilse, çıkış hedefi veritabanını içeren sunucunun tam DNS adı. Output_type SqlDatabase ise belirtilmesi gerekir. Output_server_name değerini NULL olarak sıfırlamak için, bu parametrenin değerini ' ' olarak ayarlayın (boş dize). output_server_name nvarchar (256), varsayılan değeri NULL.

[ **\@output_database_name =** ] ' output_database_name '  
Null değilse, çıkış hedefi tablosunu içeren veritabanının adı. Output_type SqlDatabase ise belirtilmesi gerekir. Output_database_name değerini NULL olarak sıfırlamak için, bu parametrenin değerini ' ' olarak ayarlayın (boş dize). output_database_name nvarchar (128), varsayılan değeri NULL.

[ **\@output_schema_name =** ] ' output_schema_name '  
Null değilse, çıkış hedefi tablosunu içeren SQL şemasının adı. Output_type SqlDatabase öğesine eşitse, varsayılan değer dbo olur. Output_schema_name değerini NULL olarak sıfırlamak için, bu parametrenin değerini ' ' olarak ayarlayın (boş dize). output_schema_name nvarchar (128).

[ **\@output_table_name =** ] ' output_table_name '  
Null değilse, komutun ilk sonuç kümesinin yazılacağı tablonun adı. Tablo önceden yoksa, döndürülen sonuç kümesi şemasına göre oluşturulur. Output_type SqlDatabase ise belirtilmesi gerekir. Output_server_name değerini NULL olarak sıfırlamak için, bu parametrenin değerini ' ' olarak ayarlayın (boş dize). output_table_name, varsayılan değeri NULL olan nvarchar (128) değeridir.

[ **\@job_version =** ] job_version çıkışı  
Yeni iş sürümü numarasına atanacak çıkış parametresi. job_version int.

[ **\@max_parallelism =** ] max_parallelism çıkışı  
Elastik havuz başına en yüksek paralellik düzeyi. Ayarlanırsa, iş adımı yalnızca elastik havuz başına en fazla veritabanı üzerinde çalışacak şekilde kısıtlanır. Bu, doğrudan hedef gruba dahil edilen veya hedef gruba dahil olan bir sunucu içinde olan her esnek havuz için geçerlidir. Max_parallelism değerini null olarak sıfırlamak için, bu parametrenin değerini-1 olarak ayarlayın. max_parallelism int.


#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
İşin devam eden tüm yürütmeleri etkilenmeyecektir. Sp_update_jobstep başarılı olduğunda, işin sürüm numarası artırılır. İşin bir sonraki çalıştırılışında yeni sürüm kullanılacaktır.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:

- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir




### <a name="sp_delete_jobstep"></a>sp_delete_jobstep

Bir işten iş adımını kaldırır.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@job_name =** ] ' job_name '  
Adımın kaldırılacağı işin adı. job_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@step_id =** ] step_id  
Silinecek iş adımının kimlik numarası. Step_id ya da step_name belirtilmelidir. step_id bir int.

[ **\@step_name =** ] ' step_name '  
Silinecek adımın adı. Step_id ya da step_name belirtilmelidir. step_name nvarchar (128).

[ **\@job_version =** ] job_version çıkışı  
Yeni iş sürümü numarasına atanacak çıkış parametresi. job_version int.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
İşin devam eden tüm yürütmeleri etkilenmeyecektir. Sp_update_jobstep başarılı olduğunda, işin sürüm numarası artırılır. İşin bir sonraki çalıştırılışında yeni sürüm kullanılacaktır.

Diğer iş adımları, silinen iş adımının solundaki boşluğu dolduracak şekilde otomatik olarak yeniden numaralandırılır.
 
#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.






### <a name="sp_start_job"></a>sp_start_job

İşi yürütmeye başlar.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@job_name =** ] ' job_name '  
Adımın kaldırılacağı işin adı. job_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@job_execution_id =** ] job_execution_id çıkışı  
İş yürütmenin kimliğine atanacak çıkış parametresi. job_version uniqueidentifier.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Yok.
 
#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.

### <a name="sp_stop_job"></a>sp_stop_job

Bir iş yürütmesini sonlandırır.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@job_execution_id =** ] job_execution_id  
Durdurulacak iş yürütmenin kimlik numarası. job_execution_id, varsayılan değeri NULL olan uniqueidentifier.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Yok.
 
#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.


### <a name="sp_add_target_group"></a>sp_add_target_group

Bir hedef grup ekler.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@target_group_name =** ] ' target_group_name '  
Oluşturulacak hedef grubun adı. target_group_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@target_group_id =** ] target_group_id çıkış başarıyla oluşturulursa, işe atanan hedef grup kimlik numarası. target_group_id, varsayılan değeri NULL olan uniqueidentifier türünde bir çıkış değişkenidir.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Hedef gruplar, bir işi veritabanı koleksiyonunda hedeflemek için kolay bir yol sağlar.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.

### <a name="sp_delete_target_group"></a>sp_delete_target_group

Hedef grubu siler.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@target_group_name =** ] ' target_group_name '  
Silinecek hedef grubun adı. target_group_name, varsayılan değer olmadan nvarchar (128) ' dir.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Yok.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.

### <a name="sp_add_target_group_member"></a>sp_add_target_group_member

Hedef gruba bir veritabanı veya veritabanı grubu ekler.

#### <a name="syntax"></a>Sözdizimi

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@target_group_name =** ] ' target_group_name '  
Üyenin ekleneceği hedef grubun adı. target_group_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@membership_type =** ] ' membership_type '  
Hedef grubu üyesinin dahil edilip edilmeyeceğini veya dışlanacağını belirtir. target_group_name, varsayılan değer olan ' Include ' ile nvarchar (128). Target_group_name için geçerli değerler ' Include ' veya ' exclude '.

[ **\@target_type =** ] ' target_type '  
Bir sunucudaki tüm veritabanları, bir elastik havuzdaki tüm veritabanları, parça haritadaki tüm veritabanları veya ayrı bir veritabanı dahil olmak üzere hedef veritabanının türü veya veritabanı koleksiyonu. target_type, varsayılan değer olmadan nvarchar (128) ' dir. Target_type için geçerli değerler ' SqlServer ', ' Sqtalakpool ', ' SqlDatabase ' veya ' SqlShardMap '. 

[ **\@refresh_credential_name =** ] ' refresh_credential_name '  
SQL veritabanı sunucusunun adı. refresh_credential_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@SERVER_NAME =** ] ' server_name '  
Belirtilen hedef gruba eklenmesi gereken SQL veritabanı sunucusunun adı. target_type ' SqlServer ' olduğunda server_name belirtilmelidir. server_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@database_name =** ] ' database_name '  
Belirtilen hedef gruba eklenmesi gereken veritabanının adı. target_type ' SqlDatabase ' olduğunda database_name belirtilmelidir. database_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@elastic_pool_name =** ] ' elastic_pool_name '  
Belirtilen hedef gruba eklenmesi gereken elastik havuzun adı. target_type ' Sqtalakpool ' olduğunda elastic_pool_name belirtilmelidir. elastic_pool_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@shard_map_name =** ] ' shard_map_name '  
Belirtilen hedef gruba eklenmesi gereken parça eşleme havuzunun adı. target_type ' SqlSqlShardMap ' olduğunda elastic_pool_name belirtilmelidir. shard_map_name, varsayılan değer olmadan nvarchar (128) ' dir.

[ **\@target_id =** ] target_group_id çıkışı  
Oluşturulmuş hedef grup üyesine atanan hedef kimlik numarası, hedef gruba eklendiyse. target_id, varsayılan değeri NULL olan uniqueidentifier türünde bir çıkış değişkenidir.
Dönüş kodu değerleri 0 (başarı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Bir iş, hedef gruba bir SQL veritabanı sunucusu veya elastik havuz dahil edildiğinde, bir SQL veritabanı sunucusu içindeki tüm tek veritabanlarında veya yürütme sırasında elastik havuzda yürütülür.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.

#### <a name="examples"></a>Örnekler
Aşağıdaki örnek, Londra ve NewYork sunucularındaki tüm veritabanlarını müşteri bilgilerini tutarak grup sunucularına ekler. Bu durumda, iş aracısını oluştururken belirtilen işler veritabanına bağlanmanız gerekir.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Hedef grup üyesini hedef gruptan kaldırır.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Bağımsız değişkenler [@target_group_name =] ' target_group_name '  
Hedef grup üyesinin kaldırılacağı hedef grubun adı. target_group_name, varsayılan değer olmadan nvarchar (128) ' dir.

[@target_id =] target_id  
 Kaldırılacak hedef grup üyesine atanan hedef kimlik numarası. target_id, varsayılan değeri NULL olan bir uniqueidentifier.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarılı) veya 1 (hata)

#### <a name="remarks"></a>Açıklamalar
Hedef gruplar, bir işi veritabanı koleksiyonunda hedeflemek için kolay bir yol sağlar.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.

#### <a name="examples"></a>Örnekler
Aşağıdaki örnek, müşteri bilgilerini korumak için Londra sunucusunu grup sunucularından kaldırır. Bu durumda, iş aracısını oluştururken belirtilen işler veritabanına bağlanmanız gerekir.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Bir işin geçmiş kayıtlarını kaldırır.

#### <a name="syntax"></a>Sözdizimi


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Bağımsız Değişkenler
[ **\@job_name =** ] ' job_name '  
Geçmiş kayıtlarının silineceği işin adı. job_name nvarchar (128), varsayılan değeri NULL. Job_id ya da job_name belirtilmelidir, ancak her ikisi de belirtilemez.

[ **\@job_id =** ] job_id  
 Silinecek kayıtlar için işin iş kimliği numarası. job_id, varsayılan değeri NULL olan uniqueidentifier. Job_id ya da job_name belirtilmelidir, ancak her ikisi de belirtilemez.

[ **\@oldest_date =** ] oldest_date  
 Geçmişte tutulacak en eski kayıt. oldest_date, varsayılan değer olan DATETIME2. Oldest_date belirtildiğinde, sp_purge_jobhistory yalnızca belirtilen değerden eski olan kayıtları kaldırır.

#### <a name="return-code-values"></a>Dönüş kodu değerleri
0 (başarı) veya 1 (hata) açıklamalar hedef grupları, bir işi veritabanı koleksiyonunda hedeflemek için kolay bir yol sağlar.

#### <a name="permissions"></a>İzinler
Varsayılan olarak, sysadmin sabit sunucu rolünün üyeleri bu saklı yordamı yürütebilir. Bir kullanıcıyı yalnızca işleri izlemeye kısıtlama, kullanıcıyı iş Aracısı oluştururken belirtilen iş Aracısı veritabanında aşağıdaki veritabanı rolünün bir parçası olarak verebilirsiniz:
- jobs_reader

Bu rollerin izinleri hakkında daha fazla bilgi için bu belgenin Izin bölümüne bakın. Yalnızca sysadmin üyeleri, diğer kullanıcılara ait olan işlerin özniteliklerini düzenlemek için bu saklı yordamı kullanabilir.

#### <a name="examples"></a>Örnekler
Aşağıdaki örnek, Londra ve NewYork sunucularındaki tüm veritabanlarını müşteri bilgilerini tutarak grup sunucularına ekler. Bu durumda, iş aracısını oluştururken belirtilen işler veritabanına bağlanmanız gerekir.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>İş görünümleri

Aşağıdaki görünümler [işler veritabanında](sql-database-job-automation-overview.md#job-database)kullanılabilir.


|Görünüm  |Açıklama  |
|---------|---------|
|[job_executions](#job_executions-view)     |  İş yürütme geçmişini gösterir.      |
|[Çizelge](#jobs-view)     |   Tüm işleri gösterir.      |
|[job_versions](#job_versions-view)     |   Tüm iş sürümlerini gösterir.      |
|[JobSteps](#jobsteps-view)     |     Her bir işin geçerli sürümündeki tüm adımları gösterir.    |
|[jobstep_versions](#jobstep_versions-view)     |     Her bir işin tüm sürümlerindeki tüm adımları gösterir.    |
|[target_groups](#target_groups-view)     |      Tüm hedef gruplarını gösterir.   |
|[target_group_members](#target_groups_members-view)     |   Tüm hedef grupların tüm üyelerini gösterir.      |


### <a name="job_executions-view"></a>job_executions görünümü

[işler]. [job_executions]

İş yürütme geçmişini gösterir.


|Sütun adı|   Veri türü   |Açıklama|
|---------|---------|---------|
|**job_execution_id**   |benzersiz tanımlayıcı|  İş yürütme örneğinin benzersiz KIMLIĞI.
|**job_name**   |nvarchar (128)  |İşin adı.
|**job_id** |benzersiz tanımlayıcı|  İşin benzersiz KIMLIĞI.
|**job_version**    |int    |İşin sürümü (iş her değiştirililişinde otomatik olarak güncelleştirilir).
|**step_id**    |int|   Adım için benzersiz (Bu iş için) tanımlayıcı. NULL, bunun ana iş yürütmesi olduğunu gösterir.
|**is_active**| sürümleri |Bilgilerin etkin mi yoksa devre dışı mı olduğunu gösterir. 1 etkin işleri gösterir ve 0 etkin değil anlamına gelir.
|**uyor**| nvarchar (50)|İşin durumunu gösteren değer: ' Created ', ' sürüyor ', ' Failed ', ' başarılı ', ' atlandı ', ' SucceededWithSkipped '|
|**create_time**|   datetime2 (7)|   İşin oluşturulduğu tarih ve saat.
|**start_time** |datetime2 (7)|  İşin yürütmeye başladığı tarih ve saat. İş henüz yürütülmemişse NULL.
|**end_time**|  datetime2 (7)    |İşin yürütmenin bittiği tarih ve saat. İş henüz yürütülmemişse veya henüz yürütmeyi tamamlamamışsa NULL.
|**current_attempts**   |int    |Adımın yeniden denenme sayısı. Ana iş 0 olacak, alt iş yürütmeleri yürütme ilkesine göre 1 veya daha büyük olacaktır.
|**current_attempt_start_time** |datetime2 (7)|  İşin yürütmeye başladığı tarih ve saat. NULL, bunun ana iş yürütmesi olduğunu gösterir.
|**last_message**   |nvarchar (max)| İş veya adım geçmişi iletisi. 
|**target_type**|   nvarchar (128)   |Bir sunucudaki tüm veritabanları, bir elastik havuzdaki veya bir veritabanındaki tüm veritabanları dahil hedef veritabanı veya veritabanı koleksiyonu türü. Target_type için geçerli değerler ' SqlServer ', ' Sqtalakpool ' veya ' SqlDatabase ' değerleridir. NULL, bunun ana iş yürütmesi olduğunu gösterir.
|**target_id**  |benzersiz tanımlayıcı|  Hedef grubu üyesinin benzersiz KIMLIĞI.  NULL, bunun ana iş yürütmesi olduğunu gösterir.
|**target_group_name**  |nvarchar (128)  |Hedef grubun adı. NULL, bunun ana iş yürütmesi olduğunu gösterir.
|**target_server_name**|    nvarchar (256)|  Hedef grupta bulunan SQL veritabanı sunucusunun adı. Yalnızca target_type ' SqlServer ' ise belirtilir. NULL, bunun ana iş yürütmesi olduğunu gösterir.
|**target_database_name**   |nvarchar (128)| Hedef grupta bulunan veritabanının adı. Yalnızca target_type ' SqlDatabase ' olduğunda belirtilir. NULL, bunun ana iş yürütmesi olduğunu gösterir.


### <a name="jobs-view"></a>işler görünümü

[işler]. Çizelge

Tüm işleri gösterir.

|Sütun adı|   Veri türü|  Açıklama|
|------|------|-------|
|**job_name**|  nvarchar (128)   |İşin adı.|
|**job_id**|    benzersiz tanımlayıcı    |İşin benzersiz KIMLIĞI.|
|**job_version**    |int    |İşin sürümü (iş her değiştirililişinde otomatik olarak güncelleştirilir).|
|**açıklaması**    |nvarchar (512)| İş için açıklama. etkin bit, işin etkin veya devre dışı olduğunu belirtir. 1 etkin işleri gösterir ve 0 devre dışı işleri gösterir.|
|**schedule_interval_type** |nvarchar (50)   |İşin ne zaman yürütüleceğini belirten değer: ' bir kez ', ' dakika ', ' saat ', ' Days ', ' hafta ', ' ay '
|**schedule_interval_count**|   int|    İşin her yürütmesi arasında gerçekleştirilecek schedule_interval_type dönemi sayısı.|
|**schedule_start_time**    |datetime2 (7)|  İşin en son başladığı tarih ve saat.|
|**schedule_end_time**| datetime2 (7)|   İşin yürütmenin en son tamamlandığı tarih ve saat.|


### <a name="job_versions-view"></a>job_versions görünümü

[işler]. [job_versions]

Tüm iş sürümlerini gösterir.

|Sütun adı|   Veri türü|  Açıklama|
|------|------|-------|
|**job_name**|  nvarchar (128)   |İşin adı.|
|**job_id**|    benzersiz tanımlayıcı    |İşin benzersiz KIMLIĞI.|
|**job_version**    |int    |İşin sürümü (iş her değiştirililişinde otomatik olarak güncelleştirilir).|


### <a name="jobsteps-view"></a>JobSteps görünümü

[işler]. [JobSteps]

Her bir işin geçerli sürümündeki tüm adımları gösterir.

|Sütun adı    |Veri türü| Açıklama|
|------|------|-------|
|**job_name**   |nvarchar (128)| İşin adı.|
|**job_id** |benzersiz tanımlayıcı   |İşin benzersiz KIMLIĞI.|
|**job_version**|   int|    İşin sürümü (iş her değiştirililişinde otomatik olarak güncelleştirilir).|
|**step_id**    |int    |Adım için benzersiz (Bu iş için) tanımlayıcı.|
|**step_name**  |nvarchar (128)  |Adımın benzersiz (Bu iş için) adı.|
|**command_type**   |nvarchar (50)   |İş adımında yürütülecek komutun türü. V1 için, değer ' TSql ' değerine eşit ve varsayılan değer olarak ayarlanmalıdır.|
|**command_source** |nvarchar (50)|  Komutun konumu. V1 için, ' Inline ' varsayılan ve yalnızca kabul edilen değerdir.|
|**komutundaki**|   nvarchar (max)|  Command_type aracılığıyla elastik işler tarafından yürütülecek komutlar.|
|**credential_name**|   nvarchar (128)   |İşi yürütmek için kullanılan veritabanı kapsamlı kimlik bilgisinin adı.|
|**target_group_name**| nvarchar (128)   |Hedef grubun adı.|
|**target_group_id**|   benzersiz tanımlayıcı|   Hedef grubun benzersiz KIMLIĞI.|
|**initial_retry_interval_seconds**|    int |İlk yeniden deneme denemesinden önceki gecikme. Varsayılan değer 1 ' dir.|
|**maximum_retry_interval_seconds** |int|   Yeniden deneme girişimleri arasındaki gecikme üst sınırı. Denemeler arasındaki gecikme bu değerden daha büyük büyürken, bunun yerine bu değere eşit hale gelir. Varsayılan değer 120 ' dir.|
|**retry_interval_backoff_multiplier**  |gerçek|  Birden çok iş adımı yürütme denemesi başarısız olursa yeniden deneme gecikmesine uygulanacak çarpan. Varsayılan değer 2,0 ' dir.|
|**retry_attempts** |int|   Bu adım başarısız olursa, kullanılacak yeniden deneme sayısı. Varsayılan değer 10 ' dur. Bu, yeniden deneme girişimi olmadığını gösterir.|
|**step_timeout_seconds**   |int|   Yeniden deneme girişimleri arasındaki dakika cinsinden süre. Varsayılan değer 0 dakikalık bir aralığı gösteren 0 ' dır.|
|**output_type**    |nvarchar (11)|  Komutun konumu. Geçerli önizlemede, ' Inline ' varsayılan ve yalnızca kabul edilen değerdir.|
|**output_credential_name**|    nvarchar (128)   |Sonuç kümesini depolamak üzere hedef sunucuya bağlanmak için kullanılacak kimlik bilgilerinin adı.|
|**output_subscription_id**|    benzersiz tanımlayıcı|   Sorgu yürütmeden sonuçlar kümesi için hedef server\database aboneliğinin benzersiz KIMLIĞI.|
|**output_resource_group_name** |nvarchar (128)| Hedef sunucunun bulunduğu kaynak grubu adı.|
|**output_server_name**|    nvarchar (256)   |Sonuç kümesi için hedef sunucunun adı.|
|**output_database_name**   |nvarchar (128)| Sonuç kümesi için hedef veritabanının adı.|
|**output_schema_name** |nvarchar (max)| Hedef şemanın adı. Belirtilmezse, varsayılan olarak dbo olur.|
|**output_table_name**| nvarchar (max)|  Sorgu sonuçlarından sonuçları kümesinin depolayabileceği tablonun adı. Tablo, zaten mevcut değilse, sonuçlar kümesi şemasına göre otomatik olarak oluşturulur. Şemanın sonuçlar kümesi şemasıyla eşleşmesi gerekir.|
|**max_parallelism**|   int|    İş adımının her seferinde çalışacağı, esnek havuz başına en fazla veritabanı sayısı. Varsayılan değer NULL, yani hiçbir sınır yoktur. |


### <a name="jobstep_versions-view"></a>jobstep_versions görünümü

[işler]. [jobstep_versions]

Her bir işin tüm sürümlerindeki tüm adımları gösterir. Şema, [JobSteps](#jobsteps-view)ile aynıdır.

### <a name="target_groups-view"></a>target_groups görünümü

[işler]. [target_groups]

Tüm hedef gruplarını listeler.

|Sütun adı|Veri türü| Açıklama|
|-----|-----|-----|
|**target_group_name**| nvarchar (128)   |Hedef grubun adı, veritabanı koleksiyonu. 
|**target_group_id**    |benzersiz tanımlayıcı   |Hedef grubun benzersiz KIMLIĞI.

### <a name="target_groups_members-view"></a>target_groups_members görünümü

[işler]. [target_groups_members]

Tüm hedef grupların tüm üyelerini gösterir.

|Sütun adı|Veri türü| Açıklama|
|-----|-----|-----|
|**target_group_name**  |nvarchar (128|Hedef grubun adı, veritabanı koleksiyonu. |
|**target_group_id**    |benzersiz tanımlayıcı   |Hedef grubun benzersiz KIMLIĞI.|
|**membership_type**    |int|   Hedef grup üyesinin hedef gruba dahil edilip edilmediğini belirtir. Target_group_name için geçerli değerler ' Include ' veya ' exclude '.|
|**target_type**    |nvarchar (128)| Bir sunucudaki tüm veritabanları, bir elastik havuzdaki veya bir veritabanındaki tüm veritabanları dahil hedef veritabanı veya veritabanı koleksiyonu türü. Target_type için geçerli değerler ' SqlServer ', ' Sqtalakpool ', ' SqlDatabase ' veya ' SqlShardMap '.|
|**target_id**  |benzersiz tanımlayıcı|  Hedef grubu üyesinin benzersiz KIMLIĞI.|
|**refresh_credential_name**    |nvarchar (128)  |Hedef grup üyesine bağlanmak için kullanılan veritabanı kapsamlı kimlik bilgisinin adı.|
|**subscription_id**    |benzersiz tanımlayıcı|  Aboneliğin benzersiz KIMLIĞI.|
|**resource_group_name**    |nvarchar (128)| Hedef grup üyesinin bulunduğu kaynak grubunun adı.|
|**server_name**    |nvarchar (128)  |Hedef grupta bulunan SQL veritabanı sunucusunun adı. Yalnızca target_type ' SqlServer ' ise belirtilir. |
|**database_name**  |nvarchar (128)  |Hedef grupta bulunan veritabanının adı. Yalnızca target_type ' SqlDatabase ' olduğunda belirtilir.|
|**elastic_pool_name**  |nvarchar (128)| Hedef grupta bulunan elastik havuzun adı. Yalnızca target_type ' Sqtalakpool ' olduğunda belirtilir.|
|**shard_map_name** |nvarchar (128)| Hedef grupta bulunan parça eşlemesinin adı. Yalnızca target_type ' SqlShardMap ' olduğunda belirtilir.|


## <a name="resources"></a>Kaynaklar

 - ![Konu bağlantı simgesi](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Konu bağlantı simgesi") [Transact-SQL sözdizimi kuralları](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Sonraki adımlar

- [PowerShell’i kullanarak Elastik İşler oluşturma ve yönetme](elastic-jobs-powershell.md)
- [Yetkilendirme ve Izinler SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)

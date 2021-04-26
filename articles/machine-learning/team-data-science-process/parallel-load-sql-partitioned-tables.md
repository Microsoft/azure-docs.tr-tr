---
title: SQL bölüm tablolarında paralel toplu veri içeri aktarma-takım veri bilimi Işlemi
description: Verilerin bir SQL Server veritabanına hızlı paralel toplu içe aktarılması için bölümlenmiş tablolar oluşturun.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 456e881d84697f4542f972ac0798cc95a3455b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322413"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Verileri bir Azure VM 'de SQL Server hızlı paralel içeri aktarma için tabloları derleme ve iyileştirme

Bu makalede, verilerin bir SQL Server veritabanına hızlı paralel toplu içe aktarılması için bölümlenmiş tabloların nasıl oluşturulacağı açıklanır. Büyük veri yükleme/bir SQL veritabanına aktarma için SQL veritabanına veri aktarma ve sonraki sorgular *bölümlenmiş tablolar ve görünümler* kullanılarak artırılabilir. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Yeni bir veritabanı ve dosya grubu kümesi oluşturma
* Henüz yoksa [Yeni bir veritabanı oluşturun](/sql/t-sql/statements/create-database-transact-sql).
* Veritabanı dosya gruplarını, bölümlenmiş fiziksel dosyaları tutan veritabanına ekleyin. 
* Bu, veritabanı zaten varsa [Create Database](/sql/t-sql/statements/create-database-transact-sql) for New veya [alter database](/sql/t-sql/statements/alter-database-transact-sql-set-options) ile yapılabilir.
* Her veritabanı dosya grubuna bir veya daha fazla dosya (gerektiğinde) ekleyin.
  
  > [!NOTE]
  > Bu bölüm için verileri tutan hedef dosya grubunu ve dosya grubu verilerinin depolandığı fiziksel veritabanı dosya adlarını belirtin.
  > 
  > 

Aşağıdaki örnek, her birinde bir fiziksel dosya içeren birincil ve günlük gruplarından farklı üç dosya grubuna sahip yeni bir veritabanı oluşturur. Veritabanı dosyaları, SQL Server örneğinde yapılandırıldığı gibi varsayılan SQL Server veri klasöründe oluşturulur. Varsayılan dosya konumları hakkında daha fazla bilgi için bkz. [varsayılan ve adlandırılmış SQL Server örnekleri Için dosya konumları](/sql/sql-server/install/file-locations-for-default-and-named-instances-of-sql-server).

```sql
   DECLARE @data_path nvarchar(256);
   SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

   EXECUTE ('
      CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
```

## <a name="create-a-partitioned-table"></a>Bölümlenmiş tablo oluşturma
Önceki adımda oluşturulan veritabanı dosya grupları ile eşlenen veri şemasına göre bölümlenmiş tablolar oluşturmak için, önce bir bölüm işlevi ve düzeni oluşturmanız gerekir. Veriler bölümlenmiş tablo (lar) a toplu olarak içeri aktarıldığında, kayıtlar, aşağıda açıklandığı gibi bir bölüm şemasına göre dosya grupları arasında dağıtılır.

### <a name="1-create-a-partition-function"></a>1. Bölüm işlevi oluşturma
[Bölüm Işlevi oluşturma](/sql/t-sql/statements/create-partition-function-transact-sql) Bu işlev, her bir bölüm tablosuna dahil edilecek değer/sınır aralığını tanımlar, örneğin, bölümleri aya göre (bazı \_ Tarih saat \_ alanı) 2013 yılında sınırlamak için:
  
```sql
   CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
      AS RANGE RIGHT FOR VALUES (
         '20130201', '20130301', '20130401',
         '20130501', '20130601', '20130701', '20130801',
         '20130901', '20131001', '20131101', '20131201' )
```

### <a name="2-create-a-partition-scheme"></a>2. Bölüm şeması oluşturma
[Bölüm Şeması oluşturun](/sql/t-sql/statements/create-partition-scheme-transact-sql). Bu düzen, Bölüm işlevindeki her bölüm aralığını fiziksel bir dosya grubuyla eşler, örneğin:
  
```sql
      CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
```
 
Her bölümde işlev/düzene göre geçerli olan aralıkları doğrulamak için aşağıdaki sorguyu çalıştırın:
  
```sql
   SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
   FROM sys.partition_functions AS pfun
   INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
   INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
   WHERE pfun.name = <DatetimeFieldPFN>
```

### <a name="3-create-a-partition-table"></a>3. bölüm tablosu oluşturma
Veri şemanıza göre [bölümlenmiş tablolar oluşturun](/sql/t-sql/statements/create-table-transact-sql)ve tabloyu bölümlemek için kullanılan bölüm şemasını ve kısıtlama alanını belirtin, örneğin:
  
```sql
   CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)
```

Daha fazla bilgi için bkz. [bölümlenmiş tablolar ve dizinler oluşturma](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Her bir bölüm tablosu için verileri toplu içe aktarma

* BCP, BULK INSERT ya da [SQL Server Geçiş Sihirbazı](https://sqlazuremw.codeplex.com/)gibi diğer yöntemleri kullanabilirsiniz. Belirtilen örnek BCP yöntemini kullanır.
* Günlüğe kaydetme yükünü en aza indirmek için, işlem günlüğü düzenini BULK_LOGGED değiştirmek üzere [veritabanını](/sql/t-sql/statements/alter-database-transact-sql-set-options) değiştirin, örneğin:
  
   ```sql
      ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
   ```
* Veri yüklemeyi hızlandırmak için toplu içeri aktarma işlemlerini paralel olarak başlatın. Büyük verilerin SQL Server veritabanlarına toplu olarak içe aktarılması hakkında ipuçları için [1 saatten az bir süre içinde 1 TB yükleme](/archive/blogs/sqlcat/load-1tb-in-less-than-1-hour)bölümüne bakın.

Aşağıdaki PowerShell betiği BCP kullanarak bir paralel veri yükleme örneğidir.

```powershell
    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date
```

## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Birleştirmeleri ve sorgu performansını iyileştirmek için dizinler oluşturma
* Verileri birden çok tablodan modellemeye yönelik olarak ayıkladıysanız, JOIN anahtarlarında, JOIN performansını geliştirmek için dizinler oluşturun.
* Her bölüm için aynı dosya grubunu hedefleyen dizinler (kümelenmiş veya kümelenmemiş) [oluşturun](/sql/t-sql/statements/create-index-transact-sql) , örneğin:
  
```sql
   CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
--  or,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
 ```
 
  > [!NOTE]
  > Verileri toplu içe aktarmadan önce dizinleri oluşturmayı tercih edebilirsiniz. Toplu içeri aktarma işlemi, veri yüklemeyi yavaşlatmadan önce dizin oluşturma.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Gelişmiş analiz Işlemi ve teknoloji eylem örneği
Ortak bir veri kümesiyle ekip veri bilimi Işlemini kullanan uçtan uca bir anlatım örneği için, bkz. [Team Data Science Process ın Action: using SQL Server](sql-walkthrough.md).
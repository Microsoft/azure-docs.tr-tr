---
title: Verileri SQL Server sanal makineye taşıma-Team Data Science Işlemi
description: Verileri düz dosyalardan veya şirket içi SQL Server Azure VM 'de SQL Server taşıyın.
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
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320344"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Bir Azure sanal makinesinde SQL Server’a veri taşıma

Bu makalede, verileri düz dosyalardan (CSV veya TSV biçimlerinden) veya şirket içi SQL Server Azure sanal makinesinde SQL Server taşıma seçenekleri özetlenmektedir. Verileri buluta taşımaya yönelik bu görevler, ekip veri bilimi sürecinin bir parçasıdır.

Machine Learning için verileri bir Azure SQL veritabanına taşıma seçeneklerini özetleyen bir konu için bkz. [Azure Machine Learning için verileri Azure SQL veritabanına taşıma](move-sql-azure.md).

Aşağıdaki tabloda, verileri bir Azure sanal makinesinde SQL Server taşıma seçenekleri özetlenmektedir.

| <b>KAYNAKTAKI</b> | <b>Hedef: Azure VM 'de SQL Server</b> |
| --- | --- |
| <b>Düz dosya</b> |1. <a href="#insert-tables-bcp">komut satırı toplu kopyalama yardımcı programı (bcp) </a><br> 2. <a href="#insert-tables-bulkquery">SQL sorgusunu toplu ekleme </a><br> 3. <a href="#sql-builtin-utilities">SQL Server Içindeki grafik yerleşik yardımcı programlar</a> |
| <b>Şirket Içi SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">bir SQL Server veritabanını MICROSOFT Azure VM sihirbazına dağıtma</a><br> 2. <a href="#export-flat-file">düz bir dosyaya aktarın </a><br> 3. <a href="#sql-migration">SQL veritabanı geçiş Sihirbazı </a> <br> 4. <a href="#sql-backup">veritabanı yedekleme ve geri yükleme </a><br> |

Bu belgede SQL komutlarının SQL Server Management Studio veya Visual Studio Veritabanı Gezgini yürütüldüğü varsayılmaktadır.

> [!TIP]
> Alternatif olarak, verileri Azure 'da bir SQL Server VM taşıyacağınız bir işlem hattı oluşturmak ve zamanlamak için [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Data Factory verileri kopyalama (kopyalama etkinliği)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Önkoşullar
Bu öğreticide şunları kabul edersiniz:

* Bir **Azure aboneliği**. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
* Bir **Azure depolama hesabı**. Bu öğreticide verileri depolamak için bir Azure depolama hesabı kullanacaksınız. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../../storage/common/storage-account-create.md) makalesine bakın. Depolama hesabını oluşturduktan sonra, depolamaya erişmek için kullanılan hesap anahtarını edinmeniz gerekecektir. Bkz. [depolama hesabı erişim anahtarlarını yönetme](../../storage/common/storage-account-keys-manage.md).
* **Azure VM üzerinde SQL Server** sağlandı. Yönergeler için bkz. [Azure SQL Server sanal makinesini gelişmiş analizler için bir IPython Not defteri sunucusu olarak ayarlama](../data-science-virtual-machine/overview.md).
* **Azure PowerShell** yerel olarak yüklendi ve yapılandırıldı. Yönergeler için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Verileri bir Azure VM 'deki SQL Server düz bir dosya kaynağından taşıma
Verileriniz düz bir dosya (satır/sütun biçiminde düzenlenmiş) ise, aşağıdaki yöntemlerle Azure 'da SQL Server VM taşıyabilirsiniz:

1. [Komut satırı toplu kopyalama yardımcı programı (BCP)](#insert-tables-bcp)
2. [SQL sorgusunu toplu ekleme](#insert-tables-bulkquery)
3. [SQL Server 'de grafik yerleşik yardımcı programları (Içeri/dışarı aktarma, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Komut satırı toplu kopyalama yardımcı programı (BCP)
BCP, SQL Server ile yüklenen bir komut satırı yardımcı programıdır ve verileri taşımanın en hızlı yöntemlerinden biridir. Bu, üç SQL Server varyantları (Şirket içi SQL Server, SQL Azure ve SQL Server VM Azure 'da) üzerinde çalışmaktadır.

> [!NOTE]
> **Veri BCP için nerede olacak?**  
> Gerekli olmasa da, hedef SQL Server aynı makinede bulunan kaynak verileri içeren dosyaların bulunması daha hızlı aktarımlara izin verir (ağ hızı ile yerel disk GÇ hızı). Veri içeren düz dosyaları, [AzCopy](../../storage/common/storage-use-azcopy-v10.md), [Azure Depolama Gezgini](https://storageexplorer.com/) veya Windows kopyalama/yapıştırma gibi çeşitli dosya kopyalama araçlarını Uzak Masaüstü Protokolü (RDP) kullanarak taşıyabilirsiniz SQL Server.
>
>

1. Veritabanının ve tabloların hedef SQL Server veritabanında oluşturulduğundan emin olun. Ve komutlarını kullanarak nasıl yapılacağını gösteren bir örnek aşağıda verilmiştir `Create Database` `Create Table` :

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. BCP 'nin yüklü olduğu makinenin komut satırından aşağıdaki komutu vererek tablo şemasını açıklayan biçim dosyasını oluşturun.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Aynı makineye SQL Server yüklendiğinde komut satırından çalışması gereken bcp komutunu kullanarak verileri veritabanına ekleyin:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Bcp eklemeleri Iyileştiriliyor** Bu tür eklemeleri iyileştirmek için lütfen aşağıdaki makaleye [toplu Içeri aktarmayı iyileştirmek Için yönergelere](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) bakın.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Daha hızlı veri hareketi için paralelleştirme eklemeleri
Taşıdığınız veriler büyükse, bir PowerShell betiğinden paralel olarak birden çok BCP komutunu aynı anda yürüten şeyleri hızlandırabilirsiniz.

> [!NOTE]
> **Büyük veri** alımı Büyük ve çok büyük veri kümelerinde veri yüklemeyi iyileştirmek için, mantıksal ve fiziksel veritabanı tablolarınızı birden çok dosya grubu ve bölüm tablosu kullanarak bölümleyin. Bölüm tablolarına veri oluşturma ve yükleme hakkında daha fazla bilgi için bkz. [paralel yük SQL Bölüm tabloları](parallel-load-sql-partitioned-tables.md).
>
>

Aşağıdaki örnek PowerShell betiği bcp kullanarak paralel eklemeleri gösterir:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>SQL sorgusunu toplu ekleme
[Toplu ekleme SQL sorgusu](/sql/t-sql/statements/bulk-insert-transact-sql) , satır/sütun tabanlı dosyalardan veritabanına veri aktarmak için kullanılabilir (desteklenen türler,[Toplu dışa aktarma veya içeri aktarma (SQL Server) için verileri hazırlama](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server)bölümünde ele alınmıştır.

Toplu ekleme için bazı örnek komutlar aşağıda verilmiştir:  

1. SQL Server veritabanının tarih gibi özel alanlar için aynı biçimde olduğundan emin olmak için Verilerinizi çözümleyin ve içeri aktarmadan önce özel seçenekleri ayarlayın. Tarih biçiminin yıl-ay-gün olarak nasıl ayarlanalınacağını gösteren bir örnek aşağıda verilmiştir (verileriniz yıl-ay-gün biçiminde tarihi içeriyorsa):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Toplu içeri aktarma deyimlerini kullanarak verileri içeri aktarın:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>SQL Server yerleşik yardımcı programlar
SQL Server Integration Services (SSIS) kullanarak verileri Azure üzerinde SQL Server VM bir düz dosyadan içeri aktarabilirsiniz.
SSIS iki Studio ortamında kullanılabilir. Ayrıntılar için bkz. [Integration Services (SSIS) ve Studio ortamları](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* SQL Server Veri Araçları Ayrıntılar için bkz. [Microsoft SQL Server veri araçları](/sql/ssdt/download-sql-server-data-tools-ssdt)  
* Içeri/dışarı aktarma Sihirbazı hakkında daha fazla bilgi için bkz. [SQL Server içeri ve dışarı aktarma Sihirbazı](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Verileri şirket içi SQL Server Azure sanal makinesine SQL Server taşıma
Aşağıdaki geçiş stratejilerini de kullanabilirsiniz:

1. [Microsoft Azure VM sihirbazına SQL Server veritabanı dağıtma](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Düz dosyaya aktar](#export-flat-file)
3. [SQL Veritabanı Geçiş Sihirbazı](#sql-migration)
4. [Veritabanı yedekleme ve geri yükleme](#sql-backup)

Aşağıdaki seçeneklerin her birini aşağıda anladık:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Microsoft Azure VM sihirbazına SQL Server veritabanı dağıtma
**SQL Server veritabanını MICROSOFT Azure VM 'ye Dağıtma Sihirbazı** , verileri şirket içi SQL Server örneğinden BIR Azure sanal makinesinde SQL Server taşımak için basit ve önerilen bir yoldur. Ayrıntılı adımlar ve diğer alternatiflere ilişkin bir açıklama için bkz. bir [veritabanını Azure VM 'de SQL Server geçirme](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Düz dosyaya aktar
[SQL Server verileri toplu Içe aktarma ve dışarı aktarma](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) konusunun belgelendiği gibi şirket içi SQL Server verileri toplu olarak dışarı aktarmak için çeşitli yöntemler kullanılabilir. Bu belge, örnek olarak toplu kopyalama programı (BCP) ile ele alınacaktır. Veriler düz bir dosyaya aktarıldığında, toplu içeri aktarma kullanılarak başka bir SQL Server 'a aktarılabilir.

1. Şirket içi SQL Server verileri bcp yardımcı programını kullanarak aşağıdaki gibi bir dosyaya aktarın

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. `create database` `create table` Adım 1 ' de bulunan tablo şeması için ve kullanarak Azure üzerinde SQL Server VM veritabanı ve tablo oluşturun.
3. Aktarılan/içeri aktarılan verilerin tablo şemasını açıklayan bir biçim dosyası oluşturun. Biçim dosyasının ayrıntıları, [Biçim dosyası oluşturma (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server)bölümünde açıklanmaktadır.

    SQL Server bilgisayardan BCP çalıştırırken dosya oluşturmayı Biçimlendir

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    SQL Server için BCP 'yi uzaktan çalıştırırken dosya oluşturmayı Biçimlendir

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Verileri düz dosyalardaki bir SQL Server taşımak için [dosya kaynağından verileri taşıma](#filesource_to_sqlonazurevm) bölümünde açıklanan yöntemlerden herhangi birini kullanın.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>SQL Veritabanı Geçiş Sihirbazı
[SQL Server veritabanı geçiş Sihirbazı](https://sqlazuremw.codeplex.com/) , VERILERI iki SQL Server örneği arasında taşımak için kolay bir yol sağlar. Kullanıcının kaynak ve hedef tablolar arasında veri şemasını eşlemesine izin verir, sütun türlerini ve diğer diğer işlevleri seçin. Bu, kapsamakta olan toplu kopyalama (BCP) kullanır. SQL veritabanı geçiş Sihirbazı için hoş geldiniz ekranının ekran görüntüsü aşağıda gösterilmiştir.  

![SQL Server Geçiş Sihirbazı][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Veritabanı yedekleme ve geri yükleme
SQL Server şunları destekler:

1. [Veritabanı yedekleme ve geri yükleme işlevselliği](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (hem yerel bir dosya ya da bacpac blob 'a dışarı aktarma) ve [veri katmanı uygulamaları](/sql/relational-databases/data-tier-applications/data-tier-applications) (bacpac kullanılarak).
2. Kopyalanmış bir veritabanıyla Azure 'da doğrudan SQL Server VM 'Ler oluşturma veya SQL veritabanı 'nda var olan bir veritabanına kopyalama özelliği. Daha fazla bilgi için bkz. [veritabanını kopyalama Sihirbazı 'Nı kullanma](/sql/relational-databases/databases/use-the-copy-database-wizard).

Veritabanı yedekleme/geri yükleme seçeneklerinin SQL Server Management Studio ekran görüntüsü aşağıda gösterilmiştir.

![SQL Server Içeri aktarma aracı][1]

## <a name="resources"></a>Kaynaklar
[Azure VM 'de bir veritabanını SQL Server geçirme](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Azure Sanal Makineler’de SQL Server’a genel bakış](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
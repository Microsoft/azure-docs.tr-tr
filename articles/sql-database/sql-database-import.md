---
title: Veritabanı oluşturmak için BACPAC dosyasını içeri aktarma
description: Bir BACPAC dosyasını içeri aktararak newAzure SQL veritabanı oluşturun.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 07949f0aac1d18b88eb3ac59a6e7ad91d6865fcd
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166309"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Hızlı başlangıç: BACPAC dosyasını Azure SQL veritabanı 'nda bir veritabanına aktarma

Bir SQL Server veritabanını, [bacpac](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) dosyasını kullanarak Azure SQL veritabanındaki bir veritabanına aktarabilirsiniz. Verileri Azure Blob depolama (yalnızca standart depolama) veya şirket içi bir konumdaki yerel depolama alanında depolanan bir `BACPAC` dosyasından içeri aktarabilirsiniz. Daha fazla ve daha hızlı kaynak sağlayarak içeri aktarma hızını en üst düzeye çıkarmak için, içeri aktarma işlemi sırasında veritabanınızı daha yüksek bir hizmet katmanına ve işlem boyutuna ölçeklendirin. Ardından içeri aktarma işlemi başarılı olduktan sonra ölçeği azaltabilirsiniz.

> [!NOTE]
> İçeri aktarılan veritabanının uyumluluk düzeyi, kaynak veritabanının uyumluluk düzeyi üzerinde temel alır.
> [!IMPORTANT]
> Veritabanınızı içeri aktardıktan sonra veritabanını, geçerli uyumluluk düzeyinde (düzey 100 AdventureWorks2008R2 veritabanı için) veya daha yüksek bir düzeyde çalışılacak seçebilirsiniz. Veritabanını belirli bir uyumluluk düzeyinde çalıştırmanın etkileri ve buna yönelik seçenekler hakkında daha fazla bilgi için, bkz. [Veritabanı Uyumluluk Düzeyini Değiştirme](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Uyumluluk düzeyleriyle ilgili ek veritabanı düzeyi ayarları hakkında bilgi için, ayrıca bkz. [VERİTABANI KAPSAMLI YAPILANDIRMAYI DEĞİŞTİRME](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Azure portalında bir BACPAC dosyasından alma

Azure portal BACPAC dosyasından içeri aktarmayı öğrenmek için bu videoyu izleyin veya aşağıdaki okumaya devam edin:

> [!VIDEO hhttps://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure Portal](https://portal.azure.com) *yalnızca* Azure SQL veritabanı 'Nda ve *yalnızca* Azure Blob depolamada depolanan bir bacpac dosyasından tek bir veritabanı oluşturulmasını destekler.

Bir veritabanını Azure PowerShell kullanılarak BACPAC dosyasından [yönetilen bir örneğe](sql-database-managed-instance.md) geçirme Şu anda desteklenmiyor. Bunun yerine SQL Server Management Studio veya SQLPackage kullanın.

> [!NOTE]
> Azure portal veya PowerShell aracılığıyla gönderilen içeri/dışarı aktarma isteklerini işleyen makineler, BACPAC dosyasını ve veri katmanı uygulama çerçevesi (DacFX) tarafından oluşturulan geçici dosyaları da depolaması gerekir. Gerekli disk alanı, aynı boyuta sahip veritabanları arasında önemli ölçüde farklılık gösterir ve veritabanı boyutunun 3 katına kadar disk alanı gerektirebilir. İçeri/dışarı aktarma isteği çalıştıran makinelerde yalnızca 450GB yerel disk alanı vardır. Sonuç olarak, bazı istekler `There is not enough space on the disk`hata vererek başarısız olabilir. Bu durumda geçici çözüm, SqlPackage. exe ' yi yeterli yerel disk alanına sahip bir makinede çalıştırmalıdır. Bu sorundan kaçınmak için, [SQL paketini](#import-from-a-bacpac-file-using-sqlpackage) 150gb 'den büyük veritabanlarını içeri/dışarı aktarmak için kullanmayı öneririz.
 
1. Bir BACPAC dosyasından Azure portal kullanarak yeni bir tek veritabanına aktarmak için, uygun veritabanı sunucusu sayfasını açın ve ardından araç çubuğundan **veritabanını Içeri aktar**' ı seçin.  

   ![Veritabanı import1](./media/sql-database-import/import1.png)

2. BACPAC dosyası için depolama hesabını ve kapsayıcıyı seçin ve ardından İçeri aktarılacak BACPAC dosyasını seçin.
3. Yeni veritabanı boyutu (genellikle kaynak aynı) belirtin ve hedef SQL Server kimlik bilgilerini sağlayın. Yeni bir Azure SQL veritabanı için olası değerlerin bir listesi için, bkz. [veritabanı oluşturma](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Veritabanı import2](./media/sql-database-import/import2.png)

4. **OK (Tamam)** düğmesine tıklayın.

5. İçeri aktarmanın ilerlemesini izlemek için veritabanının sunucu sayfasını açın ve **Ayarlar**altında **Içeri/dışarı aktarma geçmişi**' ni seçin. Başarılı olduğunda, içeri aktarma sahip bir **tamamlandı** durumu.

   ![Veritabanı içeri aktarma durumu](./media/sql-database-import/import-status.png)

6. Veritabanının veritabanı sunucusunda canlı olduğunu doğrulamak için **SQL veritabanları** ' nı seçin ve yeni veritabanının **çevrimiçi**olduğunu doğrulayın.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SqlPackage kullanarak BACPAC dosyasından alma

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) komut satırı yardımcı programını kullanarak bir SQL Server veritabanını içeri aktarmak için, bkz. [içeri aktarma parametreleri ve özellikleri](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage, [Visual Studio için](https://msdn.microsoft.com/library/mt204009.aspx)en son [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ve SQL Server veri araçları sahiptir. En son da indirebilirsiniz [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) Microsoft İndirme Merkezi'nden.

Ölçek ve performans için, Azure portal kullanmak yerine, çoğu üretim ortamında SqlPackage kullanılması önerilir. `BACPAC` dosyalarını kullanarak geçirme hakkında bir SQL Server müşteri danışmanlık ekibi blogu için, bkz. [BACPAC dosyalarını kullanarak SQL Server 'Den Azure SQL veritabanı 'na geçiş](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Aşağıdaki SqlPackage komutu, **AdventureWorks2008R2** veritabanını yerel depolamadan **Mynewserver20170403**adlı bir Azure SQL veritabanı sunucusuna aktarır. Adlı yeni bir veritabanı oluşturur **myMigratedDatabase** ile bir **Premium** hizmet katmanı ve bir **P6** hizmet hedefi. Bu değerleri ortamınız için uygun şekilde değiştirin.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Şirket güvenlik duvarının ardında tek bir veritabanını yöneten bir SQL veritabanı sunucusuna bağlanmak için, güvenlik duvarında 1433 numaralı bağlantı noktası açık olmalıdır. Yönetilen bir örneğe bağlanmak için, [Noktadan siteye bağlantı](sql-database-managed-instance-configure-p2s.md) veya Express Route bağlantısına sahip olmanız gerekir.
>

Bu örnek, bir veritabanını Active Directory Evrensel kimlik doğrulaması ile SqlPackage kullanarak içeri aktarma gösterir.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>PowerShell kullanarak BACPAC dosyasından tek bir veritabanına aktarma

> [!NOTE]
> [Yönetilen bir örnek](sql-database-managed-instance.md) Şu anda bir veritabanını Azure PowerShell kullanılarak bacpac dosyasından bir örnek veritabanına geçirmeyi desteklememektedir. Yönetilen bir örneğe aktarmak için SQL Server Management Studio veya SQLPackage kullanın.

> [!NOTE]
> Portal veya PowerShell aracılığıyla gönderilen içeri/dışarı aktarma isteklerini işleyen makinelerin bacpac dosyasını ve veri katmanı uygulaması çerçevesi (DacFX) tarafından oluşturulan geçici dosyaları depolaması gerekir. Gerekli disk alanı, aynı boyuta sahip olan veritabanları arasında önemli ölçüde farklılık gösterir ve veritabanı boyutunun 3 ' i kadar katına yararlanabilir. İçeri/dışarı aktarma isteği çalıştıran makinelerde yalnızca 450GB yerel disk alanı vardır. Sonuç olarak, bazı istekler "diskte yeterli alan yok" hatasını vererek başarısız olabilir. Bu durumda geçici çözüm, SqlPackage. exe ' yi yeterli yerel disk alanına sahip bir makinede çalıştırmalıdır. 150GB 'den büyük veritabanlarını içeri/dışarı aktarırken, bu sorundan kaçınmak için [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) kullanın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Azure SQL veritabanı hizmetine bir veritabanı içeri aktarma isteği göndermek için [New-Azsqldatabaseımport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet 'ini kullanın. Veritabanı boyutuna bağlı olarak içeri aktarma tamamlanması biraz zaman alabilir.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Almanın ilerlemesini denetlemek için [Get-Azsqldatabaseımportexportstatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet 'ini kullanabilirsiniz. Cmdlet hemen sonra istek genellikle döndürür çalıştıran **durumu: Inprogress**. Gördüğünüzde, içeri aktarma tamamlandıktan **durumu: başarılı**.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Başka bir komut dosyası örneği için bkz. [veritabanını BACPAC dosyasından içeri aktarma](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Sınırlamalar

Elastik havuzdaki bir veritabanı için içeri aktarma desteklenmiyor. Verileri tek bir veritabanına aktarabilir ve sonra veritabanını elastik bir havuza taşıyabilirsiniz.

## <a name="import-using-wizards"></a>İçeri aktarma sihirbazları

Bu sihirbazlar de kullanabilirsiniz.

- [SQL Server Management Studio'daki veri katmanı Uygulama Sihirbazı Alma](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server içeri ve Dışarı Aktarma Sihirbazı'nı](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Sonraki adımlar

- Bağlanmak ve içeri aktarılan bir SQL veritabanını sorgulama hakkında bilgi edinmek için [hızlı başlangıç: Azure SQL veritabanı: bağlanmak ve veri sorgulamak için kullanım SQL Server Management Studio](sql-database-connect-query-ssms.md).
- BACPAC dosyalarını kullanarak geçiş hakkında bir SQL Server Müşteri Danışmanlık Ekibi blogu için bkz. [BACPAC Dosyalarını kullanarak SQL Server’dan Azure SQL Veritabanına Geçiş](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Performans önerileri de dahil olmak üzere tüm SQL Server veritabanı geçiş işlemi, hakkında ayrıntılı bilgi için bkz. [Azure SQL veritabanı için SQL Server veritabanı geçişi](sql-database-single-database-migrate.md).
- Depolama anahtarları ve paylaşılan erişim imzaları güvenli bir şekilde, bkz: yönetmek ve paylaşmak hakkında bilgi edinmek için [Azure depolama Güvenlik Kılavuzu](https://docs.microsoft.com/azure/storage/common/storage-security-guide).

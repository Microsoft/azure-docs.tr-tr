---
title: Bir yedeklemeyi yönetilen örneğe geri yükleme
description: SSMS kullanarak veritabanı yedeklemesini Azure SQL Veritabanı Yönetilen Örneğine geri yükleyin.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 37f7366d6622356017e458fb8f893b0be0851335
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825703"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Hızlı başlangıç: bir veritabanını yönetilen örneğe geri yükleme

Bu hızlı başlangıçta, Azure Blob depolamadan bir veritabanını (geniş dünya Importers-standart yedekleme dosyası) Azure SQL veritabanı [yönetilen örneğine](sql-database-managed-instance.md)geri yüklemek için SQL Server Management Studio (SSMS) kullanacaksınız.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Azure veritabanı geçiş hizmeti 'ni (DMS) kullanarak geçiş hakkında daha fazla bilgi için bkz. [DMS kullanarak yönetilen örnek geçişi](../dms/tutorial-sql-server-to-managed-instance.md).
> Çeşitli geçiş yöntemleri hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı yönetilen örneği 'ne SQL Server örnek geçişi](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıç:

- [Yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md) hızlı başlangıcı ' ndan kaynakları kullanır.
- Bilgisayarınızda en son [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) yüklü olmasını gerektirir.
- Yönetilen örneğinize bağlanmak için SSMS kullanılması gerekir. Nasıl bağlanadığına ilişkin şu hızlı başlangıçlara bakın:
  - Yönetilen örnekte [ortak uç noktayı etkinleştir](sql-database-managed-instance-public-endpoint-configure.md) -Bu öğretici için önerilen yaklaşımdır.
  - [Bir Azure VM'den bir Azure SQL Veritabanı Yönetilen Örneğine bağlanma](sql-database-managed-instance-configure-vm.md)
  - [Şirket Içinden Azure SQL veritabanı yönetilen örneği ile noktadan siteye bağlantı yapılandırın](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Azure Blob depolama ve [paylaşılan erişim imzası (SAS) anahtarı](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)kullanarak bir SQL Server veritabanını yedekleme ve geri yükleme hakkında daha fazla bilgi için bkz. [SQL Server yedekleme URL 'si](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-the-database-from-a-backup-file"></a>Veritabanını bir yedekleme dosyasından geri yükleme

SSMS 'de Wide World Importers veritabanını yönetilen örneğinize geri yüklemek için aşağıdaki adımları izleyin. Veritabanı yedekleme dosyası önceden yapılandırılmış bir Azure Blob depolama hesabında depolanır.

1. SMSS 'yi açın ve yönetilen örneğinize bağlanın.
2. Sol taraftaki menüden, yönetilen örneğe sağ tıklayıp yeni **sorgu** ' yı seçerek yeni bir sorgu penceresi açın.
3. Yönetilen örnekte [bir kimlik bilgisi oluşturmak](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) için önceden yapılandırılmış bir depolama HESABı ve SAS anahtarı kullanan aşağıdaki SQL betiğini çalıştırın.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![kimlik bilgisi oluşturma](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Kimlik bilgilerinizi denetlemek için, bir yedekleme dosyası listesi almak üzere bir [kapsayıcı](https://azure.microsoft.com/services/container-instances/) URL 'si kullanan aşağıdaki betiği çalıştırın.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![dosya listesi](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Wide World Importers veritabanını geri yüklemek için aşağıdaki betiği çalıştırın.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![geri yükleme](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Geri yüklemenin durumunu izlemek için aşağıdaki betiği çalıştırın.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Geri yükleme tamamlandığında, veritabanını Nesne Gezgini görüntüleyin. Veritabanı geri yüklemenin, [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) View kullanarak tamamlandığını doğrulayabilirsiniz.

> [!NOTE]
> Veritabanı geri yükleme işlemi zaman uyumsuzdur ve yeniden alınamıyor. Bağlantı kesildiyse veya bir zaman aşımı süresi dolarsa SQL Server Management Studio bir hata alabilirsiniz. Azure SQL veritabanı, arka planda veritabanını geri yüklemeye çalışmaya devam eder ve [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ve [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) görünümlerini kullanarak geri yüklemenin ilerlemesini izleyebilirsiniz.
> Geri yükleme işleminin bazı aşamalarında, sistem görünümlerinde gerçek veritabanı adı yerine benzersiz tanımlayıcı görürsünüz. [Burada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement)`RESTORE` ifade davranışı farklılıkları hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

- Bir URL 'de yedekleme sorunlarını gidermek için, bkz. [SQL Server yedekleme URL 'Si En Iyi uygulamalar ve sorun giderme](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Uygulama bağlantısı seçeneklerine genel bakış için bkz. [uygulamalarınızı yönetilen örneğe bağlama](sql-database-managed-instance-connect-app.md).
- En sevdiğiniz araçları veya dilleri kullanarak sorgulamak için bkz. [hızlı başlangıç: Azure SQL veritabanı Connect ve Query](sql-database-connect-query.md).

---
title: Sorgulamak için PHP kullanın
description: PHP kullanarak Azure SQL veritabanına bağlanan ve T-SQL deyimlerini kullanarak sorgulayan bir program oluşturma.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: ae119dd23da670f16c0239b14119519c431e6326
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827009"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Hızlı Başlangıç: PHP kullanarak Azure SQL veritabanı sorgulama

Bu makalede, Azure SQL veritabanına bağlanmak için [php](https://php.net/manual/en/intro-whatis.php) 'nin nasıl kullanılacağı gösterilmektedir. Daha sonra verileri sorgulamak için T-SQL deyimlerini kullanabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu örneği gerçekleştirmek için aşağıdaki önkoşullara sahip olduğunuzdan emin olun:

- Bir Azure SQL veritabanı. Azure SQL veritabanı 'nda bir veritabanı oluşturmak ve yapılandırmak için bu hızlı başlangıçlardan birini kullanabilirsiniz:

  || Tek veritabanı | Yönetilen örnek |
  |:--- |:--- |:---|
  | Oluşturma| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Yapılandırma | [Sunucu düzeyi IP güvenlik duvarı kuralı](sql-database-server-level-firewall-rule.md)| [Bir VM 'den bağlantı](sql-database-managed-instance-configure-vm.md)|
  |||[Siteden bağlantı](sql-database-managed-instance-configure-p2s.md)
  |Veri yükleme|Hızlı başlangıç başına yüklenen Adventure Works|[Geniş dünyada içeri aktarıcılar geri yükleme](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 'Dan [bacpac](sql-database-import.md) dosyasından Adventure Works 'ü geri yükleme veya içe aktarma|
  |||

  > [!IMPORTANT]
  > Bu makaledeki betikler, Adventure Works veritabanını kullanmak için yazılmıştır. Yönetilen bir örnek ile, Adventure Works veritabanını bir örnek veritabanına aktarmanız veya bu makaledeki betikleri Wide World Importers veritabanını kullanacak şekilde değiştirmeniz gerekir.

- İşletim sisteminiz için yüklenmiş PHP ile ilgili yazılımlar:

  - **MacOS**, php 'YI, ODBC sürücüsünü yükleyip SQL Server Için php sürücüsünü yükler. Bkz. [Adım 1, 2 ve 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, php 'YI, ODBC sürücüsünü yükleyip SQL Server Için php sürücüsünü yükler. Bkz. [Adım 1, 2 ve 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, IIS Express ve CHOCOLATEY için php 'yi yükleyip, ardından ODBC sürücüsünü ve sqlcmd 'yi yükler. Bkz: [1.2 ve 1.3 adımları](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>SQL Server bağlantı bilgilerini al

Azure SQL veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, tek bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya yönetilen örnek Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

## <a name="add-code-to-query-database"></a>Sorgu veritabanına kod ekleme

1. Sık kullandığınız metin düzenleyicisinde *sqltest.php* adında yeni bir dosya oluşturun.  

1. İçeriğini aşağıdaki kodla değiştirin. Daha sonra sunucunuz, veritabanınız, Kullanıcı ve parolanız için uygun değerleri ekleyin.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Kodu çalıştırma

1. Komut isteminde uygulamayı çalıştırın.

   ```bash
   php sqltest.php
   ```

1. İlk 20 satırın döndürüldüğünden emin olun ve uygulama penceresini kapatın.

## <a name="next-steps"></a>Sonraki adımlar

- [İlk Azure SQL veritabanınızı tasarlama](sql-database-design-first-database.md)

- [SQL Server için Microsoft PHP Sürücüleri](https://github.com/Microsoft/msphpsql/)

- [Sorun bildirin veya soru sorun](https://github.com/Microsoft/msphpsql/issues)

- [Yeniden deneme mantığı örneği: PHP ile dayanıklı bağlantısı 'i SQL 'e bağlama](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

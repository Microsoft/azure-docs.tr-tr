---
title: Sorgulamak için PHP kullanın
description: PHP kullanarak Azure SQL veritabanı veya Azure SQL yönetilen örneği 'nde bir veritabanına bağlanan ve T-SQL deyimlerini kullanarak sorgulayan bir program oluşturma.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576659"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>Hızlı başlangıç: Azure SQL veritabanı 'nda bir veritabanını sorgulamak için PHP kullanma
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu makalede, [php](https://php.net/manual/en/intro-whatis.php) kullanarak Azure SQL veritabanı veya Azure SQL yönetilen örneği ' nde bir veritabanına bağlanma gösterilmektedir. Daha sonra verileri sorgulamak için T-SQL deyimlerini kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakilere ihtiyacınız vardır:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure SQL veritabanı veya Azure SQL yönetilen örneği içindeki bir veritabanı. Bir veritabanı oluşturup yapılandırmak için bu hızlı başlangıçlardan birini kullanabilirsiniz:

  | Eylem | SQL Veritabanı | SQL Yönetilen Örnek | Azure VM’lerde SQL Server |
  |:--- |:--- |:---|:---|
  | Oluştur| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Yapılandırma | [Sunucu düzeyi IP güvenlik duvarı kuralı](firewall-create-server-level-portal-quickstart.md)| [Bir VM 'den bağlantı](../managed-instance/connect-vm-instance-configure.md)|
  |||[Şirket içinden bağlantı](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server örneğine bağlanma](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Veri yükleme|Hızlı başlangıç başına yüklenen Adventure Works|[Geniş dünyada içeri aktarıcılar geri yükleme](../managed-instance/restore-sample-database-quickstart.md) | [Geniş dünyada içeri aktarıcılar geri yükleme](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 'Dan bir [bacpac](database-import.md) dosyasından Adventure Works 'ü geri yükleme veya içeri aktarma| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 'Dan bir [bacpac](database-import.md) dosyasından Adventure Works 'ü geri yükleme veya içeri aktarma|
  |||




  > [!IMPORTANT]
  > Bu makaledeki betikler, Adventure Works veritabanını kullanmak için yazılmıştır. SQL yönetilen örneği ile, Adventure Works veritabanını bir örnek veritabanına aktarmanız veya bu makaledeki betikleri Wide World Importers veritabanını kullanacak şekilde değiştirmeniz gerekir.

- İşletim sisteminiz için yüklenmiş PHP ile ilgili yazılımlar:

  - **MacOS**, php 'YI, ODBC sürücüsünü yükleyip SQL Server Için php sürücüsünü yükler. Bkz. [Adım 1, 2 ve 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, php 'YI, ODBC sürücüsünü yükleyip SQL Server Için php sürücüsünü yükler. Bkz. [Adım 1, 2 ve 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, PHP ve php sürücülerini YÜKLEDIKTEN sonra ODBC sürücüsünü ve sqlcmd 'yi yükler. Bkz: [1.2 ve 1.3 adımları](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-server-connection-information"></a>Sunucu bağlantı bilgilerini al

Azure SQL veritabanı 'nda veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, Azure SQL veritabanı 'ndaki bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya Azure SQL yönetilen örneği için **konak** ' ın YANıNDAKI tam sunucu adını (veya IP adresini) veya bir Azure VM 'de SQL Server ' yi gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

> [!NOTE]
> Azure VM 'de SQL Server yönelik bağlantı bilgileri için bkz. [SQL Server örneğine bağlanma](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="add-code-to-query-the-database"></a>Veritabanını sorgulamak için kod ekleme

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

- [Azure SQL veritabanı 'nda ilk veritabanınızı tasarlama](design-first-database-tutorial.md)
- [SQL Server için Microsoft PHP Sürücüleri](https://github.com/Microsoft/msphpsql/)
- [Sorun bildirin veya soru sorun](https://github.com/Microsoft/msphpsql/issues)
- [Yeniden deneme mantığı örneği: PHP ile dayanıklı bağlantısı 'i Azure SQL 'e bağlama](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

---
title: 'Hızlı başlangıç: sorgulamak için Python kullanma'
description: Bu konu başlığı altında, Python kullanarak Azure SQL veritabanına bağlanan ve Transact-SQL deyimlerini kullanarak sorgulayan bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 42d5b500a48e427aad2372710597e0266b2e80aa
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826989"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Hızlı Başlangıç: Python kullanarak Azure SQL veritabanı sorgulama

 Bu makalede, [Python](https://python.org) kullanarak Azure SQL veritabanına bağlanma ve Transact-SQL deyimleriyle veri sorgulama işlemlerinin nasıl yapılacağı gösterilmektedir. Daha fazla SDK ayrıntıları için [başvuru](https://docs.microsoft.com/python/api/overview/azure/sql) belgelerimize, [Pyodbc GitHub deposuna](https://github.com/mkleehammer/pyodbc/wiki/)ve bir [pyodbc örneğine](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)göz atın.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakilere sahip olduğunuzdan emin olun:

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
  
- İşletim sisteminiz için Python ve ilgili yazılımlar:
  
  - **MacOS**: homebrew ve Python 'u yükler, ODBC sürücüsünü ve sqlcmd 'yi yükleyip SQL Server için Python sürücüsünü yükler. [MacOS 'ta SQL Server kullanarak Python uygulamaları oluşturma konusunda](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)1,2, 1,3 ve 2,1 adımlarına bakın. Daha fazla bilgi için bkz. [Linux ve macOS 'Ta MICROSOFT ODBC sürücüsünü yüklemeyi](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Python ve diğer gerekli paketleri `sudo apt-get install python python-pip gcc g++ build-essential`ile yükler. SQL Server için ODBC sürücüsünü, SQLCMD ve Python sürücüsünü indirip yükleyin. Yönergeler için bkz. [pyodbc Python geliştirmesi için bir geliştirme ortamı yapılandırma](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Python, ODBC sürücüsünü ve sqlcmd 'yi ve SQL Server için Python sürücüsünü yükler. Yönergeler için bkz. [pyodbc Python geliştirmesi için bir geliştirme ortamı yapılandırma](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>SQL Server bağlantı bilgilerini al

Azure SQL veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, tek bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya yönetilen örnek Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

## <a name="create-code-to-query-your-sql-database"></a>SQL veritabanınızı sorgulamak için kod oluşturma 

1. Bir metin düzenleyicisinde, *SQLtest.py*adlı yeni bir dosya oluşturun.  
   
1. Aşağıdaki kodu ekleyin. \<Server >, \<veritabanı >, \<Kullanıcı adı > ve \<Password > için kendi değerlerinizi değiştirin.
   
   >[!IMPORTANT]
   >Bu örnekteki kod, veritabanınızı oluştururken kaynak olarak seçebileceğiniz örnek AdventureWorksLT verilerini kullanır. Veritabanınızda farklı veriler varsa, SELECT sorgusunda kendi veritabanınızdaki tabloları kullanın. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Kodu çalıştırma

1. Komut isteminde aşağıdaki komutu çalıştırın:

   ```cmd
   python sqltest.py
   ```

1. İlk 20 kategori/ürün satırlarının döndürüldüğünden emin olun ve ardından komut penceresini kapatın.

## <a name="next-steps"></a>Sonraki adımlar

- [İlk Azure SQL veritabanınızı tasarlama](sql-database-design-first-database.md)
- [SQL Server için Microsoft Python sürücüleri](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/?v=17.23h)


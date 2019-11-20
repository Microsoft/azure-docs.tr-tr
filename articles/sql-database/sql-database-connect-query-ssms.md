---
title: 'SSMS: verileri bağlama ve sorgulama'
description: SQL Server Management Studio (SSMS) kullanarak Azure'da SQL Database'e nasıl bağlanılacağını öğrenin. Ardından, verileri sorgulamak ve düzenlemek için Transact-SQL (T-SQL) deyimleri çalıştırın.
keywords: sql veritabanına bağlanma,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 69a6939cdbcc9b2c4e496c8d47aa20c9f6e85d57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826946"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Hızlı başlangıç: Azure SQL veritabanına bağlanmak ve veritabanını sorgulamak için SQL Server Management Studio kullanma

Bu hızlı başlangıçta, Azure SQL veritabanına bağlanmak için [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) kullanacaksınız. Ardından, verileri sorgulamak, eklemek, güncelleştirmek ve silmek için Transact-SQL deyimlerini çalıştıracaksınız. SSMS 'yi, Microsoft Windows için SQL Server 'den SQL veritabanı 'na herhangi bir SQL altyapısını yönetmek üzere kullanabilirsiniz.  

## <a name="prerequisites"></a>Ön koşullar

Bir Azure SQL veritabanı. Azure SQL veritabanı 'nda bir veritabanı oluşturmak ve yapılandırmak için bu hızlı başlangıçlardan birini kullanabilirsiniz:

  || Tek veritabanı | Yönetilen örnek |
  |:--- |:--- |:---|
  | Oluşturma| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Yapılandırma | [Sunucu düzeyi IP güvenlik duvarı kuralı](sql-database-server-level-firewall-rule.md)| [Bir VM 'den bağlantı](sql-database-managed-instance-configure-vm.md)|
  |||[Siteden bağlantı](sql-database-managed-instance-configure-p2s.md)
  |Veri yükleme|Hızlı başlangıç başına yüklenen Adventure Works|[Geniş dünyada içeri aktarıcılar geri yükleme](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 'Dan [bacpac](sql-database-import.md) dosyasından Adventure Works 'ü geri yükleme veya içe aktarma|

  > [!IMPORTANT]
  > Bu makaledeki betikler, Adventure Works veritabanını kullanmak için yazılmıştır. Yönetilen bir örnek ile, Adventure Works veritabanını bir örnek veritabanına aktarmanız veya bu makaledeki betikleri Wide World Importers veritabanını kullanacak şekilde değiştirmeniz gerekir.

## <a name="install-the-latest-ssms"></a>En son SSMS’yi yükleyin

Başlamadan önce, en son [SSMS][ssms-install-latest-84g]'yi yüklediğinizden emin olun.

## <a name="get-sql-server-connection-information"></a>SQL Server bağlantı bilgilerini al

Azure SQL veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, tek bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya yönetilen örnek Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

## <a name="connect-to-your-database"></a>Veritabanınıza bağlanın

SMSS 'de Azure SQL veritabanı sunucunuza bağlanın.

> [!IMPORTANT]
> Azure SQL veritabanı sunucusu 1433 numaralı bağlantı noktasını dinler. Şirket güvenlik duvarının arkasındaki bir SQL veritabanı sunucusuna bağlanmak için, güvenlik duvarında bu bağlantı noktasının açık olması gerekir.
>

1. SSMS’i açın. **Sunucuya Bağlan** iletişim kutusu görüntülenir.

2. Aşağıdaki bilgileri girin:

   | Ayar      | Önerilen değer    | Açıklama |
   | ------------ | ------------------ | ----------- |
   | **Sunucu türü** | Veritabanı altyapısı | Gerekli değer. |
   | **Sunucu adı** | Tam sunucu adı | Şöyle bir şey: **mynewserver20170313.Database.Windows.net**. |
   | **Kimlik doğrulaması** | SQL Server Kimlik Doğrulaması | Bu öğretici, SQL kimlik doğrulamasını kullanır. |
   | **Oturum açma** | Sunucu Yöneticisi hesabı kullanıcı KIMLIĞI | Sunucu oluşturmak için kullanılan sunucu yönetici hesabındaki Kullanıcı KIMLIĞI. |
   | **Parola** | Sunucu Yöneticisi hesap parolası | Sunucu oluşturmak için kullanılan sunucu yönetici hesabındaki parola. |
   ||||

   ![sunucuya bağlan](./media/sql-database-connect-query-ssms/connect.png)  

3. **Sunucuya Bağlan** Iletişim kutusunda **seçenekleri** belirleyin. **Veritabanına Bağlan** açılan menüsünde **mysampledatabase**' i seçin.

   ![sunucuda veritabanına bağlanma](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **Bağlan**’ı seçin. Nesne Gezgini penceresi açılır.

5. Veritabanının nesnelerini görüntülemek için **veritabanları** ' nı genişletin ve ardından **mysampledatabase**' i genişletin.

   ![mySampleDatabase nesneleri](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Verileri sorgulama

Kategoriye göre ilk 20 ürünü sorgulamak için bu [Select](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL kodunu çalıştırın.

1. Nesne Gezgini, **Mysampledatabase** ' e sağ tıklayın ve **Yeni sorgu**' yu seçin. Veritabanınıza bağlı yeni bir sorgu penceresi açılır.

2. Sorgu penceresinde bu SQL sorgusunu yapıştırın.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Araç çubuğunda, `Product` ve `ProductCategory` tablolarından veri almak için **Yürüt** ' ü seçin.

    ![Tablo ürününden ve ProductCategory 'tan veri alma sorgusu](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Veri ekleme

`SalesLT.Product` tablosunda yeni bir ürün oluşturmak için bu [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL kodunu çalıştırın.

1. Önceki sorguyu bununla değiştirin.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. `Product` tablosuna yeni bir satır eklemek için **Yürüt** ' ü seçin. **İletiler** bölmesi **(1 satır etkilendi)** görüntülenir.

## <a name="view-the-result"></a>Sonucu görüntüleme

1. Önceki sorguyu bununla değiştirin.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. **Yürüt**’ü seçin. Aşağıdaki sonuç görüntülenir.

   ![Ürün tablosu sorgusunun sonucu](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>Verileri güncelleştirme

Yeni ürününüzü değiştirmek için bu [güncelleştirme](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL kodunu çalıştırın.

1. Önceki sorguyu bununla değiştirin.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. `Product` tablosundaki belirtilen satırı güncelleştirmek için **Yürüt** ' ü seçin. **İletiler** bölmesi **(1 satır etkilendi)** görüntülenir.

## <a name="delete-data"></a>Verileri silme

Yeni ürününüzü kaldırmak için bu [Delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL kodunu çalıştırın.

1. Önceki sorguyu bununla değiştirin.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. `Product` tablosundaki belirtilen satırı silmek için **Yürüt** ' ü seçin. **İletiler** bölmesi **(1 satır etkilendi)** görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

- SSMS hakkında daha fazla bilgi için bkz. [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Azure portalını kullanarak bağlanmak ve sorgulamak için bkz. [Azure Portal SQL Sorgusu düzenleyicisiyle bağlanma ve sorgulama](sql-database-connect-query-portal.md).
- Visual Studio Code’u kullanarak bağlanmak ve sorgulamak için bkz. [Visual Studio Code ile bağlanma ve sorgulama](sql-database-connect-query-vscode.md).
- .NET kullanarak bağlanıp sorgulamak için bkz. [.NET ile bağlanma ve sorgulama](sql-database-connect-query-dotnet.md).
- PHP kullanarak bağlanıp sorgulamak için bkz. [PHP ile bağlanma ve sorgulama](sql-database-connect-query-php.md).
- Node.js kullanarak bağlanıp sorgulamak için bkz. [Node.js ile bağlanma ve sorgulama](sql-database-connect-query-nodejs.md).
- Java kullanarak bağlanıp sorgulamak için bkz. [Java ile bağlanma ve sorgulama](sql-database-connect-query-java.md).
- Python kullanarak bağlanıp sorgulamak için bkz. [Python ile bağlanma ve sorgulama](sql-database-connect-query-python.md).
- Ruby kullanarak bağlanıp sorgulamak için bkz. [Ruby ile bağlanma ve sorgulama](sql-database-connect-query-ruby.md).

<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms

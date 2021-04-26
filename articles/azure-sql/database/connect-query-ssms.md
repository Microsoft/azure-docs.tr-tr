---
title: 'SSMS: verileri bağlama ve sorgulama'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: SQL Server Management Studio (SSMS) kullanarak Azure SQL veritabanı veya SQL yönetilen örneği 'ne bağlanmayı öğrenin. Ardından, verileri sorgulamak ve düzenlemek için Transact-SQL (T-SQL) deyimleri çalıştırın.
keywords: sql veritabanına bağlanma,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/28/2020
ms.openlocfilehash: 60977b9388af3a93d0ebbbc6aad50628b79e0e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91598258"
---
# <a name="quickstart-use-ssms-to-connect-to-and-query-azure-sql-database-or-azure-sql-managed-instance"></a>Hızlı başlangıç: Azure SQL veritabanı veya Azure SQL yönetilen örneği 'ne bağlanmak ve veritabanını sorgulamak için SSMS kullanma
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu hızlı başlangıçta, SQL Server Management Studio (SSMS) kullanarak Azure SQL veritabanı veya Azure SQL yönetilen örneği 'ne bağlanıp bazı sorguları nasıl çalıştıracağınızı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdaki öğeler gereklidir:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).

- Azure SQL veritabanı 'nda bir veritabanı. Azure SQL veritabanı 'nda bir veritabanı oluşturmak ve yapılandırmak için bu hızlı başlangıçlardan birini kullanabilirsiniz:

  | Eylem | SQL Veritabanı | SQL Yönetilen Örnek | Azure VM’lerde SQL Server |
  |:--- |:--- |:---|:---|
  | Oluştur| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Yapılandırma | [Sunucu düzeyi IP güvenlik duvarı kuralı](firewall-create-server-level-portal-quickstart.md)| [Bir VM 'den bağlantı](../managed-instance/connect-vm-instance-configure.md)|
  |||[Siteden bağlantı](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server’a bağlanma](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Veri yükleme|Hızlı başlangıç başına yüklenen Adventure Works|[Geniş dünyada içeri aktarıcılar geri yükleme](../managed-instance/restore-sample-database-quickstart.md) | [Geniş dünyada içeri aktarıcılar geri yükleme](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 'Dan [bacpac](database-import.md) dosyasından Adventure Works 'ü geri yükleme veya içe aktarma| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 'Dan [bacpac](database-import.md) dosyasından Adventure Works 'ü geri yükleme veya içe aktarma|
  |||

  > [!IMPORTANT]
  > Bu makaledeki betikler, Adventure Works veritabanını kullanmak için yazılmıştır. Yönetilen bir örnek ile, Adventure Works veritabanını bir örnek veritabanına aktarmanız veya bu makaledeki betikleri Wide World Importers veritabanını kullanacak şekilde değiştirmeniz gerekir.

Yalnızca bazı geçici sorguları SSMS yüklemeden çalıştırmak istiyorsanız, bkz. [hızlı başlangıç: Azure SQL veritabanında bir veritabanını sorgulamak için Azure Portal sorgu düzenleyicisini kullanın](connect-query-portal.md).

## <a name="get-server-connection-information"></a>Sunucu bağlantı bilgilerini al

Veritabanınıza bağlanmak için gereken bağlantı bilgilerini alın. Bu hızlı başlangıcı tamamlayabilmeniz için tam [sunucu](logical-servers.md) adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. Sorgulamak istediğiniz **veritabanına** veya **yönetilen örneğe** gidin.

3. **Genel bakış** sayfasında, SQL veritabanı 'nda veritabanınız için **sunucu adı** ' nın yanında tam sunucu adını veya SQL yönetilen örneği veya sanal makinenizde SQL Server Örneğiniz için **konak** ' ın YANıNDAKI tam sunucu adını (veya IP adresini) gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

> [!NOTE]
> Azure VM 'de SQL Server bağlantı bilgileri için bkz. [Connect SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="connect-to-your-database"></a>Veritabanınıza bağlanma

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

SSMS 'de sunucunuza bağlanın.

> [!IMPORTANT]
> Bir sunucu 1433 numaralı bağlantı noktasını dinler. Şirket güvenlik duvarının arkasındaki bir sunucuya bağlanmak için, güvenlik duvarında bu bağlantı noktasının açık olması gerekir.

1. SSMS’i açın.

2. **Sunucuya Bağlan** iletişim kutusu görüntülenir. Aşağıdaki bilgileri girin:

   | Ayar      | Önerilen değer    | Açıklama |
   | ------------ | ------------------ | ----------- |
   | **Sunucu türü** | Veritabanı altyapısı | Gerekli değer. |
   | **Sunucu adı** | Tam sunucu adı | Şöyle bir şey: **ServerName.Database.Windows.net**. |
   | **Kimlik Doğrulaması** | SQL Server Kimlik Doğrulaması | Bu öğretici, SQL kimlik doğrulamasını kullanır. |
   | **Oturum aç** | Sunucu Yöneticisi hesabı kullanıcı KIMLIĞI | Sunucu oluşturmak için kullanılan sunucu yönetici hesabındaki Kullanıcı KIMLIĞI. |
   | **Parola** | Sunucu yönetici hesabının parolası | Sunucu oluşturmak için kullanılan sunucu yönetici hesabındaki parola. |
   ||||

   ![sunucuya bağlan](./media/connect-query-ssms/connect.png)  

> [!NOTE]
> Bu öğretici SQL Server kimlik doğrulamasını kullanır.

3. **Sunucuya Bağlan** Iletişim kutusunda **seçenekleri** belirleyin. **Veritabanına Bağlan** açılan menüsünde **mysampledatabase**' i seçin. [Önkoşullar bölümünde](#prerequisites) hızlı başlangıcı tamamlamak, mysampledatabase adlı bir AdventureWorksLT veritabanı oluşturur. AdventureWorks veritabanının çalışma kopyanız mySampleDatabase öğesinden farklı bir ada sahipse, bunun yerine bunu seçin.

   ![sunucuda veritabanına bağlanma](./media/connect-query-ssms/options-connect-to-db.png)  

4. **Bağlan**’ı seçin. Nesne Gezgini penceresi açılır.

5. Veritabanının nesnelerini görüntülemek için **Veritabanları**'nı genişletin ve sonra da veritabanı düğümünüzü genişletin.

   ![mySampleDatabase nesneleri](./media/connect-query-ssms/connected.png)  

## <a name="query-data"></a>Verileri sorgulama

Kategoriye göre ilk 20 ürünü sorgulamak için bu [Select](/sql/t-sql/queries/select-transact-sql/) Transact-SQL kodunu çalıştırın.

1. Nesne Gezgini, **Mysampledatabase** ' e sağ tıklayın ve **Yeni sorgu**' yu seçin. Veritabanınıza bağlı yeni bir sorgu penceresi açılır.

2. Sorgu penceresinde, aşağıdaki SQL sorgusunu yapıştırın:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Araç çubuğunda **Yürüt** ' ü seçerek sorguyu çalıştırın ve `Product` ve tablolarından verileri alın `ProductCategory` .

    ![Tablo ürününden ve ProductCategory 'tan veri alma sorgusu](./media/connect-query-ssms/query2.png)

### <a name="insert-data"></a>Veri ekleme

Tabloda yeni bir ürün oluşturmak için bu [Insert](/sql/t-sql/statements/insert-transact-sql/) Transact-SQL kodunu çalıştırın `SalesLT.Product` .

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

2. Tabloya yeni bir satır eklemek için **Yürüt**  ' ü seçin `Product` . **İletiler** bölmesi **(1 satır etkilendi)** görüntülenir.

#### <a name="view-the-result"></a>Sonucu görüntüleme

1. Önceki sorguyu bununla değiştirin.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. **Yürüt**’ü seçin. Aşağıdaki sonuç görüntülenir.

   ![Ürün tablosu sorgusunun sonucu](./media/connect-query-ssms/result.png)

### <a name="update-data"></a>Verileri güncelleştirme

Yeni ürününüzü değiştirmek için bu [güncelleştirme](/sql/t-sql/queries/update-transact-sql) Transact-SQL kodunu çalıştırın.

1. Önceki sorguyu, daha önce oluşturulan yeni kaydı döndüren bir ile değiştirin:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Tablodaki belirtilen satırı güncelleştirmek için **Yürüt** ' ü seçin `Product` . **İletiler** bölmesi **(1 satır etkilendi)** görüntülenir.

### <a name="delete-data"></a>Verileri silme

Yeni ürününüzü kaldırmak için bu [Delete](/sql/t-sql/statements/delete-transact-sql/) Transact-SQL kodunu çalıştırın.

1. Önceki sorguyu bununla değiştirin.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Tablodaki belirtilen satırı silmek için **Yürüt** ' ü seçin `Product` . **İletiler** bölmesi **(1 satır etkilendi)** görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

- SSMS hakkında daha fazla bilgi için bkz. [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms/).
- Azure portal kullanarak bağlanmak ve sorgulamak için bkz. [Azure Portal SQL sorgu Düzenleyicisi Ile bağlanma ve sorgulama](connect-query-portal.md).
- Visual Studio Code’u kullanarak bağlanmak ve sorgulamak için bkz. [Visual Studio Code ile bağlanma ve sorgulama](connect-query-vscode.md).
- .NET kullanarak bağlanıp sorgulamak için bkz. [.NET ile bağlanma ve sorgulama](connect-query-dotnet-visual-studio.md).
- PHP kullanarak bağlanıp sorgulamak için bkz. [PHP ile bağlanma ve sorgulama](connect-query-php.md).
- Node.js kullanarak bağlanıp sorgulamak için bkz. [Node.js ile bağlanma ve sorgulama](connect-query-nodejs.md).
- Java kullanarak bağlanıp sorgulamak için bkz. [Java ile bağlanma ve sorgulama](connect-query-java.md).
- Python kullanarak bağlanıp sorgulamak için bkz. [Python ile bağlanma ve sorgulama](connect-query-python.md).
- Ruby kullanarak bağlanıp sorgulamak için bkz. [Ruby ile bağlanma ve sorgulama](connect-query-ruby.md).

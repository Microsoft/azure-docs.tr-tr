---
title: Bağlanmak ve sorgulamak için VS Code kullanma
description: Visual Studio Code kullanarak SQL Veritabanına bağlanma hakkında bilgi edinin. Ardından, verileri sorgulamak ve düzenlemek için Transact-SQL (T-SQL) deyimleri çalıştırın.
keywords: sql veritabanına bağlanma
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
ms.openlocfilehash: 28b35f273783b2e4d0b8f59c5bc5be384b933ba2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826886"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Hızlı başlangıç: Azure SQL veritabanına bağlanmak ve veritabanını sorgulamak için Visual Studio Code kullanma

[Visual Studio Code](https://code.visualstudio.com/docs) , Linux, MacOS ve Windows için bir grafik kod düzenleyicisidir. Microsoft SQL Server, Azure SQL veritabanı ve SQL veri ambarı 'nı sorgulamak için [MSSQL uzantısı](https://aka.ms/mssql-marketplace) dahil olmak üzere uzantıları destekler. Bu hızlı başlangıçta, Azure SQL veritabanına bağlanmak ve ardından Transact-SQL deyimlerini kullanarak verileri sorgulamak, eklemek, güncelleştirmek ve silmek için Visual Studio Code kullanacaksınız.

## <a name="prerequisites"></a>Ön koşullar

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

## <a name="install-visual-studio-code"></a>Visual Studio Code yüklensin

En son [Visual Studio Code](https://code.visualstudio.com/Download) yüklediğinizden ve [MSSQL uzantısını](https://aka.ms/mssql-marketplace)yüklediğinizden emin olun. MSSQL uzantısını yükleme hakkında yönergeler için, bkz. [yükleme vs Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) ve [MSSQL Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Visual Studio Code Yapılandır

### <a name="mac-os"></a>**Mac OS**

MacOS için, MSSQL uzantısının kullandığı .NET Core için bir önkoşul olan OpenSSL 'yi yüklemeniz gerekir. **brew** ve **OpenSSL**’yi yüklemek için terminalinizi açın aşağıdaki komutları girin.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Hiçbir özel yapılandırma gerekmez.

### <a name="windows"></a>**Windows**

Hiçbir özel yapılandırma gerekmez.

## <a name="get-sql-server-connection-information"></a>SQL Server bağlantı bilgilerini al

Azure SQL veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, tek bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya yönetilen örnek Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

## <a name="set-language-mode-to-sql"></a>Dili modunu SQL’e ayarlama

Visual Studio Code, MSSQL komutlarını ve T-SQL IntelliSense 'i etkinleştirmek için dil modunu **SQL** olarak ayarlayın.

1. Yeni bir Visual Studio Code penceresi açın.

2. **Ctrl**+**N**tuşuna basın. Yeni bir düz metin dosyası açılır.

3. Durum çubuğunun sağ alt köşesindeki **düz metin** ' i seçin.

4. Açılan **dil modunu seç** açılan menüsünde **SQL**' i seçin.

## <a name="connect-to-your-database"></a>Veritabanınıza bağlanın

Visual Studio Code’u kullanarak Azure SQL Veritabanı sunucunuzla bağlantı kurun.

> [!IMPORTANT]
> Devam etmeden önce, sunucunuzun ve oturum açma bilgilerinin hazırlanmasına sahip olduğunuzdan emin olun. Bağlantı profili bilgilerini girmeye başladıktan sonra, Visual Studio Code odağınızı değiştirirseniz, profil oluşturma işlemini yeniden başlatmanız gerekir.

1. Visual Studio Code ' de, **CTRL + SHIFT + P** (veya **F1**) tuşlarına basarak komut paletini açın.

2. **MS SQL: Connect** ' i seçin ve **ENTER**' u seçin.

3. **Bağlantı profili oluştur**' u seçin.

4. Yeni profilin bağlantı özelliklerini belirtmek için istemleri izleyin. Her bir değeri belirttikten sonra, devam etmek için **ENTER** ' ı seçin.

   | Özellik       | Önerilen değer | Açıklama |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Sunucu adı** | Tam sunucu adı | Şöyle bir şey: **mynewserver20170313.Database.Windows.net**. |
   | **Veritabanı adı** | mySampleDatabase | Bağlanılacak veritabanı. |
   | **Kimlik doğrulaması** | SQL Oturum Açma| Bu öğretici, SQL kimlik doğrulamasını kullanır. |
   | **Kullanıcı adı** | Kullanıcı adı | Sunucuyu oluşturmak için kullanılan sunucu yöneticisi hesabının Kullanıcı adı. |
   | **Parola (SQL Oturum Açma)** | Parola | Sunucuyu oluşturmak için kullanılan sunucu yöneticisi hesabının parolası. |
   | **Parola kaydedilsin mi?** | Evet veya Hayır | Her seferinde parolayı girmek istemiyorsanız **Evet** ' i seçin. |
   | **Bu profil için bir ad girin** | **Mysampleprofile** gibi bir profil adı | Kaydedilen bir profil, sonraki oturumlarda bağlantınızı hızlandırır. |

   Başarılı olursa, profiliniz oluşturulup bağlandığını belirten bir bildirim görüntülenir.

## <a name="query-data"></a>Verileri sorgulama

Kategoriye göre ilk 20 ürünü sorgulamak için aşağıdaki [Select](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ifadesini çalıştırın.

1. Düzenleyici penceresinde, aşağıdaki SQL sorgusunu yapıştırın.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Sorguyu çalıştırmak ve `Product` ve `ProductCategory` tablolarından sonuçları göstermek için **Ctrl**+**SHIFT**+**E** tuşlarına basın.

    ![2 tablodan veri alma sorgusu](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Veri ekleme

`SalesLT.Product` tablosuna yeni bir ürün eklemek için aşağıdaki [Insert](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin.

   ```sql
   INSERT INTO [SalesLT].[Product]
        ( [Name]
        , [ProductNumber]
        , [Color]
        , [ProductCategoryID]
        , [StandardCost]
        , [ListPrice]
        , [SellStartDate]
        )
     VALUES
        ('myNewProduct'
        ,123456789
        ,'NewColor'
        ,1
         ,100
         ,100
         ,GETDATE() );
   ```

2. `Product` tablosuna yeni bir satır eklemek için **Ctrl**+**SHIFT**+**E** tuşlarına basın.

## <a name="update-data"></a>Verileri güncelleştirme

Eklenen ürünü güncelleştirmek için aşağıdaki [Update](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. `Product` tablodaki belirtilen satırı güncelleştirmek için **Ctrl**+**SHIFT**+**E** tuşlarına basın.

## <a name="delete-data"></a>Verileri silme

Yeni ürünü kaldırmak için aşağıdaki [Delete](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. `Product` tablodaki belirtilen satırı silmek için **Ctrl**+**SHIFT**+**E** tuşlarına basın.

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server Management Studio kullanarak bağlanmak ve sorgulamak için bkz. [hızlı başlangıç: Azure SQL veritabanına bağlanmak ve verileri sorgulamak için SQL Server Management Studio kullanma](sql-database-connect-query-ssms.md).
- Azure portal kullanarak bağlanmak ve sorgulamak için bkz. [hızlı başlangıç: veri bağlamak ve sorgulamak için Azure Portal SQL sorgu düzenleyicisini kullanın](sql-database-connect-query-portal.md).
- Visual Studio Code'u kullanmaya ilişkin MSDN dergisi makalesi için bkz. [MSSQL uzantısı blog gönderisinden yararlanarak veritabanı IDE'si oluşturma](https://msdn.microsoft.com/magazine/mt809115).

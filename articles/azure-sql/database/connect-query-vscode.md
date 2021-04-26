---
title: Bağlanmak ve sorgulamak için Visual Studio Code kullanma
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Visual Studio Code kullanarak Azure SQL veritabanı 'na veya SQL yönetilen örneği 'ne nasıl bağlanacağınızı öğrenin. Ardından, verileri sorgulamak ve düzenlemek için Transact-SQL (T-SQL) deyimleri çalıştırın.
keywords: sql veritabanına bağlanma
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: f823b6d04a217328fe2e825e64906460cd9cbae9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92672491"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query"></a>Hızlı başlangıç: bağlanmak ve sorgulamak için Visual Studio Code kullanma 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Visual Studio Code](https://code.visualstudio.com/docs) , Linux, MacOS ve Windows için bir grafik kod düzenleyicisidir. SQL Server örneği, Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics 'teki bir veritabanını sorgulamak için [MSSQL uzantısı](https://aka.ms/mssql-marketplace) dahil olmak üzere uzantıları destekler. Bu hızlı başlangıçta, Azure SQL veritabanı veya Azure SQL yönetilen örneği 'ne bağlanmak için Visual Studio Code kullanacaksınız ve sonra verileri sorgulamak, eklemek, güncelleştirmek ve silmek için Transact-SQL deyimlerini çalıştırmanız gerekir.

## <a name="prerequisites"></a>Önkoşullar

- Azure SQL veritabanı veya Azure SQL yönetilen örneği içindeki bir veritabanı. Azure SQL veritabanı 'nda bir veritabanı oluşturmak ve yapılandırmak için bu hızlı başlangıçlardan birini kullanabilirsiniz:

  | Eylem | Azure SQL Veritabanı | Azure SQL Yönetilen Örnek |
  |:--- |:--- |:---|
  | Oluştur| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) |
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) |
  | Yapılandırma | [Sunucu DÜZEYI IP güvenlik duvarı kuralı](firewall-create-server-level-portal-quickstart.md))| [Bir sanal makineden (VM) bağlantı](../managed-instance/connect-vm-instance-configure.md)|
  |||[Şirket içinden bağlantı](../managed-instance/point-to-site-p2s-configure.md)
  |Veri yükleme|Hızlı başlangıç başına yüklenen Adventure Works|[Geniş dünyada içeri aktarıcılar geri yükleme](../managed-instance/restore-sample-database-quickstart.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 'Dan bir [bacpac](database-import.md) dosyasından Adventure Works 'ü geri yükleme veya içeri aktarma|
  |||

  > [!IMPORTANT]
  > Bu makaledeki betikler, Adventure Works veritabanını kullanmak için yazılmıştır. SQL yönetilen örneği ile, Adventure Works veritabanını bir örnek veritabanına aktarmanız veya bu makaledeki betikleri Wide World Importers veritabanını kullanacak şekilde değiştirmeniz gerekir.

## <a name="install-visual-studio-code"></a>Visual Studio Code’u yükleme

En son [Visual Studio Code](https://code.visualstudio.com/Download) yüklediğinizden ve [MSSQL uzantısını](https://aka.ms/mssql-marketplace)yüklediğinizden emin olun. MSSQL uzantısını yükleme hakkında yönergeler için, bkz. [yükleme Visual Studio Code](/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) ve [MSSQL Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Visual Studio Code’u yapılandırma

### <a name="macos"></a>**macOS**

MacOS için, MSSQL uzantısının kullandığı .NET Core için bir önkoşul olan OpenSSL 'yi yüklemeniz gerekir. Terminalinizi açın ve **Brew** ve **OpenSSL**'yi yüklemek için aşağıdaki komutları girin.

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

## <a name="get-server-connection-information"></a>Sunucu bağlantı bilgilerini al

Azure SQL veritabanı 'na bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında SQL veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya SQL yönetilen örneği Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

## <a name="set-language-mode-to-sql"></a>Dili modunu SQL’e ayarlama

Visual Studio Code, MSSQL komutlarını ve T-SQL IntelliSense 'i etkinleştirmek için dil modunu **SQL**  olarak ayarlayın.

1. Yeni bir Visual Studio Code penceresi açın.

2. **CTRL** + **N** tuşuna basın. Yeni bir düz metin dosyası açılır.

3. Durum çubuğunun sağ alt köşesindeki **düz metin** ' i seçin.

4. Açılan **dil modunu seç** açılan menüsünde **SQL**' i seçin.

## <a name="connect-to-your-database"></a>Veritabanınıza bağlanma

Sunucunuza bağlantı kurmak için Visual Studio Code kullanın.

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
   | **Kimlik Doğrulaması** | SQL Oturum Açma| Bu öğretici, SQL kimlik doğrulamasını kullanır. |
   | **Kullanıcı adı** | Kullanıcı adı | Sunucuyu oluşturmak için kullanılan sunucu yöneticisi hesabının Kullanıcı adı. |
   | **Parola (SQL Oturum Açma)** | Parola | Sunucuyu oluşturmak için kullanılan sunucu yöneticisi hesabının parolası. |
   | **Parola kaydedilsin mi?** | Evet veya Hayır | Her seferinde parolayı girmek istemiyorsanız **Evet** ' i seçin. |
   | **Bu profil için bir ad girin** | **Mysampleprofile** gibi bir profil adı | Kaydedilen bir profil, sonraki oturumlarda bağlantınızı hızlandırır. |

   Başarılı olursa, profiliniz oluşturulup bağlandığını belirten bir bildirim görüntülenir.

## <a name="query-data"></a>Verileri sorgulama

Kategoriye göre ilk 20 ürünü sorgulamak için aşağıdaki [Select](/sql/t-sql/queries/select-transact-sql) Transact-SQL ifadesini çalıştırın.

1. Düzenleyici penceresinde, aşağıdaki SQL sorgusunu yapıştırın.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2.  +  + Sorguyu çalıştırmak ve ve tablolarından sonuçları göstermek için CTRL SHIFT **E** tuşlarına basın `Product` `ProductCategory` .

    ![2 tablodan veri alma sorgusu](./media/connect-query-vscode/query.png)

## <a name="insert-data"></a>Veri ekleme

Tabloya yeni bir ürün eklemek için aşağıdaki [Insert](/sql/t-sql/statements/insert-transact-sql) Transact-SQL ifadesini çalıştırın `SalesLT.Product` .

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

2.  +  + Tabloya yeni bir satır eklemek için CTRL SHIFT **E** tuşlarına basın `Product` .

## <a name="update-data"></a>Verileri güncelleştirme

Eklenen ürünü güncelleştirmek için aşağıdaki [Update](/sql/t-sql/queries/update-transact-sql) Transact-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2.  +  + Tablodaki belirtilen satırı güncelleştirmek için CTRL SHIFT **E** tuşlarına basın `Product` .

## <a name="delete-data"></a>Verileri silme

Yeni ürünü kaldırmak için aşağıdaki [Delete](/sql/t-sql/statements/delete-transact-sql) Transact-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2.  +  + Tablodaki belirtilen satırı silmek için CTRL SHIFT **E** tuşlarına basın `Product` .

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server Management Studio kullanarak bağlanmak ve sorgulamak için bkz. [hızlı başlangıç: Azure SQL veritabanı 'nda bir veritabanına bağlanmak ve verileri sorgulamak için SQL Server Management Studio kullanma](connect-query-ssms.md).
- Azure portal kullanarak bağlanmak ve sorgulamak için bkz. [hızlı başlangıç: veri bağlamak ve sorgulamak için Azure Portal SQL sorgu düzenleyicisini kullanın](connect-query-portal.md).
- Visual Studio Code'u kullanmaya ilişkin MSDN dergisi makalesi için bkz. [MSSQL uzantısı blog gönderisinden yararlanarak veritabanı IDE'si oluşturma](/archive/msdn-magazine/2017/june/data-points-visual-studio-code-create-a-database-ide-with-mssql-extension).
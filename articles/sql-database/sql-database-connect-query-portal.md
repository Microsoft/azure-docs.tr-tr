---
title: 'Azure portal: sorgu düzenleyicisini kullanarak sorgulama'
description: SQL Sorgu Düzenleyicisi'ni kullanarak Azure portalında SQL Veritabanına bağlanmayı öğrenin. Ardından, verileri sorgulamak ve düzenlemek için Transact-SQL (T-SQL) deyimleri çalıştırın.
keywords: sql veritabanına bağlanma,azure portalı, portal, sorgu düzenleyicisi
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: carlrab
ms.date: 10/24/2019
ms.openlocfilehash: 3990d7ec63c312d38168fe76269e1a920f1a6817
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827105"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>Hızlı başlangıç: Azure portal SQL sorgu düzenleyicisini kullanarak bağlanma ve veri sorgulama

SQL sorgu Düzenleyicisi, Azure SQL veritabanı veya Azure SQL veri ambarı 'nda SQL sorguları yürütmek için kolay bir yol sağlayan bir Azure portal tarayıcı aracıdır. Bu hızlı başlangıçta, sorgu düzenleyicisini kullanarak bir SQL veritabanına bağlanın ve ardından Transact-SQL deyimlerini çalıştırarak verileri sorgulama, ekleme, güncelleştirme ve silme.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

- Bir Azure SQL veritabanı. Azure SQL veritabanı 'nda bir veritabanı oluşturmak ve yapılandırmak için bu hızlı başlangıçlardan birini kullanabilirsiniz:

  || Tek veritabanı |
  |:--- |:--- |
  | Oluşturma| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Yapılandırma | [Sunucu düzeyi IP güvenlik duvarı kuralı](sql-database-server-level-firewall-rule.md)|
  |||

> [!NOTE]
> Sorgu Düzenleyicisi, iletişim kurmak için 443 ve 1443 bağlantı noktalarını kullanır.  Lütfen bu bağlantı noktalarında giden HTTPS trafiğini etkinleştirmiş olduğunuzdan emin olun. Ayrıca, veritabanlarına ve veri ambarlarınıza erişmek için giden IP adresinizi sunucunun izin verilen güvenlik duvarı kurallarına eklemeniz gerekir.

## <a name="sign-in-the-azure-portal"></a>Azure portal oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="connect-using-sql-authentication"></a>SQL kimlik doğrulaması kullanarak bağlanma

1. Bir SQL veritabanına bağlanmak için Azure portal gidin. **SQL veritabanlarını**arayın ve seçin.

    ![SQL veritabanı listesi ' ne gidin Azure portal](./media/sql-database-connect-query-portal/search-for-sql-databases.png)

2. SQL veritabanınızı seçin.

    ![Bir SQL veritabanı seçin Azure portal](./media/sql-database-connect-query-portal/select-a-sql-database.png)

3. **SQL veritabanı** menüsünde **sorgu Düzenleyicisi 'ni (Önizleme)** seçin.

    ![sorgu düzenleyicisini bulma](./media/sql-database-connect-query-portal/find-query-editor.PNG)

4. **Oturum açma** sayfasında, **SQL Server kimlik doğrulama** etiketi altında, veritabanını oluşturmak için kullanılan sunucu yöneticisi hesabının **oturum açma** kimliğini ve **parolasını** girin. Sonra **Tamam**’ı seçin.

    ![oturum aç](./media/sql-database-connect-query-portal/login-menu.png)

## <a name="connect-using-azure-active-directory"></a>Azure Active Directory kullanarak bağlanma

Azure Active Directory (Azure AD) Yöneticisi yapılandırmak, Azure portal ve SQL veritabanınızda oturum açmak için tek bir kimlik kullanmanıza olanak sağlar. SQL sunucunuz için bir Azure AD yöneticisi yapılandırmak üzere aşağıdaki adımları izleyin.

> [!NOTE]
> * E-posta hesapları (örneğin, outlook.com, gmail.com, yahoo.com, vb.) henüz Azure AD yöneticileri olarak desteklenmez. Azure AD 'de yerel olarak oluşturulmuş bir kullanıcı veya Azure AD 'de Federasyon seçeneklerinden birini seçtiğinizden emin olun.
> * Azure AD yönetici oturum açma özelliği, 2 öğeli kimlik doğrulaması etkinleştirilmiş hesaplarla çalışmaz.

1. Azure portal menüsünde veya **giriş** sayfasından **tüm kaynaklar**' ı seçin.

2. SQL Server 'nizi seçin.

3. **SQL Server** menüsünde, **Ayarlar**' ın altında, **yönetici Active Directory**' yi seçin.

4. SQL Server **Active Directory yönetici** sayfası araç çubuğundan **yönetici ayarla** ' yı seçin ve Azure AD yöneticinizle Kullanıcı veya grup ' u seçin.

    ![active directory seçme](./media/sql-database-connect-query-portal/select-active-directory.png)

5. **Yönetici Ekle** sayfasında, ara kutusuna bulunacak Kullanıcı veya grubu girin, yönetici olarak seçin ve ardından **Seç** düğmesini seçin.

6. SQL Server **Active Directory yönetici** sayfası araç çubuğuna geri dönerek **Kaydet**' i seçin.

7. **SQL Server** menüsünde **SQL veritabanları**' nı seçin ve ardından SQL veritabanınızı seçin.

8. **SQL veritabanı** menüsünde **sorgu Düzenleyicisi 'ni (Önizleme)** seçin. **Oturum açma** sayfasında, **Active Directory kimlik doğrulama** etiketi altında, bir Azure AD yöneticisiyseniz oturum açmış olduğunuzu bildiren bir ileti görüntülenir. Sonra, *Kullanıcı veya grup kimliği > düğmesini\<* **olarak devam et** ' i seçin.

## <a name="view-data"></a>Verileri görüntüleme

1. Kimlik doğrulamasından geçtikten sonra, kategoriye göre ilk 20 ürünü almak için aşağıdaki SQL 'i sorgu düzenleyicisine yapıştırın.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Araç çubuğunda **Çalıştır** ' ı seçin ve ardından **sonuçlar** bölmesinde çıktıyı gözden geçirin.

   ![sorgu düzenleyicisi sonuçları](./media/sql-database-connect-query-portal/query-editor-results.png)

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


2. `Product` tablosuna yeni bir satır eklemek için **Çalıştır** ' ı seçin. **İletiler** bölmesi **sorgu başarılı: etkilenen satırlar: 1 ' i**görüntüler.


## <a name="update-data"></a>Verileri güncelleştirme

Yeni ürününüzü değiştirmek için aşağıdaki [Update](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. `Product` tablosundaki belirtilen satırı güncelleştirmek için **Çalıştır** ' ı seçin. **İletiler** bölmesi **sorgu başarılı: etkilenen satırlar: 1 ' i**görüntüler.

## <a name="delete-data"></a>Verileri silme

Yeni ürününüzü kaldırmak için aşağıdaki [Delete](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ifadesini çalıştırın.

1. Önceki sorguyu bununla değiştirin:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. `Product` tablosundaki belirtilen satırı silmek için **Çalıştır** ' ı seçin. **İletiler** bölmesi **sorgu başarılı: etkilenen satırlar: 1 ' i**görüntüler.


## <a name="query-editor-considerations"></a>Sorgu Düzenleyicisi konuları

Sorgu Düzenleyicisi ile çalışırken bilmemiz gereken birkaç nokta vardır.

* Sorgu Düzenleyicisi, iletişim kurmak için 443 ve 1443 bağlantı noktalarını kullanır.  Lütfen bu bağlantı noktalarında giden HTTPS trafiğini etkinleştirmiş olduğunuzdan emin olun. Ayrıca, veritabanlarına ve veri ambarlarınıza erişmek için giden IP adresinizi sunucunun izin verilen güvenlik duvarı kurallarına eklemeniz gerekir.

* F5 tuşuna basıldığında sorgu Düzenleyicisi sayfası yenilenir ve üzerinde çalışılan sorgular kaybolur.

* Sorgu Düzenleyicisi `master` veritabanına bağlanmayı desteklemiyor.

* Sorgu yürütmesi için 5 dakikalık bir zaman aşımı vardır.

* Sorgu Düzenleyicisi, Coğrafya veri türleri için yalnızca silindir projeksiyonu destekler.

* Veritabanı tabloları ve görünümleri için IntelliSense desteği yoktur. Ancak, düzenleyici zaten girilmiş olan adlarla otomatik tamamlamayı destekler.


## <a name="next-steps"></a>Sonraki adımlar

Azure SQL veritabanlarında desteklenen Transact-SQL hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanına geçiş sırasında Transact-SQL farklılıklarını çözümleme](sql-database-transact-sql-information.md).

---
title: Visual Studio 'Yu .NET ile ve C# sorgulamak için kullanın
description: Azure SQL veritabanına bağlanan ve Transact C# -SQL deyimleriyle sorgulayan bir uygulama oluşturmak Için Visual Studio 'yu kullanın.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: b9bc7d077acd3e6e3716cf0a012205e6de54a4b7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827066"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Hızlı başlangıç: Azure SQL C# veritabanına bağlanmak ve veritabanını sorgulamak için .net ve Visual Studio 'da kullanın

Bu hızlı başlangıçta Transact-SQL deyimleriyle bir Azure C# SQL veritabanını sorgulamak için [.NET Framework](https://www.microsoft.com/net/) ve Code 'un Visual Studio 'da nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için şunlar gerekir:

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

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional veya Enterprise Edition.

## <a name="get-sql-server-connection-information"></a>SQL Server bağlantı bilgilerini al

Azure SQL veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, tek bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya yönetilen örnek Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin. 

## <a name="create-code-to-query-the-sql-database"></a>SQL veritabanını sorgulamak için kod oluşturma

1. Visual Studio 'da **dosya** > **Yeni** > **Proje**' yi seçin. 
   
1. **Yeni proje** iletişim kutusunda **görsel C#** ' i seçin ve **konsol uygulaması ' nı (.NET Framework)** seçin.
   
1. Proje adı için *SQLtest* girin ve ardından **Tamam**' ı seçin. Yeni proje oluşturulur. 
   
1. **NuGet paketlerini yönetmek** > **Proje** ' yi seçin. 
   
1. **NuGet Paket Yöneticisi**' nde, **Gözden** geçirme sekmesini seçin, sonra **System. Data. SqlClient**öğesini arayıp seçin.
   
1. **System. Data. SqlClient** sayfasında, **Install**' ı seçin. 
   - İstenirse, yüklemeye devam etmek için **Tamam** ' ı seçin. 
   - Bir **Lisans kabul** penceresi görüntülenirse **kabul ediyorum**' u seçin.
   
1. Yüklemesi tamamlandığında, **NuGet Paket Yöneticisi 'ni**kapatabilirsiniz. 
   
1. Kod Düzenleyicisi 'nde, **program.cs** içeriğini aşağıdaki kodla değiştirin. `<server>`, `<username>`, `<password>`ve `<database>`için değerlerinizi değiştirin.
   
   >[!IMPORTANT]
   >Bu örnekteki kod, veritabanınızı oluştururken kaynak olarak seçebileceğiniz örnek AdventureWorksLT verilerini kullanır. Veritabanınızda farklı veriler varsa, SELECT sorgusunda kendi veritabanınızdaki tabloları kullanın. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Kodu çalıştırma

1. Uygulamayı çalıştırmak için **hata ayıkla** > hata **ayıklamayı Başlat**' ı seçin ya da araç çubuğundan **Başlat** ' ı seçin veya **F5**tuşuna basın.
1. Veritabanınızdaki ilk 20 kategori/ürün satırı döndürüldüğünden emin olun ve ardından uygulama penceresini kapatın.

## <a name="next-steps"></a>Sonraki adımlar

- Windows/Linux/macOS 'ta [.NET Core kullanarak bir Azure SQL veritabanını bağlamayı ve sorgulamayı](sql-database-connect-query-dotnet-core.md) öğrenin.  
- [Komut satırını kullanarak Windows/Linus/macOS’ta .NET Core ile çalışmaya başlama](/dotnet/core/tutorials/using-with-xplat-cli) hakkında bilgi edinin.
- [SSMS kullanarak ilk Azure SQL veritabanınızı tasarlamayı](sql-database-design-first-database.md) veya [.NET kullanarak ilk Azure SQL veritabanınızı tasarlamayı](sql-database-design-first-database-csharp.md) öğrenin.
- .NET hakkında daha fazla bilgi edinmek için [.NET belgelerine](https://docs.microsoft.com/dotnet/) bakın.
- Yeniden deneme mantığı örneği: [ADO.net Ile SQL 'e dayanıklı bağlantısı bağlayın][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net


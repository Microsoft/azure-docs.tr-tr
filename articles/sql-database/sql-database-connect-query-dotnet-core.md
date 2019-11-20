---
title: Sorgulamak için .NET Core kullanın
description: Bu konu başlığı altında, .NET Core kullanarak Azure SQL veritabanına bağlanan ve Transact-SQL deyimlerini kullanarak sorgulayan bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 369c708fd3181076c6deb9d7ac9134c57a18f819
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827101"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Hızlı Başlangıç: .NET Core (C#) kullanarak Azure SQL veritabanı sorgulama

Bu hızlı başlangıçta, [.NET Core](https://www.microsoft.com/net/) ve C# Code 'U kullanarak bir Azure SQL veritabanına bağlanabilirsiniz. Daha sonra verileri sorgulamak için bir Transact-SQL ifadesini çalıştıracaksınız.

> [!TIP]
> Aşağıdaki Microsoft Learn modülü, [bir Azure SQL veritabanını sorgulayan bir ASP.NET uygulamasının nasıl geliştirileceği ve yapılandırılacağı](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/) hakkında bilgi edinmenize yardımcı olur

## <a name="prerequisites"></a>Ön koşullar

Bu öğretici için şunlar gerekir:

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

- [İşletim sisteminiz için .NET Core](https://www.microsoft.com/net/core) yüklendi.

> [!NOTE]
> Bu hızlı başlangıç, *Mysampledatabase* veritabanını kullanır. Farklı bir veritabanı kullanmak istiyorsanız, veritabanı başvurularını değiştirmeniz ve C# koddaki `SELECT` sorgusunu değiştirmeniz gerekir.

## <a name="get-sql-server-connection-information"></a>SQL Server bağlantı bilgilerini al

Azure SQL veritabanına bağlanmak için gereken bağlantı bilgilerini alın. Yaklaşan yordamlar için tam sunucu adı veya ana bilgisayar adı, veritabanı adı ve oturum açma bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. **SQL veritabanları** veya **SQL yönetilen örnekler** sayfasına gidin.

3. **Genel bakış** sayfasında, tek bir veritabanı için **sunucu adı** ' nın yanında tam sunucu adını veya yönetilen örnek Için **ana bilgisayar ' ın** yanındaki tam sunucu adını gözden geçirin. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

## <a name="get-adonet-connection-information-optional"></a>ADO.NET bağlantı bilgilerini al (isteğe bağlı)

1. **Mysampledatabase** sayfasına gidin ve **Ayarlar**altında **bağlantı dizeleri**' ni seçin.

2. Tam **ADO.NET** bağlantı dizesini gözden geçirin.

    ![ADO.NET bağlantı dizesi](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Kullanmak istiyorsanız **ADO.net** bağlantı dizesini kopyalayın.
  
## <a name="create-a-new-net-core-project"></a>Yeni bir .NET Core projesi oluştur

1. Komut istemini açın ve **sqltest** adlı bir klasör oluşturun. Bu klasöre gidin ve bu komutu çalıştırın.

    ```cmd
    dotnet new console
    ```
    Bu komut, bir ilk C# kod dosyası (**program.cs**), bir XML yapılandırma dosyası (**SQLtest. csproj**) ve gerekli ikili dosyalar dahil olmak üzere yeni uygulama proje dosyaları oluşturur.

2. Bir metin düzenleyicisinde **SQLtest. csproj** dosyasını açın ve aşağıdaki XML 'i `<Project>` etiketleri arasına yapıştırın. Bu XML bir bağımlılık olarak `System.Data.SqlClient` ekler.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>SQL veritabanını sorgulamak için kod girme

1. Bir metin düzenleyicisinde **program.cs**öğesini açın.

2. İçeriği aşağıdaki kodla değiştirin ve sunucunuz, veritabanınız, Kullanıcı adınız ve parolanız için uygun değerleri ekleyin.

> [!NOTE]
> Bir ADO.NET bağlantı dizesi kullanmak için, sunucu, veritabanı, Kullanıcı adı ve parolayı aşağıdaki satırla değiştirmek üzere koddaki 4 satırı değiştirin. Dizesinde, Kullanıcı adınızı ve parolanızı ayarlayın.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Kodu çalıştırma

1. Komut isteminde aşağıdaki komutları çalıştırın.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. İlk 20 satırın döndürüldüğünden emin olun.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Uygulama penceresini kapatmak için **ENTER** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Komut satırını kullanarak Windows/Linus/macOS’ta .NET Core ile çalışmaya başlama](/dotnet/core/tutorials/using-with-xplat-cli).
- [.NET Framework ve Visual Studio kullanarak bir Azure SQL veritabanını bağlamayı ve sorgulamayı](sql-database-connect-query-dotnet-visual-studio.md)öğrenin.  
- [SSMS kullanarak Ilk Azure SQL veritabanınızı tasarlamayı](sql-database-design-first-database.md) veya [bir Azure SQL veritabanı tasarlama ve ADO.NET ile C# bağlanma](sql-database-design-first-database-csharp.md)hakkında bilgi edinin.
- .NET hakkında daha fazla bilgi edinmek için [.NET belgelerine](https://docs.microsoft.com/dotnet/) bakın.

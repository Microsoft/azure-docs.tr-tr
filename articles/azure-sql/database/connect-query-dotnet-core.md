---
title: .NET Core kullanarak bir veritabanını bağlama ve sorgulama
description: Bu konu başlığında, .NET Core kullanarak Azure SQL veritabanı veya Azure SQL yönetilen örneği 'nde bir veritabanına bağlanan ve Transact-SQL deyimlerini kullanarak sorgulayan bir program oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 221b69d428556b031efd3bd91e16d12cfeb71393
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874810"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Hızlı başlangıç: bir veritabanını sorgulamak için .NET Core (C#) kullanma
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu hızlı başlangıçta, bir veritabanına bağlanmak için [.NET Core](https://dotnet.microsoft.com) ve C# Code 'u kullanacaksınız. Daha sonra verileri sorgulamak için bir Transact-SQL ifadesini çalıştıracaksınız.

> [!TIP]
> Aşağıdaki Microsoft Learn modülü, [Azure SQL veritabanında bir veritabanını sorgulayan bir ASP.NET uygulamasının nasıl geliştirileceği ve yapılandırılacağı](/learn/modules/develop-app-that-queries-azure-sql/) hakkında bilgi edinmenize yardımcı olur

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakilere ihtiyacınız vardır:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [İşletim sisteminiz .NET Core SDK](https://dotnet.microsoft.com/download) yüklendi.
- Sorgunuzu çalıştırabileceğiniz bir veritabanı. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Yeni bir .NET Core projesi oluştur

1. Komut istemini açın ve **sqltest** adlı bir klasör oluşturun. Bu klasöre gidin ve bu komutu çalıştırın.

    ```cmd
    dotnet new console
    ```

    Bu komut, bir ilk C# kod dosyası (**program. cs**), bir XML yapılandırma dosyası (**SQLtest. csproj**) ve gerekli ikili dosyalar dahil olmak üzere yeni uygulama proje dosyaları oluşturur.

2. Bir metin düzenleyicisinde **SQLtest. csproj** dosyasını açın ve aşağıdaki XML 'i Etiketler arasına yapıştırın `<Project>` . Bu XML `System.Data.SqlClient` bir bağımlılık olarak ekler.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda veritabanını sorgulamak için kod ekleme

1. Bir metin düzenleyicisinde **program. cs**' yi açın.

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

                    String sql = "SELECT name, collation_name FROM sys.databases";

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

2. Satırların döndürüldüğünden emin olun.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Uygulama penceresini kapatmak için **ENTER** ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Komut satırını kullanarak Windows/Linus/macOS’ta .NET Core ile çalışmaya başlama](/dotnet/core/tutorials/using-with-xplat-cli).
- [.NET Framework ve Visual Studio kullanarak Azure SQL veritabanı veya Azure SQL yönetilen örneği bağlama ve sorgulama](connect-query-dotnet-visual-studio.md)hakkında bilgi edinin.  
- [SSMS ile ilk veritabanınızı tasarlamayı](design-first-database-tutorial.md) veya [bir veritabanı tasarlamanızı ve C# ve ADO.NET ile nasıl bağlanacağınızı](design-first-database-csharp-tutorial.md)öğrenin.
- .NET hakkında daha fazla bilgi edinmek için [.NET belgelerine](/dotnet/) bakın.

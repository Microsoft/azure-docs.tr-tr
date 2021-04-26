---
title: Sorgulamak Için Visual Studio ile .NET ve C# kullanın
description: Azure SQL veritabanı veya Azure SQL yönetilen örneği 'nde bir veritabanına bağlanan ve sorguları çalıştıran bir C# uygulaması oluşturmak için Visual Studio 'Yu kullanın.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1fe39e0ff9acc0c092199ab2dd199cf396f67d01
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874774"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Hızlı başlangıç: bir veritabanına bağlanmak ve veritabanını sorgulamak için Visual Studio 'Da .NET ve C# kullanın
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu hızlı başlangıçta, Azure SQL veya SYNAPSE SQL ile Transact-SQL deyimleriyle bir veritabanını sorgulamak için Visual Studio 'da [.NET Framework](https://dotnet.microsoft.com) ve C# kodunun nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakilere ihtiyacınız vardır:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional veya Enterprise Edition.
- Sorgu çalıştırabileceğiniz bir veritabanı.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda veritabanını sorgulamak için kod oluşturma

1. Visual Studio 'da yeni bir proje oluşturun. 
   
1. **Yeni proje** iletişim kutusunda, **Visual C#**, **konsol uygulaması (.NET Framework)** seçeneğini belirleyin.
   
1. Proje adı için *SQLtest* girin ve ardından **Tamam**' ı seçin. Yeni proje oluşturulur. 
   
1. **Proje**  >  **NuGet Paketlerini Yönet**' i seçin. 
   
1. **NuGet Paket Yöneticisi**' nde, **Gözden** geçirme sekmesini seçin, sonra **Microsoft. Data. SqlClient**' ı arayın ve seçin.
   
1. **Microsoft. Data. SqlClient** sayfasında, **Install**' ı seçin. 
   - İstenirse, yüklemeye devam etmek için **Tamam** ' ı seçin. 
   - Bir **Lisans kabul** penceresi görüntülenirse **kabul ediyorum**' u seçin.
   
1. Yüklemesi tamamlandığında, **NuGet Paket Yöneticisi 'ni** kapatabilirsiniz. 
   
1. Kod Düzenleyicisi 'nde **program. cs** içeriğini aşağıdaki kodla değiştirin. ,, Ve için değerlerinizi değiştirin `<your_server>` `<your_username>` `<your_password>` `<your_database>` .
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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

1. Uygulamayı çalıştırmak için **hata**  >  **ayıklamayı Başlat hata** Ayıkla ' yı seçin ya da araç çubuğundan **Başlat** ' ı seçin veya **F5** tuşuna basın.
1. Veritabanı adlarının ve harmanlamaların döndürüldüğünden emin olun ve ardından uygulama penceresini kapatın.

## <a name="next-steps"></a>Sonraki adımlar

- Windows/Linux/macOS 'ta [.NET Core kullanarak Azure SQL veritabanı 'nda bir veritabanını bağlamayı ve sorgulamayı](connect-query-dotnet-core.md) öğrenin.  
- [Komut satırını kullanarak Windows/Linus/macOS’ta .NET Core ile çalışmaya başlama](/dotnet/core/tutorials/using-with-xplat-cli) hakkında bilgi edinin.
- [SMS kullanarak ilk veritabanınızı Azure SQL veritabanı 'nda nasıl tasarlayacağınızı](design-first-database-tutorial.md) öğrenin veya [.NET kullanarak Azure SQL veritabanı 'Nda ilk veritabanınızı tasarlayın](design-first-database-csharp-tutorial.md).
- .NET hakkında daha fazla bilgi edinmek için [.NET belgelerine](/dotnet/) bakın.
- Yeniden deneme mantığı örneği: [ADO.net Ile Azure SQL 'e dayanıklı bağlantısı bağlayın][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

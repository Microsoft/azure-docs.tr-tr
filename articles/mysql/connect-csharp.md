---
title: 'Hızlı başlangıç: C# kullanarak bağlanma-MySQL için Azure veritabanı'
description: Bu hızlı başlangıçta, MySQL için Azure Veritabanı'na bağlanmak ve buradan veri sorgulamak için kullanabileceğiniz bir C# (.NET) kod örneği sağlanmıştır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 3b90d8819b5d327c3ccd143257198c7ec8538f03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535835"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>Hızlı başlangıç: MySQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için .NET (C#) kullanma

Bu hızlı başlangıçta C# uygulaması kullanarak MySQL için Azure Veritabanı'na nasıl bağlanacağınız gösterilmiştir. Ayrıca veritabanında veri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağınız da gösterilmiştir. 

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıç için şunlar gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free).
- [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) kullanarak MySQL Için Azure veritabanı tek sunucu oluşturma <br/> ya da bir tane yoksa [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) .
- Genel veya özel erişim kullanıyor olmanız temelinde, bağlantıyı etkinleştirmek için aşağıdaki eylemlerden **birini** doldurun.

|Eylem| Bağlantı yöntemi|Nasıl yapılır kılavuzu|
|:--------- |:--------- |:--------- |
| **Güvenlik duvarı kurallarını yapılandırma** | Genel | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
| **Hizmet uç noktasını yapılandır** | Genel | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
| **Özel bağlantıyı Yapılandır** | Özel | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Veritabanı ve yönetici olmayan kullanıcı oluşturma](./howto-create-users.md)

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/mysql-doc-feedback)

## <a name="create-a-c-project"></a>C# projesi oluşturma
Bir komut isteminde şunu çalıştırın:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma
MySQL için Azure Veritabanı'na bağlanmak üzere gereken bağlantı bilgilerini alın. Tam sunucu adına ve oturum açma kimlik bilgilerine ihtiyacınız vardır.

1. [Azure Portal](https://portal.azure.com/)oturum açın.
2. Azure portalında sol taraftaki menüden **Tüm kaynaklar**'a tıklayın ve oluşturduğunuz sunucuyu (örneğin, **mydemoserver**) arayın.
3. Sunucunun adına tıklayın.
4. Sunucunun **Genel Bakış** panelinden **Sunucu adı** ile **Sunucu yöneticisi oturum açma adı**’nı not alın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="MySQL için Azure Veritabanı sunucu adı":::

## <a name="step-1-connect-and-insert-data"></a>1. Adım: verileri bağlama ve ekleme
`CREATE TABLE` ve `INSERT INTO` SQL deyimlerini kullanarak verilere bağlanmak ve verileri yüklemek için aşağıdaki kodu kullanın. Kod, sınıfının yöntemlerini kullanır `MySqlConnection` :
- MySQL bağlantısı kurmak için [openAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) .
- [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand), CommandText özelliğini ayarlar
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) veritabanı komutlarını çalıştırmak. 

`Server`, `Database`, `UserID` ve `Password` parametrelerini, sunucu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/mysql-doc-feedback)


## <a name="step-2-read-data"></a>2. Adım: verileri okuma

Bir `SELECT` SQL deyimini kullanarak bağlanmak ve verileri okumak için aşağıdaki kodu kullanın. Kod, `MySqlConnection` yöntemi ile sınıfını kullanır:
- MySQL bağlantısı kurmak için [openAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) .
- CommandText özelliğini ayarlamak için [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) .
- Veritabanı komutlarını çalıştırmak için [Executereadsilinebilir Sync ()](/dotnet/api/system.data.common.dbcommand.executereaderasync) . 
- , Sonuçlardaki kayıtlara ilerlemek için [ReadAsync ()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) . Ardından kod, GetInt32 ve GetString yöntemini kullanarak kayıttaki değerleri ayrıştırır.


`Server`, `Database`, `UserID` ve `Password` parametrelerini, sunucu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/mysql-doc-feedback)

## <a name="step-3-update-data"></a>3. Adım: verileri güncelleştirme
Bir `UPDATE` SQL deyimini kullanarak bağlanmak ve verileri okumak için aşağıdaki kodu kullanın. Kod, `MySqlConnection` yöntemi ile sınıfını kullanır:
- MySQL bağlantısı kurmak için [openAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) . 
- CommandText özelliğini ayarlamak için [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand)
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) veritabanı komutlarını çalıştırmak. 


`Server`, `Database`, `UserID` ve `Password` parametrelerini, sunucu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/mysql-doc-feedback)

## <a name="step-4-delete-data"></a>4. Adım: verileri silme
Bir `DELETE` SQL deyimini kullanarak bağlanmak ve verileri silmek için aşağıdaki kodu kullanın. 

Kod, `MySqlConnection` yöntemi ile sınıfını kullanır
- MySQL bağlantısı kurmak için [openAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) .
- CommandText özelliğini ayarlamak için [CreateCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) .
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) veritabanı komutlarını çalıştırmak. 


`Server`, `Database`, `UserID` ve `Password` parametrelerini, sunucu ve veritabanını oluştururken belirttiğiniz değerlerle değiştirin. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç sırasında kullanılan tüm kaynakları temizlemek için, aşağıdaki komutu kullanarak kaynak grubunu silin:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Portalı kullanarak MySQL için Azure veritabanı sunucusunu yönetme](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLı kullanarak MySQL için Azure veritabanı sunucusunu yönetme](./how-to-manage-single-server-cli.md)

[Aradığınızı bulamıyor musunuz? Bize bilgi verin.](https://aka.ms/mysql-doc-feedback)

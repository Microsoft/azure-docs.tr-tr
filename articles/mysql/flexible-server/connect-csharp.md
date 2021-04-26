---
title: 'Hızlı başlangıç: C# kullanarak bağlanma-MySQL için Azure veritabanı esnek sunucu'
description: Bu hızlı başlangıçta, MySQL esnek sunucusu için Azure veritabanı 'na bağlanmak ve buradan veri sorgulamak için kullanabileceğiniz bir C# (.net) kod örneği sağlanmıştır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: c13b8392a766dc29d8c83f9986d0b43271caabbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98606168"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Hızlı başlangıç: MySQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için .NET (C#) kullanma-esnek sunucu

Bu hızlı başlangıçta C# uygulaması kullanarak MySQL için Azure Veritabanı'na nasıl bağlanacağınız gösterilmiştir. Ayrıca veritabanında veri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağınız da gösterilmiştir.

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıç için şunlar gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free).
- [Azure Portal](./quickstart-create-server-portal.md) kullanarak MySQL Için Azure veritabanı esnek sunucusu oluşturma <br/> ya da bir tane yoksa [Azure CLI](./quickstart-create-server-cli.md) .
- Genel veya özel erişim kullanıyor olmanız temelinde, bağlantıyı etkinleştirmek için aşağıdaki eylemlerden **birini** doldurun.
- [Veritabanı ve yönetici olmayan kullanıcı oluşturma](../howto-create-users.md)

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://github.com/MicrosoftDocs/azure-docs/issues)

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
2. Azure Portal sol taraftaki menüden **tüm kaynaklar**' ı seçin ve oluşturduğunuz sunucuyu (örneğin, **demosunucum**) arayın.
3. Sunucu adını seçin.
4. Sunucunun **Genel Bakış** panelinden **Sunucu adı** ile **Sunucu yöneticisi oturum açma adı**’nı not alın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.
 :::image type="content" source="./media/connect-csharp/server-overview-name-login.png" alt-text="MySQL için Azure Veritabanı sunucu adı":::

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
                UserID = "USER",
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

[Herhangi bir sorun mu yaşıyorsunuz? Bize bilgi verin.](https://github.com/MicrosoftDocs/azure-docs/issues)

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

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://github.com/MicrosoftDocs/azure-docs/issues)

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
                UserID = "USER",
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
                UserID = "USER",
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
> [Portalı kullanarak MySQL için Azure veritabanı sunucusunu yönetme](./how-to-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLı kullanarak MySQL için Azure veritabanı sunucusunu yönetme](./how-to-manage-server-cli.md)



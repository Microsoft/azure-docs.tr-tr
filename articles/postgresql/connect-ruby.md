---
title: 'Hızlı başlangıç: Ruby ile bağlanma-PostgreSQL için Azure veritabanı-tek sunucu'
description: Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'na bağlanmak ve bu verileri sorgulamak için kullanabileceğiniz bir Ruby kodu örneği sunulmaktadır-tek sunucu.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 20e45454284af230da74896d5b3f5e9da676dbb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831712"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Hızlı başlangıç: PostgreSQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Ruby kullanma-tek sunucu

Bu hızlı başlangıçta, [Ruby](https://www.ruby-lang.org) uygulaması kullanılarak PostgreSQL için Azure Veritabanı’na nasıl bağlanılacağı gösterilmiştir. Ayrıca veritabanında veri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağınız da gösterilmiştir. Bu makaledeki adımlarda, Ruby kullanarak geliştirmeyle ilgili bilgi sahibi olduğunuz ve PostgreSQL için Azure Veritabanı ile çalışmaya yeni başladığınız varsayılır.

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıçta, başlangıç noktası olarak şu kılavuzlardan birinde oluşturulan kaynaklar kullanılmaktadır:
- [DB Oluşturma - Portal](quickstart-create-server-database-portal.md)
- [DB Oluşturma - Azure CLI](quickstart-create-server-database-azure-cli.md)

Ayrıca yüklü olması gerekir:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- Ruby için PostgreSQL modülü olan [Ruby pg](https://rubygems.org/gems/pg/)

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma
PostgreSQL için Azure Veritabanı'na bağlanmak üzere gereken bağlantı bilgilerini alın. Tam sunucu adına ve oturum açma kimlik bilgilerine ihtiyacınız vardır.

1. [Azure Portal](https://portal.azure.com/)oturum açın.
2. Azure portalında sol taraftaki menüden **Tüm kaynaklar**'a tıklayın ve oluşturduğunuz sunucuyu (örneğin, **mydemoserver**) arayın.
3. Sunucunun adına tıklayın.
4. Sunucunun **Genel Bakış** panelinden **Sunucu adı** ile **Sunucu yöneticisi oturum açma adı**’nı not alın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.
 :::image type="content" source="./media/connect-ruby/1-connection-string.png" alt-text="PostgreSQL için Azure Veritabanı sunucu adı":::

> [!NOTE]
> `@`Azure Postgres Kullanıcı adı içindeki simge, `%40` tüm bağlantı dizelerinde olduğu gibi URL 'yi kodladı.

## <a name="connect-and-create-a-table"></a>Bağlanma ve tablo oluşturma
**CREATE TABLE** SQL deyimini kullanarak bir tabloyu bağlamak ve oluşturmak ve ardından **INSERT INTO** SQL deyimlerini kullanarak tabloya satırlar eklemek için aşağıdaki kodu kullanın.

Kod, ```PG::Connection``` ```new``` PostgreSQL Için Azure veritabanı 'na bağlanmak için Oluşturucu içeren bir nesne kullanır. Ardından ```exec()``` Drop, create table ve INSERT INTO komutlarını çalıştırmak için yöntemini çağırır. Kod, sınıfını kullanarak hata olup olmadığını denetler ```PG::Error``` . Ardından, ```close()``` sonlandırmadan önce bağlantıyı kapatmak için yöntemini çağırır. Bu sınıflar ve yöntemler hakkında daha fazla bilgi için bkz. Ruby pg Reference belgeleri.

`host`, `database`, `user` ve `password` dizelerini kendi değerlerinizle değiştirin.


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Verileri okuma
Bağlanmak ve **SELECT** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın.

Kod,  ```PG::Connection``` ```new``` PostgreSQL Için Azure veritabanı 'na bağlanmak için Oluşturucu içeren bir nesne kullanır. Ardından ```exec()``` , SELECT komutunu çalıştırmak için yöntemini çağırır ve sonuçları bir sonuç kümesinde tutun. Sonuç kümesi koleksiyonu, `resultSet.each do` döngüsü kullanılarak ve geçerli satır değerleri `row` değişkeninde tutularak yinelenir. Kod, sınıfını kullanarak hata olup olmadığını denetler ```PG::Error``` . Ardından, ```close()``` sonlandırmadan önce bağlantıyı kapatmak için yöntemini çağırır. Bu sınıflar ve yöntemler hakkında daha fazla bilgi için bkz. Ruby pg Reference belgeleri.

`host`, `database`, `user` ve `password` dizelerini kendi değerlerinizle değiştirin.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Verileri güncelleştirme
**UPDATE** SQL deyimini kullanarak bağlanmak ve verileri güncelleştirmek için aşağıdaki kodu kullanın.

Kod,  ```PG::Connection``` ```new``` PostgreSQL Için Azure veritabanı 'na bağlanmak için Oluşturucu içeren bir nesne kullanır. Sonra ```exec()``` Update komutunu çalıştırmak için yöntemini çağırır. Kod, sınıfını kullanarak hata olup olmadığını denetler ```PG::Error``` . Ardından, ```close()``` sonlandırmadan önce bağlantıyı kapatmak için yöntemini çağırır. Bu sınıflar ve yöntemler hakkında daha fazla bilgi için bkz. [Ruby pg Reference belgeleri](https://rubygems.org/gems/pg) .

`host`, `database`, `user` ve `password` dizelerini kendi değerlerinizle değiştirin.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Verileri silme
Bağlanmak ve **DELETE** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın.

Kod,  ```PG::Connection``` ```new``` PostgreSQL Için Azure veritabanı 'na bağlanmak için Oluşturucu içeren bir nesne kullanır. Sonra ```exec()``` Update komutunu çalıştırmak için yöntemini çağırır. Kod, sınıfını kullanarak hata olup olmadığını denetler ```PG::Error``` . Ardından, ```close()``` sonlandırmadan önce bağlantıyı kapatmak için yöntemini çağırır.

`host`, `database`, `user` ve `password` dizelerini kendi değerlerinizle değiştirin.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
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
> [Dışarı Aktarma ve İçeri Aktarma seçeneğini kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md) <br/>
> [!div class="nextstepaction"]
> [Ruby pg başvuru belgeleri](https://rubygems.org/gems/pg)

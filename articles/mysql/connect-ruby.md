---
title: "Hızlı başlangıç: bir MySQL için Azure veritabanı 'nı kullanarak bağlanma"
description: Bu hızlı başlangıçta, MySQL için Azure Veritabanı'na bağlanmak ve buradan veri sorgulamak için kullanabileceğiniz birkaç Ruby kod örneği sağlanmıştır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 4eba3fabee50e0011d5a63297c726a9647dd84c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831542"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>Hızlı başlangıç: MySQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Ruby kullanma

Bu hızlı başlangıçta, Windows, Ubuntu Linux ve Mac platformlarından bir [Ruby](https://www.ruby-lang.org) uygulaması ve [mysql2](https://rubygems.org/gems/mysql2) gem kullanılarak MySQL için Azure Veritabanı’na nasıl bağlanılacağı gösterilmiştir. Ayrıca veritabanında veri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağınız da gösterilmiştir. Bu konuda, Ruby kullanarak geliştirmeyle ilgili bilgi sahibi olduğunuz ve MySQL için Azure Veritabanı ile çalışmaya yeni başladığınız varsayılır.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıçta, başlangıç noktası olarak şu kılavuzlardan birinde oluşturulan kaynaklar kullanılmaktadır:
- [Azure portalını kullanarak MySQL için Azure Veritabanı sunucusu oluşturma](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI aracını kullanarak MySQL için Azure Veritabanı sunucusu oluşturma](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT]
> Bağlanmakta olduğunuz IP adresinin [Azure Portal](./howto-manage-firewall-using-portal.md) veya [Azure CLI](./howto-manage-firewall-using-cli.md) kullanarak sunucunun güvenlik duvarı kurallarını eklendiğinden emin olun

## <a name="install-ruby"></a>Ruby’yi yükleme
Kendi bilgisayarınıza Ruby, Gem ve MySQL2 kitaplığı yükleyin.

### <a name="windows"></a>Windows
1. [Ruby](https://rubyinstaller.org/downloads/)'nin 2.3 sürümünü indirip yükleyin.
2. Başlat menüsünden yeni bir komut istemi (cmd) başlatın.
3. Dizini değiştirip, Ruby’nin 2.3 sürümü dizinine geçin. `cd c:\Ruby23-x64\bin`
4. Yüklenen sürümü görmek için `ruby -v` komutunu çalıştırarak Ruby yüklemesini test edin.
5. Yüklenen sürümü görmek için `gem -v` komutunu çalıştırarak Gem yüklemesini test edin.
6. `gem install mysql2` komutunu çalıştırarak, Ruby için Mysql2 modülünü Gem kullanarak derleyin.

### <a name="macos"></a>Mac OS
1. `brew install ruby` komutunu çalıştırarak Homebrew kullanan Ruby’yi yükleyin. Daha fazla yükleme seçeneği için Ruby [yükleme belgelerine](https://www.ruby-lang.org/en/documentation/installation/#homebrew)bakın.
2. Yüklenen sürümü görmek için `ruby -v` komutunu çalıştırarak Ruby yüklemesini test edin.
3. Yüklenen sürümü görmek için `gem -v` komutunu çalıştırarak Gem yüklemesini test edin.
4. `gem install mysql2` komutunu çalıştırarak, Ruby için Mysql2 modülünü Gem kullanarak derleyin.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. `sudo apt-get install ruby-full` komutunu çalıştırarak Ruby’yi yükleyin. Daha fazla yükleme seçeneği için Ruby [yükleme belgelerine](https://www.ruby-lang.org/en/documentation/installation/)bakın.
2. Yüklenen sürümü görmek için `ruby -v` komutunu çalıştırarak Ruby yüklemesini test edin.
3. `sudo gem update --system` komutunu çalıştırarak Gem için en son güncelleştirmeleri yükleyin.
4. Yüklenen sürümü görmek için `gem -v` komutunu çalıştırarak Gem yüklemesini test edin.
5. `sudo apt-get install build-essential` komutunu çalıştırarak gcc, make ve diğer derleme araçlarını yükleyin.
6. `sudo apt-get install libmysqlclient-dev` komutunu çalıştırarak MySQL istemci geliştirme kitaplığını yükleyin.
7. `sudo gem install mysql2` komutunu çalıştırarak, Ruby için mysql2 modülünü Gem kullanarak derleyin.

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma
MySQL için Azure Veritabanı'na bağlanmak üzere gereken bağlantı bilgilerini alın. Tam sunucu adına ve oturum açma kimlik bilgilerine ihtiyacınız vardır.

1. [Azure Portal](https://portal.azure.com/)oturum açın.
2. Azure portalında sol taraftaki menüden **Tüm kaynaklar**'a tıklayın ve oluşturduğunuz sunucuyu (örneğin, **mydemoserver**) arayın.
3. Sunucunun adına tıklayın.
4. Sunucunun **Genel Bakış** panelinden **Sunucu adı** ile **Sunucu yöneticisi oturum açma adı**’nı not alın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.
 :::image type="content" source="./media/connect-ruby/1_server-overview-name-login.png" alt-text="MySQL için Azure Veritabanı sunucu adı":::

## <a name="run-ruby-code"></a>Ruby kodunu çalıştırma
1. Aşağıdaki bölümde bulunan Ruby kodunu metin dosyalarına yapıştırın ve dosyaları .rb uzantısıyla bir proje klasörüne kaydedin (örneğin, `C:\rubymysql\createtable.rb` veya `/home/username/rubymysql/createtable.rb`).
2. Kodu çalıştırmak için komut istemi veya Bash kabuğu başlatın. Dizini değiştirerek, proje klasörünüze geçin (`cd rubymysql`)
3. Sonra uygulamayı çalıştırmak için Ruby komutunu ve ardından `ruby createtable.rb` örneğindeki gibi dosya adını yazın.
4. Windows işletim sisteminde Ruby uygulamanız yol ortam değişkeninde değilse düğüm uygulamasını başlatmak için tam yolu kullanmanız gerekebilir; örneğin, `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Bağlanma ve tablo oluşturma
**CREATE TABLE** SQL deyimini kullanarak bir tabloyu bağlamak ve oluşturmak ve ardından **INSERT INTO** SQL deyimlerini kullanarak tabloya satırlar eklemek için aşağıdaki kodu kullanın.

Kod, MySQL sunucusuna bağlanmak için bir mysql2:: Client sınıfı kullanır. Ardından ```query()``` Drop, create table ve INSERT INTO komutlarını çalıştırmak için yöntemini çağırır. Son olarak, ```close()``` sonlandırmadan önce bağlantıyı kapatmak için öğesini çağırın.

`host`, `database`, `username` ve `password` dizelerini kendi değerlerinizle değiştirin.
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Verileri okuma
Bağlanmak ve **SELECT** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın.

Kod, yöntemi ile MySQL için Azure veritabanı 'na bağlanmak için bir mysql2:: Client sınıfı kullanır ```new()``` . Ardından ```query()``` Select komutlarını çalıştırmak için yöntemini çağırır. Ardından, ```close()``` sonlandırmadan önce bağlantıyı kapatmak için yöntemini çağırır.

`host`, `database`, `username` ve `password` dizelerini kendi değerlerinizle değiştirin.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Verileri güncelleştirme
Bağlanmak ve bir **UPDATE** SQL deyimi kullanarak verileri güncelleştirmek için aşağıdaki kodu kullanın.

Kod, [mysql2::client](https://rubygems.org/gems/mysql2-client-general_log) sınıfı .new() yöntemini kullanarak MySQL için Azure Veritabanıyla bağlantı kurar. Sonra ```query()```  Update komutlarını çalıştırmak için yöntemini çağırır. Ardından, ```close()``` sonlandırmadan önce bağlantıyı kapatmak için yöntemini çağırır.

`host`, `database`, `username` ve `password` dizelerini kendi değerlerinizle değiştirin.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Verileri silme
Bağlanmak ve **DELETE** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın.

Kod, MySQL sunucusuna bağlanmak için bir [mysql2:: Client](https://rubygems.org/gems/mysql2/) sınıfı KULLANıR, DELETE komutunu çalıştırın ve ardından sunucu bağlantısını kapatın.

`host`, `database`, `username` ve `password` dizelerini kendi değerlerinizle değiştirin.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
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
> [Dışarı Aktarma ve İçeri Aktarma seçeneğini kullanarak veritabanınızı geçirme](./concepts-migrate-import-export.md) <br/>

> [!div class="nextstepaction"]
> [MySQL2 istemcisi hakkında daha fazla bilgi edinin](https://rubygems.org/gems/mysql2-client-general_log) <br/>


---
title: 'Hızlı başlangıç: Python kullanarak bağlanma-esnek sunucu için Azure veritabanı'
description: Bu hızlı başlangıçta, MySQL için Azure veritabanı-esnek sunucusu 'na bağlanmak ve veri sorgulamak için kullanabileceğiniz çeşitli Python kod örnekleri sunulmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: e99beb024690bf888d0acbb747e98252eced7e06
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110143"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Hızlı başlangıç: MySQL kullanarak MySQL için Azure veritabanı 'na bağlanma ve veri sorgulama-esnek sunucu

> [!IMPORTANT] 
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu hızlı başlangıçta Python kullanarak MySQL için Azure veritabanı esnek sunucusuna bağlanırsınız. Ardından, Mac, Ubuntu Linux ve Windows platformlarındaki veritabanındaki verileri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini kullanın. 

Bu makalede, Python kullanarak geliştirmeyle ilgili bilgi sahibi olduğunuz ve MySQL için Azure veritabanı esnek sunucusu ile çalışmaya yeni başladığınızı varsaymaktadır.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* MySQL için Azure veritabanı esnek sunucu. Esnek sunucu oluşturmak için [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucusu oluşturma](./quickstart-create-server-portal.md) veya [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu oluşturma](./quickstart-create-server-cli.md)bölümüne bakın.

## <a name="preparing-your-client-workstation"></a>İstemci iş istasyonunuzu hazırlama
- Esnek sunucunuzu *özel erişim (VNET tümleştirmesi)* ile oluşturduysanız, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan VNet 'e ekleyebilirsiniz. [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağı oluşturma ve yönetme](./how-to-manage-virtual-network-cli.md)bölümüne bakın.
- Esnek sunucunuzu *ortak erişim (izin VERILEN IP adresleri)* ile oluşturduysanız, sunucunuzdaki güvenlik duvarı kuralları lıstesıne yerel IP adresinizi ekleyebilirsiniz. [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları oluşturma ve yönetme](./how-to-manage-firewall-cli.md)bölümüne bakın.

## <a name="install-python-and-the-mysql-connector"></a>Python’u ve MySQL bağlayıcısını yükleme

Aşağıdaki adımları kullanarak bilgisayarınıza Python ve Python için MySQL bağlayıcısını yüklersiniz: 

> [!NOTE]
> Bu hızlı başlangıç, MySQL 'e bağlanmak için ham SQL sorgu yaklaşımını kullanır. Bir Web çerçevesi kullanıyorsanız, Framework için önerilen bağlayıcıyı kullanın, örneğin, Docgo için [mysqlclient](https://pypi.org/project/mysqlclient/) .

1. İşletim sistemi için [Python 3,7 veya üstünü](https://www.python.org/downloads/) indirin ve yükleyin. MySQL Bağlayıcısı için gerekli olduğundan Python 'u uygulamanıza eklediğinizden emin olun `PATH` .
   
1. Bir komut istemi veya `bash` kabuk açın ve `python -V` büyük harf V anahtarıyla çalıştırarak Python sürümünüzü denetleyin.
   
1. `pip`Paket yükleyicisi, Python 'un en son sürümlerine dahildir. ' İ `pip` çalıştırarak en son sürüme güncelleştirin `pip install -U pip` . 
   
   `pip`Yüklü değilse, ile indirip yükleyebilirsiniz `get-pip.py` . Daha fazla bilgi için bkz. [yükleme](https://pip.pypa.io/en/stable/installing/). 
   
1. `pip`Python Için MySQL bağlayıcısını ve bağımlılıklarını yüklemek için kullanın:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   MySQL için Python bağlayıcısını [MySQL.com](https://dev.mysql.com/downloads/connector/python/)adresinden da yükleyebilirsiniz. Python için MySQL Bağlayıcısı hakkında daha fazla bilgi için [MySQL Bağlayıcısı/Python Geliştirici Kılavuzu](https://dev.mysql.com/doc/connector-python/en/)' na bakın. 

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma

Azure portal MySQL için Azure veritabanı esnek sunucusuna bağlanmak için gereken bağlantı bilgilerini alın. Sunucu adı, veritabanı adı ve oturum açma kimlik bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
   
1. Portal arama çubuğunda, oluşturduğunuz MySQL için Azure veritabanı esnek sunucusu ' nu arayın ve örneğin, **demosunucum**' ı seçin.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
1. Sunucunun **genel bakış** sayfasında **sunucu adı** ve **Sunucu Yöneticisi oturum açma adı**' nı bir yere göz atın. Parolanızı unutursanız, bu sayfadan parolayı da sıfırlayabilirsiniz.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Kod örnekleri

### <a name="run-below-mentioned-python-code-samples"></a>Aşağıda belirtilen Python kodu örneklerini Çalıştır
Bu makaledeki her kod örneği için:

1. Metin düzenleyicisinde yeni bir dosya oluşturun.
1. Kod örneğini dosyaya ekleyin. Kodda,,, `<mydemoserver>` `<myadmin>` `<mypassword>` ve `<mydatabase>` yer tutucuları MySQL sunucunuzun ve veritabanınızın değerleriyle değiştirin.
1. Dosyayı, *C:\pythonmysql\createtable.py* veya */Home/username/pythonmysql/CreateTable.py* gibi *. Kopyala* uzantısıyla bir proje klasörüne kaydedin.
1. Kodu çalıştırmak için, bir komut istemi veya kabuğu açın `bash` ve dizini proje klasörünüze değiştirin (örneğin,) `cd pythonmysql` . `python`Komutun ardından dosya adını yazın `python createtable.py` ve ENTER tuşuna basın. 
   
   > [!NOTE]
   > Windows 'ta *python.exe* BULUNAMAZSA, yol ortam değişkeninizin Python yolunu eklemeniz veya örneğin *python.exe* tam yolunu sağlamanız gerekebilir `C:\python27\python.exe createtable.py` .

### <a name="create-a-table-and-insert-data"></a>Tablo oluşturma ve veri ekleme

Sunucu ve veritabanına bağlanmak, tablo oluşturmak ve **Insert** SQL ifadesini kullanarak verileri yüklemek için aşağıdaki kodu kullanın. 

Kod MySQL. Connector kitaplığını içeri aktarır ve yapılandırma koleksiyonundaki [bağımsız değişkenleri](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) kullanarak esnek sunucuya bağlanmak için [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) işlevini kullanır. Kod, bağlantıda bir imleç kullanır ve [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemi MySQL veritabanında SQL sorgusunu yürütür. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="read-data"></a>Verileri okuma

Bağlanmak ve **SELECT** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın. 

Kod MySQL. Connector kitaplığını içeri aktarır ve yapılandırma koleksiyonundaki [bağımsız değişkenleri](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) kullanarak esnek sunucuya bağlanmak için [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) işlevini kullanır. Kod, bağlantıda bir imleç kullanır ve [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemi MySQL veritabanında SQL sorgusunu yürütür. 

Kod, [fetchAll ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) yöntemini kullanarak veri satırlarını okur, sonuç kümesini bir koleksiyon satırında tutar ve `for` satırlarda döngü yapmak için bir yineleyici kullanır.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="update-data"></a>Verileri güncelleştirme

Bağlanmak ve bir **UPDATE** SQL deyimi kullanarak verileri güncelleştirmek için aşağıdaki kodu kullanın. 

Kod MySQL. Connector kitaplığını içeri aktarır ve yapılandırma koleksiyonundaki [bağımsız değişkenleri](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) kullanarak esnek sunucuya bağlanmak için [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) işlevini kullanır. Kod, bağlantıda bir imleç kullanır ve [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemi MySQL veritabanında SQL sorgusunu yürütür. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

### <a name="delete-data"></a>Verileri silme

Bağlanmak ve **DELETE** SQL deyimini kullanarak verileri kaldırmak için aşağıdaki kodu kullanın. 

Kod MySQL. Connector kitaplığını içeri aktarır ve yapılandırma koleksiyonundaki [bağımsız değişkenleri](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) kullanarak esnek sunucuya bağlanmak için [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) işlevini kullanır. Kod, bağlantıda bir imleç kullanır ve [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemi MySQL veritabanında SQL sorgusunu yürütür. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL Için Azure veritabanı-esnek sunucu 'Da Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantı](./how-to-connect-tls-ssl.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'Nda ağ](./concepts-networking.md)hakkında daha fazla bilgi edinin.
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları oluşturun ve yönetin](./how-to-manage-firewall-portal.md).
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-portal.md).

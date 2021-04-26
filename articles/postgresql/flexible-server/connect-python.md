---
title: 'Hızlı başlangıç: Python kullanarak bağlanma-PostgreSQL için Azure veritabanı-esnek sunucu'
description: Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'na bağlanmak ve buradan veri sorgulamak için kullanabileceğiniz çeşitli Python kod örnekleri sunulmaktadır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 823dd6fc64da2c4900f5a0a35a6cd27a9a2b43ff
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227493"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Hızlı başlangıç: PostgreSQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Python kullanma-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Bu hızlı başlangıçta Python kullanarak PostgreSQL için Azure veritabanı 'na (esnek sunucu) bağlanırsınız. Ardından, Mac, Ubuntu Linux ve Windows platformlarındaki veritabanındaki verileri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini kullanın. 

Bu makalede, Python kullanarak geliştirmeyle ilgili bilgi sahibi olduğunuz ve PostgreSQL için Azure veritabanı ile çalışmaya yeni başladığınızı varsaymaktadır.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* PostgreSQL için Azure veritabanı-esnek sunucu. Esnek sunucu oluşturmak için [Azure Portal kullanarak PostgreSQL Için Azure veritabanı oluşturma-esnek sunucu](./quickstart-create-server-portal.md)' ya bakın.
* [Python](https://www.python.org/downloads/) 2,7 veya 3.6 +.
* En son [PIP](https://pip.pypa.io/en/stable/installing/) paketi yükleyicisi.

## <a name="preparing-your-client-workstation"></a>İstemci iş istasyonunuzu hazırlama
- Esnek sunucunuzu *özel erişim (VNET tümleştirmesi)* ile oluşturduysanız, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan VNet 'e ekleyebilirsiniz. [Azure CLI kullanarak PostgreSQL Için Azure veritabanı-esnek sunucu sanal ağı oluşturma ve yönetme](./how-to-manage-virtual-network-cli.md)bölümüne bakın.
- Esnek sunucunuzu *ortak erişim (izin VERILEN IP adresleri)* ile oluşturduysanız, sunucunuzdaki güvenlik duvarı kuralları lıstesıne yerel IP adresinizi ekleyebilirsiniz. [Azure CLI kullanarak PostgreSQL Için Azure veritabanı (esnek sunucu güvenlik duvarı kuralları oluşturma ve yönetme](./how-to-manage-firewall-cli.md)) konusuna bakın.

## <a name="install-the-python-libraries-for-postgresql"></a>PostgreSQL için Python kitaplıklarını yükleme
[Psycopg2](https://pypi.python.org/pypi/psycopg2/) modülü, bir PostgreSQL veritabanına bağlanma ve sorgulama sağlar ve Linux, MacOS veya Windows [tekerlek](https://pythonwheels.com/) paketi olarak kullanılabilir. Tüm bağımlılıklar dahil olmak üzere modülün ikili sürümünü yükler. Yükleme ve gereksinimler hakkında daha fazla bilgi için `psycopg2` bkz. [yükleme](http://initd.org/psycopg/docs/install.html). 

Yüklemek için `psycopg2` bir Terminal veya komut istemi açın ve komutunu çalıştırın `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Veritabanı bağlantı bilgilerini al
PostgreSQL için Azure veritabanı 'na bağlanma-esnek sunucu, tam sunucu adı ve oturum açma kimlik bilgilerini gerektirir. Bu bilgileri Azure portal alabilirsiniz.

1. [Azure Portal](https://portal.azure.com/), esnek sunucu adınızı arayıp seçin. 
2. Sunucunun **genel bakış** sayfasında, tam **sunucu adı** ve **Yönetici Kullanıcı adı**' nı kopyalayın. Tam **sunucu adı** her zaman *\<my-server-name> . Postgres.Database.Azure.com* biçiminde olur.

   Yönetici parolanızla de ihtiyacınız vardır. Unutursanız, genel bakış sayfasından sıfırlayabilirsiniz. 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>Python örneklerini çalıştırma

Bu makaledeki her kod örneği için:

1. Metin düzenleyicisinde yeni bir dosya oluşturun. 

1. Kod örneğini dosyaya ekleyin. Kodda, şunu değiştirin:
   - `<server-name>` ve `<admin-username>` Azure Portal kopyaladığınız değerlerle birlikte.
   - `<admin-password>` Sunucu parolanızla birlikte.
   - `<database-name>` PostgreSQL için Azure veritabanınızın adıyla (esnek sunucu veritabanı). Sunucunuzu oluştururken *Postgres* adlı varsayılan veritabanı otomatik olarak oluşturulmuştur. Bu veritabanını yeniden adlandırabilir veya SQL komutlarını kullanarak yeni bir veritabanı oluşturabilirsiniz. 

1. Dosyayı, *Postgres-insert.py* gibi bir *. Kopyala* uzantısıyla birlikte proje klasörünüze kaydedin. Windows için, dosyayı kaydettiğinizde UTF-8 kodlamasının seçildiğinden emin olun. 

1. Dosyayı çalıştırmak için, bir komut satırı arabirimindeki proje klasörünüze geçin ve `python` ardından dosya adını (örneğin,) yazın `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Tablo oluşturma ve veri ekleme
Aşağıdaki kod örneği, [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) Işlevini kullanarak PostgreSQL Için Azure veritabanınıza ve esnek sunucu veritabanına bağlanır ve VERILERI bir SQL **Insert** ifadesiyle yükler. [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) işlevi SQL sorgusunu veritabanına karşı yürütür. 

```Python
import psycopg2
# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")
# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")
# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")
# Clean up
conn.commit()
cursor.close()
conn.close()
```

Kod başarıyla çalıştırıldığında, şu çıktıyı üretir:

![Komut satırı çıkışı](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Verileri okuma
Aşağıdaki kod örneği, PostgreSQL için Azure veritabanınıza ve esnek sunucu veritabanına bağlanır ve verileri okumak için SQL **Select** ifadesiyle [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) kullanır. Bu işlev bir sorguyu kabul eder ve [Cursor. fetchAll ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)kullanılarak yinelemek için bir sonuç kümesi döndürür. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()
# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Verileri güncelleştirme
Aşağıdaki kod örneği, PostgreSQL için Azure veritabanınıza ve esnek sunucu veritabanına bağlanır ve verileri güncelleştirmek için SQL **Update** ifadesiyle [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) 'yi kullanır. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Verileri silme
Aşağıdaki kod örneği, PostgreSQL için Azure veritabanınıza ve esnek sunucu veritabanına bağlanır ve daha önce eklediğiniz bir envanter öğesini silmek için SQL **Delete** ifadesiyle [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) 'yi kullanır. 

```Python
import psycopg2
# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"
# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")
cursor = conn.cursor()
# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Döküm ve geri yükleme kullanarak veritabanınızı geçirme](../howto-migrate-using-dump-and-restore.md)

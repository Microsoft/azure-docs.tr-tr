---
title: PostgreSQL için Azure veritabanı 'na bağlanmak için Python kullanma-tek sunucu
description: Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'na bağlanmak ve bu verilere veri sorgulamak için kullanabileceğiniz Python kod örnekleri sağlanır-tek sunucu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 441ff1ebeffde36d1940520404050f6cc29ea53e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74066286"
---
# <a name="use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Python kullanma-tek sunucu
Bu hızlı başlangıçta, macOS, Ubuntu Linux veya Windows üzerinde Python kullanarak PostgreSQL için Azure veritabanı ile nasıl çalışılacağı gösterilmektedir. Hızlı başlangıç, veritabanına bağlanmayı ve verileri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini kullanmayı gösterir. Makalede, Python hakkında bilgi sahibi olduğunuz ancak PostgreSQL için Azure veritabanı ile çalışmaya yeni başladığınız varsayılır.

## <a name="prerequisites"></a>Önkoşullar
- PostgreSQL için Azure veritabanı- [hızlı başlangıç: Azure Portal veya hızlı başlangıç: PostgreSQL Için Azure veritabanı sunucusu oluşturma](quickstart-create-server-database-portal.md) - [Azure CLI kullanarak](quickstart-create-server-database-azure-cli.md)bir PostgreSQL için Azure veritabanı oluşturma. 
  
- [Python](https://www.python.org/downloads/) 2.7.9 + veya 3.4 +.
  
- `pip install -U pip`kullanılarak yüklenen, [PIP](https://pip.pypa.io/en/stable/installing/) paketi yükleyicisinin en son güncelleştirmesi. 

## <a name="install-the-python-libraries-for-postgresql"></a>PostgreSQL için Python kitaplıklarını yükleme
[Psycopg2](https://pypi.python.org/pypi/psycopg2/) modülü, bir PostgreSQL veritabanına bağlanma ve sorgulama sağlar ve Linux, MacOS veya Windows [tekerlek](https://pythonwheels.com/) paketi olarak kullanılabilir. Tüm bağımlılıklar dahil olmak üzere modülün ikili sürümünü yükler. `psycopg2` yükleme ve gereksinimler hakkında daha fazla bilgi için bkz. [yükleme](http://initd.org/psycopg/docs/install.html). 

`psycopg2`yüklemek için bir Terminal veya komut istemi açın ve `pip install psycopg2`komutunu çalıştırın.

## <a name="get-database-connection-information"></a>Veritabanı bağlantı bilgilerini al
PostgreSQL için Azure veritabanı veritabanına bağlanmak için tam sunucu adı ve oturum açma kimlik bilgileri gerekir. Bu bilgileri Azure portal alabilirsiniz.

1. [Azure Portal](https://portal.azure.com/), PostgreSQL Için Azure veritabanı sunucu adı ' nı arayıp seçin. 
1. Sunucunun **genel bakış** sayfasında, tam **sunucu adı** ve **Yönetici Kullanıcı adı**' nı kopyalayın. Tam **sunucu adı** her zaman *\<-sunucu adı >. Postgres. Database. Azure. com*' dır ve **Yönetici Kullanıcı** adı her zaman şu biçimdedir *\<my-admin-username > @\<My-Server-Name >* . 
   
   Yönetici parolanızla de ihtiyacınız vardır. Unutursanız, bu sayfadan sıfırlayabilirsiniz. 
   
   ![PostgreSQL için Azure veritabanı sunucu adı](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Python örneklerini çalıştırma

Bu makaledeki her kod örneği için:

1. Metin düzenleyicisinde yeni bir dosya oluşturun. 
   
1. Kod örneğini dosyaya ekleyin. Kodda, şunu değiştirin:
   - Azure portal kopyaladığınız değerlerle `<server-name>` ve `<admin-username>`.
   - Sunucu parolanızla `<admin-password>`.
   - PostgreSQL için Azure veritabanı veritabanınızın adıyla `<database-name>`. Sunucunuzu oluştururken *Postgres* adlı varsayılan veritabanı otomatik olarak oluşturulmuştur. Bu veritabanını yeniden adlandırabilir veya SQL komutlarını kullanarak yeni bir veritabanı oluşturabilirsiniz. 
   
1. Dosyayı, *Postgres-insert.py*gibi bir *. Kopyala* uzantısıyla birlikte proje klasörünüze kaydedin. Windows için, dosyayı kaydettiğinizde UTF-8 kodlamasının seçildiğinden emin olun. 
   
1. Dosyayı çalıştırmak için, bir komut satırı arabirimindeki proje klasörünüze geçin ve dosya adının ardından `python`, örneğin `python postgres-insert.py`yazın.

## <a name="create-a-table-and-insert-data"></a>Tablo oluşturma ve veri ekleme
Aşağıdaki kod örneği, [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) Işlevini kullanarak PostgreSQL Için Azure veritabanı veritabanınıza bağlanır ve VERILERI bir SQL **Insert** ifadesiyle yükler. [Cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) işlevi SQL sorgusunu veritabanına karşı yürütür. 

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
Aşağıdaki kod örneği, PostgreSQL için Azure veritabanı veritabanınıza bağlanır ve [imleç kullanır.](http://initd.org/psycopg/docs/cursor.html#execute) verileri okumak Için SQL **Select** ifadesiyle yürütün. Bu işlev bir sorguyu kabul eder ve [Cursor. fetchAll ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)kullanılarak yinelemek için bir sonuç kümesi döndürür. 

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
Aşağıdaki kod örneği PostgreSQL için Azure veritabanı veritabanınıza bağlanır ve [Cursor. Execute](http://initd.org/psycopg/docs/cursor.html#execute) öğesini SQL **Update** ifadesiyle kullanarak verileri güncelleştirin. 

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
Aşağıdaki kod örneği PostgreSQL için Azure veritabanı veritabanınıza bağlanır ve [imleç kullanır.](http://initd.org/psycopg/docs/cursor.html#execute) daha önce eklediğiniz bir envanter öğesini sılmek Için SQL **Delete** ifadesiyle yürütün. 

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
> [Dışarı Aktarma ve İçeri Aktarma seçeneğini kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md)

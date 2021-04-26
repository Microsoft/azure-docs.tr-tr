---
title: 'Hızlı başlangıç: Python ile bağlanma-PostgreSQL için Azure veritabanı-tek sunucu'
description: Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'na bağlanmak ve bu verilere veri sorgulamak için kullanabileceğiniz Python kod örnekleri sağlanır-tek sunucu.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 6c589f3f0913609640ac14534c170c741c16a084
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048179"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Hızlı başlangıç: PostgreSQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Python kullanma-tek sunucu

Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'nda veritabanına nasıl bağlanacağınızı ve macOS, Ubuntu Linux veya Windows üzerinde Python kullanarak sorgulamak için SQL deyimlerini çalıştırmayı öğreneceksiniz.

> [!TIP]
> PostgreSQL ile bir Docgo uygulaması oluşturmak istiyorsanız öğreticiyi kullanıma alın ve [PostgreSQL öğreticisi ile bir docgo Web uygulaması dağıtın](../app-service/tutorial-python-postgresql-app.md) .


## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıç için şunlar gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free).
- [Azure Portal](./quickstart-create-server-database-portal.md) kullanarak PostgreSQL Için Azure veritabanı tek sunucu oluşturma <br/> ya da bir tane yoksa [Azure CLI](./quickstart-create-server-database-azure-cli.md) .
- Genel veya özel erişim kullanıyor olmanız temelinde, bağlantıyı etkinleştirmek için aşağıdaki eylemlerden **birini** doldurun.

  |Eylem| Bağlantı yöntemi|Nasıl yapılır kılavuzu|
  |:--------- |:--------- |:--------- |
  | **Güvenlik duvarı kurallarını yapılandırma** | Genel | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Hizmet uç noktasını yapılandır** | Genel | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Özel bağlantıyı Yapılandır** | Özel | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2,7 veya 3.6 +.

- En son [PIP](https://pip.pypa.io/en/stable/installing/) paketi yükleyicisi.
- [](https://pypi.python.org/pypi/psycopg2/) `pip install psycopg2` Bir Terminal veya komut istemi penceresinde psycopg2 ' ü kullanarak. Daha fazla bilgi için bkz. [nasıl yüklenir `psycopg2` ](http://initd.org/psycopg/docs/install.html).

## <a name="get-database-connection-information"></a>Veritabanı bağlantı bilgilerini al
PostgreSQL için Azure veritabanı veritabanına bağlanmak için tam sunucu adı ve oturum açma kimlik bilgileri gerekir. Bu bilgileri Azure portal alabilirsiniz.

1. [Azure Portal](https://portal.azure.com/), PostgreSQL Için Azure veritabanı sunucu adı ' nı arayıp seçin.
1. Sunucunun **genel bakış** sayfasında, tam **sunucu adı** ve **Yönetici Kullanıcı adı**' nı kopyalayın. Tam **sunucu adı** her zaman *\<my-server-name> . Postgres.Database.Azure.com* olur ve **Yönetici Kullanıcı adı** her zaman formundadır *\<my-admin-username>@\<my-server-name>* .

   Yönetici parolanızla de ihtiyacınız vardır. Unutursanız, bu sayfadan sıfırlayabilirsiniz.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="PostgreSQL için Azure Veritabanı sunucu adı":::

> [!IMPORTANT]
>  Aşağıdaki değerleri değiştirin:
>   - `<server-name>` ve `<admin-username>` Azure Portal kopyaladığınız değerlerle birlikte.
>   - `<admin-password>` Sunucu parolanızla birlikte.
>   - `<database-name>` sunucunuzu oluştururken *Postgres* adlı varsayılan veritabanı otomatik olarak oluşturulmuştur. Bu veritabanını yeniden adlandırabilir veya SQL komutlarını kullanarak [Yeni bir veritabanı oluşturabilirsiniz](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) .

## <a name="step-1-connect-and-insert-data"></a>1. Adım: verileri bağlama ve ekleme
Aşağıdaki kod örneği, kullanarak PostgreSQL için Azure veritabanı veritabanına bağlanır
-  [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) IşLEVI ve SQL **Insert** ifadesiyle veri yükler.
- [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) işlevi SQL sorgusunu veritabanına karşı yürütür.

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

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Komut satırı çıkışı":::


[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>2. Adım: verileri okuma
Aşağıdaki kod örneği, PostgreSQL için Azure veritabanı veritabanınıza ve kullanımlarına bağlanır
- Verileri okumak için SQL **Select** ifadesiyle [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) .
- [Cursor. fetchAll ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) bir sorgu kabul eder ve kullanarak yinelemek için bir sonuç kümesi döndürür

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>3. Adım: verileri güncelleştirme
Aşağıdaki kod örneği, verileri güncelleştirmek için SQL **Update** ifadesiyle [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) kullanır.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>5. Adım: verileri silme
Aşağıdaki kod örneği, daha önce eklediğiniz bir envanter öğesini silmek için SQL **Delete** ifadesiyle [cursor.exeşirin](http://initd.org/psycopg/docs/cursor.html#execute) ' i çalıştırır.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/postgres-doc-feedback)

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
> [CLı kullanarak MySQL için Azure veritabanı sunucusunu yönetme](./how-to-manage-server-cli.md)<br/>

[Aradığınızı bulamıyor musunuz? Bize bilgi verin.](https://aka.ms/postgres-doc-feedback)

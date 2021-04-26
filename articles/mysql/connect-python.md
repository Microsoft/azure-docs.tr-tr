---
title: 'Hızlı başlangıç: Python kullanarak bağlanma-MySQL için Azure veritabanı'
description: Bu hızlı başlangıçta, MySQL için Azure Veritabanı'na bağlanmak ve buradan veri sorgulamak için kullanabileceğiniz birkaç Python kod örneği sağlanmıştır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889034"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Hızlı başlangıç: MySQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için Python kullanma

Bu hızlı başlangıçta Python kullanarak MySQL için Azure veritabanı 'na bağlanırsınız. Ardından, Mac, Ubuntu Linux ve Windows platformlarındaki veritabanındaki verileri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini kullanın. 

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

## <a name="install-python-and-the-mysql-connector"></a>Python’u ve MySQL bağlayıcısını yükleme

Aşağıdaki adımları kullanarak bilgisayarınıza Python ve Python için MySQL bağlayıcısını yüklersiniz: 

> [!NOTE]
> Bu hızlı başlangıçta [MySQL Bağlayıcısı/Python Geliştirici Kılavuzu](https://dev.mysql.com/doc/connector-python/en/)kullanılıyor.

1. İşletim sistemi için [Python 3,7 veya üstünü](https://www.python.org/downloads/) indirin ve yükleyin. MySQL Bağlayıcısı için gerekli olduğundan Python 'u uygulamanıza eklediğinizden emin olun `PATH` .
   
2. Bir komut istemi veya `bash` kabuk açın ve `python -V` büyük harf V anahtarıyla çalıştırarak Python sürümünüzü denetleyin.
   
3. `pip`Paket yükleyicisi, Python 'un en son sürümlerine dahildir. ' İ `pip` çalıştırarak en son sürüme güncelleştirin `pip install -U pip` . 
   
   `pip`Yüklü değilse, ile indirip yükleyebilirsiniz `get-pip.py` . Daha fazla bilgi için bkz. [yükleme](https://pip.pypa.io/en/stable/installing/). 
   
4. `pip`Python Için MySQL bağlayıcısını ve bağımlılıklarını yüklemek için kullanın:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma

Azure portal MySQL için Azure veritabanı 'na bağlanmak üzere gereken bağlantı bilgilerini alın. Sunucu adı, veritabanı adı ve oturum açma kimlik bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
   
1. Portal arama çubuğunda, oluşturduğunuz MySQL sunucusu için Azure veritabanı 'nı ( **demosunucum** gibi) arayın ve seçin.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="MySQL için Azure Veritabanı sunucu adı":::
   
1. Sunucunun **genel bakış** sayfasında **sunucu adı** ve **Sunucu Yöneticisi oturum açma adı**' nı bir yere göz atın. Parolanızı unutursanız, bu sayfadan parolayı da sıfırlayabilirsiniz.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="MySQL için Azure veritabanı sunucu adı 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>1. Adım: tablo oluşturma ve veri ekleme

Sunucu ve veritabanına bağlanmak, tablo oluşturmak ve **Insert** SQL ifadesini kullanarak verileri yüklemek için aşağıdaki kodu kullanın. Kod MySQL. Connector kitaplığını içeri aktarır ve yöntemini kullanır:
- config koleksiyonundaki [bağımsız değişkenleri](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) kullanarak MySQL Için Azure veritabanı 'na bağlanmak üzere [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) işlevi. 
- [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemi MySQL veritabanında SQL sorgusunu yürütür. 
- imleç kullanarak işiniz bittiğinde [Cursor. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) .
- bağlantıyı bağlantıyı kapatmak için [conn. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) .

> [!IMPORTANT]
> - SSL varsayılan olarak etkindir. Yerel ortamınızdan bağlantı kurmak için [DIGICERTGLOBALROOTG2 SSL sertifikasını](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) indirmeniz gerekebilir.
> - ,, `<mydemoserver>` `<myadmin>` `<mypassword>` Ve `<mydatabase>` yer tutucuları MySQL sunucunuzun ve veritabanınızın değerleriyle değiştirin.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
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

[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>2. Adım: verileri okuma

Bağlanmak ve **SELECT** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın. Kod MySQL. Connector kitaplığını içeri aktarır ve [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemini kullanarak MySQL veritabanında SQL sorgusunu yürütür. 

Kod, [fetchAll ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) yöntemini kullanarak veri satırlarını okur, sonuç kümesini bir koleksiyon satırında tutar ve `for` satırlarda döngü yapmak için bir yineleyici kullanır.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>3. Adım: verileri güncelleştirme

Bağlanmak ve bir **UPDATE** SQL deyimi kullanarak verileri güncelleştirmek için aşağıdaki kodu kullanın. Kod MySQL. Connector kitaplığını içeri aktarır ve [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemini kullanarak MySQL veritabanında SQL sorgusunu yürütür. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>4. Adım: verileri silme

Bağlanmak ve **DELETE** SQL deyimini kullanarak verileri kaldırmak için aşağıdaki kodu kullanın. Kod MySQL. Connector kitaplığını içeri aktarır ve [cursor.exeCute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) yöntemini kullanarak MySQL veritabanında SQL sorgusunu yürütür. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
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

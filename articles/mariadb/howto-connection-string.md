---
title: Bağlantı dizeleri-MariaDB için Azure veritabanı
description: Bu belgede, ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python ve Ruby dahil olmak üzere MariaDB için Azure veritabanı 'na bağlanmak üzere uygulamalar için şu anda desteklenen bağlantı dizeleri listelenmektedir.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662186"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>MariaDB için Azure Veritabanı'na uygulama bağlama
Bu konu, MariaDB için Azure veritabanı tarafından şablonlar ve örneklerle birlikte desteklenen bağlantı dizesi türlerini listeler. Bağlantı dizeniz içinde farklı parametrelere ve ayarlara sahip olabilirsiniz.

- Sertifikayı almak için bkz. [SSL 'yi yapılandırma](./howto-configure-ssl.md).
- {your_host} = [servername]. MariaDB. Database. Azure. com
- {your_user} @ {ServerName} = kimlik doğrulaması için Kullanıcı kimliği biçimi doğru.  Yalnızca Kullanıcı kimliğini kullanıyorsanız kimlik doğrulaması başarısız olur.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Bu örnekte sunucu adı, `mydemoserver` veritabanı adı, `wpdb` Kullanıcı adı `WPAdmin` ve parola olur `mypassword!2` . Sonuç olarak, bağlantı dizesi şöyle olmalıdır:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Azure portal bağlantı dizesi ayrıntılarını alın
[Azure Portal](https://portal.azure.com), MariaDB sunucusu Için Azure veritabanı 'na gidin ve sonra, örneğinizin dize listesini almak için **bağlantı dizeleri** ' ne tıklayın: ![ Azure Portal bağlantı dizeleri bölmesi](./media/howto-connection-strings/connection-strings-on-portal.png)

Dize, sürücü, sunucu ve diğer veritabanı bağlantı parametreleri gibi ayrıntılar sağlar. Bu örnekleri, veritabanı adı, parola vb. gibi kendi parametrelerinizi kullanacak şekilde değiştirin. Daha sonra bu dizeyi, kodunuzun ve uygulamalarınızdan sunucuya bağlanmak için kullanabilirsiniz.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->

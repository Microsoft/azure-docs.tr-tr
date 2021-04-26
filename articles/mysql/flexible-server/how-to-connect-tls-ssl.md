---
title: MySQL için Azure veritabanı 'nda TLS/SSL kullanılarak şifrelenmiş bağlantı-esnek sunucu
description: MySQL için Azure veritabanı 'nda TLS/SSL kullanarak bağlanma ile ilgili yönergeler ve bilgiler-esnek sunucu.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6dbb1b46aef40986fc2d601aee152aed02591ac0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312612"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Şifrelenmiş bağlantılarla, MySQL için Azure veritabanı 'na bağlanma-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı esnek sunucu şu anda genel önizlemede

MySQL için Azure veritabanı esnek sunucu, Aktarım Katmanı Güvenliği (TLS) şifrelemesi ile Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarınızın MySQL sunucusuna bağlanmasını destekler. TLS, veritabanı sunucunuz ile istemci uygulamalarınız arasında şifrelenmiş ağ bağlantıları sağlayan ve uyumluluk gereksinimlerine bağlı olmanızı sağlayan bir endüstri standardı protokolüdür.

MySQL için Azure veritabanı esnek sunucu, varsayılan olarak Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantıları destekler ve TLS 1,0 ve TLS 1,1 tüm gelen bağlantıları varsayılan olarak reddedilir. Esnek sunucunuzdaki şifreli bağlantı zorlaması veya TLS sürüm yapılandırması, bu makalede anlatıldığı şekilde değiştirilebilir. 

Esnek sunucunuz için kullanabileceğiniz SSL ve TLS ayarlarının farklı yapılandırmalarının aşağıda verilmiştir:

| Senaryo   | Sunucu parametresi ayarları      | Açıklama                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL zorlamayı devre dışı bırak | require_secure_transport = kapalı |Eski uygulamanız MySQL sunucusuna şifreli bağlantıları desteklemiyorsa, require_secure_transport = kapalı ayarını yaparak esnek sunucunuza şifreli bağlantıların uygulanmasını devre dışı bırakabilirsiniz.|
|TLS sürümü < SSL 'yi zorunlu kıl 1,2 | require_secure_transport = ON ve tls_version = TLSV1 veya TLSV 1.1| Eski uygulamanız şifreli bağlantıları destekliyorsa, ancak TLS sürümü 1,2 < gerektiriyorsa, şifrelenmiş bağlantıları etkinleştirebilir ancak esnek sunucunuzu uygulamanızın desteklediği TLS sürümü (v 1.0 veya v 1.1) ile bağlantılara izin verecek şekilde yapılandırabilirsiniz|
|TLS sürümü = 1.2 ile SSL 'yi zorunlu kıl (varsayılan yapılandırma)|require_secure_transport = ON ve tls_version = TLSV 1.2| Bu, esnek sunucu için önerilen ve varsayılan yapılandırmadır.|
|TLS sürümü = 1.3 ile SSL 'yi zorunlu kıl (MySQL v 8.0 ve üstü ile desteklenir)| require_secure_transport = ON ve tls_version = TLSV 1.3| Bu, yeni uygulamaların geliştirilmesi için yararlıdır ve önerilir|

> [!Note]
> Esnek sunucuda SSL şifrede yapılan değişiklikler desteklenmez. FIPS şifre paketleri tls_version TLS sürüm 1,2 ' e ayarlandığında varsayılan olarak zorlanır. Sürüm 1,2 dışındaki TLS sürümleri için SSL şifresi, MySQL Community yüklemesiyle birlikte sunulan varsayılan ayarlara ayarlanır.

Bu makalede şunları yapmayı öğreneceksiniz:
* Esnek sunucunuzu yapılandırma 
  * SSL devre dışı 
  * SSL, TLS sürümü < 1,2 ile zorlanır
* MySQL komut satırı kullanarak esnek sunucunuza bağlanma 
  * Şifrelenmiş bağlantılarla devre dışı
  * Şifreli bağlantıları etkin
* Bağlantınız için şifreleme durumunu doğrulayın
* Çeşitli uygulama çerçeveleri kullanarak şifrelenmiş bağlantılarla esnek sunucunuza bağlanma

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>Esnek sunucunuzda SSL zorlamayı devre dışı bırakma
İstemci uygulamanız şifreli bağlantıları desteklemiyorsa, esnek sunucunuzda şifreli bağlantıları zorlamayı devre dışı bırakmanız gerekir. Şifrelenmiş bağlantıları zorlamayı devre dışı bırakmak için, ekran görüntüsünde gösterildiği gibi require_secure_transport Server parametresini kapalı olarak ayarlamanız ve sunucu parametre yapılandırmasını etkili olabilmesi için kaydetmeniz gerekir. require_secure_transport, hemen geçerli olan ve sunucu yeniden başlatmanın etkili olması gerekmeyen bir **dinamik sunucu parametresidir** .

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="MySQL için Azure veritabanı esnek sunucusu ile SSL 'nin devre dışı bırakılacağını gösteren ekran görüntüsü.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>SSL ile MySQL komut satırı istemcisini kullanarak bağlanma devre dışı

Aşağıdaki örnek, MySQL komut satırı arabirimi kullanılarak sunucunuza nasıl bağlanılacağını gösterir. `--ssl-mode=DISABLED`MySQL ISTEMCISINDEN TLS/SSL bağlantısını devre dışı bırakmak için bağlantı dizesi ayarını kullanın. Değerleri gerçek sunucu adı ve parolasıyla değiştirin. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
Require_secure_transport ayarının kapalı olarak ayarlanması, şifreli bağlantıların sunucu tarafında desteklenmediğini unutmayın. Require_secure_transport esnek sunucu üzerinde kapalı olarak ayarlarsanız ancak istemci şifreli bağlantıyla bağlanıyorsa, yine de kabul edilecektir. MySQL istemcisini kullanmak require_secure_transport = kapalı ile yapılandırılan esnek bir sunucuya aşağıdaki şekilde, aşağıda gösterildiği gibi da çalışır.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

Özet ' de require_secure_transport = kapalı ayarı esnek sunucu üzerinde şifrelenmiş bağlantıların uygulanmasını sağlar ve şifreli bağlantılara ek olarak istemciden sunucuya şifrelenmemiş bağlantılara izin verir.

## <a name="enforce-ssl-with-tls-version--12"></a>TLS sürümü < SSL 'yi zorunlu kıl 1,2

Uygulamanız SSL ile MySQL sunucusuna bağlantıları destekliyorsa, ancak TLS sürümünü destekler < 1,2, esnek sunucunuzda TLS sürümleri sunucu parametresini ayarlamanız gerekir. Esnek sunucunuzun desteklemesini istediğiniz TLS sürümlerini ayarlamak için, ekran görüntüsünde gösterildiği gibi tls_version Server parametresini TLSV1, TLSV 1.1 veya TLSV1 ve TLSV 1.1 olarak ayarlamanız gerekir ve sunucu parametre yapılandırmasını etkili olması için kaydedin. tls_version, parametrenin etkili olması için sunucu yeniden başlatması gereken bir **statik sunucu parametresidir** .

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="MySQL için Azure veritabanı esnek sunucusu için TLS sürümünün nasıl ayarlanacağını gösteren ekran görüntüsü.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>MySQL komut satırı istemcisini TLS/SSL ile bağlama

### <a name="download-the-public-ssl-certificate"></a>Ortak SSL sertifikasını indirin
İstemci uygulamalarınızla şifreli bağlantıları kullanmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi Azure portal ağ dikey penceresinde de bulunan [genel SSL sertifikasını](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) indirmeniz gerekir.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Azure portal 'den ortak SSL sertifikasının nasıl indirileceği gösteren ekran görüntüsü.":::

Sertifika dosyasını tercih ettiğiniz konuma kaydedin. Örneğin, bu öğretici `c:\ssl` `\var\www\html\bin` Yerel ortamınızda veya uygulamanızın barındırıldığı istemci ortamında kullanılır. Bu, uygulamaların SSL üzerinden veritabanına güvenli bir şekilde bağlanmasına imkan tanır.

Esnek sunucunuzu *özel erişim (VNET tümleştirmesi)* ile oluşturduysanız, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan VNet 'e ekleyebilirsiniz.

Esnek sunucunuzu *ortak erişim (izin VERILEN IP adresleri)* ile oluşturduysanız, sunucunuzdaki güvenlik duvarı kuralları lıstesıne yerel IP adresinizi ekleyebilirsiniz.

Yerel ortamınızdan sunucusuna bağlanmak için [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ya da [MySQL çalışma ekranı](./connect-workbench.md)--> seçebilirsiniz. 

Aşağıdaki örnek, MySQL komut satırı arabirimi kullanılarak sunucunuza nasıl bağlanılacağını gösterir. `--ssl-mode=REQUIRED`TLS/SSL sertifika doğrulamasını zorlamak için bağlantı dizesi ayarını kullanın. Yerel sertifika dosya yolunu `--ssl-ca` parametreye geçirin. Değerleri gerçek sunucu adı ve parolasıyla değiştirin. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Geçirilen değerin, `--ssl-ca` kaydettiğiniz sertifikanın dosya yolu ile eşleştiğinden emin olun.

Sunucunuza şifrelenmemiş bağlantılarla bağlanmaya çalışırsanız, güvenli olmayan aktarım kullanarak bağlantıların aşağıdakine benzer şekilde yasaklanmış olduğunu belirten bir hata görürsünüz:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>TLS/SSL bağlantısını doğrulama

TLS/SSL kullanarak MySQL sunucunuza bağlandığınızdan emin olmak için MySQL **Status** komutunu yürütün:

```dos
mysql> status
```
Çıktıyı inceleyerek bağlantının şifrelendiğini onaylayın; bu, şunu göstermelidir: * * SSL: kullanımdaki şifre * *. Bu şifre paketi bir örnek gösterir ve istemciye dayalı olarak farklı bir şifre paketi görebilirsiniz.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Çeşitli uygulama çerçeveleri kullanarak şifrelenmiş bağlantılarla esnek sunucunuza bağlanma

Azure portal sunucunuz için kullanılabilir olan "bağlantı dizeleri" sayfasında önceden tanımlanmış bağlantı dizeleri, TLS/SSL kullanarak veritabanı sunucunuza bağlanmak için gereken ortak diller için gerekli parametreleri içerir. TLS/SSL parametresi bağlayıcıya göre değişir. Örneğin, "useSSL = true", "sslmode = Required" veya "ssl_verify_cert = true" ve diğer Çeşitlemeler.

Uygulamanızdaki TLS/SSL üzerinden esnek sunucunuza şifreli bir bağlantı kurmak için aşağıdaki kod örneklerine bakın:

### <a name="wordpress"></a>WordPress
[SSL ortak sertifikasını](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) indirin ve satırdan sonra wp-config. php içine aşağıdaki satırları ekleyin ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (PDO kullanarak)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Docgo (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (Java için MySQL Bağlayıcısı)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (Java için MariaDB Bağlayıcısı)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL için Azure veritabanı esnek sunucusu 'na bağlanmak ve veri sorgulamak için MySQL çalışma ekranı kullanma](./connect-workbench.md)
- [MySQL kullanarak MySQL için Azure veritabanı esnek sunucusu 'na bağlanma ve veri sorgulama](./connect-php.md)
- [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-cli.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin
- [MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları](./concepts-networking.md#public-access-allowed-ip-addresses) hakkında daha fazla bilgi

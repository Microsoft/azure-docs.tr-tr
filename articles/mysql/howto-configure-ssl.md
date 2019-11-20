---
title: MySQL için Azure veritabanı 'na güvenli bir şekilde bağlanmak üzere SSL bağlantısını yapılandırma
description: MySQL için Azure veritabanı 'nı ve ilişkili uygulamaları doğru şekilde SSL bağlantıları kullanacak şekilde yapılandırma yönergeleri
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 07/02/2019
ms.openlocfilehash: 7a6c42da3fb92dc7dda014faa64fb8daa8c58774
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359453"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>MySQL için Azure veritabanı 'na güvenli bir şekilde bağlanmak üzere uygulamanızda SSL bağlantısı yapılandırma
MySQL için Azure veritabanı, MySQL Server için Azure veritabanı sunucunuzu Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuzla istemci uygulamalarınız arasında SSL bağlantılarının zorunlu tutulması, sunucuya uygulamanız arasındaki veri akışını şifreleyerek "bağlantıyı izinsiz izleme" saldırılarına karşı korumaya yardımcı olur.

## <a name="step-1-obtain-ssl-certificate"></a>1\. adım: SSL sertifikası al
MySQL için Azure veritabanı sunucusu [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) ile SSL üzerinden iletişim kurmak için gereken sertifikayı indirin ve sertifika dosyasını yerel sürücünüze kaydedin (Bu öğreticide c:\ssl kullanılır).
**Microsoft Internet Explorer ve Microsoft Edge için:** İndirme tamamlandıktan sonra, sertifikayı BaltimoreCyberTrustRoot. CRT. ped olarak yeniden adlandırın.

## <a name="step-2-bind-ssl"></a>2\. adım: SSL bağlama

Belirli programlama dili bağlantı dizeleri için lütfen aşağıdaki [örnek koda](howto-configure-ssl.md#sample-code) bakın.

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>SSL üzerinde MySQL çalışma ekranı kullanarak sunucuya bağlanma
MySQL çalışma ekranı 'nı SSL üzerinden güvenli bir şekilde bağlanacak şekilde yapılandırın. 

1. Yeni bağlantı kur iletişim kutusunda **SSL** sekmesine gidin. 

1. **SSL kullan** alanını "gerektir" olarak güncelleştirin.

1. **SSL CA dosyası:** alanına, **Baltimorecybertrustroot. CRT. ped**dosyasının konumunu girin. 
    
    ![SSL yapılandırmasını Kaydet](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Mevcut bağlantılar için, bağlantı simgesine sağ tıklayıp Düzenle ' yi seçerek SSL 'yi bağlayabilirsiniz. Ardından **SSL** sekmesine gidin ve sertifika dosyasını bağlayın.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>SSL üzerinde MySQL CLı kullanarak sunucuya bağlanma
SSL sertifikasını bağlamak için başka bir yöntem de aşağıdaki komutları yürüterek MySQL komut satırı arabirimini kullanmaktır. 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Windows üzerinde MySQL komut satırı arabirimini kullanırken bir hata `SSL connection error: Certificate signature check failed`alabilirsiniz. Bu gerçekleşirse, `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametreleri ile `--ssl`değiştirin.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>3\. adım:  Azure 'da SSL bağlantılarını zorunlu tutma 
### <a name="using-the-azure-portal"></a>Azure portalını kullanma
Azure portal kullanarak MySQL için Azure veritabanı sunucunuzu ziyaret edin ve **bağlantı güvenliği**' ne tıklayın. **SSL bağlantısını zorla** ayarını etkinleştirmek veya devre dışı bırakmak için iki durumlu düğmeyi kullanın ve ardından **Kaydet**' e tıklayın. Microsoft, gelişmiş güvenlik için her zaman **SSL bağlantısını zorla** ayarını etkinleştirmenizi önerir.
![Enable-SSL](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma
Azure CLı 'de sırasıyla etkin veya devre dışı değerler kullanarak **SSL zorlama** parametresini etkinleştirebilir veya devre dışı bırakabilirsiniz.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>4\. Adım: SSL bağlantısını doğrulama
SSL kullanarak MySQL sunucunuza bağlandığınızı doğrulamak için MySQL **Status** komutunu yürütün:
```dos
mysql> status
```
Çıktıyı inceleyerek bağlantının şifrelendiğini onaylayın ve şunları göstermelidir:  **SSL Kullanılan şifre AES256-SHA** 

## <a name="sample-code"></a>Örnek kod
Uygulamanızdan SSL üzerinden MySQL için Azure veritabanı 'na güvenli bir bağlantı kurmak için aşağıdaki kod örneklerine bakın:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (PDO kullanarak)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="django-pymysql"></a>Docgo (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
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
properties.setProperty("user", 'myadmin@mydemoserver');
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Sonraki adımlar
[MySQL Için Azure veritabanı bağlantı kitaplıklarını](concepts-connection-libraries.md) izleyen çeşitli uygulama bağlantı seçeneklerini gözden geçirin

---
title: SSL 'yi yapılandırma-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanı 'nı ve ilişkili uygulamaları doğru şekilde SSL bağlantıları kullanacak şekilde yapılandırma yönergeleri
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8f541e2302bc1f1de132de76e0638f9843d7003f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662279"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'na güvenli bir şekilde bağlanmak üzere uygulamanızda SSL bağlantısı yapılandırma
MariaDB için Azure veritabanı, MariaDB sunucusu için Azure veritabanınızı Güvenli Yuva Katmanı (SSL) kullanarak istemci uygulamalarına bağlamayı destekler. Veritabanı sunucunuzla istemci uygulamalarınız arasında SSL bağlantılarının zorunlu tutulması, sunucuya uygulamanız arasındaki veri akışını şifreleyerek "bağlantıyı izinsiz izleme" saldırılarına karşı korumaya yardımcı olur.

## <a name="obtain-ssl-certificate"></a>SSL sertifikası al


SSL üzerinden, MariaDB sunucusu için Azure veritabanı ile iletişim kurmak için gereken sertifikayı indirin [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) ve sertifika dosyasını yerel sürücünüze kaydedin (Bu öğretici örneğin c:\ssl kullanır).
**Microsoft Internet Explorer ve Microsoft Edge için:** İndirme tamamlandıktan sonra, sertifikayı BaltimoreCyberTrustRoot. CRT. ped olarak yeniden adlandırın.

>[!NOTE]
> Müşterilerin geri bildirimlerine bağlı olarak, var olan Baltidaha fazla kök CA 'sı için 15 Şubat 2021 (02/15/2021) tarihine kadar kök sertifikayı kullanımdan kaldırmayı genişlettik.

> [!IMPORTANT] 
> SSL kök sertifikası 15 Şubat 2021 tarihinden itibaren sona ermek üzere ayarlanmıştır (02/15/2021). Lütfen uygulamanızı [yeni sertifikayı](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)kullanacak şekilde güncelleştirin. Daha fazla bilgi için bkz. [planlı sertifika güncelleştirmeleri](concepts-certificate-rotation.md)

Bağımsız bulutlarda bulunan sunucular için sertifikalar için aşağıdaki bağlantılara bakın: [Azure Kamu](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Çin](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)ve [Azure Almanya](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="bind-ssl"></a>SSL bağlama

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>SSL üzerinde MySQL çalışma ekranı kullanarak sunucuya bağlanma
MySQL çalışma ekranı 'nı SSL üzerinden güvenli bir şekilde bağlanacak şekilde yapılandırın. 

1. Yeni bağlantı kur iletişim kutusunda **SSL** sekmesine gidin. 

1. **SSL kullan** alanını "gerektir" olarak güncelleştirin.

1. **SSL CA dosyası:** alanına, **Baltimorecybertrustroot. CRT. ped** dosyasının konumunu girin. 
    
    ![SSL yapılandırmasını Kaydet](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Mevcut bağlantılar için, bağlantı simgesine sağ tıklayıp Düzenle ' yi seçerek SSL 'yi bağlayabilirsiniz. Ardından **SSL** sekmesine gidin ve sertifika dosyasını bağlayın.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>SSL üzerinde MySQL CLı kullanarak sunucuya bağlanma
SSL sertifikasını bağlamak için başka bir yöntem de aşağıdaki komutları yürüterek MySQL komut satırı arabirimini kullanmaktır. 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Windows üzerinde MySQL komut satırı arabirimini kullanırken bir hata alabilirsiniz `SSL connection error: Certificate signature check failed` . Bu gerçekleşirse, `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametreleri ile değiştirin `--ssl` .

## <a name="enforcing-ssl-connections-in-azure"></a>Azure 'da SSL bağlantılarını zorunlu tutma 

### <a name="using-the-azure-portal"></a>Azure portalını kullanma
Azure portal kullanarak, MariaDB sunucusu için Azure veritabanı ' nı ziyaret edin ve **bağlantı güvenliği**' ne tıklayın. **SSL bağlantısını zorla** ayarını etkinleştirmek veya devre dışı bırakmak için iki durumlu düğmeyi kullanın ve ardından **Kaydet**' e tıklayın. Microsoft, gelişmiş güvenlik için her zaman **SSL bağlantısını zorla** ayarını etkinleştirmenizi önerir.
![MariaDB sunucusu için Enable-SSL](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma
Azure CLı 'de sırasıyla etkin veya devre dışı değerler kullanarak **SSL zorlama** parametresini etkinleştirebilir veya devre dışı bırakabilirsiniz.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>SSL bağlantısını doğrulama
SSL kullanarak MariaDB sunucunuza bağlandığınızı doğrulamak için MySQL **Status** komutunu yürütün:
```sql
status
```
Çıktıyı inceleyerek bağlantının şifrelendiğini onaylayın; bu, şunu göstermelidir:  **SSL: kullanımdaki ŞIFRE AES256-SHA** 

## <a name="sample-code"></a>Örnek kod
Uygulamanızdan SSL üzerinden MariaDB için Azure veritabanı 'na güvenli bir bağlantı kurmak için aşağıdaki kod örneklerine bakın:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Ruby on rayları
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
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

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->

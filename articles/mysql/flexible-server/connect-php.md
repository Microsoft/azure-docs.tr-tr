---
title: 'Hızlı başlangıç: PHP kullanarak bağlanma-esnek sunucu için Azure veritabanı'
description: Bu hızlı başlangıçta, MySQL için Azure veritabanı-esnek sunucusu 'na bağlanmak ve veri sorgulamak için kullanabileceğiniz birkaç PHP kod örneği sağlanmıştır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: baba4d373d4a79ab0c339aac00bb9ab48de9262b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109616"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Hızlı başlangıç: MySQL kullanarak MySQL için Azure veritabanı 'na bağlanma ve veri sorgulama-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu hızlı başlangıçta, bir [php](https://secure.php.net/manual/intro-whatis.php) uygulaması kullanarak MySQL Için Azure veritabanı esnek sunucusuna nasıl bağlanacağı gösterilmektedir. Ayrıca veritabanında veri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağınız da gösterilmiştir. Bu makalede, PHP kullanarak geliştirmeyle ilgili bilgi sahibi olduğunuz ve MySQL için Azure veritabanı esnek sunucusu ile çalışmaya yeni başladığınız varsayılır.

## <a name="prerequisites"></a>Önkoşullar
Bu hızlı başlangıçta, başlangıç noktası olarak şu kılavuzlardan birinde oluşturulan kaynaklar kullanılmaktadır:

- [Azure portal kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma](./quickstart-create-server-portal.md)
- [Azure CLı kullanarak MySQL için Azure veritabanı esnek sunucusu oluşturma](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>İstemci iş istasyonunuzu hazırlama
1. Esnek sunucunuzu *özel erişim (VNET tümleştirmesi)* ile oluşturduysanız, sunucunuza aynı VNET içindeki bir kaynaktan sunucunuza bağlanmanız gerekir. Bir sanal makine oluşturabilir ve bunu esnek sunucunuz ile oluşturulan VNet 'e ekleyebilirsiniz. [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağı oluşturma ve yönetme](./how-to-manage-virtual-network-cli.md)bölümüne bakın.

2. Esnek sunucunuzu *ortak erişim (izin VERILEN IP adresleri)* ile oluşturduysanız, sunucunuzdaki güvenlik duvarı kuralları lıstesıne yerel IP adresinizi ekleyebilirsiniz. [Azure CLI kullanarak MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları oluşturma ve yönetme](./how-to-manage-firewall-cli.md)bölümüne bakın.

### <a name="install-php"></a>PHP'yi yükleme

PHP'yi kendi sunucunuza yükleyin veya PHP içeren bir Azure [web uygulaması](../../app-service/overview.md) oluşturun.  Güvenlik duvarı kuralları oluşturmayı öğrenmek için [güvenlik duvarı kuralları oluşturma ve yönetme](./how-to-manage-firewall-portal.md) bölümüne bakın.

#### <a name="macos"></a>Mac OS

1. [PHP 7.1.4 sürümünü](https://secure.php.net/downloads.php) indirin.
2. PHP'yi yükleyin ve diğer yapılandırmalar için [PHP kılavuzuna](https://secure.php.net/manual/install.macosx.php) bakın.

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. [PHP 7.1.4 iş parçacığı güvenli olmayan (x64) sürümünü](https://secure.php.net/downloads.php) indirin.
2. PHP'yi yükleyin ve diğer yapılandırmalar için [PHP kılavuzuna](https://secure.php.net/manual/install.unix.php) bakın.

#### <a name="windows"></a>Windows

1. [PHP 7.1.4 iş parçacığı güvenli olmayan (x64) sürümünü](https://windows.php.net/download#php-7.1) indirin.
2. PHP'yi yükleyin ve diğer yapılandırmalar için [PHP kılavuzuna](https://secure.php.net/manual/install.windows.php) bakın.

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma

MySQL için Azure veritabanı esnek sunucusuna bağlanmak için gereken bağlantı bilgilerini alın. Tam sunucu adı ve oturum açma kimlik bilgileri gerekir.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Azure Portal sol taraftaki menüden **tüm kaynaklar**' ı seçin ve oluşturduğunuz sunucuyu (örneğin, **demosunucum**) arayın.
3. Sunucu adını seçin.
4. Sunucunun **Genel Bakış** panelinden **Sunucu adı** ile **Sunucu yöneticisi oturum açma adı**’nı not alın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>PHP 'de TLS/SSL kullanarak esnek sunucuya bağlanma

Uygulamanızdaki TLS/SSL üzerinden esnek sunucunuza şifreli bir bağlantı kurmak için aşağıdaki kod örneklerine bakın. TLS/SSL üzerinden iletişim kurmak için gereken sertifikayı şuradan indirebilirsiniz [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>Bağlanma ve tablo oluşturma

Bağlanmak ve **CREATE TABLE** SQL deyimini kullanarak tablo oluşturmak için aşağıdaki kodu kullanın.

Kod, PHP'de bulunan **MySQL Improved extension** (mysqli) sınıfını kullanır. Kod, MySQL'e bağlanmak için [mysqli_init](https://secure.php.net/manual/mysqli.init.php) ve [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) yöntemlerini çağırır. Ardından sorgu çalıştırmak için [mysqli_query](https://secure.php.net/manual/mysqli.query.php) yöntemini çağırır. Daha sonra bağlantıyı kapatmak için [mysqli_close](https://secure.php.net/manual/mysqli.close.php) yöntemini çağırır.

host, username, password ve db_name parametre değerlerini kendi değerlerinizle değiştirin.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Veri ekleme

Bağlanmak ve **INSERT** SQL deyimi kullanarak veri eklemek için aşağıdaki kodu kullanın.

Kod, PHP'de bulunan **MySQL Improved extension** (mysqli) sınıfını kullanır. Kod, [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) yöntemini kullanarak hazır bir INSERT deyimi oluşturur, ardından [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) yöntemini kullanarak, eklenen her bir sütun değerine ilişkin parametreleri bağlar. Kod, [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) yöntemini kullanarak deyimi çalıştırır ve ardından [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) yöntemini kullanarak deyimi kapatır.

host, username, password ve db_name parametre değerlerini kendi değerlerinizle değiştirin.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Verileri okuma

Bağlanmak ve **SELECT** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın.  Kod, PHP'de bulunan **MySQL Improved extension** (mysqli) sınıfını kullanır. Kod, [mysqli_query](https://secure.php.net/manual/mysqli.query.php) yöntemini kullanarak SQL sorgusunu gerçekleştirir ve [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) yöntemini kullanarak elde edilen satırları getirir.

host, username, password ve db_name parametre değerlerini kendi değerlerinizle değiştirin.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Verileri güncelleştirme

Bağlanmak ve bir **UPDATE** SQL deyimi kullanarak verileri güncelleştirmek için aşağıdaki kodu kullanın.

Kod, PHP'de bulunan **MySQL Improved extension** (mysqli) sınıfını kullanır. Kod, [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) yöntemini kullanarak hazır bir UPDATE deyimi oluşturur, ardından [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) yöntemini kullanarak, güncelleştirilen her bir sütun değerine ilişkin parametreleri bağlar. Kod, [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) yöntemini kullanarak deyimi çalıştırır ve ardından [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) yöntemini kullanarak deyimi kapatır.

host, username, password ve db_name parametre değerlerini kendi değerlerinizle değiştirin.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Verileri silme
Bağlanmak ve **DELETE** SQL deyimi kullanarak verileri okumak için aşağıdaki kodu kullanın.

Kod, PHP'de bulunan **MySQL Improved extension** (mysqli) sınıfını kullanır. Kod, [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) yöntemini kullanarak hazır bir DELETE deyimi oluşturur, ardından [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) yöntemini kullanarak deyimdeki WHERE yan tümcesine ilişkin parametreleri bağlar. Kod, [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) yöntemini kullanarak deyimi çalıştırır ve ardından [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) yöntemini kullanarak deyimi kapatır.

host, username, password ve db_name parametre değerlerini kendi değerlerinizle değiştirin.

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>Sonraki adımlar
- [MySQL Için Azure veritabanı-esnek sunucu 'Da Aktarım Katmanı Güvenliği (TLS 1,2) kullanılarak şifrelenmiş bağlantı](./how-to-connect-tls-ssl.md).
- [MySQL Için Azure veritabanı esnek sunucusu 'Nda ağ](./concepts-networking.md)hakkında daha fazla bilgi edinin.
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu güvenlik duvarı kuralları oluşturun ve yönetin](./how-to-manage-firewall-portal.md).
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-portal.md).
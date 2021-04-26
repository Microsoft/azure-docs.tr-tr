---
title: 'Hızlı başlangıç: PHP kullanarak bağlanma-MySQL için Azure veritabanı'
description: Bu hızlı başlangıçta, MySQL için Azure Veritabanı'na bağlanmak ve buradan veri sorgulamak için kullanabileceğiniz birkaç PHP kod örneği sağlanmıştır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bf62eca26d846cc529df43b519b1377f88c8aede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132911"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Hızlı başlangıç: MySQL için Azure veritabanı 'na bağlanmak ve veri sorgulamak için PHP kullanma
Bu hızlı başlangıçta [PHP](https://secure.php.net/manual/intro-whatis.php) uygulaması kullanarak MySQL için Azure Veritabanı'na nasıl bağlanacağınız gösterilmiştir. Ayrıca veritabanında veri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerini nasıl kullanacağınız da gösterilmiştir.

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

- [Veritabanı ve yönetici olmayan kullanıcı oluşturma](./howto-create-users.md?tabs=single-server)
- İşletim sisteminiz için en son PHP sürümünü yükler
    - [MacOS üzerinde PHP](https://secure.php.net/manual/install.macosx.php)
    - [Linux üzerinde PHP](https://secure.php.net/manual/install.unix.php)
    - [Windows üzerinde PHP](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> Bu hızlı başlangıçta, Connect 'i yönetmek ve sunucuyu sorgulamak için [mysqli](https://www.php.net/manual/en/book.mysqli.php) Library kullanıyoruz.

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma
Aşağıdaki adımları izleyerek Azure portal veritabanı sunucusu bağlantı bilgilerini alabilirsiniz:

1. [Azure Portal](https://portal.azure.com/)oturum açın.
2. MySQL için Azure veritabanları sayfasına gidin. **MySQL Için Azure veritabanı**'nı arayabilir ve seçebilirsiniz.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="MySQL için Azure veritabanı 'nı bulun":::

2. MySQL sunucunuzu (örneğin, **demosunucum**) seçin.
3. **Genel bakış** sayfasında sunucu **adı** ' nın yanında tam sunucu adını ve **Sunucu Yöneticisi oturum açma adı**' nın yanındaki yönetici kullanıcı adını kopyalayın. Sunucu adını veya ana bilgisayar adını kopyalamak için üzerine gelin ve **Kopyala** simgesini seçin.

> [!IMPORTANT]
> - Parolanızı unuttuysanız [parolayı sıfırlayabilirsiniz](./howto-create-manage-server-portal.md#update-admin-password).
> - **Ana bilgisayar, Kullanıcı adı, parola** ve **db_name** parametrelerini kendi değerlerinizle değiştirin * *

## <a name="step-1-connect-to-the-server"></a>1. Adım: sunucuya bağlanma
SSL varsayılan olarak etkindir. Yerel ortamınızdan bağlantı kurmak için [DIGICERTGLOBALROOTG2 SSL sertifikasını](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) indirmeniz gerekebilir. Bu kod şunu çağırır:
- MySQLi 'u başlatmak için [mysqli_init](https://secure.php.net/manual/mysqli.init.php) .
- SSL sertifika yolunu işaret [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) . Bu, yerel ortamınız için gereklidir, ancak App Service Web uygulaması veya Azure sanal makineleri için gerekli değildir.
- MySQL 'e bağlanmak için [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) .
- bağlantıyı kapatmak için [mysqli_close](https://secure.php.net/manual/mysqli.close.php) .


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Sorun mu yaşıyorsunuz? Bize bilgi verin](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>2. Adım: tablo oluşturma
Bağlanmak için aşağıdaki kodu kullanın. Bu kod şunu çağırır:
- sorguyu çalıştırmak için [mysqli_query](https://secure.php.net/manual/mysqli.query.php) .
```php
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
```

## <a name="step-3-insert-data"></a>3. Adım: veri ekleme
**Insert** SQL ifadesini kullanarak veri eklemek için aşağıdaki kodu kullanın. Bu kod yöntemleri kullanır:
- hazırlanmış INSERT deyimleri oluşturmak için [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)
- Her yerleştirilen sütun değeri için parametreleri bağlamak [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) .
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- yöntemi kullanarak deyimin kapatılmasını [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>4. Adım: verileri okuma
Bir **Select** SQL ifadesini kullanarak verileri okumak için aşağıdaki kodu kullanın.  Kod yöntemini kullanır:
- **Select** sorgusunu yürütmek [mysqli_query](https://secure.php.net/manual/mysqli.query.php)
- elde edilen satırları getirmek için [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) .

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>5. Adım: verileri silme
**Delete** SQL ifadesini kullanarak aşağıdaki kod satırlarını silin. Kod yöntemleri kullanır:
- hazırlanmış bir DELETE ifadesini oluşturmak için [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)
- parametreleri [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) bağlar
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) hazırlanan DELETE ifadesini yürütür
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) , ifadeyi kapatır

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
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

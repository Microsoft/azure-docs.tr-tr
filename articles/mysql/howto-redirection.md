---
title: Yeniden yönlendirme ile bağlanma-MySQL için Azure veritabanı
description: Bu makalede, uygulamanızı yeniden yönlendirme ile MySQL için Azure veritabanı 'na bağlanmak üzere nasıl yapılandırabileceğiniz açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 199810f835924ccbb51948169244622a19c5e8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201442"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Yeniden yönlendirme ile MySQL için Azure Veritabanı'na bağlanma

Bu konuda, MySQL için Azure veritabanı sunucusuna yeniden yönlendirme moduyla bir uygulamanın nasıl bağlanacağı açıklanmaktadır. Uygulamaların doğrudan arka uç sunucu düğümlerine bağlanmasına izin vererek istemci uygulamaları ve MySQL sunucuları arasındaki ağ gecikmesini azaltmak için amaçlar 'yi yeniden yönlendirme.

## <a name="before-you-begin"></a>Başlamadan önce
[Azure portalında](https://portal.azure.com) oturum açın. Altyapı sürümü 5,6, 5,7 veya 8,0 olan MySQL için Azure veritabanı sunucusu oluşturun. 

Ayrıntılar için [Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) veya [Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)kullanarak MySQL için Azure veritabanı sunucusu oluşturma bölümüne bakın.

> [!IMPORTANT]
> Yönlendirme şu anda [MySQL Için Azure veritabanı özel bağlantısı](concepts-data-access-security-private-link.md)ile desteklenmiyor.

## <a name="enable-redirection"></a>Yeniden yönlendirmeyi etkinleştir

MySQL için Azure veritabanı sunucusunda, `redirect_enabled` parametresini `ON` yeniden yönlendirme moduyla bağlantılara izin verecek şekilde yapılandırın. Bu sunucu parametresini güncelleştirmek için [Azure Portal](howto-server-parameters.md) veya [Azure CLI](howto-configure-server-parameters-using-cli.md)kullanın.

## <a name="php"></a>PHP

PHP uygulamalarında yeniden yönlendirme desteği, Microsoft tarafından geliştirilen [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) uzantısı aracılığıyla kullanılabilir. 

Mysqlnd_azure uzantısı,,,,,,,,,,, bir,,, bir, ve resmi olarak yayımlanmış bir [paket](https://pecl.php.net/package/mysqlnd_azure)aracılığıyla, uzantıyı yüklemek ve yapılandırmak önemle önerilir.

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) uzantısında yeniden yönlendirme desteği şu anda önizleme aşamasındadır.

### <a name="redirection-logic"></a>Yeniden yönlendirme mantığı

>[!IMPORTANT]
> Yeniden yönlendirme mantığı/davranışı 1.1.0 sürümü güncelleştirildi ve **1.1.0 + sürümünün kullanılması önerilir**.

Yeniden yönlendirme davranışı değerine göre belirlenir `mysqlnd_azure.enableRedirect` . Aşağıdaki tabloda, **1.1.0 + sürümündeki** bu parametrenin değerine göre yeniden yönlendirme davranışı özetlenmektedir.

Mysqlnd_azure uzantısının (sürüm 1.0.0-1.0.3) eski bir sürümünü kullanıyorsanız, yeniden yönlendirme davranışı değerine göre belirlenir `mysqlnd_azure.enabled` . Geçerli değerler şunlardır `off` (aşağıdaki tabloda özetlenen davranış olarak benzer şekilde davranır) ve `on` (aşağıdaki tabloda olduğu gibi davranır `preferred` ).  

|**mysqlnd_azure. enableRedirect değeri**| **Davranış**|
|----------------------------------------|-------------|
|`off` veya `0`|Yeniden yönlendirme kullanılmayacak. |
|`on` veya `1`|-Bağlantı, sürücü tarafında SSL kullanmıyorsa bağlantı yapılmaz. Şu hata döndürülecek: *"mysqlnd_azure. enableRedirect açık, ancak SSL seçeneği bağlantı dizesinde ayarlanmadı. Yeniden yönlendirme yalnızca SSL ile yapılabilir. "*<br>-Sürücü tarafında SSL kullanılıyorsa, ancak sunucuda yeniden yönlendirme desteklenmiyorsa, ilk bağlantı durdurulur ve şu hata döndürülür: *"yönlendirme, MySQL sunucusunda etkin olmadığından veya ağ paketi yeniden yönlendirme protokolü ile uyumlu olmadığından, bağlantı iptal edildi."*<br>-MySQL sunucusu yeniden yönlendirmeyi destekliyorsa, ancak yeniden yönlendirilen bağlantı herhangi bir nedenden dolayı başarısız olursa, ilk ara sunucu bağlantısını da iptal edin. Yeniden yönlendirilen bağlantının hatasını döndürür.|
|`preferred` veya `2`<br> (varsayılan değer)|-mysqlnd_azure mümkünse yeniden yönlendirmeyi kullanacaktır.<br>-Bağlantı sürücü tarafında SSL kullanmıyorsa, sunucu yeniden yönlendirmeyi desteklemez ya da ara sunucu bağlantısı hala geçerli olduğu sürece yeniden yönlendirilen bağlantı önemli olmayan bir nedenle bağlanamaz, ilk proxy bağlantısına geri dönecektir.|

Belgenin sonraki bölümlerinde, `mysqlnd_azure` Bu parametrenin değerini nasıl yükleyeceğiniz ve bu parametrenin değeri nasıl ayarlanacağı ana hatlarıyla gösterilir.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Önkoşullar 
- PHP sürümleri 7.2.15 + ve 7.3.2 +
- PHP PEAR 
- PHP-MySQL
- MySQL için Azure veritabanı sunucusu

1. [Mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) [,](https://pecl.php.net/package/mysqlnd_azure) 1.1.0 + sürümünün kullanılması önerilir.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Aşağıdaki çalıştırarak uzantı dizinini ( `extension_dir` ) bulun:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Dizini döndürülen klasöre değiştirin ve bu klasörde bulunduğundan emin olun `mysqlnd_azure.so` . 

4. Aşağıdaki çalıştırarak. ini dosyaları klasörünü bulun: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Dizinleri bu döndürülen klasör olarak değiştirin. 

6. İçin yeni bir. ini dosyası oluşturun `mysqlnd_azure` . Modüller, INI dosyalarının ad sırasına göre yüklendiğinden, bu adın alfabetik sırasının mysqnld ' den sonra olduğundan emin olun. Örneğin, `mysqlnd` . ini adlandırılmışsa `10-mysqlnd.ini` , mysqlnd ini olarak adlandırın `20-mysqlnd-azure.ini` .

7. Yeni. ini dosyası içinde yeniden yönlendirmeyi etkinleştirmek için aşağıdaki satırları ekleyin.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Önkoşullar 
- PHP sürümleri 7.2.15 + ve 7.3.2 +
- PHP-MySQL
- MySQL için Azure veritabanı sunucusu

1. Aşağıdaki komutu çalıştırarak PHP 'nin x64 veya x86 sürümünü çalıştırıp çalıştırmadığını belirleyebilirsiniz:

    ```cmd
    php -i | findstr "Thread"
    ```

2. PHP sürümünüz ile eşleşen bir [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) [DLL 'nin karşılık](https://pecl.php.net/package/mysqlnd_azure) gelen x64 veya x86 sürümünü indirin. 1.1.0 + sürümünün kullanılması önerilir.

3. ZIP dosyasını ayıklayın ve adlı DLL dosyasını bulun `php_mysqlnd_azure.dll` .

4. `extension_dir`Aşağıdaki komutu çalıştırarak uzantı dizinini () bulun:

    ```cmd
    php -i | find "extension_dir"
    ```

5. `php_mysqlnd_azure.dll`Dosyayı 4. adımda döndürülen dizine kopyalayın. 

6. `php.ini`Aşağıdaki komutu kullanarak dosyayı IÇEREN php klasörünü bulun:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Dosyayı değiştirin `php.ini` ve yeniden yönlendirmeyi etkinleştirmek için aşağıdaki ek satırları ekleyin. 

    Dinamik uzantılar bölümünde: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Modül ayarları bölümünde:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Yeniden yönlendirmeyi Onayla

Yeniden yönlendirmeyi de aşağıdaki örnek PHP kodu ile yapılandırıldığını doğrulayabilirsiniz. Adlı bir PHP dosyası oluşturun `mysqlConnect.php` ve aşağıdaki kodu yapıştırın. Sunucu adını, Kullanıcı adını ve parolayı kendi kendinize güncelleştirin. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Sonraki adımlar
Bağlantı dizeleri hakkında daha fazla bilgi için bkz. [bağlantı dizeleri](howto-connection-string.md).

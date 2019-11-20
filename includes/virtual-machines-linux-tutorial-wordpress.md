---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 76a080d40721fa78ad703f77f6dbe7a3363ab77e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857516"
---
## <a name="install-wordpress"></a>WordPress yükleme

Yığınınızı denemek istiyorsanız örnek bir uygulama yükleyin. Örneğin aşağıdaki adımları izleyerek açık kaynak web sitesi ve blog oluşturma platformu olan [WordPress](https://wordpress.org/)'i yükleyebilirsiniz. [Drupal](http://www.drupal.org) ve [Moodle](https://moodle.org/) iş yüklerini de deneyebilirsiniz. 

Bu WordPress kurulumu yalnızca kavram kanıtı amaçlıdır. En güncel WordPress sürümünü önerilen güvenlik ayarlarıyla üretim ortamına yüklemek için bkz. [WordPress belgeleri](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>WordPress paketi yükleme

Şu komutu çalıştırın:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>WordPress’i yapılandırma

WordPress'i MySQL ve PHP kullanacak şekilde yapılandırın.

Çalışma dizininde WordPress için MySQL veritabanını yapılandırmak için kullanacağınız `wordpress.sql` adlı bir metin dosyası oluşturun: 

```bash
sudo sensible-editor wordpress.sql
```

Aşağıdaki komutları ekleyin ve *yourPassword* yerine istediğiniz veritabanı parolasını yazın (diğer değerleri değiştirmeyin). Daha önceden parola gücünü doğrulama amacıyla bir MySQL güvenlik ilkesi oluşturduysanız parolanın gereksinimlere uygun olduğundan emin olun. Dosyayı kaydedin.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
```

Veritabanını oluşturmak için aşağıdaki komutu çalıştırın:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

`wordpress.sql` dosyasında veritabanı kimlik bilgileri bulunduğu için kullandıktan sonra silin:

```bash
sudo rm wordpress.sql
```

PHP'yi yapılandırmak için aşağıdaki komutu çalıştırarak istediğiniz bir metin düzenleyiciyi açın ve `/etc/wordpress/config-localhost.php` dosyasını oluşturun:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Aşağıdaki satırları dosyaya kopyalayın ve *yourPassword* yerine WordPress veritabanı parolanızı yazın (diğer değerleri değiştirmeyin). Ardından dosyayı kaydedin.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


WordPress yüklemesini web sunucusunun belge kök dizinine taşıyın:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Şimdi WordPress kurulumunu tamamlayabilir ve platformda içerik yayımlayabilirsiniz. Bir tarayıcı açın ve `http://yourPublicIPAddress/wordpress` adresine gidin. Sanal makinenizin genel IP adresini değiştirin. Şu görüntüye benzemelidir.

![WordPress yükleme sayfası](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)

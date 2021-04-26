---
title: PHP uygulamalarını yapılandırma
description: Yerel Windows örneklerinde veya önceden oluşturulmuş bir PHP kapsayıcısında bir PHP uygulamasını Azure App Service ' de nasıl yapılandıracağınızı öğrenin. Bu makalede en yaygın yapılandırma görevlerine yer verilmiştir.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 94cbe0fa6669546cee8e989a6db2fcbb428cb9d0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829449"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Azure App Service için bir PHP uygulaması yapılandırma

Bu kılavuzda, Azure App Service ' de PHP web uygulamalarınızı, mobil arka uçlarınızın ve API uygulamalarınızın nasıl yapılandırılacağı gösterilmektedir.

Bu kılavuzda, App Service için uygulama dağıtan PHP geliştiricilerine yönelik temel kavramlar ve yönergeler sağlanmaktadır. Azure App Service hiç kullanmadıysanız, önce [MySQL öğreticisiyle](tutorial-php-mysql-app.md) [php hızlı başlangıç](quickstart-php.md) ve php 'yi izleyin.

## <a name="show-php-version"></a>PHP sürümünü göster

::: zone pivot="platform-windows"  

Geçerli PHP sürümünü göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

> [!NOTE]
> Bir geliştirme yuvasına yönelik olarak, `--slot` ardından yuvanın adı gelen parametresini ekleyin.

Tüm desteklenen PHP sürümlerini göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Geçerli PHP sürümünü göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

> [!NOTE]
> Bir geliştirme yuvasına yönelik olarak, `--slot` ardından yuvanın adı gelen parametresini ekleyin.

Tüm desteklenen PHP sürümlerini göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>PHP sürümünü ayarla

::: zone pivot="platform-windows"  

PHP sürümünü 7,4 olarak ayarlamak için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

PHP sürümünü 7,2 olarak ayarlamak için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Besteci Çalıştır

Dağıtım sırasında [besteci](https://getcomposer.org/) App Service çalıştırmak istiyorsanız, en kolay yol, besteci deponuzu eklemek olur.

Yerel bir Terminal penceresinde, Dizin ' i depo kökinizle değiştirin ve besteci [yükleme Oluşturucu](https://getcomposer.org/download/) ' daki yönergeleri izleyerek *Oluşturucu. phar* ' yi Dizin köküne indirin.

Aşağıdaki komutları çalıştırın ( [NPM](https://www.npmjs.com/get-npm) yüklü olmalıdır):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Depo kökünde Şu anda iki ek dosya vardır: *. Deployment* ve *Deploy.sh*.

*Deploy.sh* açın ve aşağıdaki `Deployment` gibi görünen bölümü bulun:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Bölüm *sonunda* gerekli aracı çalıştırmak için gereken kod bölümünü ekleyin `Deployment` :

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Tüm değişikliklerinizi işleyin ve git kullanarak kodunuzu dağıtın veya derleme Otomasyonu etkin olan ZIP dağıtımını yapın. Besteci Dağıtım Otomasyonu 'nun bir parçası olarak çalışıyor olmalıdır.

## <a name="run-gruntbowergulp"></a>Grsıt/Bower/Gulp çalıştırma

App Service, Grsıt, Bower veya Gulp gibi popüler Otomasyon araçlarını dağıtım zamanında çalıştırmak istiyorsanız, [özel bir dağıtım betiği](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)sağlamanız gerekir. App Service, git ile dağıtırken veya derleme Otomasyonu etkinken [ZIP dağıtımıyla](deploy-zip.md) , bu betiği çalıştırır. 

Bu araçları çalıştırmak üzere deponuzu etkinleştirmek için, bunlarıpackage.jsiçindeki bağımlılıklara eklemeniz gerekir *.* Örnek:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Yerel bir terminal penceresinden dizini depo kökinizle değiştirin ve aşağıdaki komutları çalıştırın ( [NPM](https://www.npmjs.com/get-npm) yüklü olmalıdır):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Depo kökünde Şu anda iki ek dosya vardır: *. Deployment* ve *Deploy.sh*.

*Deploy.sh* açın ve aşağıdaki `Deployment` gibi görünen bölümü bulun:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Bu bölüm çalışmayı sonlandırır `npm install --production` . Bölüm *sonunda* gerekli aracı çalıştırmak için gereken kod bölümünü ekleyin `Deployment` :

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

[MEAN.js örneğinde](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), dağıtım komut dosyasının da özel bir komut çalıştırdığı bir örnek görürsünüz `npm install` .

### <a name="bower"></a>Bower

Bu kod parçacığı çalışır `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Bu kod parçacığı çalışır `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Bu kod parçacığı çalışır `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Derleme Otomasyonu 'nu özelleştirme

Uygulamanızı, derleme Otomasyonu açıkken git veya ZIP paketleri kullanarak dağıtırsanız, App Service aşağıdaki sırayla Otomasyon adımları oluşturun:

1. Tarafından belirtilmişse özel betiği çalıştırın `PRE_BUILD_SCRIPT_PATH` .
1. `php composer.phar install` öğesini çalıştırın.
1. Tarafından belirtilmişse özel betiği çalıştırın `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` ve `POST_BUILD_COMMAND` Varsayılan olarak boş olan ortam değişkenleridir. Oluşturma öncesi komutları çalıştırmak için, tanımlayın `PRE_BUILD_COMMAND` . Oluşturma sonrası komutları çalıştırmak için, tanımlayın `POST_BUILD_COMMAND` .

Aşağıdaki örnek, virgülle ayrılmış bir dizi komuta iki değişkeni belirtir.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Derleme Otomasyonu 'nu özelleştirmek için ek ortam değişkenleri için bkz. [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

App Service nasıl çalıştığı ve Linux 'ta PHP uygulamaları oluşturma hakkında daha fazla bilgi için bkz. [Oryx belgeleri: PHP uygulamaları nasıl algılanır ve oluşturulur](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Başlatmayı özelleştirme

Varsayılan olarak, yerleşik PHP kapsayıcısı Apache sunucusunu çalıştırır. Başlangıçta çalışır `apache2ctl -D FOREGROUND"` . İsterseniz, aşağıdaki komutu [Cloud Shell](https://shell.azure.com)çalıştırarak başlangıç sırasında farklı bir komut çalıştırabilirsiniz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a> Ortam değişkenlerine erişme

App Service'te uygulama kodunuzun dışında [uygulama ayarlarını düzenleyebilirsiniz](configure-common.md#configure-app-settings). Ardından, standart [getenv ()](https://secure.php.net/manual/function.getenv.php) modelini kullanarak bunlara erişebilirsiniz. Örneğin `DB_HOST` adlı bir uygulama ayarına erişmek için şu kodu kullanabilirsiniz:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Site kökünü Değiştir

::: zone pivot="platform-windows"  

Seçtiğiniz Web çerçevesi, site kökü olarak bir alt dizin kullanabilir. Örneğin, [Laralevel](https://laravel.com/), site kökü olarak *ortak/* alt dizini kullanır.

Site kökünü özelleştirmek için, komutunu kullanarak uygulamanın sanal uygulama yolunu ayarlayın [`az resource update`](/cli/azure/resource#az_resource_update) . Aşağıdaki örnek, site kökünü deponuzdaki *ortak/* alt dizine ayarlar. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Varsayılan olarak, Azure App Service kök sanal uygulama yolunu ( _/_ ) dağıtılan uygulama dosyalarının kök dizinine (_sites\wwwroot_) yönlendirir.

::: zone-end

::: zone pivot="platform-linux"

Seçtiğiniz Web çerçevesi, site kökü olarak bir alt dizin kullanabilir. Örneğin, [Laralevel](https://laravel.com/), `public/` alt dizini site kökü olarak kullanır.

App Service için varsayılan PHP görüntüsü Apache kullanır ve uygulamanız için site kökünü özelleştirmenize izin vermez. Bu sınırlamaya geçici bir çözüm bulmak için, depo köküne aşağıdaki içerikle bir *. htaccess* dosyası ekleyin:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

*.htaccess* yeniden yazma işlemini kullanmazsanız, Laravel uygulamanızı bunun yerine bir [özel Docker görüntüsü](quickstart-custom-container.md) ile dağıtabilirsiniz.

::: zone-end

## <a name="detect-https-session"></a>HTTPS oturumunu Algıla

App Service, [SSL sonlandırması](https://wikipedia.org/wiki/TLS_termination_proxy) ağ yükü dengeleyicilerde gerçekleşinceye kadar, tüm https istekleri UYGULAMANıZA şifrelenmemiş HTTP istekleri olarak ulaşacak. Uygulama mantığınızın kullanıcı isteklerinin şifrelenip şifrelenmediğini denetlemesi gerekiyorsa, `X-Forwarded-Proto` üstbilgiyi inceleyin.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

Popüler Web çerçeveleri `X-Forwarded-*` Standart uygulama hiyerarşinizdeki bilgilere erişmenizi sağlar. [Codeigniter](https://codeigniter.com/)'da, [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) `X_FORWARDED_PROTO` Varsayılan olarak değerini denetler.

## <a name="customize-phpini-settings"></a>php.ini ayarlarını özelleştirme

PHP yüklemenizde değişiklikler yapmanız gerekiyorsa, bu adımları izleyerek [php.ini yönergelerinden](https://www.php.net/manual/ini.list.php) herhangi birini değiştirebilirsiniz.

> [!NOTE]
> PHP sürümünü ve geçerli *php.ini* yapılandırmasını görmenin en iyi yolu, uygulamanızda [PHPINFO ()](https://php.net/manual/function.phpinfo.php) öğesini çağırmanız.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Özelleştirme-PHP_INI_SYSTEM olmayan yönergeler

::: zone pivot="platform-windows"  

PHP_INI_USER, PHP_INI_PERDIR ve PHP_INI_ALL yönergelerini özelleştirmek için (bkz. [php.ini yönergeleri](https://www.php.net/manual/ini.list.php)), `.user.ini` uygulamanızın kök dizinine bir dosya ekleyin.

Dosya için `.user.ini` kullandığınız söz dizimini kullanarak yapılandırma ayarlarını dosyaya ekleyin `php.ini` . Örneğin, `display_errors` ayarı etkinleştirmek ve `upload_max_filesize` ayarı 10m olarak ayarlamak isterseniz `.user.ini` dosyanız şu metni içerir:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Değişiklikleri ile uygulamanızı yeniden dağıtın ve yeniden başlatın.

Bir dosya kullanımına alternatif olarak `.user.ini` , bu PHP_INI_SYSTEM olmayan yönergeleri özelleştirmek için uygulamanızdaki [ini_set ()](https://www.php.net/manual/function.ini-set.php) kullanabilirsiniz.

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_USER, PHP_INI_PERDIR ve PHP_INI_ALL yönergelerini özelleştirmek için (bkz. [php.ini yönergeleri](https://www.php.net/manual/ini.list.php)), uygulamanızın kök dizinine bir *. htaccess* dosyası ekleyin.

*. Htaccess* dosyasında, söz dizimini kullanarak yönergeleri ekleyin `php_value <directive-name> <value>` . Örnek:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Değişiklikleri ile uygulamanızı yeniden dağıtın ve yeniden başlatın. Kudu ile dağıtırsanız (örneğin, [Git](deploy-local-git.md)kullanarak), dağıtımdan sonra otomatik olarak yeniden başlatılır.

*. Htaccess* kullanımına alternatif olarak, bu PHP_INI_SYSTEM olmayan yönergeleri özelleştirmek için uygulamanızdaki [ini_set ()](https://www.php.net/manual/function.ini-set.php) kullanabilirsiniz.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM yönergeleri özelleştirme

::: zone pivot="platform-windows"  

PHP_INI_SYSTEM yönergeleri özelleştirmek için (bkz. [php.ini yönergeleri](https://www.php.net/manual/ini.list.php)), *. htaccess* yaklaşımını kullanamazsınız. App Service, uygulama ayarını kullanarak ayrı bir mekanizma sağlar `PHP_INI_SCAN_DIR` .

İlk olarak, adlı bir uygulama ayarı eklemek için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Kudu konsoluna gidin ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) ve adresine gidin `d:\home\site` .

Çağrılan bir dizin oluşturun `d:\home\site` `ini` ve ardından  `d:\home\site\ini` dizinde (örneğin, *settings.ini)* özelleştirmek istediğiniz yönergeleri kullanarak bir. ini dosyası oluşturun. *php.ini* dosyasında kullanacağınız söz dizimini kullanın. 

Örneğin, [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) değerini değiştirmek için aşağıdaki komutları çalıştırın:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Değişikliklerin etkili olması için uygulamayı yeniden başlatın.

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_SYSTEM yönergeleri özelleştirmek için (bkz. [php.ini yönergeleri](https://www.php.net/manual/ini.list.php)), *. htaccess* yaklaşımını kullanamazsınız. App Service, uygulama ayarını kullanarak ayrı bir mekanizma sağlar `PHP_INI_SCAN_DIR` .

İlk olarak, adlı bir uygulama ayarı eklemek için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` , *php.ini* var olan varsayılan dizindir. `/home/site/ini` , özel bir *. ini* dosyası ekleyeceğiniz özel dizindir. Değerleri bir ile ayırın `:` .

Linux kapsayıcınızda () Web SSH oturumuna gidin `https://<app-name>.scm.azurewebsites.net/webssh/host` .

Çağrılan bir dizin oluşturun `/home/site` `ini` ve ardından  `/home/site/ini` dizinde (örneğin, *settings.ini)* özelleştirmek istediğiniz yönergeleri kullanarak bir. ini dosyası oluşturun. *php.ini* dosyasında kullanacağınız söz dizimini kullanın. 

> [!TIP]
> App Service 'daki yerleşik Linux kapsayıcılarında, */Home* kalıcı paylaşılan depolama alanı olarak kullanılır. 
>

Örneğin, [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) değerini değiştirmek için aşağıdaki komutları çalıştırın:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Değişikliklerin etkili olması için uygulamayı yeniden başlatın.

::: zone-end

## <a name="enable-php-extensions"></a>PHP uzantılarını etkinleştir

::: zone pivot="platform-windows"  

Yerleşik PHP yüklemeleri en yaygın olarak kullanılan uzantıları içerir. [php.ini yönergelerini özelleştirdiğiniz](#customize-php_ini_system-directives)şekilde ek uzantıları etkinleştirebilirsiniz.

> [!NOTE]
> PHP sürümünü ve geçerli *php.ini* yapılandırmasını görmenin en iyi yolu, uygulamanızda [PHPINFO ()](https://php.net/manual/function.phpinfo.php) öğesini çağırmanız.
>

Ek uzantıları etkinleştirmek için aşağıdaki adımları izleyin:

`bin`Uygulamanızın kök dizinine bir dizin ekleyin ve `.dll` uzantı dosyalarını içine yerleştirin (örneğin, *mongodb.dll*). Uzantıların Azure 'daki PHP sürümü ile uyumlu olduğundan ve VC9 ve iş parçacığı olmayan güvenli (,) uyumlu olduğundan emin olun.

Değişikliklerinizi dağıtın.

[PHP_INI_SYSTEM yönergelerini özelleştirme](#customize-php_ini_system-directives)bölümündeki adımları uygulayın, uzantıları özel *. ını* dosyasına [uzantı](https://www.php.net/manual/ini.core.php#ini.extension) veya [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) yönergeleriyle ekleyin.

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Değişikliklerin etkili olması için uygulamayı yeniden başlatın.

::: zone-end

::: zone pivot="platform-linux"

Yerleşik PHP yüklemeleri en yaygın olarak kullanılan uzantıları içerir. [php.ini yönergelerini özelleştirdiğiniz](#customize-php_ini_system-directives)şekilde ek uzantıları etkinleştirebilirsiniz.

> [!NOTE]
> PHP sürümünü ve geçerli *php.ini* yapılandırmasını görmenin en iyi yolu, uygulamanızda [PHPINFO ()](https://php.net/manual/function.phpinfo.php) öğesini çağırmanız.
>

Ek uzantıları etkinleştirmek için aşağıdaki adımları izleyin:

`bin`Uygulamanızın kök dizinine bir dizin ekleyin ve `.so` uzantı dosyalarını içine yerleştirin (örneğin, *MongoDB.so*). Uzantıların Azure 'daki PHP sürümü ile uyumlu olduğundan ve VC9 ve iş parçacığı olmayan güvenli (,) uyumlu olduğundan emin olun.

Değişikliklerinizi dağıtın.

[PHP_INI_SYSTEM yönergelerini özelleştirme](#customize-php_ini_system-directives)bölümündeki adımları uygulayın, uzantıları özel *. ını* dosyasına [uzantı](https://www.php.net/manual/ini.core.php#ini.extension) veya [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) yönergeleriyle ekleyin.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Değişikliklerin etkili olması için uygulamayı yeniden başlatın.

::: zone-end

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

::: zone pivot="platform-windows"  

Tanılama günlüklerinizin Azure App Service görünmesini sağlamak için standart [error_log ()](https://php.net/manual/function.error-log.php) yardımcı programını kullanın.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Sorun giderme

Çalışan bir PHP uygulaması App Service veya hata durumunda farklı davrandığı zaman, aşağıdakileri deneyin:

- [Günlük akışına erişin](#access-diagnostic-logs).
- Uygulamayı üretim modunda yerel olarak test edin. App Service uygulamanızı üretim modunda çalıştırır, bu nedenle projenizin üretim modunda yerel olarak beklendiği gibi çalıştığından emin olmanız gerekir. Örnek:
    - *composer.js* bağlı olarak, üretim modu ( `require` vs.) için farklı paketler yüklenebilir `require-dev` .
    - Bazı Web çerçeveleri, statik dosyaları üretim modunda farklı şekilde dağıtabilir.
    - Belirli Web çerçeveleri, üretim modunda çalışırken özel başlatma betikleri kullanabilir.
- Uygulamanızı hata ayıklama modunda App Service çalıştırın. Örneğin, [Laraesin](https://meanjs.org/)içinde, uygulama [ `APP_DEBUG` ayarını `true` olarak ayarlayarak ](configure-common.md#configure-app-settings)uygulamanızı üretim sırasında hata ayıklama iletilerini verecek şekilde yapılandırabilirsiniz.

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: MySQL ile PHP uygulaması](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux Hakkında SSS](faq-app-service-linux.md)

::: zone-end

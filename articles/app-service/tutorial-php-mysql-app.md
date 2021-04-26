---
title: 'Öğretici: MySQL ile PHP uygulaması'
description: Azure’da çalışan ve bir MySQL veritabanı ile bağlantısı olan PHP uygulamasını nasıl edinebileceğinizi öğrenin. Laralevel, öğreticide kullanılır.
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 0810f023f4e2e192f2cb0d83f2a028cdded9e275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779479"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>Öğretici: Azure App Service bir PHP ve MySQL uygulaması derleme

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) , Windows işletim sistemini kullanarak yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. Bu öğreticide, Azure 'da bir PHP uygulamasının nasıl oluşturulacağı ve bir MySQL veritabanına nasıl bağlanacağı gösterilmektedir. İşiniz bittiğinde, Windows üzerinde Azure App Service çalışan bir [Laralevel](https://laravel.com/) uygulamanız olacaktır.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) , Linux işletim sistemini kullanarak yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. Bu öğreticide, Azure 'da bir PHP uygulamasının nasıl oluşturulacağı ve bir MySQL veritabanına nasıl bağlanacağı gösterilmektedir. İşiniz bittiğinde, Linux üzerinde Azure App Service üzerinde çalışan bir [Laralevel](https://laravel.com/) uygulamanız olacaktır.

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/complete-checkbox-published.png" alt-text="Görev Listesi başlıklı bir PHP uygulaması örneğini ekran görüntüsü.":::

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure’da MySQL veritabanı oluşturma
> * PHP uygulamasını MySQL’e bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- [Git'i yükleyin](https://git-scm.com/)
- [PHP 5.6.4 veya sonraki sürümü yükleme](https://php.net/downloads.php)
- [Oluşturucu Yükleme](https://getcomposer.org/doc/00-intro.md)
- Laravel için gereken şu PHP uzantılarını etkinleştirin: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
- [MySQL 'i yükleyip başlatın](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

## <a name="prepare-local-mysql"></a>Yerel MySQL hazırlama

Bu adımda, bu öğreticide kullanmak üzere yerel MySQL sunucunuzda bir veritabanı oluşturursunuz.

### <a name="connect-to-local-mysql-server"></a>Yerel MySQL sunucusuna bağlanma

Bir terminal penceresinde yerel MySQL sunucunuza bağlanın. Bu öğreticideki tüm komutları çalıştırmak için bu terminal penceresini kullanabilirsiniz.

```bash
mysql -u root -p
```

Parola istenirse `root` hesabının parolasını girin. Kök hesap parolanızı hatırlamıyorsanız bkz [MySQL: Kök Parolayı Sıfırlama](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Komutunuz başarıyla çalışırsa, MySQL sunucunuz çalışıyor demektir. Çalışmıyorsa, yerel MySQL sunucunuzun aşağıdaki [MySQL yükleme sonrası adımları](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) kullanılarak başlatıldığından emin olun.

### <a name="create-a-database-locally"></a>Yerel olarak veritabanı oluşturma

`mysql` isteminde bir veritabanı oluşturun.

```sql 
CREATE DATABASE sampledb;
```

`quit` yazarak sunucu bağlantınızdan çıkış yapın.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Yerel olarak PHP uygulaması oluşturma
Bu adımda bir Laravel örnek uygulaması edinir, veritabanı bağlantısını yapılandırır ve yerel olarak çalıştırırsınız. 

### <a name="clone-the-sample"></a>Örneği

Terminal penceresinde, `cd` ile bir çalışma dizinine gidin.

Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` komutuyla kopyalanmış dizininize geçin.
Gereken paketleri yükleyin.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL bağlantısını yapılandırma

Depo kökünde *.env* adlı bir dosya oluşturun. Aşağıdaki değişkenleri *.env* dosyasına kopyalayın. _&lt; Root_password>_ yer tutucusunu MySQL kök kullanıcısının parolasıyla değiştirin.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Laravel’in _.env_ dosyasını nasıl kullandığı hakkında bilgi için bkz. [Laravel Ortamı Yapılandırması](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>Örneği yerel olarak çalıştırma

Uygulama için gereken tabloları oluşturmak üzere [Laravel veritabanı geçişlerini](https://laravel.com/docs/5.4/migrations) çalıştırın. Geçişlerde hangi tabloların oluşturulduğunu görmek için, Git deposundaki _veritabanı/geçişler_ dizinine bakın.

```bash
php artisan migrate
```

Yeni bir Laravel uygulama anahtarı oluşturun.

```bash
php artisan key:generate
```

Uygulamayı çalıştırın.

```bash
php artisan serve
```

Bir tarayıcıda `http://localhost:8000` sayfasına gidin. Sayfaya birkaç görev ekleyin.

![PHP başarıyla MySQL’e bağlanır](./media/tutorial-php-mysql-app/mysql-connect-success.png)

PHP’yi durdurmak için terminale `Ctrl + C` yazın.

## <a name="create-mysql-in-azure"></a>Azure’da MySQL oluşturma

Bu adımda, [MySQL için Azure Veritabanı](../mysql/index.yml) içinde bir MySQL veritabanı oluşturursunuz. Daha sonra, PHP uygulamasını bu veritabanına bağlanacak şekilde yapılandırırsınız.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL sunucusu oluşturma

Cloud Shell, komutunu kullanarak MySQL için Azure veritabanı 'nda bir sunucu oluşturun [`az mysql server create`](/cli/azure/mysql/server#az_mysql_server_create) .

Aşağıdaki komutta, yer tutucu için benzersiz bir sunucu adı *\<mysql-server-name>* , için bir Kullanıcı adı *\<admin-user>* ve yer tutucu için bir parola koyun *\<admin-password>*  . Sunucu adı, MySQL uç noktasının (`https://<mysql-server-name>.mysql.database.azure.com`) bir parçası olarak kullanıldığından, adın Azure’daki tüm sunucularda benzersiz olması gerekir. MySQL DB SKU 'SU seçme hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı sunucusu oluşturma](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

MySQL sunucusu oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir:

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Sunucu güvenlik duvarını yapılandırma

Cloud Shell, komutunu kullanarak MySQL sunucunuzun istemci bağlantılarına izin vermek için bir güvenlik duvarı kuralı oluşturun [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) . Hem başlangıç hem bitiş IP’si 0.0.0.0 olarak ayarlandığında, güvenlik duvarı yalnızca diğer Azure kaynakları için açılır. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [Yalnızca uygulamanızın kullandığı giden IP adreslerini kullanarak](overview-inbound-outbound-ips.md#find-outbound-ips) güvenlik duvarı kurallarınızda daha da kısıtlayıcı olabilirsiniz.
>

Cloud Shell, yerel *\<your-ip-address>* [IPv4 IP adresinizle](https://www.whatsmyip.org/)değiştirerek yerel bilgisayarınızdan erişime izin vermek için komutu yeniden çalıştırın.

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Üretim MySQL sunucusuna yerel olarak bağlanma

Yerel terminal penceresinde, Azure’da MySQL sunucusuna bağlanın. Daha önce _&lt; Yönetici-Kullanıcı>_ ve _&lt; mysql-sunucu-adı>_ için belirttiğiniz değeri kullanın. Parola sorulduğunda, Azure’da veritabanı oluştururken belirttiğiniz parolayı kullanın.

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Üretim veritabanı oluşturma

`mysql` isteminde bir veritabanı oluşturun.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>İzinleri olan bir kullanıcı oluşturma

_phpappuser_ adlı bir veritabanı kullanıcısı oluşturun ve bu kullanıcıya `sampledb` veritabanındaki tüm ayrıcalıkları verin. Öğreticinin basitliği için parola olarak _MySQLAzure2017_ kullanın.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

`quit` yazarak sunucu bağlantısından çıkış yapın.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Uygulamayı Azure MySQL’e bağlama

Bu adımda, PHP uygulamasını MySQL için Azure Veritabanı içinde oluşturduğunuz MySQL veritabanına bağlarsınız.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Veritabanı bağlantısını yapılandırma

Depo kökünde bir _.env.production_ dosyası oluşturun ve içine aşağıdaki değişkenleri kopyalayın. Placeholder_ &lt; MySQL-sunucu-adı>_ hem *DB_Host* hem de *DB_USERNAME* değiştirin.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Değişiklikleri kaydedin.

> [!TIP]
> MySQL bağlantı bilgilerinizin güvenliğini sağlamak için bu dosya zaten Git deposunun dışında bırakılmıştır (Depo kökünde _.gitignore_ dosyasına bakın). Daha sonra, App Service’teki ortam değişkenlerini, MySQL için Azure Veritabanı içinde veritabanınıza bağlanmak üzere nasıl yapılandıracağınızı öğreneceksiniz. Ortam değişkenlerini kullandığınızda App Service içinde *.env* dosyası gerekli değildir.
>

### <a name="configure-tlsssl-certificate"></a>TLS/SSL sertifikası yapılandırma

Varsayılan olarak, MySQL için Azure veritabanı istemcilerden gelen TLS bağlantılarını uygular. Azure’da MySQL veritabanınıza bağlanmak üzere MySQL için Azure Veritabanı tarafından sağlanan [_.pem_ sertifikasını kullanmanız gerekir](../mysql/howto-configure-ssl.md).

_config/database.php_ dosyasını açın ve aşağıdaki kodda gösterildiği gibi `sslmode` ve `options` parametrelerini `connections.mysql` içine ekleyin.

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

`BaltimoreCyberTrustRoot.crt.pem` sertifikası, bu öğreticide kolaylık sağlaması açısından depoda sunulmuştur. 

### <a name="test-the-application-locally"></a>Uygulamayı yerel olarak test etme

MySQL için Azure Veritabanı içinde tablolar oluşturmak için, _.env.production_ ile Laravel veritabanı geçişlerini ortam dosyası olarak çalıştırın. _.env.production_ dosyasının, Azure’da MySQL veritabanınızla bağlantı bilgilerini içerdiğini unutmayın.

```bash
php artisan migrate --env=production --force
```

_.env.production_ henüz geçerli bir uygulama anahtarına sahip değildir. Terminalde bu dosya için yeni bir tane oluşturun.

```bash
php artisan key:generate --env=production --force
```

Örnek uygulamayı _.env.production_ ortam dosyası ile birlikte çalıştırın.

```bash
php artisan serve --env=production
```

`http://localhost:8000` sayfasına gidin. Sayfa hatasız yüklenirse, PHP uygulaması Azure’da MySQL veritabanına bağlanıyor demektir.

Sayfaya birkaç görev ekleyin.

![PHP, MySQL için Azure Veritabanı’na başarıyla bağlanır](./media/tutorial-php-mysql-app/mysql-connect-success.png)

PHP’yi durdurmak için terminale `Ctrl + C` yazın.

### <a name="commit-your-changes"></a>Değişikliklerinizi kaydetme

Değişikliklerinizi kaydetmek için aşağıdaki Git komutlarını çalıştırın:

```bash
git add .
git commit -m "database.php updates"
```

Uygulamanız dağıtılmaya hazırdır.

## <a name="deploy-to-azure"></a>Azure’a dağıtın

Bu adımda, MySQL’e bağlı PHP uygulamasını Azure App Service'e dağıtırsınız.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Web uygulaması oluşturma

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>Veritabanı ayarlarını yapılandırma

App Service'te, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) komutunu kullanıp ortam değişkenlerini _uygulama ayarları_ olarak belirlersiniz.

Aşağıdaki komut `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` ve `DB_PASSWORD` uygulama ayarlarını yapılandırır. _&lt; App-name>_ ve _&lt; mysql-Server-Name>_ yer tutucularını değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Ayarlara erişmek için PHP [getenv](https://www.php.net/manual/en/function.getenv.php) yöntemini kullanabilirsiniz. Laravel kodu, PHP `getenv` üzerinde bir [env](https://laravel.com/docs/5.4/helpers#method-env) sarmalayıcı kullanır. Örneğin, _config/database.php_ içindeki MySQL yapılandırması aşağıdaki kod gibi görünür:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel ortam değişkenlerini yapılandırma

Laravel, App Service'te bir uygulama anahtarı gerektirir. Uygulama anahtarını uygulama ayarları ile yapılandırabilirsiniz.

Yerel terminal penceresinde, uygulama anahtarını _.env_ dosyasına kaydetmeden yeni bir uygulama anahtarı oluşturmak için `php artisan` seçeneğini kullanın.

```bash
php artisan key:generate --show
```

Cloud Shell, komutunu kullanarak App Service uygulamasındaki uygulama anahtarını ayarlayın [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) . _&lt; App-name>_ ve _&lt; outputofphpartisankey: Generate>_ yer tutucuları değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` , dağıtılmış uygulama hata ile karşılaştığında hata ayıklama bilgilerini döndürmesini söyler. Bir üretim uygulaması çalıştırırken daha güvenli olan `false` seçeneğine ayarlayın.

### <a name="set-the-virtual-application-path"></a>Sanal uygulama yolu ayarlama

::: zone pivot="platform-windows"  

Uygulamanın sanal uygulama yolunu ayarlayın. [Laravel uygulaması yaşam döngüsü](https://laravel.com/docs/5.4/lifecycle), uygulamanın kök dizini yerine _public_ dizininde başladığı için bu adım gereklidir. Yaşam döngüsü kök dizinde başlayan diğer PHP çerçeveleri, sanal uygulama yolu el ile yapılandırılmadan çalışabilir.

Cloud Shell, komutunu kullanarak sanal uygulama yolunu ayarlayın [`az resource update`](/cli/azure/resource#az_resource_update) . _&lt; App-name>_ yer tutucusunu değiştirin.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Varsayılan olarak, Azure App Service kök sanal uygulama yolunu ( _/_ ) dağıtılan uygulama dosyalarının kök dizinine (_sites\wwwroot_) yönlendirir.

::: zone-end

::: zone pivot="platform-linux"

[Laralevel uygulama yaşam döngüsü](https://laravel.com/docs/5.4/lifecycle) , uygulamanın kök dizini yerine _ortak_ dizin içinde başlar. App Service için varsayılan PHP Docker görüntüsü, Apache kullanır ve Laravel için `DocumentRoot` öğesini özelleştirmenize izin vermez. Bununla birlikte, `.htaccess` kullanarak tüm istekleri kök dizin yerine _/public_ dizinini işaret edecek şekilde yeniden yazabilirsiniz. Depo köküne bu amaçla bir `.htaccess` zaten eklenmiştir. Bununla, Laravel uygulamanız dağıtılmaya hazırdır.

Daha fazla bilgi için bkz. [site kökünü değiştirme](configure-language-php.md#change-site-root).

::: zone-end

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

> [!NOTE]
> Dağıtım işleminin sonunda [Composer](https://getcomposer.org/) paketleri yüklediğini fark edebilirsiniz. App Service, varsayılan dağıtım sırasında bu otomasyonları çalıştırmadığı için bu örnek depoyu etkinleştirmek üzere kök dizinde üç ek dosya bulunur:
>
> - `.deployment` - Bu dosya, App Service’ten özel dağıtım betiği olarak `bash deploy.sh` komutunu çalıştırmasını ister.
> - `deploy.sh` - Özel dağıtım betiği. Dosyayı gözden geçirirseniz, `npm install` komutundan sonra `php composer.phar install` çalıştırdığını görürsünüz.
> - `composer.phar` - Composer paket yöneticisi.
>
> App Service’e Git tabanlı dağıtımınıza herhangi bir adım eklemek için bu yaklaşımı kullanabilirsiniz. Daha fazla bilgi için bkz. [Özel Dağıtım Betiği](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Azure uygulamasına gidin

`http://<app-name>.azurewebsites.net` listesine göz atın ve listeye birkaç görev ekleyin.

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Eklenen yeni görevleri gösteren Görev Listesi adlı Azure uygulaması örneği ekran görüntüsü.":::

Tebrikler, Azure App Service'te veri temelli bir PHP uygulaması çalıştırıyorsunuz.

## <a name="update-model-locally-and-redeploy"></a>Modeli yerel olarak güncelleştirme ve yeniden dağıtma

Bu adımda, `task` veri modeli ve web uygulamasında basit bir değişiklik yaptıktan sonra güncelleştirmeyi Azure'da yayımlarsınız.

Görevler senaryosu için, görevi tamamlandı olarak işaretleyebileceğiniz şekilde uygulamayı değiştirirsiniz.

### <a name="add-a-column"></a>Sütun ekleme

Yerel terminal penceresinde Git deposunun kök dizinine gidin.

`tasks` tablosu için yeni bir veritabanı geçişi oluşturun:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Bu komut, oluşturulan geçiş dosyasının adını gösterir. _database/migrations_ içinde bu dosyayı bulup açın.

`up` yöntemini aşağıdaki kod ile değiştirin:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Yukarıdaki kod, `tasks` tablosuna `complete` adlı bir boole sütunu ekler.

Geri alma eylemi için `down` yöntemini aşağıdaki kod ile değiştirin:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Yerel terminal penceresinde, Laravel veritabanı geçişlerini çalıştırarak yerel veritabanında değişikliği yapın.

```bash
php artisan migrate
```

[Laravel adlandırma kuralına](https://laravel.com/docs/5.4/eloquent#defining-models) göre `Task` modeli (bkz. _app/Task.php_) varsayılan olarak `tasks` tablosu ile eşlenir.

### <a name="update-application-logic"></a>Uygulama mantığını güncelleştirme

*routes/web.php* dosyasını açın. Uygulama, yollarını ve iş mantığını burada tanımlar.

Dosyanın sonuna aşağıdaki kod ile bir yol ekleyin:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Yukarıdaki kod, `complete` değerini değiştirerek veri modelinde basit bir güncelleştirme yapar.

### <a name="update-the-view"></a>Görünümü güncelleştirme

*resources/views/tasks.blade.php* dosyasını açın. `<tr>` açma etiketini bulup şununla değiştirin:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Yukarıdaki kod, görevin tamamlanıp tamamlanmamasına bağlı olarak satır rengini değiştirir.

Sonraki satırda şu kodu görürsünüz:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Tüm satırı aşağıdaki kod ile değiştirin:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Yukarıdaki kod, daha önce tanımladığınız yola başvuran Gönder düğmesini ekler.

### <a name="test-the-changes-locally"></a>Değişiklikleri yerel olarak test etme

Yerel terminal penceresinde, Git deposunun kök dizininden geliştirme sunucusunu çalıştırın.

```bash
php artisan serve
```

Görev durumu değişikliğini görmek için `http://localhost:8000` öğesine gidip onay kutusunu işaretleyin.

![Göreve eklenen onay kutusu](./media/tutorial-php-mysql-app/complete-checkbox.png)

PHP’yi durdurmak için terminale `Ctrl + C` yazın.

### <a name="publish-changes-to-azure"></a>Değişiklikleri Azure’da yayımlama

Yerel terminal penceresinde, Laravel veritabanı geçişlerini üretim bağlantı dizesi ile birlikte çalıştırarak değişikliği Azure veritabanında yapın.

```bash
php artisan migrate --env=production --force
```

Tüm değişiklikleri Git’e kaydedin ve ardından kod değişikliklerini Azure’a gönderin.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

Tamamlandıktan sonra `git push` Azure uygulamasına gidin ve yeni işlevleri test edin.

![Azure’da yayımlanan model ve veritabanı değişiklikleri](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Herhangi bir görevi eklediyseniz veritabanında tutulur. Veri şemasında yapılan güncelleştirmeler var olan verileri olduğu gibi bırakır.

## <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma

::: zone pivot="platform-windows"  

PHP uygulaması Azure App Service'te çalışırken, terminalinize yönlendirilen konsol günlüklerini alabilirsiniz. Böylece, uygulama hatalarını ayıklamanıza yardımcı olan tanılama iletilerinin aynısını alabilirsiniz.

Günlük akışını başlatmak için [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) Cloud Shell komutunu kullanın.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Günlük akışı başladıktan sonra bazı web trafiği almak için tarayıcıda Azure uygulamasını yenileyin. Artık konsol günlüklerinin terminale yöneltildiğini görebilirsiniz. Konsol günlüklerini hemen görmüyorsanız, 30 saniye içinde yeniden kontrol edin.

Günlük akışını dilediğiniz zaman durdurmak için `Ctrl`+`C` yazın.

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> Bir PHP uygulaması, konsol çıktısı için standart [error_log()](https://php.net/manual/function.error-log.php) seçeneğini kullanabilir. Örnek uygulama _app/Http/routes.php_ içinde bu yaklaşımı kullanır.
>
> Bir web çerçevesi olarak, [Laravel, Monolog günlük sağlayıcısını kullanır](https://laravel.com/docs/5.4/errors). İletileri konsola çıkarmak üzere Monolog kullanma hakkında bilgi almak için bkz. [PHP: Konsola çıktı için monolog kullanma (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-app"></a>Azure uygulamasını yönetme

Oluşturduğunuz uygulamayı yönetmek için [Azure Portal](https://portal.azure.com) gidin.

Sol menüden **uygulama hizmetleri**' ne ve ardından Azure uygulamanızın adına tıklayın.

![Azure uygulamasına portal gezintisi](./media/tutorial-php-mysql-app/access-portal.png)

Uygulamanızın genel bakış sayfasını görürsünüz. Buradan durdurma, başlatma, yeniden başlatma, göz atma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

Soldaki menü, uygulamanızı yapılandırmaya yönelik sayfalar sağlar.

![Azure portalında App Service sayfası](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure’da MySQL veritabanı oluşturma
> * PHP uygulamasını MySQL’e bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

Uygulamaya özel bir DNS adı eşlemeyle ilgili bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Öğretici: özel DNS adını uygulamanıza eşleyin](app-service-web-tutorial-custom-domain.md)

Ya da diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [PHP uygulamasını yapılandırma](configure-language-php.md)

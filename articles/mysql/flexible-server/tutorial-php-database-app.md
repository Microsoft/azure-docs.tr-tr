---
title: 'Öğretici: MySQL için Azure veritabanı esnek sunucusu ile PHP (Laray) uygulaması derleme'
description: Bu öğreticide, esnek sunucu ile bir PHP uygulamasının nasıl oluşturulacağı açıklanmaktadır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bb38b72af6e7c649c0904c41d3052b15a4c36955
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770066"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Öğretici: Azure App Service bir PHP (Laray) ve MySQL esnek sunucusu (Önizleme) uygulaması derleme


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Esnek sunucu ile Azure 'da PHP Web uygulaması":::

[Azure App Service](../../app-service/overview.md) , Linux işletim sistemini kullanarak yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. Bu öğreticide, Azure 'da bir PHP uygulamasının nasıl oluşturulacağı ve bir MySQL veritabanına nasıl bağlanacağı gösterilmektedir. İşiniz bittiğinde, Linux üzerinde Azure App Service üzerinde çalışan bir [Laralevel](https://laravel.com/) uygulamanız olacaktır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Yerel MySQL ile PHP (Laralevel) uygulaması kurma
> * MySQL esnek sunucusu oluşturma (Önizleme)
> * Bir PHP uygulamasını MySQL esnek sunucusuna bağlama (Önizleme)
> * Uygulamayı Azure App Service dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Uygulamayı Azure portalında yönetme

[Azure aboneliğiniz](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

1. [Git'i yükleyin](https://git-scm.com/)
2. [PHP 5.6.4 veya sonraki sürümü yükleme](https://php.net/downloads.php)
3. [Oluşturucu Yükleme](https://getcomposer.org/doc/00-intro.md)
4. Laravel için gereken şu PHP uzantılarını etkinleştirin: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
5. [MySQL'i yükleyin ve başlatın](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

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

Terminal penceresinde, örnek uygulamayı klonlayabileceğiniz boş bir dizine gidin.  Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın.

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP başarıyla MySQL’e bağlanır":::

PHP’yi durdurmak için terminale `Ctrl + C` yazın.

## <a name="create-a-mysql-flexible-server-preview"></a>MySQL esnek sunucusu oluşturma (Önizleme)
Bu adımda, [MySQL Için Azure veritabanı esnek sunucusu](../index.yml) 'nda genel önizlemede olan bir MySQL veritabanı oluşturacaksınız. Daha sonra, PHP uygulamasını bu veritabanına bağlanacak şekilde yapılandırırsınız. [Azure Cloud Shell](../../cloud-shell/overview.md), komutuyla ' de bir sunucu oluşturun [`az flexible-server create`](/cli/azure/mysql/server#az_mysql_flexible_server_create) .

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Bir sonraki adımda kullanmak için **ServerName** ve **Connection dizesini** bir yere, laralevel veri geçişini bağlamak ve çalıştırmak için bu dizeyi bir yere iade edin.
> - **IP adresi** bağımsız değişkeni için, ISTEMCI makinenizin IP 'sini sağlayın. Sunucu oluşturulduğunda kilitlenir ve sunucuyu yerel olarak yönetmek için istemci makinenize erişime izin vermeniz gerekir.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Web uygulamasının sunucuya bağlanmasına izin vermek için sunucu güvenlik duvarını yapılandırma

Cloud Shell, az MySQL Server Firewall-Rule Create komutunu kullanarak MySQL sunucunuzun istemci bağlantılarına izin vermek için bir güvenlik duvarı kuralı oluşturun. Hem başlangıç IP 'si hem de bitiş IP 'si olarak ayarlandığında ```0.0.0.0``` , güvenlik duvarı yalnızca sunucuya bağlanmak için statik IP 'si olmayan diğer Azure hizmetleri için açılır.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Üretim MySQL sunucusuna yerel olarak bağlanma

Yerel terminal penceresinde, Azure’da MySQL sunucusuna bağlanın. Daha önce ve için belirttiğiniz değeri kullanın ```<admin-user>``` ```<mysql-server-name>``` . Parola sorulduğunda, Azure’da veritabanı oluştururken belirttiğiniz parolayı kullanın.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Üretim veritabanı oluşturma

`mysql` isteminde bir veritabanı oluşturun.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>İzinleri olan bir kullanıcı oluşturma

_phpappuser_ adlı bir veritabanı kullanıcısı oluşturun ve bu kullanıcıya `sampledb` veritabanındaki tüm ayrıcalıkları verin. Öğreticinin basitliği için parola olarak _MySQLAzure2020_ kullanın.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

`quit` yazarak sunucu bağlantısından çıkış yapın.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Uygulamayı MySQL esnek sunucusuna bağlama

Bu adımda, PHP uygulamasını MySQL için Azure Veritabanı içinde oluşturduğunuz MySQL veritabanına bağlarsınız.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Veritabanı bağlantısını yapılandırma

Depo kökünde bir _.env.production_ dosyası oluşturun ve içine aşağıdaki değişkenleri kopyalayın. _&lt; MySQL-Server-Name>_ yer tutucusunu hem *DB_Host* hem *DB_USERNAME* ile değiştirin.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Değişiklikleri kaydedin.

> [!TIP]
> MySQL bağlantı bilgilerinizin güvenliğini sağlamak için bu dosya zaten Git deposunun dışında bırakılmıştır (Depo kökünde _.gitignore_ dosyasına bakın). Daha sonra, App Service’teki ortam değişkenlerini, MySQL için Azure Veritabanı içinde veritabanınıza bağlanmak üzere nasıl yapılandıracağınızı öğreneceksiniz. Ortam değişkenlerini kullandığınızda App Service içinde *.env* dosyası gerekli değildir.
>

### <a name="configure-tlsssl-certificate"></a>TLS/SSL sertifikası yapılandırma

Varsayılan olarak, MySQL esnek sunucusu istemcilerden TLS bağlantıları uygular. Azure 'da MySQL veritabanınıza bağlanmak için [MySQL Için Azure veritabanı esnek sunucusu tarafından sağlanan _. Pee_ sertifikasını](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)kullanmanız gerekir. [Bu sertifikayı](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)indirin) ve örnek uygulama deposunun yerel kopyasına **SSL** klasörüne yerleştirin.

_config/database.php_ dosyasını açın ve aşağıdaki kodda gösterildiği gibi `sslmode` ve `options` parametrelerini `connections.mysql` içine ekleyin.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP, MySQL için Azure Veritabanı’na başarıyla bağlanır":::

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

FTP ve yerel git, bir dağıtım kullanıcısı kullanarak bir Azure Web uygulamasına dağıtabilir. Dağıtım kullanıcısını yapılandırdıktan sonra tüm Azure dağıtımlarınız için kullanabilirsiniz. Hesap düzeyinde dağıtım Kullanıcı adınız ve parolanız, Azure aboneliği kimlik bilgilerinizden farklı.

Dağıtım kullanıcısını yapılandırmak için, Azure Cloud Shell bölümünde [az WebApp Deployment User set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) komutunu çalıştırın. Kullanıcı adı _&lt;>_ ve _&lt; parola>_ , dağıtım Kullanıcı Kullanıcı adınızla ve parolasıyla değiştirin.

Kullanıcı adı Azure içinde benzersiz olmalıdır ve yerel git gönderimleri için ' @ ' sembolünü içermemelidir.
Parola en az sekiz karakter uzunluğunda olmalıdır ve şu üç öğeden ikisi vardır: harfler, rakamlar ve semboller.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

JSON çıktısı, parolayı null olarak gösterir. ' Çakışma ' alırsanız. Ayrıntılar: 409 hatası, Kullanıcı adını değiştirin. ' Hatalı Istek ' alırsanız. Ayrıntılar: 400 hatası, daha güçlü bir parola kullanın. **Web uygulamalarınızı dağıtmak için kullanmak üzere Kullanıcı adınızı ve parolanızı kaydedin.**

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

Cloud Shell, [az appservice plan Create](/cli/azure/appservice/plan#az_appservice_plan_create) komutuyla kaynak grubunda bir App Service planı oluşturun. Aşağıdaki örnek, ücretsiz fiyatlandırma katmanında (--SKU F1) ve bir Linux kapsayıcısında (--,-Linux) myAppServicePlan adlı bir App Service planı oluşturur.

az appservice plan Create--Name myAppServicePlan--Resource-Group myResourceGroup--SKU F1---Linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Web uygulaması oluşturma

MyAppServicePlan App Service planında bir [Web uygulaması](../../app-service/overview.md#app-service-on-linux) oluşturun.

Cloud Shell [az WebApp Create](/cli/azure/webapp#az_webapp_create) komutunu kullanabilirsiniz. Aşağıdaki örnekte, _&lt; app-name>_ değerini genel olarak benzersiz bir uygulama adıyla değiştirin (geçerli karakterler `a-z` , `0-9` ve `-` ). Çalışma zamanı `PHP|7.0` olarak ayarlanmıştır. Desteklenen tüm çalışma zamanlarını görmek için [az WebApp List-çalışma zamanları--Linux](/cli/azure/webapp#az_webapp_list_runtimes)' u çalıştırın.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Web uygulaması oluşturulduğunda Azure CLI aşağıda yer alan çıktıdaki gibi bilgiler gösterir:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Git dağıtımı etkin boş bir yeni web uygulaması oluşturdunuz.

> [!NOTE]
> Git Remote 'in URL 'SI deploymentLocalGitUrl özelliğinde, https:// <username> @ <app-name>. scm.azurewebsites.net/<app-name>. git biçiminde gösterilir. Bu URL’ye daha sonra ihtiyacınız olacağı için URL’yi kaydedin.

### <a name="configure-database-settings"></a>Veritabanı ayarlarını yapılandırma

App Service'te, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) komutunu kullanıp ortam değişkenlerini _uygulama ayarları_ olarak belirlersiniz.

Aşağıdaki komut `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` ve `DB_PASSWORD` uygulama ayarlarını yapılandırır. _&lt; App-name>_ ve _&lt; mysql-Server-Name>_ yer tutucularını değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

[Laralevel uygulama yaşam döngüsü](https://laravel.com/docs/5.4/lifecycle) , uygulamanın kök dizini yerine _ortak_ dizin içinde başlar. App Service için varsayılan PHP Docker görüntüsü, Apache kullanır ve Laravel için `DocumentRoot` öğesini özelleştirmenize izin vermez. Bununla birlikte, `.htaccess` kullanarak tüm istekleri kök dizin yerine _/public_ dizinini işaret edecek şekilde yeniden yazabilirsiniz. Depo köküne bu amaçla bir `.htaccess` zaten eklenmiştir. Bununla, Laravel uygulamanız dağıtılmaya hazırdır.

Daha fazla bilgi için bkz. [site kökünü değiştirme](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root).

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

Yerel terminal penceresine dönüp yerel Git deponuza bir Azure uzak deposu ekleyin. _&lt; Deploymentlocalgiturl-from-Create-Step>_ , [Web uygulaması oluştur](#create-a-web-app)listesinden kaydettiğiniz git uzak URL 'siyle değiştirin.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Aşağıdaki komutla uygulamanızı dağıtmak için Azure uzak deposuna gönderin. Git kimlik bilgileri Yöneticisi kimlik bilgilerini sizden isterse, Azure portal oturum açmak için kullandığınız kimlik bilgilerini değil **dağıtım kullanıcısı yapılandırma** bölümünde oluşturduğunuz kimlik bilgilerini girdiğinizden emin olun.

```bash
git push azure main
```

Bu komutun çalıştırılması birkaç dakika sürebilir. Çalıştırıldığında, aşağıdaki örneğe benzer bilgiler görüntüler:

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

### <a name="browse-to-the-azure-app"></a>Azure uygulamasına gidin

`http://<app-name>.azurewebsites.net` listesine göz atın ve listeye birkaç görev ekleyin.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="Azure 'da PHP Web uygulaması":::

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

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="Göreve eklenen onay kutusu":::

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

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Azure’da yayımlanan model ve veritabanı değişiklikleri":::

Herhangi bir görevi eklediyseniz veritabanında tutulur. Veri şemasında yapılan güncelleştirmeler var olan verileri olduğu gibi bırakır.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Bu kaynakların gelecekte gerekli olacağını düşünmüyorsanız, Cloud Shell’de aşağıdaki komutu çalıştırarak kaynak grubunu silin:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure portal kaynaklarını yönetme](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Sunucunuzu yönetme](how-to-manage-server-cli.md)

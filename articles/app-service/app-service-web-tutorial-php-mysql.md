---
title: MySQL - Azure App Service ile PHP (Laravel) | Microsoft Docs
description: Azure’da çalışan ve bir MySQL veritabanı ile bağlantısı olan PHP uygulamasını nasıl edinebileceğinizi öğrenin. Laravel öğreticide kullanılır.
services: app-service\web
documentationcenter: php
author: cephalin
manager: erikre
editor: ''
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: eddccc9897380e3ff47de49771a617bf6cacc407
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138350"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure"></a>Öğretici: PHP ve MySQL uygulaması azure'da oluşturun

> [!NOTE]
> Bu makalede bir uygulamanın Windows üzerinde App Service'e dağıtımı yapılır. App Service dağıtmak için _Linux_, bkz: [Linux üzerinde Azure App Service'te PHP ve MySQL uygulaması derleme](./containers/tutorial-php-mysql-app.md).
>

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu öğreticide, Azure'da bir PHP uygulaması oluşturma ve bir MySQL veritabanına bağlanmak gösterilir. İşlemi tamamladığınızda, gerekir bir [Laravel](https://laravel.com/) Azure App Service üzerinde çalışan uygulama.

![Azure App Service’te çalışan PHP uygulaması](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure’da MySQL veritabanı oluşturma
> * PHP uygulamasını MySQL’e bağlama
> * Uygulamayı Azure'a dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Git'i yükleyin](https://git-scm.com/)
* [PHP 5.6.4 veya sonraki sürümü yükleme](https://php.net/downloads.php)
* [Oluşturucu Yükleme](https://getcomposer.org/doc/00-intro.md)
* Laravel gereken şu PHP uzantılarını etkinleştirin: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
* [MySQL'i yükleyin ve başlatın](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Yerel MySQL hazırlama

Bu adımda, bu öğreticide kullanmak üzere yerel MySQL sunucunuzda bir veritabanı oluşturursunuz.

### <a name="connect-to-local-mysql-server"></a>Yerel MySQL sunucusuna bağlanma

Bir terminal penceresinde yerel MySQL sunucunuza bağlanın. Bu öğreticideki tüm komutları çalıştırmak için bu terminal penceresini kullanabilirsiniz.

```bash
mysql -u root -p
```

Parola istenirse `root` hesabının parolasını girin. Kök hesap parolanızı hatırlamıyorsanız bkz [MySQL: Kök parolasını nasıl Sıfırlayacağımı](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

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

### <a name="clone-the-sample"></a>Örneği kopyalama

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

Depo kökünde *.env* adlı bir metin dosyası oluşturun. Aşağıdaki değişkenleri *.env* dosyasına kopyalayın. _&lt;root_password>_ yer tutucusunu MySQL kök kullanıcı parolası ile değiştirin.

```
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

![PHP başarıyla MySQL’e bağlanır](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

PHP sunucusu durdurmak için terminale `Ctrl + C` yazın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Azure’da MySQL oluşturma

Bu adımda, [MySQL için Azure Veritabanı](/azure/mysql) içinde bir MySQL veritabanı oluşturursunuz. Daha sonra, PHP uygulamasını bu veritabanına bağlanacak şekilde yapılandırırsınız.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturun

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL sunucusu oluşturma

Cloud Shell’de, [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) komutuyla MySQL için Azure Veritabanı içinde bir sunucu oluşturun.

Aşağıdaki komutta *\<mysql_server_name>* yer tutucusunu benzersiz bir sunucu ile değiştirin, *\<admin_user>* için bir kullanıcı adı ve *\<admin_password>* yer tutucusu için bir parola girin. Sunucu adı, MySQL uç noktasının (`https://<mysql_server_name>.mysql.database.azure.com`) bir parçası olarak kullanıldığından, adın Azure’daki tüm sunucularda benzersiz olması gerekir.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name> --location "West Europe" --admin-user <admin_user> --admin-password <admin_password> --sku-name B_Gen5_1
```

> [!NOTE]
> Bu öğreticide göz önünde bulundurulacak birkaç kimlik bilgisi olduğundan, karışıklığı önlemek için `--admin-user` ve `--admin-password` sözde değerlere ayarlanmıştır. Bir üretim ortamında, Azure’daki MySQL sunucunuz için iyi bir kullanıcı adı ve parola seçerken en iyi güvenlik yöntemlerini izleyin.
>
>

MySQL sunucusu oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir:

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Sunucu güvenlik duvarını yapılandırma

Cloud Shell’de, [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) komutunu kullanarak MySQL sunucunuzun istemci bağlantılarına izin vermesi için bir güvenlik duvarı kuralı oluşturun. Hem başlangıç hem bitiş IP’si 0.0.0.0 olarak ayarlandığında, güvenlik duvarı yalnızca diğer Azure kaynakları için açılır. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [Yalnızca uygulamanızın kullandığı giden IP adreslerini kullanarak](overview-inbound-outbound-ips.md#find-outbound-ips) güvenlik duvarı kurallarınızda daha da kısıtlayıcı olabilirsiniz.
>

Cloud Shell'de *\<your_ip_address>* yerine [yerel IPv4 IP adresinizi](https://www.whatsmyip.org/) yazdıktan sonra komutu tekrar çalıştırarak yerel bilgisayarınızdan erişim izni verin.

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Üretim MySQL sunucusuna yerel olarak bağlanma

Yerel terminal penceresinde, Azure’da MySQL sunucusuna bağlanın. Daha önce _&lt;mysql_server_name>_ için belirttiğiniz değeri kullanın. Parola sorulduğunda, Azure’da veritabanı oluştururken belirttiğiniz parolayı kullanın.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Üretim veritabanı oluşturma

`mysql` isteminde bir veritabanı oluşturun.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>İzinleri olan bir kullanıcı oluşturma

_phpappuser_ adlı bir veritabanı kullanıcısı oluşturun ve bu kullanıcıya `sampledb` veritabanındaki tüm ayrıcalıkları verin. Öğreticinin kolaylığı için, parola olarak yine _MySQLAzure2017_’yi kullanın.

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

Depo kökünde bir _.env.production_ dosyası oluşturun ve içine aşağıdaki değişkenleri kopyalayın. Hem *DB_HOST* hem de *DB_USERNAME* alanında _&lt;mysql_server_name>_ yer tutucusunu değiştirin.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Değişiklikleri kaydedin.

> [!TIP]
> MySQL bağlantı bilgilerinizin güvenliğini sağlamak için bu dosya zaten Git deposunun dışında bırakılmıştır (Depo kökünde _.gitignore_ dosyasına bakın). Daha sonra, App Service’teki ortam değişkenlerini, MySQL için Azure Veritabanı içinde veritabanınıza bağlanmak üzere nasıl yapılandıracağınızı öğreneceksiniz. Ortam değişkenlerini kullandığınızda App Service içinde *.env* dosyası gerekli değildir.
>

### <a name="configure-ssl-certificate"></a>SSL sertifikası yapılandırma

Varsayılan olarak, MySQL için Azure Veritabanı, istemcilerden gelen SSL bağlantılarını zorlar. Azure’da MySQL veritabanınıza bağlanmak üzere MySQL için Azure Veritabanı tarafından sağlanan [ _.pem_ sertifikasını kullanmanız gerekir](../mysql/howto-configure-ssl.md).

_config/database.php_ dosyasını açın ve aşağıdaki kodda gösterildiği gibi `sslmode` ve `options` parametrelerini `connections.mysql` içine ekleyin.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

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

![PHP, MySQL için Azure Veritabanı’na başarıyla bağlanır](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

PHP’yi durdurmak için terminale `Ctrl + C` yazın.

### <a name="commit-your-changes"></a>Değişikliklerinizi kaydetme

Değişikliklerinizi kaydetmek için aşağıdaki Git komutlarını çalıştırın:

```bash
git add .
git commit -m "database.php updates"
```

Uygulamanız dağıtılmaya hazırdır.

## <a name="deploy-to-azure"></a>Azure’a dağıtma

Bu adımda, MySQL’e bağlı PHP uygulamasını Azure App Service'e dağıtırsınız.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Web uygulaması oluşturun

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Veritabanı ayarlarını yapılandırma

Daha önce belirtildiği gibi, Azure MySQL veritabanınıza App Service'teki ortam değişkenlerini kullanarak bağlanabilirsiniz.

Cloud Shell’de [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) komutunu kullanarak ortam değişkenlerini _uygulama ayarları_ olarak ayarlayabilirsiniz.

Aşağıdaki komut `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` ve `DB_PASSWORD` uygulama ayarlarını yapılandırır. _&lt;appname>_ ve _&lt;mysql_server_name>_ yer tutucularını değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

Cloud Shell'de, kullanarak App Service uygulamasında uygulama anahtarını ayarlayın [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) komutu. _&lt;appname>_ ve _&lt;outputofphpartisankey:generate>_ yer tutucularını değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` Dağıtılmış uygulaması hatalarla karşılaştığında laravel'den hata ayıklama bilgilerini döndürmesini ister söyler. Bir üretim uygulaması çalıştırırken daha güvenli olan `false` seçeneğine ayarlayın.

### <a name="set-the-virtual-application-path"></a>Sanal uygulama yolu ayarlama

Uygulama için sanal uygulama yolunu ayarlayın. [Laravel uygulaması yaşam döngüsü](https://laravel.com/docs/5.4/lifecycle), uygulamanın kök dizini yerine _public_ dizininde başladığı için bu adım gereklidir. Yaşam döngüsü kök dizinde başlayan diğer PHP çerçeveleri, sanal uygulama yolu el ile yapılandırılmadan çalışabilir.

Cloud Shell’de [`az resource update`](/cli/azure/resource#az-resource-update) komutunu kullanarak sanal uygulama yolunu ayarlayın. _&lt;appname>_ yer tutucusunu değiştirin.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Varsayılan olarak Azure App Service, kök sanal uygulama yolunu ( _/_ ) dağıtılmış uygulama dosyalarının kök dizinine (_sites\wwwroot_) yönlendirir.

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> Dağıtım işleminin sonunda [Composer](https://getcomposer.org/) paketleri yüklediğini fark edebilirsiniz. App Service, varsayılan dağıtım sırasında bu otomasyonları çalıştırmadığı için bu örnek depoyu etkinleştirmek üzere kök dizinde üç ek dosya bulunur:
>
> - `.deployment` - Bu dosya, App Service’ten özel dağıtım betiği olarak `bash deploy.sh` komutunu çalıştırmasını ister.
> - `deploy.sh` - Özel dağıtım betiği. Dosyayı gözden geçirirseniz, `npm install` komutundan sonra `php composer.phar install` çalıştırdığını görürsünüz.
> - `composer.phar` - Composer paket yöneticisi.
>
> App Service’e Git tabanlı dağıtımınıza herhangi bir adım eklemek için bu yaklaşımı kullanabilirsiniz. Daha fazla bilgi için bkz. [Özel Dağıtım Betiği](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-app"></a>Azure uygulamasına göz atma

`http://<app_name>.azurewebsites.net` listesine göz atın ve listeye birkaç görev ekleyin.

![Azure App Service’te çalışan PHP uygulaması](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

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

*resources/views/tasks.blade.php* dosyasını açın. `<tr>` açma etiketini arayıp şununla değiştirin:

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

![Göreve eklenen onay kutusu](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

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
git push azure master
```

Bir kez `git push` tamamlandığında, Azure uygulamasına gidin ve yeni işlevleri test edin.

![Azure’da yayımlanan model ve veritabanı değişiklikleri](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Herhangi bir görevi eklediyseniz veritabanında tutulur. Veri şemasında yapılan güncelleştirmeler var olan verileri olduğu gibi bırakır.

## <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma

PHP uygulaması Azure App Service'te çalışırken, terminalinize yönlendirilen konsol günlüklerini alabilirsiniz. Böylece, uygulama hatalarını ayıklamanıza yardımcı olan tanılama iletilerinin aynısını alabilirsiniz.

Günlük akışını başlatmak için Cloud Shell’de [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) komutunu kullanın.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Günlük akışı başlatıldıktan sonra biraz web trafiği almak için tarayıcıda Azure uygulaması yenileyin. Artık konsol günlüklerinin terminale yöneltildiğini görebilirsiniz. Konsol günlüklerini hemen görmüyorsanız, 30 saniye içinde yeniden kontrol edin.

Günlük akışını dilediğiniz zaman durdurmak için `Ctrl`+`C` yazın.

> [!TIP]
> Bir PHP uygulaması, konsol çıktısı için standart [error_log()](https://php.net/manual/function.error-log.php) seçeneğini kullanabilir. Örnek uygulama _app/Http/routes.php_ içinde bu yaklaşımı kullanır.
>
> Bir web çerçevesi olarak, [Laravel, Monolog günlük sağlayıcısını kullanır](https://laravel.com/docs/5.4/errors). Çıktı iletilerini konsola çıkarmak için nasıl görmek için bkz: [PHP: Konsolunda (php://out) oturum için monolog kullanma](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-app"></a>Azure uygulaması yönetme

Git [Azure portalında](https://portal.azure.com) oluşturduğunuz uygulamayı yönetmek için.

Sol menüden **uygulama hizmetleri**ve ardından Azure uygulamanızın adına tıklayın.

![Azure uygulamasına portal gezintisi](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Uygulamanızın genel bakış sayfasını görürsünüz. Buradan durdurma, başlatma, yeniden başlatma, göz atma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

Soldaki menü, uygulamanızı yapılandırmaya yönelik sayfalar sağlar.

![Azure portalında App Service sayfası](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure’da MySQL veritabanı oluşturma
> * PHP uygulamasını MySQL’e bağlama
> * Uygulamayı Azure'a dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

Uygulamaya özel bir DNS adı eşlemeyle ilgili bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Mevcut bir özel DNS adını Azure App Service'e eşlemek](app-service-web-tutorial-custom-domain.md)

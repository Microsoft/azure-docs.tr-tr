---
title: Postgres Linux'ta - Azure App Service ile Ruby (Rails) | Microsoft Docs
description: Azure'da çalışan ve bir PostgreSQL veritabanı ile bağlantısı olan Ruby uygulamasını nasıl edinebileceğinizi öğrenin. Rails öğreticide kullanılır.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 3ec19b1c564c09406ab1f29c38aef6332d80f8f1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130168"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Linux üzerinde Ruby ve Azure App Service'te Postgres uygulaması derleme

[Linux’ta App Service](app-service-linux-intro.md) Linux işletim sistemini kullanan yüksek oranda ölçeklenebilir, otomatik olarak düzeltme eki uygulayan bir web barındırma hizmeti sağlar. Bu öğretici bir Ruby uygulaması oluşturma ve bir PostgreSQL veritabanına bağlanma işlemi gösterilmektedir. İşiniz bittiğinde, Linux üzerinde App Service’te çalışan bir [Ruby on Rails](https://rubyonrails.org/) uygulamasına sahip olacaksınız.

![Azure App Service'te çalışan Ruby on Rails uygulaması](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure’da PostgreSQL veritabanı oluşturma
> * Ruby on Rails uygulamasını PostgreSQL'e bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Git'i yükleyin](https://git-scm.com/)
* [Ruby 2.3'ü yükleyin](https://www.ruby-lang.org/en/documentation/installation/)
* [Ruby on Rails 5.1'i yükleyin](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [PostgreSQL’i yükleyin ve çalıştırın](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>Yerel Postgres sunucusunu hazırlama

Bu adımda, bu öğreticide kullanmak üzere yerel Postgres sunucunuzda bir veritabanı oluşturursunuz.

### <a name="connect-to-local-postgres-server"></a>Yerel Postgres sunucusuna bağlanma

Yerel Postgres sunucunuza bağlanmak için terminal penceresini açın ve `psql` komutunu çalıştırın.

```bash
sudo -u postgres psql
```

Bağlantınız başarılı olursa, Postgres veritabanınız çalışır. Aksi takdirde, yerel Postgres veritabanınızın [İndirmeler - PostgreSQL Çekirdek Dağıtım](https://www.postgresql.org/download/) konusundaki adımlar izlenilerek başlatıldığından emin olun.

Postgres istemcisinden çıkmak için `\q` yazın. 

Aşağıdaki komutu oturum açtığınız Linux kullanıcı adıyla çalıştırarak veritabanı oluşturabilen bir Postgres kullanıcısı oluşturun.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Yerel olarak bir Ruby on Rails uygulaması oluşturma
Bu adımda Ruby on Rails örnek uygulaması edinir, veritabanı bağlantısını yapılandırır ve yerel olarak çalıştırırsınız. 

### <a name="clone-the-sample"></a>Örneği kopyalama

Terminal penceresinde, `cd` ile bir çalışma dizinine gidin.

Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` komutuyla kopyalanmış dizininize geçin. Gereken paketleri yükleyin.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Örneği yerel olarak çalıştırma

Uygulama için gereken tabloları oluşturmak üzere [Rails geçişlerini](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) çalıştırın. Geçişlerde hangi tabloların oluşturulduğunu görmek için, Git deposundaki _db/migrate_ dizinine bakın.

```bash
rake db:create
rake db:migrate
```

Uygulamayı çalıştırın.

```bash
rails server
```

Bir tarayıcıda `http://localhost:3000` sayfasına gidin. Sayfaya birkaç görev ekleyin.

![Ruby on Rails başarıyla Postgres'e bağlanır](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Rails sunucusunu durdurmak için terminale `Ctrl + C` yazın.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Azure'da Postgres oluşturma

Bu adımda, [PostgreSQL için Azure Veritabanı](/azure/postgresql/) içinde bir Postgres veritabanı oluşturursunuz. Daha sonra, Ruby on Rails uygulamasını bu veritabanına bağlanacak şekilde yapılandırırsınız.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Postgres sunucusu oluşturmak

[`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) komutuyla PostgreSQL sunucusu oluşturun.

Cloud Shell'de aşağıdaki komutu çalıştırın ve benzersiz bir sunucu adı için alternatif  *\<postgres sunucu adı >* yer tutucu. Sunucu adı Azure'daki tüm sunucular arasında benzersiz olmalıdır. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres-server-name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

PostgreSQL için Azure Veritabanı sunucusu oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres-server-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres-server-name>",
  "location": "westeurope",
  "name": "<postgres-server-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Sunucu güvenlik duvarını yapılandırma

Cloud Shell'de, [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create) komutunu kullanarak Postgres sunucunuzun istemci bağlantılarına izin vermesi için bir güvenlik duvarı kuralı oluşturun. Hem başlangıç hem bitiş IP’si 0.0.0.0 olarak ayarlandığında, güvenlik duvarı yalnızca diğer Azure kaynakları için açılır. Bir benzersiz sunucu adı yerine  *\<postgres sunucu adı >* yer tutucu.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres-server-name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> [Yalnızca uygulamanızın kullandığı giden IP adreslerini kullanarak](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips) güvenlik duvarı kurallarınızda daha da kısıtlayıcı olabilirsiniz.
>

### <a name="connect-to-production-postgres-server-locally"></a>Üretim Postgres sunucusuna yerel olarak bağlanma

Cloud Shell'de Azure'daki Postgres sunucusuna bağlanın. Daha önce için belirttiğiniz değeri kullanın  _&lt;postgres sunucu adı >_ yer tutucu.

```bash
psql -U adminuser@<postgres-server-name> -h <postgres-server-name>.postgres.database.azure.com postgres
```

Parola sorulduğunda, veritabanı sunucusunu oluştururken belirttiğiniz _My5up3r$tr0ngPa$w0rd!_ parolasını kullanın.

### <a name="create-a-production-database"></a>Üretim veritabanı oluşturma

`postgres` isteminde bir veritabanı oluşturun.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>İzinleri olan bir kullanıcı oluşturma

_railsappuser_ adlı bir veritabanı kullanıcısı oluşturun ve bu kullanıcıya `sampledb` veritabanındaki tüm ayrıcalıkları verin.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017';
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

`\q` yazarak sunucu bağlantısından çıkış yapın.

## <a name="connect-app-to-azure-postgres"></a>Uygulamayı Azure Postgres'e bağlama

Bu adımda, Ruby on Rails uygulamasını PostgreSQL için Azure Veritabanı içinde oluşturduğunuz Postgres veritabanına bağlarsınız.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Veritabanı bağlantısını yapılandırma

Depoda, _config/database.yml_ dosyasını açın. Dosyanın en altında, üretim değişkenlerini aşağıdaki kodla değiştirin. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Değişiklikleri kaydedin.

### <a name="test-the-application-locally"></a>Uygulamayı yerel olarak test etme

Yerel terminale geri döndüğünüzde, aşağıdaki ortam değişkenlerini ayarlayın:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres-server-name>
export DB_PASSWORD=MyPostgresAzure2017
```

PostgreSQL için Azure Veritabanı (Önizleme) içinde tablolar oluşturmak için, Postgres veritabanı geçişlerini yeni yapılandırdığınız üretim değerleriyle çalıştırın.

```bash
rake db:migrate RAILS_ENV=production
```

Üretim ortamında çalıştırırken, Rails uygulamasına önceden derlenmiş varlıklar gerekir. Aşağıdaki komutu kullanarak gerekli varlıkları oluşturun:

```bash
rake assets:precompile
```

Ayrıca Rails üretim ortamı güvenliği yönetmek için gizli diziler kullanır. Gizli anahtar oluşturun.

```bash
rails secret
```

Gizli anahtarı, Rails üretim ortamı tarafından kullanılan ilgili değişkenlere kaydedin. Kolaylık olması için, her iki değişken için de aynı anahtarı kullanırsınız.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

JavaScript ve CSS dosyalarına hizmet vermek için Rails üretim ortamını etkinleştirin.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Örnek uygulamayı üretim ortamında çalıştırın.

```bash
rails server -e production
```

`http://localhost:3000` sayfasına gidin. Sayfa hatasız yüklenirse, Ruby on Rails uygulaması Azure’da Postgres veritabanına bağlanıyor demektir.

Sayfaya birkaç görev ekleyin.

![Ruby on Rails, PostgreSQL için Azure Veritabanına başarıyla bağlanır](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Rails sunucusunu durdurmak için terminale `Ctrl + C` yazın.

### <a name="commit-your-changes"></a>Değişikliklerinizi kaydetme

Değişikliklerinizi kaydetmek için aşağıdaki Git komutlarını çalıştırın:

```bash
git add .
git commit -m "database.yml updates"
```

Uygulamanız dağıtılmaya hazırdır.

## <a name="deploy-to-azure"></a>Azure’a dağıtma

Bu adımda, Postgres'e bağlı Rails uygulamasını Azure App Service'e dağıtırsınız.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web uygulaması oluşturma

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Veritabanı ayarlarını yapılandırma

App Service’te, Cloud Shell'de [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) komutunu kullanarak ortam değişkenlerini _uygulama ayarları_ olarak ayarlayabilirsiniz.

Aşağıdaki Cloud Shell komutu `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` ve `DB_PASSWORD` uygulama ayarlarını yapılandırır. Yer tutucuları değiştirmeniz  _&lt;uygulamaadı >_ ve  _&lt;postgres sunucu adı >_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres-server-name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Rails ortam değişkenlerini yapılandırma

Yerel terminalde [yeni bir gizli dizi](configure-language-ruby.md#set-secret_key_base-manually) azure'da Rails üretim ortamı.

```bash
rails secret
```

Rails üretim ortamına gereken değişkenleri yapılandırın.

Aşağıdaki Cloud Shell iki komutta  _&lt;çıkış, rails gizli >_ yer tutucusunu yerel terminalde oluşturduğunuz yeni gizli anahtar.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`, varsayılan Ruby kapsayıcısına her Git dağıtımında varlıkları yeniden derlemesini bildirir. Daha fazla bilgi için [varlıklar ön derleme](configure-language-ruby.md#precompile-assets) ve [hizmet statik varlıklar](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

Yerel terminalde yerel Git deponuza bir Azure uzak deposu ekleyin.

```bash
git remote add azure <paste-copied-url-here>
```

Ruby on Rails uygulamasını dağıtmak için Azure uzak deposuna gönderin. Daha önce dağıtım kullanıcısı oluştururken belirttiğiniz parola istenir.

```bash
git push azure master
```

Dağıtım sırasında Azure App Service, ilerleme durumunu Git'e iletir.

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

### <a name="browse-to-the-azure-app"></a>Azure uygulamasına göz atma

`http://<app-name>.azurewebsites.net` listesine göz atın ve listeye birkaç görev ekleyin.

![Azure App Service'te çalışan Ruby on Rails uygulaması](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Tebrikler, Azure App Service'te veri temelli bir Ruby on Rails uygulaması çalıştırıyorsunuz.

## <a name="update-model-locally-and-redeploy"></a>Modeli yerel olarak güncelleştirme ve yeniden dağıtma

Bu adımda, `task` veri modeli ve web uygulamasında basit bir değişiklik yaptıktan sonra güncelleştirmeyi Azure'da yayımlarsınız.

Görevler senaryosu için, görevi tamamlandı olarak işaretleyebileceğiniz şekilde uygulamayı değiştirirsiniz.

### <a name="add-a-column"></a>Sütun ekleme

Terminalde Git deposunun kök dizinine gidin.

`Tasks` tablosuna `Done` adlı bir Boole sütunu ekleyen yeni bir geçiş oluşturun:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Bu komut yeni geçiş dosyasını _db/migrate_ dizininde oluşturur.


Terminalde, Rails veritabanı geçişlerini çalıştırarak yerel veritabanında değişikliği yapın.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Uygulama mantığını güncelleştirme

*app/controllers/tasks_controller.rb* dosyasını açın. Dosyanın en sonunda şu satırı bulun:

```rb
params.require(:task).permit(:Description)
```

Bu satırı, yeni `Done` parametresini içerecek şekilde değiştirin.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Görünümleri güncelleştirme

Düzenleme formu olan *app/views/tasks/_form.html.erb* dosyasını açın.

`<%=f.error_span(:Description) %>` satırını bulun ve bu satırın hemen altına aşağıdaki kodu ekleyin:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Tek kayıtlı Görünüm sayfası olan *app/views/tasks/show.html.erb* dosyasını açın. 

`<dd><%= @task.Description %></dd>` satırını bulun ve bu satırın hemen altına aşağıdaki kodu ekleyin:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Tüm kayıtların dizin sayfası olan *app/views/tasks/index.html.erb* dosyasını açın.

`<th><%= model_class.human_attribute_name(:Description) %></th>` satırını bulun ve bu satırın hemen altına aşağıdaki kodu ekleyin:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Aynı dosyada, `<td><%= task.Description %></td>` satırını bulun ve bu satırın hemen altına aşağıdaki kodu ekleyin:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Değişiklikleri yerel olarak test etme

Yerel terminalde Rails sunucusunu çalıştırın.

```bash
rails server
```

Görev durumu değişikliğini görmek için `http://localhost:3000` öğesine gidip öğeleri ekleyin veya düzenleyin.

![Göreve eklenen onay kutusu](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Rails sunucusunu durdurmak için terminale `Ctrl + C` yazın.

### <a name="publish-changes-to-azure"></a>Değişiklikleri Azure’da yayımlama

Terminalde, üretim ortamı için Rails veritabanı geçişlerini çalıştırarak değişikliği Azure veritabanında yapın.

```bash
rake db:migrate RAILS_ENV=production
```

Tüm değişiklikleri Git’e kaydedin ve ardından kod değişikliklerini Azure’a gönderin.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Bir kez `git push` tamamlandığında, Azure uygulamasına gidin ve yeni işlevleri test edin.

![Azure’da yayımlanan model ve veritabanı değişiklikleri](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Herhangi bir görevi eklediyseniz veritabanında tutulur. Veri şemasında yapılan güncelleştirmeler var olan verileri olduğu gibi bırakır.

## <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Azure uygulaması yönetme

Git [Azure portalında](https://portal.azure.com) oluşturduğunuz uygulamayı yönetmek için.

Sol menüden **uygulama hizmetleri**ve ardından Azure uygulamanızın adına tıklayın.

![Azure uygulamasına portal gezintisi](./media/tutorial-php-mysql-app/access-portal.png)

Uygulamanızın genel bakış sayfasını görürsünüz. Buradan durdurma, başlatma, yeniden başlatma, göz atma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

Soldaki menü, uygulamanızı yapılandırmaya yönelik sayfalar sağlar.

![Azure portalında App Service sayfası](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure'da Postgres veritabanı oluşturma
> * Ruby on Rails uygulamasını Postgres'e bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

Uygulamanıza özel bir DNS adı eşlemeyle ilgili bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: Uygulamanıza özel DNS adı eşleme](../app-service-web-tutorial-custom-domain.md)

Ya da diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [Ruby uygulamasını yapılandırma](configure-language-ruby.md)
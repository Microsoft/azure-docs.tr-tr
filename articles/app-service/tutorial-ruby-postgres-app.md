---
title: 'Öğretici: Postgres ile Linux Ruby uygulaması'
description: Azure 'da bir PostgreSQL veritabanına bağlantı ile Azure App Service çalışan Linux Ruby uygulamasını nasıl alabileceğinizi öğrenin. Rayda öğreticide kullanılır.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: 63194ab87e0f2228b8585e962394aa1ebfff48d6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767276"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Linux üzerinde Azure App Service Ruby ve Postgres uygulaması oluşturma

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Bu öğreticide, bir Ruby uygulamasının nasıl oluşturulacağı ve bir PostgreSQL veritabanına nasıl bağlanacağı gösterilmektedir. İşiniz bittiğinde, Linux üzerinde App Service’te çalışan bir [Ruby on Rails](https://rubyonrails.org/) uygulamasına sahip olacaksınız.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Ruby on rayın ekran görüntüsü görevler başlıklı uygulama örneği.":::

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure’da PostgreSQL veritabanı oluşturma
> * Ruby on Rails uygulamasını PostgreSQL'e bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- [Git'i yükleyin](https://git-scm.com/)
- [Ruby 2,6 'yi yükler](https://www.ruby-lang.org/en/documentation/installation/)
- [Ruby on Rails 5.1'i yükleyin](https://guides.rubyonrails.org/v5.1/getting_started.html)
- [PostgreSQL’i yükleme ve çalıştırma](https://www.postgresql.org/download/)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

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

### <a name="clone-the-sample"></a>Örneği

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

## <a name="create-postgres-in-azure"></a>Azure'da Postgres oluşturma

Bu adımda, [PostgreSQL için Azure Veritabanı](../postgresql/index.yml) içinde bir Postgres veritabanı oluşturursunuz. Daha sonra, Ruby on Rails uygulamasını bu veritabanına bağlanacak şekilde yapılandırırsınız.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Azure 'da Postgres veritabanı oluşturma

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Bu bölümde, PostgreSQL için Azure veritabanı sunucusu ve veritabanı oluşturacaksınız. Başlamak için, `db-up` uzantıyı aşağıdaki komutla birlikte yüklemelisiniz:

```azurecli
az extension add --name db-up
```

[`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up)Aşağıdaki örnekte gösterildiği gibi, komutuyla Azure 'Da Postgres veritabanını oluşturun. *\<postgresql-name>* *Benzersiz* bir adla değiştirin (sunucu uç noktası *https:// \<postgresql-name> . Postgres.Database.Azure.com*). *\<admin-username>* Ve için *\<admin-password>* , bu Postgres sunucusu için bir yönetici kullanıcı oluşturmak üzere kimlik bilgilerini belirtin.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Bu komut şu işlemleri yaparken biraz zaman alabilir:

- Mevcut değilse adlı bir [kaynak grubu](../azure-resource-manager/management/overview.md#terminology) oluşturur `myResourceGroup` . Her Azure kaynağının bunlardan birinde olması gerekir. `--resource-group` isteğe bağlıdır.
- Yönetici kullanıcıyla bir Postgres sunucusu oluşturur.
- Bir `sampledb` veritabanı oluşturur.
- Yerel IP adresinizden erişime izin verir.
- Azure hizmetlerinden erişime izin verir.
- Veritabanına erişimi olan bir veritabanı kullanıcısı oluşturun `sampledb` .

Tüm adımları diğer komutlarla ayrı olarak yapabilirsiniz `az postgres` `psql` , ancak `az postgres up` Tümünü tek bir adımda yapabilirsiniz.

Komut tamamlandığında, ile olan çıktı satırlarını bulun `Ran Database Query:` . Kullanıcı adı ve parolasıyla, sizin için oluşturulan veritabanı kullanıcısını gösterir `root` `Sampledb1` . Uygulamanızı daha sonra veritabanına bağlamak için kullanacaksınız.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, [Azure bölgelerinden](https://azure.microsoft.com/global-infrastructure/regions/)herhangi birine ayarlanabilir. Komutu ile aboneliğiniz için kullanılabilir bölgeleri edinebilirsiniz [`az account list-locations`](/cli/azure/account#az_account_list_locations) . Üretim uygulamaları için, veritabanınızı ve uygulamanızı aynı konuma koyun.

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
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
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

## <a name="deploy-to-azure"></a>Azure’a dağıtın

Bu adımda, Postgres'e bağlı Rails uygulamasını Azure App Service'e dağıtırsınız.

### <a name="configure-a-deployment-user"></a>Dağıtım kullanıcısı yapılandırma

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Web uygulaması oluşturma

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Veritabanı ayarlarını yapılandırma

App Service’te, Cloud Shell'de [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) komutunu kullanarak ortam değişkenlerini _uygulama ayarları_ olarak ayarlayabilirsiniz.

Aşağıdaki Cloud Shell komutu `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` ve `DB_PASSWORD` uygulama ayarlarını yapılandırır. _&lt; Appname>_ ve _&lt; Postgres-Server-Name>_ yer tutucularını değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Rails ortam değişkenlerini yapılandırma

Yerel terminalde Azure 'da [raya üretim ortamı için yeni bir gizli dizi oluşturun](configure-language-ruby.md#set-secret_key_base-manually) .

```bash
rails secret
```

Rails üretim ortamına gereken değişkenleri yapılandırın.

Aşağıdaki Cloud Shell komutunda, iki _&lt; raya-gizli>_ yer tutucuları yerel terminalde oluşturduğunuz yeni gizli anahtar ile değiştirin.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`, varsayılan Ruby kapsayıcısına her Git dağıtımında varlıkları yeniden derlemesini bildirir. Daha fazla bilgi için bkz. [varlıkları önceden derleme](configure-language-ruby.md#precompile-assets) ve [statik varlıkları](configure-language-ruby.md#serve-static-assets)sunma.

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

Yerel terminalde yerel Git deponuza bir Azure uzak deposu ekleyin.

```bash
git remote add azure <paste-copied-url-here>
```

Ruby on Rails uygulamasını dağıtmak için Azure uzak deposuna gönderin. Daha önce dağıtım kullanıcısı oluştururken belirttiğiniz parola istenir.

```bash
git push azure main
```

Dağıtım sırasında Azure App Service, ilerleme durumunu Git'e iletir.

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

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Listeye eklenen görevleri gösteren görevler adlı Azure uygulaması örneği ekran görüntüsü.":::

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
git push azure main
```

Tamamlandıktan sonra `git push` Azure uygulamasına gidin ve yeni işlevleri test edin.

![Azure’da yayımlanan model ve veritabanı değişiklikleri](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Herhangi bir görevi eklediyseniz veritabanında tutulur. Veri şemasında yapılan güncelleştirmeler var olan verileri olduğu gibi bırakır.

## <a name="stream-diagnostic-logs"></a>Tanılama günlüklerini akışla aktarma

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

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
> * Azure'da Postgres veritabanı oluşturma
> * Ruby on Rails uygulamasını Postgres'e bağlama
> * Uygulamayı Azure’da dağıtma
> * Veri modelini güncelleştirme ve uygulamayı yeniden dağıtma
> * Azure’daki tanılama günlüklerinin akışını sağlama
> * Uygulamayı Azure portalında yönetme

Özel bir DNS adını uygulamanıza nasıl eşleyeceğinizi öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: özel DNS adını uygulamanıza eşleyin](app-service-web-tutorial-custom-domain.md)

Ya da diğer kaynaklara göz atın:

> [!div class="nextstepaction"]
> [Ruby uygulamasını yapılandırma](configure-language-ruby.md)

---
title: 'Öğretici: Sanal ağda App Service ve PostgreSQL için Azure veritabanı ile Docgo uygulaması dağıtma-esnek sunucu (Önizleme)'
description: Docgo uygulamasını uygulama Serice ve PostgreSQL için Azure veritabanı-sanal ağ 'da esnek sunucu (Önizleme) ile dağıtma
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 84fd0c368bcf39af1bae90dc83336ffdb8489768
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791526"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Öğretici: App Service ve PostgreSQL için Azure veritabanı ile Docgo uygulaması dağıtma-esnek sunucu (Önizleme)

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir

Bu öğreticide, Azure 'da uygulama hizmetleri ve PostgreSQL için Azure veritabanı ile bir sanal ağ içindeki esnek sunucu aracılığıyla Docgo uygulamasını dağıtmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

Bu makalede, Azure CLı sürüm 2,0 veya üstünü yerel olarak çalıştırıyor olmanız gerekir. Yüklü sürümü görmek için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

[Az Login](/cli/azure/authenticate-azure-cli) komutunu kullanarak hesabınızda oturum açmanız gerekir. Komut çıktısındaki ilgili abonelik adına karşılık gelen **id** özelliğinin değerini not edin.

```azurecli
az login
```

Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. [az account set](/cli/azure/account) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin. **Abonelik kimliği özelliğini aboneliğiniz** için **az oturum açma** çıktısından abonelik kimliği yer tutucusuna koyun.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Örnek uygulamayı kopyalayın veya indirin

# <a name="git-clone"></a>[Git kopyası](#tab/clone)

Örnek depoyu kopyalayın:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Daha sonra bu klasöre gidin:

```terminal
cd djangoapp
```

# <a name="download"></a>[İndir](#tab/download)

Web sitesini ziyaret edin [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) ve sonra ZIP 'ı **İndir** **' i** seçin.

ZIP dosyasını *docgoapp* adlı bir klasöre ayıklayın.

Ardından bu *docgoapp* klasöründe bir Terminal penceresi açın.

---

Docgoapp örneği, Docgo belgelerine [Ilk Docgo uygulamanızı yazmayı](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) izleyerek aldığınız veri tabanlı docgo yoklamaları uygulamasını içerir. Tamamlanmış uygulama size kolaylık sağlaması için verilmiştir.

Örnek ayrıca App Service gibi bir üretim ortamında çalışacak şekilde değiştirilmiştir:

- Üretim ayarları *azuresite/Production. Kopyala* dosyasında bulunur. Geliştirme ayrıntıları *azuresite/Settings. Kopyala*' da bulunur.
- Uygulama, `DJANGO_ENV` ortam değişkeni "üretim" olarak ayarlandığında üretim ayarlarını kullanır. Bu ortam değişkenini daha sonra öğreticide, PostgreSQL veritabanı yapılandırması için kullanılan diğer kullanıcılarla birlikte oluşturursunuz.

Bu değişiklikler, DMİ git 'in herhangi bir üretim ortamında çalıştırılmasını ve App Service özel olmadığını yapılandırmaya özgüdür. Daha fazla bilgi için bkz. [Docgo dağıtım denetim listesi](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Yeni bir sanal ağda bir PostgreSQL esnek sunucusu oluşturma

Aşağıdaki komutu kullanarak bir sanal ağ (VNET) içinde özel bir esnek sunucu ve veritabanı oluşturun:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Bu komut, birkaç dakika sürebilen aşağıdaki eylemleri gerçekleştirir:

- Zaten mevcut değilse kaynak grubunu oluşturun.
- Sağlanmazsa bir sunucu adı oluşturur.
- Yeni postgreSQL sunucunuz için yeni bir sanal ağ oluşturun. Web uygulamasını aynı sanal ağa eklemeniz gerektiğinden, sunucunuz için oluşturulan **sanal ağ adını ve alt ağ adını bir yere unutmayın** .
- Sağlanmazsa, sunucunuz için Yönetici Kullanıcı adı, parola oluşturur. Bir sonraki adımda kullanılacak **Kullanıcı adını ve parolayı bir yere unutmayın** .
- ```postgres```Geliştirme için kullanılabilecek bir veritabanı oluşturun. Farklı bir veritabanı oluşturmak için [, **psql** 'i veritabanına bağlanmak üzere](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) çalıştırabilirsiniz.

> [!NOTE]
> Sağlanmazsa, sizin için üretilecek olan parolanızı not edin. Parolayı unutursanız, komutunu kullanarak parolayı sıfırlamanız gerekir ``` az postgres flexible-server update```


## <a name="deploy-the-code-to-azure-app-service"></a>Kodu Azure App Service dağıtma

Bu bölümde App Service uygulamasında uygulama konağı oluşturur, bu uygulamayı Postgres veritabanına bağlayın ve ardından kodunuzu bu konağa dağıtın.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Bir sanal ağda App Service Web uygulaması oluşturma

Terminalde, uygulama kodunu içeren depo kökünde olduğunuzdan emin olun `djangoapp` .

Komutuyla bir App Service uygulaması (ana bilgisayar işlemi) oluşturun [`az webapp up`](/cli/azure/webapp#az_webapp_up) :

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- `--location`Bağımsız değişkeni için, önceki bölümde veritabanıyla aynı konumu kullanın.
- *\<app-name>* Tüm Azure genelinde benzersiz bir adla değiştirin (sunucu uç noktası `https://\<app-name>.azurewebsites.net` ). *\<app-name>* `A` - `Z` , `0` - `9` Ve `-` için izin verilen karakterler. İyi bir model, şirketinizin adının ve uygulama tanımlayıcısının bir birleşimini kullanmaktır.
- Mevcut değilse, temel fiyatlandırma katmanında (B1) *docgopostgres-öğretici-plan* [App Service planını](../../app-service/overview-hosting-plans.md) oluşturun. `--plan` ve `--sku` isteğe bağlıdır.
- Yoksa App Service uygulamasını oluşturun.
- Zaten etkinleştirilmemişse, uygulama için varsayılan günlüğü etkinleştirin.
- Derleme Otomasyonu etkinken ZIP dağıtımını kullanarak depoyu karşıya yükleyin.
- **az WebApp VNET-Integration** komutu, Web uygulamasını Postgres sunucusuyla aynı sanal ağa ekler.
- Uygulama kodu, çeşitli ortam değişkenlerinde veritabanı bilgilerini bulmayı bekler. App Service ortam değişkenlerini ayarlamak için, [az WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) komutuyla "uygulama ayarları" oluşturursunuz.

> [!TIP]
> Birçok Azure CLı komutu, kaynak grubunun adı ve App Service planı gibi ortak parametreleri önbelleğe *. Azure/config* dosyasına dönüştürür. Sonuç olarak, sonraki komutlarla aynı parametreyi belirtmeniz gerekmez. Örneğin, değişiklikleri yaptıktan sonra uygulamayı yeniden dağıtmak için `az webapp up` herhangi bir parametre olmadan yalnızca tekrar çalıştırabilirsiniz.

### <a name="run-django-database-migrations"></a>Docgo veritabanı geçişlerini Çalıştır

Docgo veritabanı geçişleri, Azure veritabanı 'ndaki PostgreSQL içindeki şemanın kodunuzda açıklananlarla eşleştiğinden emin olun.

1. *Https:// \<app-name> . scm.azurewebsites.net/webssh/Host* sayfasına giderek ve Azure hesabı kimlik bilgilerinizle (veritabanı sunucusu kimlik bilgileri değil) oturum açarak tarayıcıda bir SSH oturumu açın.

1. SSH oturumunda aşağıdaki komutları çalıştırın ( **CTRL** + **SHIFT** + **V** kullanarak komutları yapıştırabilirsiniz):

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. Bu `createsuperuser` komut sizden süper kullanıcı kimlik bilgilerini ister. Bu öğreticinin amaçları doğrultusunda, varsayılan kullanıcı adını kullanın `root` , e-posta adresi Için **ENTER** tuşuna basarak boş bırakın ve `postgres1` parola girin.

### <a name="create-a-poll-question-in-the-app"></a>Uygulamada bir yoklama sorusu oluşturma

1. Bir tarayıcıda *http: \/ / \<app-name> . azurewebsites.net* URL 'sini açın. Veritabanında henüz belirli bir yoklama olmadığından, uygulamanın "hiçbir yoklama yok" iletisini görüntülemesi gerekir.

1. *Http: \/ / \<app-name> . azurewebsites.net/admin* öğesine gidin. Önceki bölümde bulunan Süper Kullanıcı kimlik bilgilerini kullanarak oturum açın ( `root` ve `postgres1` ). **Yoklamalar** altında, **sorular** ' ın yanındaki **Ekle** ' yi seçin ve bazı seçeneklerle bir yoklama sorusu oluşturun.

1. Soruların artık kullanıcıya sunulduğunu doğrulamak için *http: \/ / \<app-name> . azurewebsites.net/* öğesine tekrar gidin. Bu soruları yanıtlayın, ancak veritabanında bazı veriler oluşturmak istersiniz.

**Tebrikler!** Linux için Azure App Service, etkin bir Postgres veritabanıyla bir Python Docgo Web uygulaması çalıştırıyorsunuz.

> [!NOTE]
> App Service, varsayılan olarak oluşturulan her alt klasörde bir *wsgi.py* dosyası arayarak bir Docgo projesi algılar `manage.py startproject` . App Service bu dosyayı bulduğunda, Docgo Web uygulamasını yükler. Daha fazla bilgi için bkz. [yerleşik Python görüntüsünü yapılandırma](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Kod değişiklikleri yap ve yeniden Dağıt

Bu bölümde, uygulamada yerel değişiklikler yapar ve kodu App Service yeniden dağıtabilirsiniz. İşleminde, devam eden işleri destekleyen bir Python sanal ortamı ayarlarsınız.

### <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

Bir terminal penceresinde aşağıdaki komutları çalıştırın. Süper Kullanıcı oluştururken istemleri izlediğinizden emin olun:

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```
Web uygulaması tam olarak yüklendikten sonra, Dmongo geliştirme sunucusu, "geliştirme sunucusu başlatılıyor" iletisinde yerel uygulama URL 'sini sağlar http://127.0.0.1:8000/ . CTRL-BREAK ile sunucudan çıkın.

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Örnek Docgo geliştirme sunucusu çıkışı":::

Uygulamayı aşağıdaki adımlarla yerel olarak test edin:

1. *Http: \/ /localhost: 8000* adresine giderek bir tarayıcıda "hiçbir yoklama yok" iletisini görüntülemesi gerekir.

1. *Http: \/ /localhost: 8000/admin* adresine gidin ve daha önce oluşturduğunuz yönetici kullanıcıyı kullanarak oturum açın. **Yoklamalar**' ın altında, **sorular** ' ın yanındaki **Ekle** ' yi seçin ve bazı seçeneklerle bir yoklama sorusu oluşturun.

1. *Http: \/ /localhost: 8000* ' e gidin ve uygulamayı test etmek için soruyu yanıtlayın.

1. **CTRL** C tuşlarına basarak Docgo sunucusunu durdurun + .

Yerel olarak çalıştırılırken, uygulama yerel bir SQLite3 veritabanı kullanıyor ve üretim veritabanınızı engellemez. İsterseniz üretim ortamınızın benzetimini yapmak için bir yerel PostgreSQL veritabanı da kullanabilirsiniz.



### <a name="update-the-app"></a>Uygulamayı güncelleştirme

İçinde `polls/models.py` , ile başlayan satırı bulun `choice_text` ve `max_length` parametreyi 100 olarak değiştirin:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Veri modelini değiştirdiğiniz için yeni bir Docgo geçişi oluşturun ve veritabanını geçirin:

```
python manage.py makemigrations
python manage.py migrate
```

Geliştirme sunucusunu ile yeniden çalıştırın `python manage.py runserver` ve uygulamayı *http: \/ /localhost: 8000/admin* konumunda test edin:

Docgo Web sunucusunu **CTRL** + **C** ile yeniden durdurun.


### <a name="redeploy-the-code-to-azure"></a>Kodu Azure 'a yeniden dağıtın

Depo kökünde aşağıdaki komutu çalıştırın:

```azurecli
az webapp up
```

Bu komut *. Azure/config* dosyasında önbelleğe alınan parametreleri kullanır. App Service, uygulamanın zaten var olduğunu algıladığında kodu yeniden dağıtır.



### <a name="rerun-migrations-in-azure"></a>Azure 'da geçişleri yeniden çalıştırma

Veri modelinde değişiklikler yaptığınız için, App Service veritabanı geçişlerini yeniden çalıştırmanız gerekir.

*Https:// \<app-name> . scm.azurewebsites.net/webssh/Host* SAYFASıNA giderek bir SSH oturumunu tarayıcıda tekrar açın. Sonra aşağıdaki komutları çalıştırın:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Üretimde uygulamayı gözden geçirin

*Http: \/ / \<app-name> . azurewebsites.net* öğesine gidin ve uygulamayı üretimde yeniden test edin. (Yalnızca bir veritabanı alanının uzunluğunu değiştirdiğiniz için, değişiklik yalnızca bir soru oluştururken daha uzun bir yanıt girmeyi denerseniz fark edilir.)

> [!TIP]
> Azure depolama 'da statik & medya varlıklarını depolamak için [docgo-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) kullanabilirsiniz. Statik dosyalar için gzipping için Azure CDN kullanabilirsiniz.


## <a name="manage-your-app-in-the-azure-portal"></a>Uygulamanızı Azure portal yönetme

[Azure Portal](https://portal.azure.com), uygulama adını arayın ve sonuçlarda uygulamayı seçin.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Azure portal Python Docgo uygulamanıza gidin":::

Varsayılan olarak, portal genel bir performans görünümü sağlayan uygulamanızın **genel bakış** sayfasını gösterir. Burada, gezinme, durdurma, yeniden başlatma ve silme gibi temel yönetim görevlerini de gerçekleştirebilirsiniz. Sayfanın sol tarafındaki sekmeler, açabileceğiniz farklı yapılandırma sayfalarını gösterir.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Python Docgo uygulamanızı Azure portal genel bakış sayfasında yönetin":::


## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamayı sürdürmek veya sonraki öğreticiye devam etmek isterseniz, [sonraki adımlara](#next-steps)atlayın. Aksi takdirde, devam eden ücretleri önlemek için bu öğreticide kaynak grubu oluştur ' u silebilirsiniz:

```azurecli
az group delete -g myresourcegroup
```

Komut *. Azure/config* dosyasında önbelleğe alınmış kaynak grubu adını kullanır. Kaynak grubunu silerek, içinde yer alan tüm kaynakları serbest bırakabilir ve silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanıza özel bir DNS adı eşlemeyi öğrenin:

> [!div class="nextstepaction"]
> [Öğretici: özel DNS adını uygulamanıza eşleyin](../../app-service/app-service-web-tutorial-custom-domain.md)

App Service bir Python uygulamasını nasıl yürüttüğinizi öğrenin:

> [!div class="nextstepaction"]
> [Python uygulamasını yapılandırma](../../app-service/configure-language-python.md)

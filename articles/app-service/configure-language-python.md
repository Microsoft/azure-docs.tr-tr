---
title: Linux Python uygulamalarını yapılandırma
description: Hem Azure portal hem de Azure CLı kullanarak Web uygulamalarının çalıştırıldığı Python kapsayıcısını nasıl yapılandıracağınızı öğrenin.
ms.topic: quickstart
ms.date: 03/16/2021
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 605d1e0f67ac959d2c7325e04e2fd10d9d2419be
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829503"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Azure App Service için bir Linux Python uygulaması yapılandırma

Bu makalede, [Azure App Service](overview.md) Python uygulamalarının nasıl çalıştığı, mevcut uygulamaları Azure 'a nasıl geçirebileceğiniz ve gerektiğinde App Service davranışının nasıl özelleştirileceği açıklanır. Python uygulamalarının tüm gerekli [PIP](https://pypi.org/project/pip/) modülleri ile dağıtılması gerekir.

App Service dağıtım altyapısı bir sanal ortamı otomatik olarak etkinleştirir ve `pip install -r requirements.txt` bir [Git deposu](deploy-local-git.md)veya bir [ZIP paketi](deploy-zip.md)dağıttığınızda sizin için çalışır.

Bu kılavuz, App Service içinde yerleşik bir Linux kapsayıcısı kullanan Python geliştiricileri için temel kavramlar ve yönergeler sağlar. Azure App Service hiç kullanmadıysanız, önce [Python hızlı](quickstart-python.md) başlangıcı ' nı ve [PostgreSQL öğreticisi ile Python](tutorial-python-postgresql-app.md)'u izleyin.

Yapılandırma için [Azure Portal](https://portal.azure.com) ya da Azure CLI 'yi kullanabilirsiniz:

- **Azure Portal**,   >  [Azure Portal App Service uygulama yapılandırma](configure-common.md)sayfasında açıklandığı şekilde uygulamanın Ayarlar **yapılandırma** sayfasını kullanın.

- **Azure CLI**: iki seçeneğiniz vardır.

    - [Azure Cloud Shell](../cloud-shell/overview.md)komutları çalıştırın.
    - [Azure CLI](/cli/azure/install-azure-cli)'nın en son sürümünü yükleyerek komutları yerel olarak çalıştırın ve [az Login](/cli/azure/reference-index#az_login)komutunu kullanarak Azure 'da oturum açın.
    
> [!NOTE]
> Linux Şu anda App Service ' de Python uygulamalarını çalıştırmak için önerilen seçenektir. Windows seçeneği hakkında bilgi için, [App Service Windows üzerinde Python](/visualstudio/python/managing-python-on-azure-app-service)konusuna bakın.

## <a name="configure-python-version"></a>Python sürümünü Yapılandır

- **Azure Portal**: **yapılandırma** sayfasında, Linux kapsayıcıları için [genel ayarları yapılandırma](configure-common.md#configure-general-settings) bölümünde açıklandığı gibi **Genel ayarlar** sekmesini kullanın.

- **Azure CLI**:

    -  [Az WebApp config Show](/cli/azure/webapp/config#az_webapp_config_show)komutuyla geçerli Python sürümünü göster:
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        `<resource-group-name>`Ve ' i `<app-name>` Web uygulamanız için uygun adlarla değiştirin.
    
    - Python sürümünü [az WebApp config Set](/cli/azure/webapp/config#az_webapp_config_set) ile ayarla
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - [Az WebApp List-çalışma zamanları](/cli/azure/webapp#az_webapp_list_runtimes)ile Azure App Service desteklenen tüm Python sürümlerini gösterin:
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Bunun yerine kendi kapsayıcı görüntünüzü oluşturarak desteklenmeyen bir Python sürümü çalıştırabilirsiniz. Daha fazla bilgi için bkz. [Özel Docker görüntüsü kullanma](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Derleme Otomasyonu 'nu özelleştirme

App Service, Oryx olarak adlandırılan yapı sistemi, git veya ZIP paketleri kullanarak uygulamanızı dağıtırken aşağıdaki adımları gerçekleştirir:

1. Bu ayar tarafından belirtilmişse özel bir ön derleme betiği çalıştırın `PRE_BUILD_COMMAND` . (Komut dosyası, diğer Python ve Node.js betikleri, PI ve NPM komutlarını ve Yarn gibi düğüm tabanlı araçları ve örneğin, `yarn install` ve `yarn build` .) çalıştırabilir.

1. `pip install -r requirements.txt` öğesini çalıştırın. *requirements.txt* dosya projenin kök klasöründe bulunmalıdır. Aksi takdirde, yapı işlemi şu hatayı raporlar: "setup.py bulunamadı veya requirements.txt; Pınstall çalışmıyor. "

1. Depo kökünde *Manage.py* bulunursa (bir Docgo uygulaması olduğunu), *Manage.py collectstatic* komutunu çalıştırın. Ancak, `DISABLE_COLLECTSTATIC` ayar ise, `true` Bu adım atlanır.

1. Ayar tarafından belirtilmişse özel derleme sonrası betiği çalıştırın `POST_BUILD_COMMAND` . (Yine de betik, diğer Python ve Node.js betikleri, PIP ve NPM komutları ve düğüm tabanlı araçları çalıştırabilir.)

Varsayılan olarak,, `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` ve `DISABLE_COLLECTSTATIC` ayarları boştur. 

- Docgo uygulamaları oluştururken collectstatic çalışmasını devre dışı bırakmak için `DISABLE_COLLECTSTATIC` ayarı true olarak ayarlayın.

- Oluşturma öncesi komutları çalıştırmak için, `PRE_BUILD_COMMAND` ayarı gibi bir komut `echo Pre-build command` ya da proje kökle ilişkili bir betik dosyasının yolunu (gibi) olarak ayarlayın `scripts/prebuild.sh` . Tüm komutların, proje kök klasörü için göreli yollar kullanması gerekir.

- Oluşturma sonrası komutları çalıştırmak için, `POST_BUILD_COMMAND` ayarı gibi bir komut ya da, `echo Post-build command` Proje kökle ilişkili bir betik dosyasının yolunu (gibi) ya da gibi ayarlayın `scripts/postbuild.sh` . Tüm komutların, proje kök klasörü için göreli yollar kullanması gerekir.

Derleme Otomasyonu 'nu özelleştiren ek ayarlar için bkz. [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Derleme ve dağıtım günlüklerine erişmek için bkz. [erişim dağıtım günlükleri](#access-deployment-logs).

App Service çalışma ve Linux 'ta Python uygulamaları oluşturma hakkında daha fazla bilgi için bkz. [Python uygulamalarını ne şekilde algıladığını ve bunları yapılandırma](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> Ve ayarları, ve ile `PRE_BUILD_SCRIPT_PATH` `POST_BUILD_SCRIPT_PATH` aynıdır `PRE_BUILD_COMMAND` ve `POST_BUILD_COMMAND` eski amaçlar için desteklenir.
> 
> `SCM_DO_BUILD_DURING_DEPLOYMENT`Ya da 1 içeriyorsa adlı bir ayar `true` , dağıtım sırasında bir Oryx derlemesini tetikler. Bu ayar, git kullanılarak dağıtıldığında, Azure CLı komutuyla `az webapp up` ve Visual Studio Code doğru olur.

> [!NOTE]
> Oryx 'in çalıştığı yapı kapsayıcısı uygulamanın çalıştığı çalışma zamanı kapsayıcısından farklı olduğundan, her zaman tüm ön ve derleme sonrası betiklerdeki göreli yolları kullanın. Uygulama Projesi klasörünüzün kapsayıcı içinde tam olarak yerleştirilme (örneğin, *site/Wwwroot* altına yerleştirilmiş olması) hiçbir şekilde güvenmeyin.

## <a name="migrate-existing-applications-to-azure"></a>Mevcut uygulamaları Azure 'a geçirme

Mevcut Web uygulamaları Azure 'a aşağıdaki şekilde yeniden dağıtılabilir:

1. **Kaynak deposu**: kaynak kodunuzu GitHub gibi uygun bir depoda saklayın ve bu işlemin ilerleyen kısımlarında sürekli dağıtım ayarlamanıza olanak sağlar.
    1. Gerekli paketleri otomatik olarak yüklemek için *requirements.txt* dosyanız, App Service deponuzın kökünde olmalıdır.    

1. **Veritabanı**: uygulama bir veritabanına bağımlıysa, Azure 'da gerekli kaynakları da sağlayın. Bkz. [öğretici: PostgreSQL Ile Docgo Web uygulaması dağıtma-örnek için veritabanı oluşturma](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) .

1. **App Service kaynakları**: uygulamanızı barındırmak için bir kaynak grubu, App Service planı ve App Service Web uygulaması oluşturun. Öğreticide gösterildiği gibi Azure CLı komutu aracılığıyla kodunuzun ilk dağıtımını yaparak bu işlemi kolayca yapabilirsiniz `az webapp up` [: PostgreSQL Ile Docgo Web uygulaması dağıtma-kodu dağıtma](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service). Uygulamanız için daha uygun olacak kaynak grubu, App Service planı ve Web uygulaması adlarını değiştirin.

1. **Ortam değişkenleri**: uygulamanız herhangi bir ortam değişkeni gerektiriyorsa, eşdeğer [App Service uygulama ayarları](configure-common.md#configure-app-settings)oluşturun. Bu App Service ayarları, [erişim ortamı değişkenlerinde](#access-app-settings-as-environment-variables)açıklandığı şekilde kodunuzda ortam değişkenleri olarak görünür.
    - Örneğin, veritabanı bağlantıları genellikle [öğretici: PostgreSQL Ile Docgo Web uygulaması dağıtma-veritabanını bağlamak için değişkenleri Yapılandırma](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).
    - Tipik Docgo uygulamalarına yönelik belirli ayarlar için bkz. [Docgo uygulamaları Için üretim ayarları](#production-settings-for-django-apps) .

1. **Uygulama başlatma**: App Service uygulamanızı nasıl çalıştırmayı denediğinize anlamak için bu makalenin ilerleyen kısımlarında yer alarak [kapsayıcı başlangıç sürecini](#container-startup-process) inceleyin. App Service, varsayılan olarak, uygulama nesneniz veya *wsgi.py* klasörünüzü bulabilmeleri gereken gunicbir Web sunucusunu kullanır. Gerekirse, [Başlangıç komutunu özelleştirebilirsiniz](#customize-startup-command).

1. **Sürekli dağıtım**: Azure Pipelines veya kudu dağıtımı kullanılıyorsa [Azure App Service için sürekli dağıtımda](deploy-continuous-deployment.md) açıklandığı gibi sürekli dağıtım ayarlayın veya GitHub eylemleri kullanılıyorsa [GitHub eylemlerini kullanarak App Service 'e dağıtın](./deploy-continuous-deployment.md) .

1. **Özel eylemler**: uygulamanızı barındıran App Service kapsayıcısı Içinde, Docgo veritabanı geçişleri gibi eylemler gerçekleştirmek IÇIN [kapsayıcıya SSH aracılığıyla bağlanabilirsiniz](configure-linux-open-ssh-session.md). Docgo veritabanı geçişlerini çalıştırmaya ilişkin bir örnek için bkz. [öğretici: PostgreSQL Ile docgo Web uygulaması dağıtma-veritabanı geçişlerini çalıştırma](tutorial-python-postgresql-app.md#43-run-django-database-migrations).
    - Sürekli dağıtım kullanırken, [derleme Otomasyonu 'Nu özelleştirme](#customize-build-automation)altında açıklandığı gibi oluşturma sonrası komutları kullanarak bu eylemleri gerçekleştirebilirsiniz.

Bu adımlar tamamlandığında, değişiklikleri kaynak deponuza kaydedebilir ve bu güncelleştirmelerin App Service otomatik olarak dağıtılmasını sağlayabilirsiniz.

### <a name="production-settings-for-django-apps"></a>Docgo uygulamaları için üretim ayarları

Azure App Service gibi bir üretim ortamında, Docgo uygulamaları Docgo 'nun [dağıtım denetim listesini](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) (djangoproject.com) izlemelidir.

Aşağıdaki tabloda, Azure ile ilgili üretim ayarları açıklanmaktadır. Bu ayarlar, uygulamanın *Setting.py* dosyasında tanımlanmıştır.

| Docgo ayarı | Azure için yönergeler |
| --- | --- |
| `SECRET_KEY` | Değeri, [uygulama ayarlarında ortam değişkenleri olarak erişim](#access-app-settings-as-environment-variables)bölümünde açıklandığı gibi bir App Service ayarında depolayın. [Değeri, Azure Key Vault ' de bir "gizli dizi" olarak da saklayabilirsiniz](../key-vault/secrets/quick-create-python.md). |
| `DEBUG` | `DEBUG`App Service 0 (false) değeriyle bir ayar oluşturun ve ardından değeri bir ortam değişkeni olarak yükleyin. Geliştirme ortamınızda, `DEBUG` 1 değerine sahip bir ortam değişkeni oluşturun (true). |
| `ALLOWED_HOSTS` | Üretimde, Docgo, uygulamanın URL 'sini `ALLOWED_HOSTS` *Settings.py* dizisine dahil etmeniz gerekir. Bu URL 'YI çalışma zamanında bu kodla elde edebilirsiniz `os.environ['WEBSITE_HOSTNAME']` . App Service, `WEBSITE_HOSTNAME` ortam değişkenini otomatik olarak uygulamanın URL 'si olarak ayarlar. |
| `DATABASES` | Veritabanı bağlantısı için App Service ayarları tanımlayın ve sözlüğü doldurmak için ortam değişkenleri olarak yükleyin [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) . Değerleri (özellikle Kullanıcı adı ve parola) [gizli Azure Key Vault](../key-vault/secrets/quick-create-python.md)olarak saklayabilirsiniz. |

## <a name="serve-static-files-for-django-apps"></a>Docgo uygulamaları için statik dosyaları sunma

Docgo Web uygulamanız statik ön uç dosyaları içeriyorsa, önce Docgo belgelerindeki [statik dosyaları yönetme](https://docs.djangoproject.com/en/3.1/howto/static-files/) yönergelerini izleyin.

App Service için aşağıdaki değişiklikleri yapın:

1. Docgo ve değişkenlerini dinamik olarak ayarlamak için ortam değişkenlerini (yerel geliştirme için) ve uygulama ayarlarını (buluta dağıtıldığında) kullanmayı düşünün `STATIC_URL` `STATIC_ROOT` . Örnek:    

    ```python
    STATIC_URL = os.environ.get("DJANGO_STATIC_URL", "/static/")
    STATIC_ROOT = os.environ.get("DJANGO_STATIC_ROOT", "./static/")    
    ```

    `DJANGO_STATIC_URL` ve, `DJANGO_STATIC_ROOT` yerel ve bulut ortamlarınız için gerektiği şekilde değiştirilebilir. Örneğin, statik dosyalarınız için yapı işlemi bunları adlı bir klasöre yerleştirse `django-static` , `DJANGO_STATIC_URL` varsayılan değer kullanmaktan kaçınmak için olarak ' i ayarlayabilirsiniz `/django-static/` .

1. Farklı bir klasörde statik dosyalar üreten bir ön derleme `STATICFILES_DIRS` betiğiniz varsa, docgo değişkeninin bu klasörü, DIGO 'un `collectstatic` işlemesini bulduğu şekilde ekleyin. Örneğin, `yarn build` ön uç klasörünüzde çalıştırırsanız ve Yarn `build/static` statik dosyalar içeren bir klasör oluşturursa, bu klasörü şu şekilde ekleyin:

    ```python
    FRONTEND_DIR = "path-to-frontend-folder" 
    STATICFILES_DIRS = [os.path.join(FRONTEND_DIR, 'build', 'static')]    
    ```

    Burada, `FRONTEND_DIR` Yarn gibi bir yapı aracının çalıştırıldığı bir yol oluşturmak için. Bir ortam değişkenini ve uygulama ayarını istediğiniz gibi kullanabilirsiniz.

1. `whitenoise` *requirements.txt* dosyanıza ekleyin. [Whitenoıse](http://whitenoise.evans.io/en/stable/) (whitenoise.Evans.io), bir üretim Docgo uygulamasının kendi statik dosyalarını kullanmasını kolaylaştıran bir Python paketidir. Whitenoıse, Docgo değişkeni tarafından belirtilen klasörde bulunan dosyaları özellikle sunar `STATIC_ROOT` .

1. *Settings.py* dosyanıza şu satırı ekleyin:

    ```python
    STATICFILES_STORAGE = ('whitenoise.storage.CompressedManifestStaticFilesStorage')
    ```

1. Ayrıca, `MIDDLEWARE` ve `INSTALLED_APPS` listelerini de içerecek şekilde değiştirin:

    ```python
    MIDDLEWARE = [                                                                   
        'django.middleware.security.SecurityMiddleware',
        # Add whitenoise middleware after the security middleware                             
        'whitenoise.middleware.WhiteNoiseMiddleware',
        # Other values follow
    ]

    INSTALLED_APPS = [
        "whitenoise.runserver_nostatic",
        # Other values follow
    ]
    ```

## <a name="container-characteristics"></a>Kapsayıcı özellikleri

App Service 'ye dağıtıldığında, Python uygulamaları [App Service Python GitHub deposunda](https://github.com/Azure-App-Service/python)tanımlanan bir Linux Docker kapsayıcısı içinde çalışır. Görüntü yapılandırmasını sürüme özgü dizinler içinde bulabilirsiniz.

Bu kapsayıcı aşağıdaki özelliklere sahiptir:

- Uygulamalar ek `--bind=0.0.0.0 --timeout 600` bağımsız değişkenleri kullanılarak [Gunicorn WSGI HTTP Server](https://gunicorn.org/) ile çalıştırılır.
    - Gunic, [yapılandırmaya genel bakış](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org) bölümünde açıklandığı gibi, proje kökündeki bir *Gunicorn.conf.py* dosyası aracılığıyla gunic, için yapılandırma ayarları sağlayabilirsiniz. [Başlangıç komutunu](#customize-startup-command)alternatif olarak özelleştirebilirsiniz.

    - Web uygulamanızı yanlışlıkla veya bilinçli DDOS saldırılarına karşı korumak için gunictbıd, [Gunicx 'ı dağıtma](https://docs.gunicorn.org/en/latest/deploy.html) konusunda açıklandığı gibi bir NGINX ters proxy 'nin arkasında çalıştırılır (docs.gunicorn.org).

- Varsayılan olarak, temel kapsayıcı görüntüsü yalnızca Flask Web çerçevesini içerir, ancak kapsayıcı, WSGI uyumlu ve Python 3.6 + ile uyumlu olan ve Docgo gibi diğer çerçeveleri destekler.

- Docgo gibi ek paketler yüklemek için, projenizin kökünde doğrudan bağımlılıklarınızı belirten bir [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) dosyası oluşturun. App Service sonra bu bağımlılıkları projenizi dağıtırken otomatik olarak yüklenir.

    *requirements.txt* dosyanın yüklenecek bağımlılıklar için proje kökünde olması *gerekir* . Aksi takdirde, yapı işlemi şu hatayı raporlar: "setup.py bulunamadı veya requirements.txt; Pınstall çalışmıyor. " Bu hatayla karşılaşırsanız, gereksinim dosyanızın konumunu kontrol edin.

- App Service `WEBSITE_HOSTNAME` , Web uygulamasının URL 'si ile adlı bir ortam değişkenini otomatik olarak tanımlar, örneğin `msdocs-hello-world.azurewebsites.net` . Ayrıca `WEBSITE_SITE_NAME` , uygulamanızın adıyla da tanımlar `msdocs-hello-world` . 
   
- NPM ve Node.js kapsayıcıda yüklüdür, böylece Yarn gibi düğüm tabanlı derleme araçlarını çalıştırabilirsiniz.

## <a name="container-startup-process"></a>Kapsayıcı başlangıç işlemi

Başlatma sırasında Linux'ta App Service kapsayıcısı şu adımları çalıştırır:

1. Sağlanmışsa, [özel bir başlangıç komutu](#customize-startup-command)kullanın.
2. Bir [Docgo uygulamasının](#django-app)mevcut olup olmadığını denetleyin ve algılanırsa gunicman 'ı başlatın.
3. [Flask uygulamasının](#flask-app)varolup olmadığını denetleyin ve algılanırsa, gunicgını başlatın.
4. Başka bir uygulama bulunamazsa yoksa kapsayıcıda bulunan varsayılan uygulamayı başlatır.

Aşağıdaki bölümlerde bu seçeneklerle ilgili ek ayrıntılar verilmiştir.

### <a name="django-app"></a>Django uygulaması

Django uygulamaları için App Service uygulama kodunuzda `wsgi.py` adlı bir dosya olup olmadığını denetler ve ardından şu komutu kullanarak Gunicorn'u çalıştırır:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Başlangıç komutu üzerinde daha özel denetim istiyorsanız, [özel bir başlangıç komutu](#customize-startup-command)kullanın, `<module>` *wsgi.py* içeren klasörün adıyla değiştirin ve `--chdir` Bu modül proje kökünde değilse bir bağımsız değişken ekleyin. Örneğin, *wsgi.py* 'niz proje kökünden *knboard/arka uç/yapılandırma* altında bulunuyorsa, bağımsız değişkenleri kullanın `--chdir knboard/backend config.wsgi` .

Üretim günlüğünü etkinleştirmek için, `--access-logfile` ve `--error-logfile` parametrelerini [özel başlangıç komutları](#customize-startup-command)için örneklerde gösterildiği gibi ekleyin.

### <a name="flask-app"></a>Flask uygulaması

Flask için, App Service *Application.py* veya *app.py* adlı bir dosya arar ve gunicgını aşağıdaki gibi başlatır:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Ana uygulama modülünüz farklı bir dosyada bulunuyorsa uygulama nesnesi için farklı bir ad kullanın veya Gunicorn'a ek bağımsız değişkenler sağlamak istiyorsanız [özel başlangıç komutu](#customize-startup-command) kullanın.

### <a name="default-behavior"></a>Varsayılan davranış

App Service özel bir komut, Docgo uygulaması veya bir Flask uygulaması bulamazsa, _opt/Defaultsite_ klasöründe bulunan ve aşağıdaki görüntüde gösterilen varsayılan salt okunurdur bir uygulama çalıştırır.

Kodu dağıttıysanız ve yine de varsayılan uygulamayı görüyorsanız, bkz. [sorun giderme-uygulama görünmüyor](#app-doesnt-appear).

[![Varsayılan Linux'ta App Service web sayfası](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Yine, varsayılan uygulama yerine dağıtılan bir uygulamayı görmeyi düşünüyorsanız, bkz. [sorun giderme-uygulama görünmüyor](#app-doesnt-appear).

## <a name="customize-startup-command"></a>Başlangıç komutunu Özelleştir

Bu makalede daha önce belirtildiği gibi, guniclerconfiguration ['a genel bakış](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)bölümünde açıklandığı gibi, proje kökündeki bir *Gunicorn.conf.py* dosyası aracılığıyla gunicyasaları için yapılandırma ayarları sağlayabilirsiniz.

Bu yapılandırma yeterli değilse, bir başlangıç komut dosyasında özel bir başlangıç komutu veya birden çok komut sağlayarak kapsayıcının başlangıç davranışını kontrol edebilirsiniz. Bir başlangıç komut dosyası, seçtiğiniz adı (örneğin, *Startup.sh*, *Startup. cmd*, *startup.txt* vb.) kullanabilir.

Tüm komutların, proje kök klasörü için göreli yollar kullanması gerekir.

Başlangıç komutunu veya komut dosyasını belirtmek için:

- **Azure Portal**: uygulamanın **yapılandırma** sayfasını seçin ve ardından **Genel ayarlar**' ı seçin. **Başlangıç komutu** alanında, başlangıç komutunuz tam metnini veya başlangıç komut dosyanızın adını yerleştirin. Sonra değişiklikleri uygulamak için **Kaydet** ' i seçin. Bkz. Linux kapsayıcıları için [genel ayarları yapılandırma](configure-common.md#configure-general-settings) .

- **Azure CLI**: başlangıç komutunu veya dosyayı ayarlamak için [az WebApp config Set](/cli/azure/webapp/config#az_webapp_config_set) komutunu parametresiyle birlikte kullanın `--startup-file` :

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    `<custom-command>`Başlangıç komutunuz tam metniyle veya başlangıç komut dosyanızın adı ile değiştirin.
        
App Service, özel bir başlangıç komutunu veya dosyasını işlerken meydana gelen hataları yoksayar, ardından Docgo ve Flask uygulamalarını arayarak başlangıç işlemini devam ettirir. İstediğiniz davranışı görmüyorsanız, başlangıç komutınızın veya dosyanızın hatasız ve uygulama kodunuzla birlikte App Service bir başlangıç komut dosyasının dağıtıldığını kontrol edin. Daha fazla bilgi için [Tanılama günlüklerine](#access-diagnostic-logs) de bakabilirsiniz. Ayrıca [Azure Portal](https://portal.azure.com)uygulamanın **Tanılama ve çözme sorunlarını** da denetleyin.

### <a name="example-startup-commands"></a>Örnek başlangıç komutları

- **Gunicbir bağımsız değişken eklendi**: Aşağıdaki örnek, `--workers=4` bir dmongo uygulamasını başlatmak için gunicbir komut satırına ekler: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Daha fazla bilgi için bkz. [Gunicorn'u Çalıştırma](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

- **Docgo için üretim günlüğünü etkinleştir**: `--access-logfile '-'` `--error-logfile '-'` komut satırına ve bağımsız değişkenlerini ekleyin:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Bu Günlükler [App Service günlük akışında](#access-diagnostic-logs)görüntülenir.

    Daha fazla bilgi için bkz. [Gunicgınlogging](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Özel Flask ana modülü**: varsayılan olarak, App Service bir Flask uygulamasının ana modülünün *Application.py* veya *app.py* olduğunu varsayar. Ana modülünüzün farklı bir ad kullanması durumunda başlangıç komutunu özelleştirmeniz gerekir. Örneğin, YF, ana modülü *Hello.py* olan bir Flask uygulaması ve bu dosyadaki Flask uygulaması nesnesi olarak adlandırılmışsa `myapp` , komut aşağıdaki gibidir:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Ana modülünüz `website` gibi bir alt klasör ise bu klasörü `--chdir` bağımsız değişkeniyle belirtin:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Gunicıse sunucusu kullanın**: [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)gibi farklı bir Web sunucusu kullanmak için, başlangıç komutu olarak veya başlangıç komut dosyasında uygun komutu kullanın:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Uygulama ayarlarına ortam değişkenleri olarak erişin

Uygulama ayarları, uygulama [ayarlarını yapılandırma](configure-common.md#configure-app-settings)bölümünde açıklandığı gibi, buluta özel olarak depolanmış değerlerdir. Bu ayarlar, uygulama kodunuzda ortam değişkenleri olarak kullanılabilir ve standart [OS. Environ](https://docs.python.org/3/library/os.html#os.environ) düzeniyle erişilir.

Örneğin, adlı uygulama ayarı oluşturduysanız `DATABASE_SERVER` , aşağıdaki kod bu ayarın değerini alır:

```python
db_server = os.environ['DATABASE_SERVER']
```

## <a name="detect-https-session"></a>HTTPS oturumunu Algıla

App Service, [SSL sonlandırma](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) ağ yükü dengeleyicilerde meydana gelir. bu nedenle, tüm https istekleri UYGULAMANıZA şifrelenmemiş HTTP istekleri olarak ulaşacak. Uygulama mantığınızın kullanıcı isteklerinin şifrelenip şifrelenmediğini denetlemesi gerekiyorsa, `X-Forwarded-Proto` üstbilgiyi inceleyin.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Popüler Web çerçeveleri `X-Forwarded-*` Standart uygulama hiyerarşinizdeki bilgilere erişmenizi sağlar. [Codeigniter](https://codeigniter.com/)'da, [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) `X_FORWARDED_PROTO` Varsayılan olarak değerini denetler.

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Günlüklere Azure Portal erişmek için,   >  uygulamanızın sol taraftaki menüsünde izleme **günlüğü akışı** ' nı seçin.

## <a name="access-deployment-logs"></a>Dağıtım günlüklerine erişim

Kodunuzu dağıttığınızda App Service, [derleme otomasyonunu özelleştirme](#customize-build-automation)bölümünde açıklanan yapı işlemini gerçekleştirir. Derleme kendi kapsayıcısında çalıştığından, derleme günlükleri uygulamanın tanılama günlüklerinden ayrı olarak depolanır.

Dağıtım günlüklerine erişmek için aşağıdaki adımları kullanın:

1. Web uygulamanız için Azure Portal Sol menüdeki **dağıtım**  >  **Dağıtım Merkezi (Önizleme)** öğesini seçin.
1. **Günlükler** sekmesinde, en son kayıt IÇIN **tamamlama kimliğini** seçin.
1. Görüntülenen **günlük ayrıntıları** sayfasında, "çalışan Oryx Build..." seçeneğinin yanında görünen **günlükleri göster..** . bağlantısını seçin.

*requirements.txt* hatalı bağımlılıklar ve ön veya derleme sonrası betiklerdeki hatalar gibi derleme sorunları bu günlüklerde görüntülenir. Gereksinimler dosyanız *requirements.txt* tam olarak adlandırılmışsa veya projenizin kök klasöründe görünmezse hatalar da görüntülenir.

## <a name="open-ssh-session-in-browser"></a>Tarayıcıda SSH oturumu açma

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

SSH oturumuna başarıyla bağlandığınızda, pencerenin alt kısmındaki "SSH BAĞLANTıSı kuruldu" iletisini görmeniz gerekir. "SSH_CONNECTION_CLOSED" gibi hatalar veya kapsayıcının yeniden başlatıldığı bir ileti görürseniz, uygulama kapsayıcısının başlamasını engelleyen bir hata olabilir. Olası sorunları araştırmaya yönelik adımlar için bkz. [sorun giderme](#troubleshooting) .

## <a name="troubleshooting"></a>Sorun giderme

Genel olarak, sorun gidermenin ilk adımı App Service tanılamayı kullanmaktır:

1. Web uygulamanız için Azure portal, sol menüden **Tanıla ve sorunları çöz** ' ü seçin.
1. **Kullanılabilirlik ve performans ' ı** seçin.
1. **Uygulama günlükleri**, **kapsayıcı kilitlenme** ve **kapsayıcı sorunları** seçeneklerinde, en yaygın sorunların görüneceği bilgileri inceleyin.

Ardından, tüm hata iletileri için [dağıtım günlüklerini](#access-deployment-logs) ve [uygulama günlüklerini](#access-diagnostic-logs) inceleyin. Bu Günlükler genellikle uygulama dağıtımını veya uygulama başlangıcını engelleyebilen belirli sorunları belirler. Örneğin, *requirements.txt* dosyanızda yanlış dosya adı varsa veya proje kök klasörünüzde mevcut değilse, derleme başarısız olabilir.

Aşağıdaki bölümler, belirli sorunlar için ek rehberlik sağlar.

- [Uygulama görünmez-varsayılan uygulama gösterilir](#app-doesnt-appear)
- [Uygulama görünmüyor-"hizmet kullanılamıyor" iletisi](#service-unavailable)
- [Setup.py veya requirements.txtbulunamadı ](#could-not-find-setuppy-or-requirementstxt)
- [Modulenotfounbir başlangıç](#modulenotfounderror-when-app-starts)
- [Veritabanı kilitli](#database-is-locked)
- [Parola yazıldığında SSH oturumunda görünmez](#other-issues)
- [SSH oturumundaki komutlar kesilmiş görünüyor](#other-issues)
- [Statik varlıklar bir Docgo uygulamasında görünmüyor](#other-issues)
- [Önemli SSL bağlantısı gerekiyor](#other-issues)

#### <a name="app-doesnt-appear"></a>Uygulama görünmüyor

- **Kendi uygulama kodunuzu dağıttıktan sonra varsayılan uygulamayı görüyorsunuz.** App Service için uygulama kodunuzu dağıtmadığınız ya da App Service uygulama kodunuzu bulamadığı ve bunun yerine varsayılan uygulamayı çalıştırmadığınız için [Varsayılan uygulama](#default-behavior) görüntülenir.

    - App Service'i yeniden başlatın, 15-20 saniye bekleyin ve uygulamayı yeniden denetleyin.
    
    - Windows tabanlı örnek yerine Linux için App Service’i kullandığınızdan emin olun. Azure CLI’de `<resource-group-name>` ve `<app-name>` hizmetini uygun bir şekilde değiştiren `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind` komutunu çalıştırın. Çıktı olarak `app,linux` görünmelidir, aksi takdirde App Service’i yeniden oluşturun ve Linux’u seçin.
    
    - App Service kapsayıcısına doğrudan bağlanmak ve dosyalarınızın *site/Wwwroot* altında mevcut olduğunu doğrulamak için [SSH](#open-ssh-session-in-browser) kullanın. Dosyalarınız yoksa, aşağıdaki adımları kullanın:
      1. 1 değeriyle adlı bir uygulama ayarı oluşturun `SCM_DO_BUILD_DURING_DEPLOYMENT` , kodunuzu yeniden dağıtın, birkaç dakika bekleyin ve ardından uygulamaya yeniden erişmeyi deneyin. Uygulama ayarları oluşturma hakkında daha fazla bilgi için, [Azure portal App Service uygulama yapılandırma](configure-common.md)konusuna bakın.
      1. Dağıtım işleminizi gözden geçirin, [dağıtım günlüklerine bakın](#access-deployment-logs), hataları düzeltin ve uygulamayı yeniden dağıtın.
    
    - Dosyalarınız oradaysa App Service başlangıç dosyanızı tanımlayamamış olabilir. Uygulamanızın App Service'in [Django](#django-app) veya [Flask](#flask-app) için beklediği şekilde yapılandırılmış olduğundan emin olun veya [özel başlangıç komutu](#customize-startup-command) kullanın.

- <a name="service-unavailable"></a>**"Hizmet kullanılamıyor" iletisini tarayıcıda görürsünüz.** Tarayıcı App Service bir yanıt beklerken zaman aşımına uğradı. Bu, App Service Gunic,Server 'ın başlatıldığını, ancak uygulamanın başlamadığını gösterir. Bu durum, Gunicbir bağımsız değişkenlerin yanlış olduğunu veya uygulama kodunda bir hata olduğunu gösteriyor olabilir.

    - Özellikle App Service Planınızda en düşük fiyatlandırma katmanlarını kullanıyorsanız tarayıcıyı yenileyin. Ücretsiz katmanları kullandığınızda uygulamanın başlaması daha uzun sürebilir ve tarayıcıyı yenilediğinizde yanıt verebilir.

    - Uygulamanızın App Service'in [Django](#django-app) veya [Flask](#flask-app) için beklediği şekilde yapılandırılmış olduğundan emin olun veya [özel başlangıç komutu](#customize-startup-command) kullanın.

    - Herhangi bir hata iletisi için [uygulama günlüğü akışını](#access-diagnostic-logs) inceleyin. Günlükler, uygulama kodundaki hataları gösterir.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Setup.py veya requirements.txt bulunamadı

- **Günlük akışında "Setup.py bulunamadı veya requirements.txt; Pyx Install çalışmıyor. "**: Oryx derleme işlemi *requirements.txt* dosyanızı bulamadı.

    - [SSH](#open-ssh-session-in-browser) aracılığıyla Web uygulamasının kapsayıcısına bağlanın ve *requirements.txt* doğru şekilde adlandırıldığını ve doğrudan *site/Wwwroot* altında mevcut olduğunu doğrulayın. Mevcut değilse, siteyi deponuzda mevcut yapın ve dağıtımınıza dahil edin. Ayrı bir klasörde varsa, onu köke taşıyın.

#### <a name="modulenotfounderror-when-app-starts"></a>Uygulama başladığında Modulenotfoun,

Gibi bir hata görürseniz `ModuleNotFoundError: No module named 'example'` , bu, Python 'un uygulama başladığında bir veya daha fazla modülden fazlasını bulamamasıdır. Bu en sık, sanal ortamınızı kodunuzla dağıtırsanız oluşur. Sanal ortamlar taşınabilir değildir, bu nedenle bir sanal ortam, uygulama kodunuzla birlikte dağıtılmamalıdır. Bunun yerine, bir uygulama ayarı oluşturup, ve olarak ayarlayarak, sanal ortam oluşturma ve paketlerinizi web uygulamasına yüklemeye izin verin `SCM_DO_BUILD_DURING_DEPLOYMENT` `1` . Bu, App Service ' a dağıtırken paketlerinizi yüklemeye zorlaştıracaktır. Daha fazla bilgi için lütfen [sanal ortam taşınabilirliği ' nde bu makaleye](https://azure.github.io/AppService/2020/12/11/cicd-for-python-apps.html)bakın.

### <a name="database-is-locked"></a>Veritabanı kilitli

Bir Docgo uygulamasıyla veritabanı geçişlerini çalıştırmaya çalışırken, "SQLite3" görebilirsiniz. OperationalError: veritabanı kilitli. " Hata, uygulamanızın, Azure için PostgreSQL gibi bir bulut veritabanı kullanmak yerine, Docgo 'nun varsayılan olarak yapılandırıldığı bir SQLite veritabanı kullandığını gösterir.

Uygulamanızın `DATABASES` SQLite yerine bir bulut veritabanı kullandığından emin olmak için uygulamanın *Settings.py* dosyasındaki değişkeni denetleyin.

Öğreticide örnekle ilgili bu hatayla karşılaşırsanız, [PostgreSQL Ile Docgo Web uygulaması dağıtma](tutorial-python-postgresql-app.md)bölümünde, [veritabanını bağlamak Için ortam değişkenlerini yapılandırma](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database)bölümündeki adımları tamamladığınızdan emin olun.

#### <a name="other-issues"></a>Diğer sorunlar

- **Girilen parolalar SSH oturumunda görünmez**: güvenlik nedenleriyle, sız yazarken SSH oturumu parolanızı gizli tutar. Ancak karakterler kaydediliyor, bu nedenle parolanızı her zamanki gibi yazın ve bitince **ENTER** tuşuna basın.

- **SSH oturumundaki komutlar kesilmiyor**: Düzenleyici sözcük kaydırma komutları olmayabilir, ancak yine de düzgün çalışmalıdır.

- **Statik varlıklar bir Docgo uygulamasında görünmüyor**: [whitenoıse modülünü](http://whitenoise.evans.io/en/stable/django.html) etkinleştirdiğinizden emin olun

- **"ÖNEMLI SSL bağlantısı gerekiyor" iletisini görürsünüz**: uygulamanın içinden kaynaklara (veritabanları gibi) erişmek için kullanılan tüm Kullanıcı adlarını ve parolaları denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile Python uygulaması](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Öğretici: özel kapsayıcı deposundan dağıtma](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service Linux Hakkında SSS](faq-app-service-linux.md)

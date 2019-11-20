---
title: Python uygulamalarını Yapılandırma-Azure App Service
description: Bu öğreticide Linux'ta Azure App Service için Python uygulamalarını yazma ve yapılandırma seçenekleri anlatılmaktadır.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 8563e0ac060e5cce6853472dfb1c51c6c2c36a4d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071081"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Azure App Service için bir Linux Python uygulaması yapılandırma

Bu makalede, [Azure App Service](app-service-linux-intro.md) Python uygulamalarının nasıl çalıştığı ve gerektiğinde App Service davranışının nasıl özelleştirileceği açıklanır. Python uygulamalarının tüm gerekli [PIP](https://pypi.org/project/pip/) modülleri ile dağıtılması gerekir.

App Service dağıtım altyapısı sanal bir ortamı otomatik olarak etkinleştirir ve bir `pip install -r requirements.txt` [Git deposu](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)dağıtırken sizin için çalışır veya derleme işlemlerine sahip bir [ZIP paketi](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) üzerinde geçiş yapar.

Bu kılavuz, App Service içinde yerleşik bir Linux kapsayıcısı kullanan Python geliştiricileri için temel kavramlar ve yönergeler sağlar. Azure App Service hiç kullanmadıysanız, ilk olarak [Python hızlı](quickstart-python.md) başlangıcı ve [Python 'U PostgreSQL öğreticisiyle](tutorial-python-postgresql-app.md) izlemelisiniz.

> [!NOTE]
> Linux Şu anda App Service ' de Python uygulamalarını çalıştırmak için önerilen seçenektir. Windows seçeneği hakkında bilgi için, [App Service Windows üzerinde Python](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service)konusuna bakın.
>

## <a name="show-python-version"></a>Python sürümünü göster

Geçerli Python sürümünü göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Desteklenen tüm Python sürümlerini göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Bunun yerine kendi kapsayıcı görüntünüzü oluşturarak desteklenmeyen bir Python sürümü çalıştırabilirsiniz. Daha fazla bilgi için bkz. [özel bir Docker görüntüsü kullanma](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Python sürümünü ayarla

Python sürümünü 3,7 olarak ayarlamak için [Cloud Shell](https://shell.azure.com) aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>Kapsayıcı özellikleri

Linux üzerinde App Service dağıtılan Python uygulamaları GitHub deposunda, [python 3,6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) veya [Python 3,7](https://github.com/Azure-App-Service/python/tree/master/3.7.0)' de tanımlanan bir Docker kapsayıcısı içinde çalışır.

Bu kapsayıcı aşağıdaki özelliklere sahiptir:

- Uygulamalar ek `--bind=0.0.0.0 --timeout 600` bağımsız değişkenleri kullanılarak [Gunicorn WSGI HTTP Server](https://gunicorn.org/) ile çalıştırılır.

- Temel görüntü varsayılan olarak Flask web çerçevesini içerir ancak kapsayıcı Django gibi WSGI ve Python 3.7 ile uyumlu diğer çerçeveleri de destekler.

- Django gibi ek paketleri yüklemek için `pip freeze > requirements.txt` kullanarak projenizin kök dizininde bir [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) dosyası oluşturun. Ardından projenizi Git dağıtımı kullanarak App Service'te yayımlayın. Bunu yaptığınızda uygulamanızın bağımlılıklarının yüklenmesi için kapsayıcıda otomatik olarak `pip install -r requirements.txt` çalıştırılır.

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
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Başlangıç komutu üzerinde daha fazla denetime sahip olmak istiyorsanız [özel başlangıç komutu](#customize-startup-command) kullanın ve `<module>` yerine *wsgi.py* dosyasını içeren modülün adını yazın.

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

App Service özel komut dosyası, Django uygulaması veya Flask uygulaması bulamazsa _opt/defaultsite_ klasöründe bulunan varsayılan salt okunur uygulamayı çalıştırır. Varsayılan uygulama aşağıdaki gibi görünür:

![Varsayılan Linux'ta App Service web sayfası](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Başlangıç komutunu Özelleştir

Özel bir Gunicorn başlangıç komutu sağlayarak kapsayıcının başlangıç davranışını denetleyebilirsiniz. Bunu yapmak için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Örneğin, ana modülü *Hello.py* olan bir Flask uygulaması varsa ve bu dosyadaki Flask uygulaması nesnesi olarak adlandırılmışsa `myapp`,  *\<özel-komut >* aşağıdaki gibidir:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Ana modülünüz `website` gibi bir alt klasör ise bu klasörü `--chdir` bağımsız değişkeniyle belirtin:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Ayrıca, gunic, `--workers=4`  *\<gibi özel komut >* için ek bağımsız değişkenleri ekleyebilirsiniz. Daha fazla bilgi için bkz. [Gunicorn'u Çalıştırma](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

[Aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)gibi gunicbir Server kullanmak için,  *\<özel komut >* aşağıdaki gibi bir şekilde değiştirebilirsiniz:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> App Service, özel komut dosyasının işlenmesi sırasında oluşan hataları yoksayar ve başlatma işlemine Django ve Flask uygulamalarını arayarak devam eder. Beklediğiniz davranışı görmüyorsanız başlangıç dosyanızın App Service'e dağıtıldığından ve dosyada hata bulunmadığından emin olun.

## <a name="access-environment-variables"></a>Ortam değişkenlerine erişin

App Service, uygulama ayarlarınızı uygulama kodunuzun dışında [ayarlayabilirsiniz](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) . Ardından, standart [OS. Environ](https://docs.python.org/3/library/os.html#os.environ) modelini kullanarak bunlara erişebilirsiniz. Örneğin, adlı `WEBSITE_SITE_NAME`bir uygulama ayarına erişmek için aşağıdaki kodu kullanın:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>HTTPS oturumunu Algıla

App Service, [SSL sonlandırması](https://wikipedia.org/wiki/TLS_termination_proxy) ağ yükü dengeleyicilerde gerçekleşinceye kadar, tüm https istekleri UYGULAMANıZA şifrelenmemiş HTTP istekleri olarak ulaşacak. Uygulama mantığınızın kullanıcı isteklerinin şifrelenip şifrelenmediğini denetlemesi gerekiyorsa, `X-Forwarded-Proto` üstbilgiyi inceleyin.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Popüler Web çerçeveleri standart uygulama hiyerarşinizdeki `X-Forwarded-*` bilgilere erişmenizi sağlar. [Codeigniter](https://codeigniter.com/)'da, [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) varsayılan `X_FORWARDED_PROTO` olarak değerini denetler.

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH oturumunu tarayıcıda aç

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Sorun giderme

- **Kendi uygulama kodunuzu dağıttıktan sonra varsayılan uygulamayı görüyorsunuz.** App Service için uygulama kodunuzu dağıtmadığınız ya da App Service uygulama kodunuzu bulamadığı ve bunun yerine varsayılan uygulamayı çalıştırmadığınız için varsayılan uygulama görüntülenir.
- App Service'i yeniden başlatın, 15-20 saniye bekleyin ve uygulamayı yeniden denetleyin.
- Windows tabanlı örnek yerine Linux için App Service’i kullandığınızdan emin olun. Azure CLI’de `<resource_group_name>` ve `<app_service_name>` hizmetini uygun bir şekilde değiştiren `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` komutunu çalıştırın. Çıktı olarak `app,linux` görünmelidir, aksi takdirde App Service’i yeniden oluşturun ve Linux’u seçin.
- SSH veya Kudu kullanarak doğrudan App Service'e bağlanın ve dosyalarınızın *site/wwwroot* dizininde bulunduğunu doğrulayın. Dosyalarınız orada değilse dağıtım işlemlerinizi gözden geçirin ve uygulamayı yeniden dağıtın.
- Dosyalarınız oradaysa App Service başlangıç dosyanızı tanımlayamamış olabilir. Uygulamanızın App Service'in [Django](#django-app) veya [Flask](#flask-app) için beklediği şekilde yapılandırılmış olduğundan emin olun veya [özel başlangıç komutu](#customize-startup-command) kullanın.
- **Tarayıcıda "Hizmet Kullanılamıyor" iletisini görüyorsunuz.** Bu durum, tarayıcının App Service'ten yanıt beklerken zaman aşımına uğradığını gösterir. Bunun nedeni App Service'in Gunicorn sunucusunu başlatmış olması ancak uygulama kodunu belirten bağımsız değişkenlerin hatalı olmasıdır.
- Özellikle App Service Planınızda en düşük fiyatlandırma katmanlarını kullanıyorsanız tarayıcıyı yenileyin. Ücretsiz katmanları kullandığınızda uygulamanın başlaması daha uzun sürebilir ve tarayıcıyı yenilediğinizde yanıt verebilir.
- Uygulamanızın App Service'in [Django](#django-app) veya [Flask](#flask-app) için beklediği şekilde yapılandırılmış olduğundan emin olun veya [özel başlangıç komutu](#customize-startup-command) kullanın.
- [Günlük akışına erişin](#access-diagnostic-logs).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile Python uygulaması](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Öğretici: Özel kapsayıcı deposundan dağıtma](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [App Service Linux SSS](app-service-linux-faq.md)

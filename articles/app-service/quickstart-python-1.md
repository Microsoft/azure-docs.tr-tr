---
title: 'Hızlı başlangıç: Linux üzerinde bir Python uygulaması oluşturma'
description: App Service bir Linux kapsayıcısına Python uygulaması dağıtarak Azure App Service kullanmaya başlayın.
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
zone_pivot_groups: python-frameworks-01
robots: noindex
ms.openlocfilehash: 464583f9e20d55fa351a6ae0df31b608b18dc428
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379545"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Hızlı başlangıç: Linux üzerinde Azure App Service bir Python uygulaması oluşturma

Bu hızlı başlangıçta, Azure 'un yüksek düzeyde ölçeklenebilir ve kendini yayama Web barındırma hizmeti [olan Linux üzerinde App Service](overview.md#app-service-on-linux)Için bir Python web uygulaması dağıtırsınız. Bir Mac, Linux veya Windows bilgisayarında yerel [Azure komut satırı arabirimi 'ni (CLI)](/cli/azure/install-azure-cli) kullanarak Flask veya Docgo çerçeveleri ile bir örnek dağıtabilirsiniz. Yapılandırdığınız Web uygulaması ücretsiz bir App Service katmanını kullanır, bu nedenle bu makalenin kursunda hiçbir ücret ödeirsiniz.

> [!TIP]
> Uygulamaları bir IDE aracılığıyla dağıtmayı tercih ediyorsanız, bkz. **[Visual Studio Code App Service Için Python uygulamaları dağıtma](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)**.

## <a name="set-up-your-initial-environment"></a>İlk ortamınızı ayarlama

1. Etkin aboneliği olan bir Azure hesabına sahip olmanız gerekir. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. <a href="https://www.python.org/downloads/" target="_blank">Python 3,6 veya üstünü</a>yükler.
1. Azure <a href="/cli/azure/install-azure-cli" target="_blank">CLI</a> 2.0.80 veya üstünü yükleyerek Azure kaynaklarını temin etmek ve yapılandırmak için herhangi bir kabukta komut çalıştırırsınız.

Bir Terminal penceresi açın ve Python sürümünüzün 3,6 veya üzeri olduğunu denetleyin:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Azure CLı sürümünüzün 2.0.80 veya üzeri olduğunu kontrol edin:

```azurecli
az --version
```

Daha sonra CLı aracılığıyla Azure 'da oturum açın:

```azurecli
az login
```

Bu komut, kimlik bilgilerinizi toplamak için bir tarayıcı açar. Komut tamamlandığında, abonelikleriniz hakkında bilgi içeren JSON çıkışını gösterir.

Oturum açtıktan sonra, aboneliğinizdeki kaynaklarla çalışmak için Azure CLı ile Azure komutlarını çalıştırabilirsiniz.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Örneği

Aşağıdaki komutu kullanarak örnek depoyu kopyalayın ve örnek klasöre gidin. (Zaten git yoksa[Git 'ı yükleyebilirsiniz](https://git-scm.com/downloads) .)

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ardından bu klasöre gidin:

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

Ardından bu klasöre gidin:

```terminal
cd python-docs-hello-django
```
::: zone-end

Örnek, uygulamayı başlatırken Azure App Service tanıdığı çerçeveye özgü kod içerir. Daha fazla bilgi için bkz. [kapsayıcı başlatma işlemi](configure-language-python.md#container-startup-process).

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Örneği dağıtma

Şu komutu kullanarak kodu yerel klasörünüzde (*Python-docs-Hello-World*) dağıtın `az webapp up` :

```azurecli
az webapp up --sku B1 --name <app-name>
```

- `az`Komut tanınmazsa, [Ilk ortamınızı ayarlama](#set-up-your-initial-environment)bölümünde AÇıKLANDıĞı gibi Azure CLI 'nin yüklü olduğundan emin olun.
- `webapp`Azure CLI sürümünüz 2.0.80 veya üzeri olduğundan, komut tanınmazsa. Aksi takdirde, [en son sürümü yükler](/cli/azure/install-azure-cli).
- `<app_name>`Tüm Azure genelinde benzersiz olan bir adla değiştirin (*geçerli karakterler `a-z` , `0-9` ve `-`*). İyi bir model, şirketinizin adının ve uygulama tanımlayıcısının bir birleşimini kullanmaktır.
- `--sku B1`Bağımsız değişkeni, Web uygulamasını temel fiyatlandırma katmanında oluşturur ve bu da kısa bir saatlik maliyet doğurur. Daha hızlı bir Premium katmanı kullanmak için bu bağımsız değişkeni atlayın.
- İsteğe bağlı olarak `--location <location-name>` `<location_name>` , kullanılabilir bir Azure bölgesi olan bağımsız değişkeni ekleyebilirsiniz. Komutunu çalıştırarak, Azure hesabınız için izin verilen bölgelerin bir listesini alabilirsiniz [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) .
- "Uygulamanızın çalışma zamanı yığınını otomatik olarak algılamamız" hatasını görürseniz, bu komutu *requirements.txt* dosyasını içeren *Python-docs-Hello-World* klasöründe (Flask) veya *Python-docs-Hello-Docgo* klasöründe (docgo) çalıştırdığınızdan emin olun. (Bkz. [az WebApp up (GitHub) ile otomatik algılama sorunlarını giderme](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) .)

Komutun tamamlanması birkaç dakika sürebilir. Çalıştırılırken, kaynak grubu oluşturma, App Service planı ve barındırma uygulaması, günlüğü yapılandırma ve ardından ZIP dağıtımı gerçekleştirme hakkında iletiler sağlar. Daha sonra, "uygulamayı &lt; &gt; Azure 'da uygulamanın URL 'si olan http://app-name. azurewebsites.net konumunda başlatabilirsiniz.

![Az WebApp up komutunun örnek çıktısı](./media/quickstart-python/az-webapp-up-output.png)

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Uygulamaya göz atma

URL 'de Web tarayıcınızda dağıtılan uygulamaya gidin `http://<app-name>.azurewebsites.net` . Başlangıçta uygulamanın başlatılması birkaç dakika sürer.

Python örnek kodu, yerleşik bir görüntü kullanarak App Service bir Linux kapsayıcısı çalıştırıyor.

![Azure 'da örnek bir Python uygulaması çalıştırma](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Tebrikler!** App Service için Python uygulamanızı dağıttınız.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Örneği çalıştırma 

::: zone pivot="python-framework-flask"
1. *Python-docs-Hello-World* klasöründe olduğunuzdan emin olun. 

1. Sanal ortam oluşturma ve bağımlılıkları yüklemesi:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    "[Errno 2] böyle bir dosya veya dizin bulunamadı: ' requirements.txt '.", *Python-docs-Hello-World* klasöründe olduğunuzdan emin olun.

1. Geliştirme sunucusunu çalıştırın.

    ```terminal  
    flask run
    ```
    
    Varsayılan olarak sunucu, uygulamanın giriş modülünün örnekte kullanılan *app.py* içinde olduğunu varsayar. (Farklı bir modül adı kullanırsanız, `FLASK_APP` ortam değişkenini bu ad olarak ayarlayın.)

1. Bir Web tarayıcısı açın ve konumundaki örnek uygulamaya gidin `http://localhost:5000/` . Uygulama **Merhaba, dünya!** iletisini görüntüler.

    ![Örnek bir Python uygulamasını yerel olarak çalıştırma](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Terminal pencerenizde,  + geliştirme sunucusundan çıkmak için CTRL **C** tuşuna basın.
::: zone-end

::: zone pivot="python-framework-django"
1. *Python-docs-Hello-docgo* klasöründe olduğunuzdan emin olun. 

1. Sanal ortam oluşturma ve bağımlılıkları yüklemesi:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    "[Errno 2] böyle bir dosya veya dizin bulunamadı: ' requirements.txt '.", *Python-docs-Hello-docgo* klasöründe olduğunuzdan emin olun.
    
1. Geliştirme sunucusunu çalıştırın.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python3 manage.py runserver
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -3 manage.py runserver
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -3 manage.py runserver
    ```

    ---    

1. Bir Web tarayıcısı açın ve konumundaki örnek uygulamaya gidin `http://localhost:8000/` . Uygulama **Merhaba, dünya!** iletisini görüntüler.

    ![Örnek bir Python uygulamasını yerel olarak çalıştırma](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Terminal pencerenizde,  + geliştirme sunucusundan çıkmak için CTRL **C** tuşuna basın.
::: zone-end

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Güncelleştirmeleri yeniden Dağıt

Bu bölümde, küçük bir kod değişikliği yapar ve ardından kodu Azure 'a yeniden dağıtabilirsiniz. Kod değişikliği, `print` sonraki bölümde birlikte çalıştığınız günlüğe kaydetme çıkışı oluşturmak için bir ifade içerir.

::: zone pivot="python-framework-flask"
*App.py* 'i bir düzenleyicide açın ve `hello` işlevi aşağıdaki kodla eşleşecek şekilde güncelleştirin. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Bir düzenleyicide *Merhaba/views.* düzenleyene açın ve `hello` işlevi aşağıdaki kodla eşleşecek şekilde güncelleştirin.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Değişikliklerinizi kaydettikten sonra `az webapp up` komutu tekrar kullanarak uygulamayı yeniden dağıtın:

```azurecli
az webapp up
```

Bu komut, uygulama adı, kaynak grubu ve App Service planı dahil olmak üzere *. Azure/config* dosyasında yerel olarak önbelleğe alınan değerleri kullanır.

Dağıtım tamamlandıktan sonra tarayıcı penceresine geri dönün `http://<app-name>.azurewebsites.net` . Değiştirilen iletiyi görüntülemesi gereken sayfayı yenileyin:

![Azure 'da güncelleştirilmiş örnek bir Python uygulaması çalıştırma](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code, Python web uygulamalarını App Service dağıtma sürecini kolaylaştıran Python ve Azure App Service için güçlü uzantılar sağlar. Daha fazla bilgi için bkz. [Visual Studio Code App Service Için Python uygulamaları dağıtma](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Akış günlükleri

Uygulamanın içinden oluşturulan konsol günlüklerine ve çalıştığı kapsayıcıya erişebilirsiniz. Günlükler, using deyimleri kullanılarak oluşturulan herhangi bir çıktıyı içerir `print` .

Günlükleri akışa almak için [az WebApp log tail](/cli/azure/webapp/log#az_webapp_log_tail) komutunu çalıştırın:

```azurecli
az webapp log tail
```

`--logs` `az webapp up` Günlük akışını otomatik olarak dağıtımda açmak için parametresini then komutuyla da dahil edebilirsiniz.

Uygulamaya HTTP isteklerini açıklayan iletileri içeren konsol günlükleri oluşturmak için uygulamayı tarayıcıda yenileyin. Hiçbir çıkış hemen yoksa, 30 saniye içinde yeniden deneyin.

Ayrıca, tarayıcıdan `https://<app-name>.scm.azurewebsites.net/api/logs/docker` adresine giderek günlük dosyalarını inceleyebilirsiniz.

Günlük akışını istediğiniz zaman durdurmak için terminalde **CTRL** + **C** tuşuna basın.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Azure uygulamasını yönetme

Oluşturduğunuz uygulamayı yönetmek için <a href="https://portal.azure.com" target="_blank">Azure Portal</a> gidin. **Uygulama hizmetleri**' ni arayıp seçin.

![Azure portal uygulama hizmetleri 'ne gidin](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Azure uygulamanızın adını seçin.

![Azure portal App Services 'ta Python uygulamanıza gidin](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Uygulamanın seçilmesi, **genel bakış** sayfasını açar, buradan göz at, durdur, Başlat, yeniden Başlat ve Sil gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

![Python uygulamanızı Azure portal genel bakış sayfasında yönetin](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service menüsü, uygulamanızı yapılandırmak için farklı sayfalar sağlar.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Kaynak grubu, konumunuza bağlı olarak "appsvc_rg_Linux_CentralUS" gibi bir ada sahiptir. Web uygulamasını çalışır durumda tutarsanız, bazı devam eden maliyetlere (bkz. [App Service fiyatlandırması](https://azure.microsoft.com/pricing/details/app-service/linux/)) tabi olursunuz.

Gelecekte bu kaynakların gerekli olmasını istemiyorsanız, aşağıdaki komutu çalıştırarak kaynak grubunu silin:

```azurecli
az group delete --no-wait
```

Komut *. Azure/config* dosyasında önbelleğe alınmış kaynak grubu adını kullanır.

`--no-wait`Bağımsız değişkeni, işlem tamamlanmadan önce komutun dönmesini sağlar.

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile Python (Docgo) Web uygulaması](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python uygulamasını yapılandırma](configure-language-python.md)

> [!div class="nextstepaction"]
> [Python web uygulamasına kullanıcı oturumu ekleme](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Öğretici: özel kapsayıcıda Python uygulaması çalıştırma](tutorial-custom-container.md)

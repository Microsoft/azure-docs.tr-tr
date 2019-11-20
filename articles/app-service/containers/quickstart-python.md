---
title: "Hızlı başlangıç: Linux 'ta Python uygulaması oluşturma-Azure App Service"
description: Linux üzerinde Azure App Service'te ilk Python merhaba dünya uygulamanızı birkaç dakika içinde dağıtın.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 10/22/2019
ms.author: cephalin
ms.custom: seo-python-october2019
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 69e7cfef01005432a99dd10ed5bc7f004562e582
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470766"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Hızlı başlangıç: Linux üzerinde Azure App Service bir Python uygulaması oluşturma

Bu hızlı başlangıçta, Azure 'un yüksek düzeyde ölçeklenebilir ve kendini yayama Web barındırma hizmeti [olan Linux üzerinde App Service](app-service-linux-intro.md)Için bir Python web uygulaması dağıtırsınız. Yerel [Azure komut satırı arabirimi 'ni (CLI)](/cli/azure/install-azure-cli) bir Mac, Linux veya Windows bilgisayarında kullanırsınız. Yapılandırdığınız Web uygulaması ücretsiz bir App Service katmanını kullanır, bu nedenle bu makalenin kursunda hiçbir ücret ödeirsiniz.

Uygulamaları bir IDE aracılığıyla dağıtmayı tercih ediyorsanız, bkz. [Visual Studio Code App Service Için Python uygulamaları dağıtma](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (Python 3,6 de desteklenir)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

## <a name="download-the-sample"></a>Örneği indirme

Bir Terminal penceresinde, örnek uygulamayı yerel bilgisayarınıza kopyalamak için aşağıdaki komutu çalıştırın. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Daha sonra bu klasöre gidin:

```terminal
cd python-docs-hello-world
```

Depo, kodun bir Flask uygulaması içerdiğini App Service söyleyen bir *Application.py* dosyası içerir. Daha fazla bilgi için bkz. [kapsayıcı başlangıç işlemi ve özelleştirmeleri](how-to-configure-python.md).

## <a name="run-the-sample"></a>Örneği çalıştırma

Terminal penceresinde, gerekli bağımlılıkları yüklemek ve yerleşik geliştirme sunucusunu başlatmak için aşağıdaki komutları (işletim sisteminize uygun olarak) kullanın. 

# <a name="bashtabbash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Bir Web tarayıcısı açın ve `http://localhost:5000/`konumundaki örnek uygulamaya gidin. Uygulama **Merhaba Dünya!** iletisini görüntüler.

![Örnek bir Python uygulamasını yerel olarak çalıştırma](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

Terminal pencerenizde, Web sunucusundan çıkmak için **Ctrl**+**C** tuşlarına basın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure CLı, Azure kaynaklarını komut satırından sağlamak ve yönetmek için yerel terminalden kullandığınız birçok kullanışlı komut sağlar. Bir tarayıcıda Azure portal istediğiniz görevleri gerçekleştirmek için komutları kullanabilirsiniz. Yönetim işlemlerini otomatikleştirmek için betiklerdeki CLı komutlarını da kullanabilirsiniz.

Azure CLı 'de Azure komutlarını çalıştırmak için öncelikle `az login` komutunu kullanarak oturum açmanız gerekir. Bu komut, kimlik bilgilerinizi toplamak için bir tarayıcı açar.

```terminal
az login
```

## <a name="deploy-the-sample"></a>Örneği dağıtma

[`az webapp up`](/cli/azure/webapp#az-webapp-up) komutu, web uygulamasını App Service oluşturur ve kodunuzu dağıtır.

Örnek kodu içeren *Python-docs-Hello-World* klasöründe aşağıdaki `az webapp up` komutunu çalıştırın. `<app-name>`, genel olarak benzersiz bir uygulama adıyla değiştirin (*geçerli karakterler `a-z`, `0-9`ve `-`* ). Ayrıca **`<location-name>`,** **brazilsouth**, **westeurope**, **koreagüney**,, **merkezileştirme**vb **. gibi bir**Azure bölgesi ile değiştirin. ( [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) komutunu çalıştırarak, Azure hesabınız için izin verilen bölgelerin bir listesini alabilirsiniz.)


```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Bu komutun tamamlanması birkaç dakika sürebilir. Çalıştırıldığında, aşağıdaki örneğe benzer bilgiler görüntüler:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Uygulamaya göz atma

URL `http://<app-name>.azurewebsites.net`Web tarayıcınızda dağıtılan uygulamaya gidin.

Python örnek kodu, yerleşik bir görüntü kullanarak App Service bir Linux kapsayıcısı çalıştırıyor.

![Azure 'da örnek bir Python uygulaması çalıştırma](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Tebrikler!** Python uygulamanızı Linux üzerinde App Service için dağıttık.

## <a name="redeploy-updates"></a>Güncelleştirmeleri yeniden Dağıt

En sevdiğiniz kod düzenleyicisinde *Application.py* ' ı açın ve son satırdaki `return` ifadesini aşağıdaki kodla eşleşecek şekilde değiştirin. `print` deyimin bir sonraki bölümde birlikte çalıştığınız günlük çıkışını oluşturmak için buraya dahil edilir. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Değişikliklerinizi kaydedin ve düzenleyiciden çıkın. 

Uygulamayı ilk kez dağıtmak için kullandığınız komutu kullanarak uygulamayı yeniden `az webapp up` dağıtın, `<app-name>` ve `<location-name>`, daha önce kullandığınız adlarla değiştirin. 

```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Dağıtım tamamlandıktan sonra, `http://<app-name>.azurewebsites.net` için açın ve değiştirilen iletiyi görüntülemesi gereken sayfayı yenilemek için tarayıcı penceresine geri dönün:

![Azure 'da güncelleştirilmiş örnek bir Python uygulaması çalıştırma](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code, Python web uygulamalarını App Service dağıtma sürecini kolaylaştıran Python ve Azure App Service için güçlü uzantılar sağlar. Daha fazla bilgi için bkz. [Visual Studio Code App Service Için Python uygulamaları dağıtma](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Akış günlükleri

Uygulamanın içinden oluşturulan konsol günlüklerine ve çalıştığı kapsayıcıya erişebilirsiniz. Günlükler, `print` deyimleri kullanılarak oluşturulan herhangi bir çıktıyı içerir.

İlk olarak, bir terminalde aşağıdaki komutu çalıştırarak kapsayıcı günlüğünü açın, `<app-name>`, uygulamanızın adıyla değiştirin ve kullandığınız `az webapp up` komutun çıktısında gösterilen kaynak grubunun adı ile `<resource-group-name>` ("appsvc_rg_Linux_centralus" gibi). ):

```terminal
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Kapsayıcı günlüğü açıldıktan sonra, günlük akışını göstermek için aşağıdaki komutu çalıştırın:

```terminal
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Aşağıdaki metne benzer satırları içermesi gereken konsol günlükleri oluşturmak için tarayıcıda uygulamayı yenileyin. Çıktıyı hemen görmüyorsanız, 30 saniye içinde yeniden deneyin.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Ayrıca, `https://<app-name>.scm.azurewebsites.net/api/logs/docker`konumundaki tarayıcıdan günlük dosyalarını inceleyebilirsiniz.

Günlük akışını istediğiniz zaman durdurmak için `Ctrl`+`C`yazın.

## <a name="manage-the-azure-app"></a>Azure uygulamasını yönetme

Oluşturduğunuz uygulamayı yönetmek için <a href="https://portal.azure.com" target="_blank">Azure Portal</a> gidin. **Uygulama hizmetleri**' ni arayıp seçin.

![Azure portal uygulama hizmetleri 'ne gidin](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Azure uygulamanızın adını seçin.

![Azure portal App Services 'ta Python uygulamanıza gidin](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Uygulamanızın genel bakış sayfasını görürsünüz. Buradan göz atma, durdurma, başlatma, yeniden başlatma ve silme gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

![Python uygulamanızı Azure portal genel bakış sayfasında yönetin](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service menüsü, uygulamanızı yapılandırmak için farklı sayfalar sağlar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda Azure kaynakları oluşturdunuz. Kaynak grubu, konumunuza bağlı olarak "appsvc_rg_Linux_CentralUS" gibi bir ada sahiptir. Ücretsiz F1 katmanından farklı bir App Service SKU 'SU kullanıyorsanız, bu kaynaklar devam eden maliyetlere tabi olur.

Gelecekte bu kaynaklara ihtiyaç duymazsanız, aşağıdaki komutu çalıştırarak kaynak grubunu silin. `<resource-group-name>`, "appsvc_rg_Linux_centralus" gibi `az webapp up` komutunun çıktısında gösterilen kaynak grubuyla değiştirin. Komutun tamamlanması birkaç dakika sürebilir.

```terminal
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile Python (Docgo) Web uygulaması](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Python uygulamasını yapılandırma](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Öğretici: özel kapsayıcıda Python uygulaması çalıştırma](tutorial-custom-docker-image.md)

---
title: 'Hızlı başlangıç: PHP Web uygulaması oluşturma'
description: İlk PHP Merhaba Dünya Azure App Service dakikalar içinde dağıtın. App Service dağıtmak için birçok yol olan git kullanarak dağıtın.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: c04e1b33233abebde746fd7fb1ef8d761dba7e4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788215"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Azure App Service bir PHP Web uygulaması oluşturma

::: zone pivot="platform-windows"  
[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar.  Bu hızlı başlangıç öğreticisinde, Windows üzerinde Azure App Service bir PHP uygulamasının nasıl dağıtılacağı gösterilmektedir.
::: zone-end  

::: zone pivot="platform-linux"
[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar.  Bu hızlı başlangıç öğreticisinde, Linux’ta Azure App Service’e bir PHP uygulamasının nasıl dağıtılacağı gösterilmektedir.
::: zone-end  

Cloud Shell’de [Azure CLI](/cli/azure/get-started-with-azure-cli) kullanarak web uygulamasını oluşturabilir ve örnek PHP kodunu web uygulamasına dağıtmak için Git kullanabilirsiniz.

![Azure'da çalışan örnek uygulama](media/quickstart-php/hello-world-in-browser.png)

Mac, Windows veya Linux makinesi kullanarak buradaki adımları izleyebilirsiniz. Önkoşullar yüklendikten sonra adımların tamamlanması yaklaşık olarak beş dakika sürer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

* <a href="https://git-scm.com/" target="_blank">Git'i yükleyin</a>
* <a href="https://php.net/manual/install.php" target="_blank">PHP'yi yükleyin</a>

## <a name="download-the-sample-locally"></a>Örnekleri yerel makineye indirme

Bir terminal penceresinde aşağıdaki komutları çalıştırın. Bu işlem, örnek uygulamanın yerel makinenize kopyalanmasını ve örnek kodu içeren dizine gitmenizi sağlar. 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

Azure'a dağıttığınızda nasıl görüneceğini görmek için uygulamayı yerel olarak çalıştırın. Yerleşik PHP web sunucusunu başlatmak için bir terminal penceresi açın ve `php` komutunu kullanın.

```bash
php -S localhost:8080
```

Bir web tarayıcısı açın ve `http://localhost:8080` konumundaki örnek uygulamaya gidin.

Sayfada gösterilen örnek uygulamada **Merhaba Dünya!** iletisini görürsünüz.

![Yerel olarak çalışan örnek uygulama](media/quickstart-php/localhost-hello-world-in-browser.png)

Terminal pencerenizde **Ctrl+C** tuşlarına basarak web sunucusundan çıkın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

Cloud Shell, komutuyla App Service planında bir Web uygulaması oluşturun `myAppServicePlan` [`az webapp create`](/cli/azure/webapp#az_webapp_create) . 

Aşağıdaki örnekte `<app-name>` kısmını genel olarak benzersiz bir uygulama adıyla değiştirin (geçerli karakterler `a-z`, `0-9` ve `-` şeklindedir). Çalışma zamanı `PHP|7.4` olarak ayarlanmıştır. Desteklenen tüm çalışma zamanlarını görmek için öğesini çalıştırın [`az webapp list-runtimes`](/cli/azure/webapp#az_webapp_list_runtimes) . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> `(--%)`Powershell 3,0 ' de tanıtılan durdurma ayrıştırma sembolü, PowerShell komutları veya ifadeler olarak girişin yorumlanması Için PowerShell 'i yönlendirir.
>

Web uygulaması oluşturulduğunda Azure CLI aşağıda yer alan çıktıdaki gibi bilgiler gösterir:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Git dağıtımı etkinken boş bir yeni Web uygulaması oluşturdunuz.

> [!NOTE]
> Git uzak URL’si `deploymentLocalGitUrl` özelliği içinde `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` biçiminde gösterilir. Bu URL’ye daha sonra ihtiyacınız olacağı için URL’yi kaydedin.
>

Yeni oluşturduğunuz web uygulamasına göz atın. _&lt; App-name>_ , önceki adımda oluşturulan benzersiz uygulama adınızla değiştirin.

```bash
http://<app-name>.azurewebsites.net
```

Yeni web uygulamanız aşağıdaki gibi görünmelidir:

![Boş web uygulaması sayfası](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  main -> main
</pre>

## <a name="browse-to-the-app"></a>Uygulamaya göz atma

Web tarayıcınızı kullanarak, dağıtılan uygulamanın konumuna gidin.

```
http://<app-name>.azurewebsites.net
```

PHP örnek kodu bir Azure App Service web uygulamasında çalışıyor.

![Azure'da çalışan örnek uygulama](media/quickstart-php/hello-world-in-browser.png)

**Tebrikler!** App Service’e ilk PHP uygulamanızı dağıttınız.

## <a name="update-locally-and-redeploy-the-code"></a>Kodu yerel makinede güncelleştirme ve yeniden dağıtma

Bir yerel metin düzenleyicisi kullanarak `index.php` dosyasını PHP uygulaması içinde açın ve `echo` öğesinin yanındaki dizenin içinde bulunan metinde küçük bir değişiklik yapın:

```php
echo "Hello Azure!";
```

Yerel terminal penceresinde, değişikliklerinizi Git’e işleyin ve ardından kod değişikliklerini Azure’a gönderin.

```bash
git commit -am "updated output"
git push azure main
```

Dağıtım tamamlandıktan sonra **Uygulamaya göz atma** adımında açılan tarayıcı penceresine dönüp sayfayı yenileyin.

![Azure'da çalışan güncelleştirilmiş örnek uygulama](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Yeni Azure uygulamanızı yönetin

1. Oluşturduğunuz web uygulamasını yönetmek için <a href="https://portal.azure.com" target="_blank">Azure portalına</a> gidin. **Uygulama hizmetleri**' ni arayıp seçin.

    ![Uygulama Hizmetleri arama Azure portal, PHP Web uygulaması oluşturma](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Azure uygulamanızın adını seçin.

    ![Azure uygulamasına portal gezintisi](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Web uygulamanızın **genel bakış** sayfası görüntülenir. Burada, **Gözden** geçirme, **durdurma**, **yeniden başlatma** ve **silme** gibi temel yönetim görevlerini gerçekleştirebilirsiniz.

    ![Azure portalında App Service sayfası](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    Web uygulaması menüsü, uygulamanızı yapılandırmak için farklı seçenekler sağlar. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [MySQL ile PHP](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [PHP uygulamasını yapılandırma](configure-language-php.md)

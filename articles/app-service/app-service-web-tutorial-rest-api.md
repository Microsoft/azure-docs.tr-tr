---
title: 'Öğretici: CORS ile Restuz API barındırma'
description: Azure App Service’in RESTful API’lerinizi CORS desteğiyle barındırmanıza nasıl yardımcı olduğunu öğrenin. App Service, hem ön uç Web uygulamalarını hem de arka uç API 'Lerini barındırabilir.
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18
ms.openlocfilehash: 6d2f4d8fc7a2a7011a2417467f7131c4cfb26edc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788232"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Öğretici: Azure App Service’te CORS ile RESTful API barındırma

[Azure App Service](overview.md), yüksek oranda ölçeklenebilen, kendi kendine düzeltme eki uygulayan bir web barındırma hizmeti sunar. Buna ek olarak, App Service'de RESTful API'ler için yerleşik [Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) desteği vardır. Bu öğreticide CORS desteğiyle ASP.NET Core API uygulamasının App Service'e nasıl dağıtılacağı gösterilir. Komut satırı araçlarını kullanarak uygulamayı yapılandırır ve Git kullanarak dağıtırsınız. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure CLI kullanarak App Service kaynaklarını oluşturma
> * Git kullanarak Azure'a RESTful API dağıtımı yapma
> * App Service CORS desteğini etkinleştirme

Bu öğreticideki adımları MacOS, Linux ve Windows üzerinde izleyebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* <a href="https://git-scm.com/" target="_blank">Git'i yükleyin</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">En son .NET Core 3,1 SDK 'sını yükler</a>

## <a name="create-local-aspnet-core-app"></a>Yerel ASP.NET Core uygulaması oluşturma

Bu adımda, yerel ASP.NET Core projesini ayarlarsınız. App Service API'ler için diğer dillerde yazılmış aynı iş akışını destekler.

### <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Terminal penceresinde, `cd` ile bir çalışma dizinine gidin.  

Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Bu depo, şu öğreticiye dayanarak oluşturulan bir uygulama içerir: [Swagger kullanılan ASP.NET Core Web API'si yardım sayfaları](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). [Swagger kullanıcı arabirimine](https://swagger.io/swagger-ui/) ve Swagger JSON uç notasına hizmet vermek için bir Swagger oluşturucusu kullanır.

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Gerekli paketleri yüklemek, veritabanı geçişlerini çalıştırmak ve uygulamayı başlatmak için aşağıdaki komutları çalıştırın.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Swagger kullanıcı arabirimiyle çalışmak için tarayıcıda `http://localhost:5000/swagger` adresine gidin.

![Yerel olarak çalışan ASP.NET Core API'si](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

`http://localhost:5000/api/todo` adresine gidin ve ToDo JSON öğelerinin listesine bakın.

`http://localhost:5000` adresine gidin ve tarayıcı uygulamasıyla çalışın. Daha sonra CORS işlevselliğini test etmek için tarayıcı uygulamasının App Service'te bir uzak API'ye işaret etmesini sağlayacaksınız. Tarayıcı uygulamasının kodu deponun _wwwroot_ dizininde bulunabilir.

ASP.NET Core’u dilediğiniz zaman durdurmak için, terminalde `Ctrl+C` tuşlarına basın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Uygulamayı Azure’da dağıtma

Bu adımda, SQL Veritabanı’na bağlı .NET Core uygulamanızı App Service’e dağıtırsınız.

### <a name="configure-local-git-deployment"></a>Yerel git dağıtımını yapılandırma

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Web uygulaması oluşturma

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Git üzerinden Azure'a gönderme

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Azure uygulamasına gidin

Tarayıcıda `http://<app_name>.azurewebsites.net/swagger` adresine gidin ve Swagger kullanıcı arabirimiyle çalışın.

![Azure App Service'te çalışan ASP.NET Core API'si](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Dağıtılan API'nizin _swagger.json_ dosyasını görmek için `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` adresine gidin.

Dağıtılan API'nizin çalıştığını görmek için `http://<app_name>.azurewebsites.net/api/todo` adresine gidin.

## <a name="add-cors-functionality"></a>CORS işlevselliği ekleme

Ardından, API'niz için App Service'te yerleşik CORS desteğini etkinleştirirsiniz.

### <a name="test-cors-in-sample-app"></a>Örnek uygulamada CORS'yi test etme

Yerel deponuzda _wwwroot/index.html_ dosyasını açın.

51. satırda, `apiEndpoint` değişkenini dağıtılan API'nizin URL'sine (`http://<app_name>.azurewebsites.net`) ayarlayın. _\<appname>_ App Service ' deki uygulamanızın adıyla değiştirin.

Yerel terminal pencerenizde örnek uygulamayı yeniden çalıştırın.

```bash
dotnet run
```

`http://localhost:5000` adresindeki tarayıcı uygulamasına gidin. Tarayıcınızda geliştirici araçları penceresini açın ( `Ctrl` + `Shift` + `i` Windows için Chrome 'da) ve **konsol** sekmesini inceleyin. Artık hata iletisini görmeniz gerekir `No 'Access-Control-Allow-Origin' header is present on the requested resource` .

![Tarayıcı istemcisinde CORS hatası](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

Tarayıcı uygulaması (`http://localhost:5000`) ile uzak kaynak (`http://<app_name>.azurewebsites.net`) arasındaki etki alanı uyuşmazlığından ve App Service'deki API'nizin `Access-Control-Allow-Origin` üst bilgisi göndermemesinden dolayı, tarayıcınız etki alanları arası içeriğin tarayıcı uygulamanıza yüklenmesini engelledi.

Üretim ortamında, tarayıcı uygulamanızın localhost URL değeri yerine genel URL'si olabilir, ama localhost URL'ye CORS etkinleştirmesi genel URL ile aynıdır.

### <a name="enable-cors"></a>CORS'yi etkinleştirme 

Cloud Shell, komutunu kullanarak istemci URL 'nizin CORS 'yi etkinleştirin [`az webapp cors add`](/cli/azure/webapp/cors#az_webapp_cors_add) . _&lt; App-name>_ yer tutucusunu değiştirin.

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

`properties.cors.allowedOrigins` içinde birden çok istemci URL'si belirtebilirsiniz (`"['URL1','URL2',...]"`). Ayrıca `"['*']"` ile tüm istemci URL'lerini etkinleştirebilirsiniz.

> [!NOTE]
> Uygulamanız, gönderilecek tanımlama bilgileri veya kimlik doğrulama belirteçleri gibi kimlik bilgilerini gerektiriyorsa tarayıcı, `ACCESS-CONTROL-ALLOW-CREDENTIALS` yanıtta üst bilgi gerektirebilir. Bunu App Service etkinleştirmek için `properties.cors.supportCredentials` CORS yapılandırmasında olarak ayarlayın `true` . Bu, `allowedOrigins` içerdiğinde etkinleştirilemez `'*'` .

### <a name="test-cors-again"></a>CORS'yi yeniden test etme

`http://localhost:5000` adresindeki tarayıcı uygulamasını yenileyin. **Konsol** penceresindeki hata iletisi artık kaldırılır; dağıtılan API'den verileri görebilir ve etkileşimli çalışabilirsiniz. Uzak API'niz artık yerel olarak çalıştırılan tarayıcı uygulamanıza CORS'yi destekler. 

![Tarayıcı istemcisinde CORS başarılı](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Tebrikler, Azure App Service'te CORS destekli bir API çalıştırıyorsunuz.

## <a name="app-service-cors-vs-your-cors"></a>App Service CORS'si ile sizin CORS'niz

Daha fazla esneklik elde etmek için App Service CORS'si yerine kendi CORS yardımcı programlarınızı kullanabilirsiniz. Örneğin, farklı yollar veya yöntemler için izin verilen farklı çıkış noktaları belirtmek isteyebilirsiniz. App Service CORS'si tüm API yolları ve yöntemleri için tek bir izin verilen çıkış noktaları kümesi belirtmenize olanak tanıdığından, kendi CORS kodunuzu kullanmak isteyebilirsiniz. [Çıkış Noktaları Arası İstekleri Etkinleştirme (CORS)](/aspnet/core/security/cors) konusunda, ASP.NET Core'un bunu nasıl yaptığını görebilirsiniz.

> [!NOTE]
> App Service CORS'si ile kendi CORS kodunuzu birlikte kullanmaya çalışmayın. Birlikte kullanıldıklarında, App Service CORS'si öncelikli olur ve kendi CORS kodunuzun hiçbir etkisi olmaz.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Sonraki adımlar

Öğrendikleriniz:

> [!div class="checklist"]
> * Azure CLI kullanarak App Service kaynaklarını oluşturma
> * Git kullanarak Azure'a RESTful API dağıtımı yapma
> * App Service CORS desteğini etkinleştirme

Kullanıcıların kimlik doğrulamasının ve yetkilendirmesinin nasıl yapılacağını öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: Uçtan uca kullanıcıların kimliğini doğrulama ve kullanıcıları yetkilendirme](tutorial-auth-aad.md)

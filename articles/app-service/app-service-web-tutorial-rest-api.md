---
title: "Öğretici: CORS ile Restizsiz API 'YI barındırma Azure App Service"
description: Azure App Service CORS desteği sayesinde, yeniden yaptığınız API 'lerinizi barındırmanıza nasıl yardımcı olduğunu öğrenin.
services: app-service\api
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: f13b390047ea4d8280b106f3b02a8f18944a6f99
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255111"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Öğretici: Azure App Service CORS ile Restuz bir API barındırın

[Azure App Service](overview.md) , yüksek düzeyde ölçeklenebilir, kendini yayama bir Web barındırma hizmeti sağlar. Ayrıca App Service, yeniden derlenen API 'Ler için, [çıkış noktaları arası kaynak paylaşımı (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) için yerleşik desteğe sahiptir. Bu öğreticide, CORS desteğiyle App Service bir ASP.NET Core API uygulamasının nasıl dağıtılacağı gösterilmektedir. Uygulama, komut satırı araçlarını kullanarak yapılandırılır ve uygulamayı git kullanarak dağıtırsınız. 

Bu öğreticide şunların nasıl yapıladığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure CLı kullanarak App Service kaynakları oluşturma
> * Git kullanarak Azure 'a yeniden bir API dağıtma
> * App Service CORS desteğini etkinleştir

Bu öğreticideki adımları macOS, Linux, Windows üzerinde izleyebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

Bu öğreticiyi tamamlayabilmeniz için:

* [Git 'ı yükler](https://git-scm.com/).
* [.NET Core uygulamasını yükler](https://www.microsoft.com/net/core/).

## <a name="create-local-aspnet-core-app"></a>Yerel ASP.NET Core uygulaması oluşturma

Bu adımda, yerel ASP.NET Core projesini ayarlarsınız. App Service, diğer dillerde yazılmış API 'Ler için aynı iş akışını destekler.

### <a name="clone-the-sample-application"></a>Örnek uygulamayı kopyalama

Terminal penceresinde,-0 ' ı bir çalışma dizinine @no__t.  

Örnek depoyu kopyalamak için aşağıdaki komutu çalıştırın. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Bu depo, aşağıdaki öğreticiye göre oluşturulan bir uygulama içerir: [Swagger kullanarak Web API 'si yardım sayfaları ASP.NET Core](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). Swagger [Kullanıcı arabirimine](https://swagger.io/swagger-ui/) ve Swagger JSON uç noktasına hizmeti sağlamak için Swagger Oluşturucu kullanır.

### <a name="run-the-application"></a>Uygulamayı çalıştırma

Gerekli paketleri yüklemek, veritabanı geçişlerini çalıştırmak ve uygulamayı başlatmak için aşağıdaki komutları çalıştırın.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Swagger kullanıcı arabiriminden oynatmak için tarayıcıda `http://localhost:5000/swagger` ' a gidin.

![Yerel olarak çalışan ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

@No__t-0 ' a gidin ve ToDo JSON öğelerinin listesini görüntüleyin.

@No__t-0 ' a gidin ve tarayıcı uygulamasıyla yürütün. Daha sonra, CORS işlevselliğini test etmek için tarayıcı uygulamasını App Service içindeki bir uzak API 'ye işaret edersiniz. Tarayıcı uygulaması kodu, deponun _Wwwroot_ dizininde bulunur.

ASP.NET Core durdurmak için, terminalde `Ctrl+C` ' a basın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Uygulamayı Azure 'a dağıtma

Bu adımda, SQL veritabanı bağlantılı .NET Core uygulamanızı App Service 'ye dağıtırsınız.

### <a name="configure-local-git-deployment"></a>Yerel git dağıtımını yapılandırma

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service planı oluşturma

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Web uygulaması oluşturma

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Git 'ten Azure 'a gönderme

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Azure uygulamasına gidin

Bir tarayıcıda `http://<app_name>.azurewebsites.net/swagger` ' a gidin ve Swagger Kullanıcı arabirimi ile yürütün.

![Azure App Service çalıştıran API ASP.NET Core](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Dağıtılan API 'niz için _Swagger. JSON_ öğesini görmek için `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` ' a gidin.

Dağıtılan API 'nizin çalıştığını görmek için `http://<app_name>.azurewebsites.net/api/todo` ' a gidin.

## <a name="add-cors-functionality"></a>CORS işlevselliği ekleme

Daha sonra, API 'niz için App Service yerleşik CORS desteğini etkinleştirirsiniz.

### <a name="test-cors-in-sample-app"></a>Örnek uygulamada CORS 'yi test etme

Yerel deponuzda _Wwwroot/index.html_açın.

Satır 51 ' de `apiEndpoint` değişkenini dağıtılan API 'nizin URL 'SI olarak ayarlayın (`http://<app_name>.azurewebsites.net`). _@No__t-1appname >_ değerini App Service uygulamanızın adıyla değiştirin.

Yerel Terminal pencerenizde, örnek uygulamayı yeniden çalıştırın.

```bash
dotnet run
```

@No__t-0 ' da tarayıcı uygulamasına gidin. Tarayıcınızda geliştirici araçları penceresini açın (`Ctrl` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 for Windows) ve **konsol** sekmesini inceleyin. Artık `No 'Access-Control-Allow-Origin' header is present on the requested resource` hata iletisini görmeniz gerekir.

![Tarayıcı istemcisinde CORS hatası](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

Tarayıcı uygulaması (`http://localhost:5000`) ile uzak kaynak (`http://<app_name>.azurewebsites.net`) arasındaki etki alanı eşleşmediği ve App Service API 'nizin `Access-Control-Allow-Origin` üst bilgisini göndermediği konusunda, tarayıcınız etki alanları arası içeriğin tarayıcı uygulamanızda yüklenmesini engelledi.

Üretimde, tarayıcı uygulamanızın localhost URL 'SI yerine ortak bir URL 'SI vardır, ancak bir localhost URL 'sine CORS 'yi etkinleştirme yöntemi, genel bir URL ile aynıdır.

### <a name="enable-cors"></a>CORS 'yi etkinleştir 

Cloud Shell, [`az resource update`](/cli/azure/resource#az-resource-update) komutunu kullanarak istemci URL 'nizin CORS 'yi etkinleştirin. _@No__t-1appname >_ yer tutucusunu değiştirin.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.cors.allowedOrigins="['http://localhost:5000']" --api-version 2015-06-01
```

@No__t-0 ' da birden fazla istemci URL 'SI ayarlayabilirsiniz (`"['URL1','URL2',...]"`). Ayrıca, `"['*']"` ile tüm istemci URL 'Lerini de etkinleştirebilirsiniz.

> [!NOTE]
> Uygulamanız, gönderilecek tanımlama bilgileri veya kimlik doğrulama belirteçleri gibi kimlik bilgilerini gerektiriyorsa, tarayıcıda yanıtta `ACCESS-CONTROL-ALLOW-CREDENTIALS` üst bilgisi gerekebilir. Bunu App Service etkinleştirmek için, CORS yapılandırmasında `properties.cors.supportCredentials` `true` olarak ayarlayın. @No__t-2 `'*'` içerdiğinde bu etkinleştirilemez.

### <a name="test-cors-again"></a>CORS 'yi yeniden test et

@No__t-0 ' da tarayıcı uygulamasını yenileyin. **Konsol** penceresindeki hata iletisi artık kayboldu ve dağıtılan API 'den verileri görebilir ve bununla etkileşime geçebilirsiniz. Uzak API 'niz artık yerel olarak çalışan tarayıcı uygulamanızda CORS 'yi destekliyor. 

![Tarayıcı istemcisinde CORS başarısı](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Tebrikler, CORS desteği ile Azure App Service bir API çalıştırıyorsunuz.

## <a name="app-service-cors-vs-your-cors"></a>CORS ile CORS App Service

Daha fazla esneklik için App Service CORS yerine kendi CORS yardımcı programlarını kullanabilirsiniz. Örneğin, farklı yollar veya yöntemler için izin verilen farklı noktaları belirtmek isteyebilirsiniz. App Service CORS, tüm API yolları ve yöntemleri için bir kabul edilen kaynaklar kümesi belirtmenize izin verdiklerinden, kendi CORS kodunuzu kullanmak istersiniz. ASP.NET Core, [çıkış noktaları arası istekleri (CORS) etkinleştirme](/aspnet/core/security/cors)konusunda bilgi için bkz.

> [!NOTE]
> App Service CORS 'yi ve kendi CORS kodunuzu birlikte kullanmaya çalışmayın. Birlikte kullanıldığında App Service CORS önceliklidir ve kendi CORS kodunuzun etkisi yoktur.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Sonraki adımlar

Öğrendikleriniz:

> [!div class="checklist"]
> * Azure CLı kullanarak App Service kaynakları oluşturma
> * Git kullanarak Azure 'a yeniden bir API dağıtma
> * App Service CORS desteğini etkinleştir

Kullanıcıların kimliğini doğrulamak ve yetkilendirmek hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: kullanıcıların kimliğini kimlik doğrulama ve yetkilendirme](app-service-web-tutorial-auth-aad.md)

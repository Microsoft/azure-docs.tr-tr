---
title: Linux 'ta Ruby Web uygulaması oluşturma-Azure App Service | Microsoft Docs
description: Linux üzerinde App Service ile Ruby on Rails uygulaması oluşturmayı öğrenin.
keywords: azure app service, linux, oss, ruby, rails
services: app-service
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/11/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: cfcea4c4450ea806b36d1d46c17c6676f7d92893
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071077"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Linux üzerinde App Service'te Ruby on Rails uygulaması oluşturma

[Linux’ta App Service](app-service-linux-intro.md) Linux işletim sistemini kullanan yüksek oranda ölçeklenebilir, otomatik olarak düzeltme eki uygulayan bir web barındırma hizmeti sağlar. Bu hızlı başlangıç öğreticisinde, [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)kullanılarak Linux üzerinde Azure App Service bir Ruby on rayın uygulamasının nasıl dağıtılacağı gösterilmektedir.

> [!NOTE]
> Ruby geliştirme yığını şu anda yalnızca Ruby on Rails'i desteklemektedir. Sinatra gibi farklı bir platform kullanmak istiyorsanız veya [Desteklenmeyen bir Ruby sürümü](app-service-linux-intro.md)kullanmak istiyorsanız, [bunu özel bir kapsayıcıda çalıştırmanız](quickstart-docker-go.md)gerekir.

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Ruby 2,6 veya üstünü yükler</a>
* <a href="https://git-scm.com/" target="_blank">Git'i yükleyin</a>

## <a name="download-the-sample"></a>Örneği indirme

Bir terminal penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma

Azure'a dağıttığınızda nasıl görüneceğini görmek için uygulamayı yerel olarak çalıştırın. Bir terminal penceresi açın, `hello-world` dizinine geçiş yapın ve sunucuyu başlatmak için `rails server` komutunu kullanın.

İlk adım, gerekli gem'leri yüklemektir. `Gemfile` Örneğe dahil edilmiştir, bu nedenle yalnızca aşağıdaki komutu çalıştırın:

```bash
bundle install
```

Gem'ler yüklendikten sonra uygulamayı başlatmak için bundler'ı kullanacağız:

```bash
bundle exec rails server
```

Web tarayıcınızı kullanarak uygulamayı yerel olarak test etmek için `http://localhost:3000` yoluna gidin.

![Hello World yapılandırıldı](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Web uygulaması oluşturma

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Yeni oluşturduğunuz Web uygulamanızı yerleşik görüntüyle görmek için uygulamaya gidin. _&lt;app name>_ değerini kendi web uygulamanızın adıyla değiştirin.

```bash
http://<app_name>.azurewebsites.net
```

Yeni web uygulamanız aşağıdaki gibi görünmelidir:

![Karşılama sayfası](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Uygulamanızı dağıtma

Yerel uygulamayı Azure Web uygulamanıza dağıtmak için aşağıdaki komutları çalıştırın:

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

Uzaktan dağıtım işlemlerinin başarılı olarak bildirildiğini doğrulayın. Komutlar aşağıdaki metne benzer bir çıktı oluşturur:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

Dağıtım tamamlandıktan sonra, Web uygulamasının yeniden başlatılması için 10 saniye bekleyin ve sonra Web uygulamasına gidip sonuçları doğrulayın.

```bash
http://<app-name>.azurewebsites.net
```

![güncelleştirilen web uygulaması](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Uygulama yeniden başlatılırken, tarayıcıda http durum kodunu `Error 503 Server unavailable` `Hey, Ruby developers!` veya varsayılan sayfayı gözlemleyebilirsiniz. Uygulamanın tam olarak yeniden başlatılması birkaç dakika sürebilir.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Postgres ile Ruby on rayları](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby uygulamasını yapılandırma](configure-language-ruby.md)

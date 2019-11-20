---
title: Ruby uygulamalarını Yapılandırma-Azure App Service
description: Bu öğreticide, Linux üzerinde Azure App Service için Ruby uygulamalarını yazma ve yapılandırma seçenekleri açıklanmaktadır.
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
ms.openlocfilehash: 71734396e90987fb1e318f3d8bb01d957fc0fda1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071299"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Azure App Service için Linux Ruby uygulaması yapılandırma

Bu makalede, [Azure App Service](app-service-linux-intro.md) Ruby uygulamalarının nasıl çalıştığı ve gerektiğinde App Service davranışının nasıl özelleştirileceği açıklanır. Ruby uygulamalarının tüm gerekli [PIP](https://pypi.org/project/pip/) modülleri ile dağıtılması gerekir.

Bu kılavuz, App Service içinde yerleşik bir Linux kapsayıcısı kullanan Ruby geliştiricileri için temel kavramlar ve yönergeler sağlar. Azure App Service hiç kullanmadıysanız, önce [PostgreSQL öğreticisiyle](tutorial-ruby-postgres-app.md) [Ruby hızlı başlangıç](quickstart-ruby.md) ve Ruby 'yi izlemeniz gerekir.

## <a name="show-ruby-version"></a>Ruby sürümünü göster

Geçerli Ruby sürümünü göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Desteklenen tüm Ruby sürümlerini göstermek için [Cloud Shell](https://shell.azure.com)aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Bunun yerine kendi kapsayıcı görüntünüzü oluşturarak desteklenmeyen bir Ruby sürümü çalıştırabilirsiniz. Daha fazla bilgi için bkz. [özel bir Docker görüntüsü kullanma](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ruby sürümünü ayarla

[Cloud Shell](https://shell.azure.com) Ruby sürümünü 2,3 olarak ayarlamak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Dağıtım saati sırasında aşağıdakine benzer hatalar görürseniz:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> veya
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Bu, projenizde yapılandırılan Ruby sürümünün çalıştırdığınız kapsayıcıda yüklü olan sürümden (`2.3.3` Yukarıdaki örnekte) farklı olduğu anlamına gelir. Yukarıdaki örnekte, hem *gemfile* hem de *. Ruby-Version* ' ı işaretleyin ve Ruby sürümünün ayarlı olmadığını veya çalıştırmakta olduğunuz kapsayıcıda yüklü olan sürüme (`2.3.3` Yukarıdaki örnekte) ayarlandığını doğrulayın.

## <a name="access-environment-variables"></a>Ortam değişkenlerine erişin

App Service, uygulama ayarlarınızı uygulama kodunuzun dışında [ayarlayabilirsiniz](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) . Daha sonra standart [env ['\<Path-Name > ']](https://ruby-doc.org/core-2.3.3/ENV.html) modelini kullanarak bunlara erişebilirsiniz. Örneğin, adlı `WEBSITE_SITE_NAME`bir uygulama ayarına erişmek için aşağıdaki kodu kullanın:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Dağıtımı Özelleştir

Bir [Git deposunu](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)veya derleme işlemleri Içeren bir [ZIP paketini](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) dağıtırken, dağıtım altyapısı (kudu) varsayılan olarak aşağıdaki dağıtım sonrası adımları otomatik olarak çalıştırır:

1. Bir *Gemfile* var olup olmadığını denetleyin.
1. `bundle clean` öğesini çalıştırın. 
1. `bundle install --path "vendor/bundle"` öğesini çalıştırın.
1. Gems 'yi satıcı/önbellek klasörüne paketlemek için çalıştırın `bundle package` .

### <a name="use---without-flag"></a>Bayrak olmadan--kullanın

Bayrak olmadan `bundle install` [--](https://bundler.io/man/bundle-install.1.html) ile çalıştırmak için, [uygulama ayarını](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) virgülle ayrılmış gruplar listesine ayarlayın. `BUNDLE_WITHOUT` Örneğin, aşağıdaki komut olarak `development,test`ayarlanır.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Bu ayar tanımlanmışsa, dağıtım motoru ile `bundle install` `--without $BUNDLE_WITHOUT`çalışır.

### <a name="precompile-assets"></a>Varlıkları önceden derle

Dağıtım sonrası adımları, varsayılan olarak varlıkları önceden derlemeye yönelik değildir. Varlık ön derlemesini açmak için [uygulama ayarını](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) olarak `true`ayarlayın `ASSETS_PRECOMPILE` . Ardından komut `bundle exec rake --trace assets:precompile` , dağıtım sonrası adımların sonunda çalıştırılır. Örneğin:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Daha fazla bilgi için bkz. [statik varlıkları](#serve-static-assets)sunma.

## <a name="customize-start-up"></a>Başlatmayı özelleştirme

Varsayılan olarak, Ruby kapsayıcısı aşağıdaki sırada bir rayı sunucusunu başlatır (daha fazla bilgi için [Başlangıç betiğine](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)bakın):

1. Zaten yoksa bir [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) değeri oluşturun. Uygulamanın üretim modunda çalışması için bu değer gereklidir.
1. Ortam değişkenini olarak `production`ayarlayın. `RAILS_ENV`
1. Daha önce çalışan bir rayla sunucu tarafından bırakılan *tmp/PIDS* dizinindeki tüm *. pid* dosyalarını silin.
1. Tüm bağımlılıkların yüklü olup olmadığını denetleyin. Aksi takdirde, yerel *satıcı/önbellek* dizininden Gems 'yi yüklemeyi deneyin.
1. `rails server -e $RAILS_ENV` öğesini çalıştırın.

Başlangıç işlemini aşağıdaki yollarla özelleştirebilirsiniz:

- [Statik varlıkları sunma](#serve-static-assets)
- [Üretim dışı modda çalıştır](#run-in-non-production-mode)
- [Secret_key_base el ile ayarlama](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statik varlıkları sunma

Ruby kapsayıcısındaki raya sunucusu varsayılan olarak üretim modunda çalışır ve [varlıkların önceden derlenmiş olduğunu ve Web sunucunuz tarafından sunulduğunu varsayar](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Raylar sunucusundan statik varlıklara hizmeti sağlamak için iki şey yapmanız gerekir:

-  - [Statik varlıkları yerel olarak derlemeye](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) yönelik varlıkları önceden derleyin ve el ile dağıtın. Ya da, bunun yerine dağıtım altyapısının bunu işlemesini sağlayın (bkz. [varlıkları önceden derle](#precompile-assets).
- **Statik dosyalara hizmet vermeye olanak sağlama** -Ruby kapsayıcısından statik varlıklar sunmak için [ `RAILS_SERVE_STATIC_FILES` uygulama ayarını](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) olarak `true`ayarlayın. Örneğin:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Üretim dışı modda çalıştır

Rayları sunucusu varsayılan olarak üretim modunda çalışır. Geliştirme modunda çalıştırmak için, örneğin, [uygulama ayarını](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) olarak `development`ayarlayın `RAILS_ENV` .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Ancak, bu ayar tek başına, yalnızca localhost isteklerini kabul eden ve kapsayıcının dışında erişilebilir olmayan bir geliştirme modunda başlatılmasına neden olur. Uzak istemci isteklerini kabul etmek için, `APP_COMMAND_LINE` [uygulama ayarını](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) olarak `rails server -b 0.0.0.0`ayarlayın. Bu uygulama ayarı, Ruby kapsayıcısında özel bir komut çalıştırmanızı sağlar. Örneğin:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Secret_key_base el ile ayarlama

Sizin için bir tane `secret_key_base` oluşturmak App Service sağlamak yerine kendi değerini kullanmak için, [uygulama ayarını](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) istediğiniz `SECRET_KEY_BASE` değerle ayarlayın. Örneğin:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH oturumunu tarayıcıda aç

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: PostgreSQL ile raylar uygulaması](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux SSS](app-service-linux-faq.md)

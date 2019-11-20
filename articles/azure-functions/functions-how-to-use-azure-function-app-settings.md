---
title: Azure 'da işlev uygulaması ayarlarını yapılandırma
description: Azure işlevi uygulama ayarlarını yapılandırmayı öğrenin.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 7ad7f6156bbd8ea86e3e71bda4b23dac9722a0ef
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170727"
---
# <a name="manage-your-function-app"></a>İşlev uygulamanızı yönetme 

Azure Işlevlerinde bir işlev uygulaması, bireysel işlevleriniz için yürütme bağlamı sağlar. İşlev uygulaması davranışları, belirli bir işlev uygulaması tarafından barındırılan tüm işlevler için geçerlidir. Bir işlev uygulamasındaki tüm işlevler aynı [dilde](supported-languages.md)olmalıdır. 

Bir işlev uygulamasındaki tek işlevler birlikte dağıtılır ve birlikte ölçeklendirilir. İşlev uygulaması ölçeklenirken, aynı işlev uygulamasındaki tüm işlevler, örnek başına kaynakları paylaşır. 

Bağlantı dizeleri, ortam değişkenleri ve diğer uygulama ayarları her bir işlev uygulaması için ayrı olarak tanımlanır. İşlev uygulamaları arasında paylaşılması gereken tüm veriler, kalıcı bir mağazada dışarıdan depolanmalıdır.

Bu makalede, işlev uygulamalarınızın nasıl yapılandırılacağı ve yönetileceği açıklanmaktadır. 

> [!TIP]  
> Birçok yapılandırma seçeneği de [Azure CLI]kullanılarak yönetilebilir. 

## <a name="get-started-in-the-azure-portal"></a>Azure portalını kullanmaya başlama

Başlamak için [Azure Portal] gidin ve Azure hesabınızda oturum açın. Portalın en üstündeki arama çubuğunda, işlev uygulamanızın adını yazın ve uygulamayı listeden seçin. İşlev uygulamanızı seçtikten sonra aşağıdaki sayfayı görürsünüz:

![Azure portal işlev uygulamasına genel bakış](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Özel bakış sayfasından, özellikle **[uygulama ayarları](#settings)** ve **[platform özellikleri](#platform-features)** ' nde, işlev uygulamanızı yönetmek için ihtiyacınız olan her şeye gidebilirsiniz.

## <a name="settings"></a>Uygulama ayarları

**Uygulama ayarları** sekmesi, işlev uygulamanız tarafından kullanılan ayarları korur. Bu ayarlar şifreli olarak depolanır ve portalda değerleri görmek için **değerleri göster** ' i seçmeniz gerekir. Ayrıca, Azure CLı kullanarak uygulama ayarlarına erişebilirsiniz.

### <a name="portal"></a>Portal

Portalda bir ayar eklemek için **Yeni uygulama ayarı** ' nı seçin ve yeni anahtar-değer çiftini ekleyin.

![Azure portal işlev uygulaması ayarları.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) Komut, aşağıdaki örnekte olduğu gibi var olan uygulama ayarlarını döndürür:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) Komut bir uygulama ayarı ekler veya güncelleştirir. Aşağıdaki örnek, adında `CUSTOM_FUNCTION_APP_SETTING` bir anahtar ve `12345`değeri olan bir ayar oluşturur:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Uygulama ayarlarını kullanma

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Yerel olarak bir işlev uygulaması geliştirirken, bu değerlerin yerel kopyalarını yerel. Settings. JSON proje dosyasında korumanız gerekir. Daha fazla bilgi için bkz. [yerel ayarlar dosyası](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Platform özellikleri

![İşlev uygulaması platform özellikleri sekmesi.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

İşlev uygulamaları ' de çalışır ve Azure App Service platformu tarafından korunur. Bu nedenle, işlev uygulamalarınızın Azure 'un çekirdek Web barındırma platformu özelliklerinin çoğuna erişimi vardır. **Platform özellikleri** sekmesi, işlev uygulamalarınızda kullanabileceğiniz App Service platformunun birçok özelliğine erişirsiniz. 

> [!NOTE]
> Bir işlev uygulaması tüketim barındırma planı üzerinde çalıştırıldığında tüm App Service özellikleri kullanılamaz.

Bu makalenin geri kalanı, Işlevler için yararlı olan Azure portal aşağıdaki App Service özelliklerine odaklanmaktadır:

+ [App Service Düzenleyicisi](#editor)
+ [Console](#console)
+ [Gelişmiş araçlar (kudu)](#kudu)
+ [Dağıtım seçenekleri](#deployment)
+ [CORS](#cors)
+ [Kimlik Doğrulaması](#auth)

App Service ayarları ile çalışma hakkında daha fazla bilgi için bkz. [Configure Azure App Service Settings](../app-service/configure-common.md).

### <a name="editor"></a>App Service Düzenleyicisi

![App Service Düzenleyicisi](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service Düzenleyicisi, JSON yapılandırma dosyalarını ve kod dosyalarını benzer şekilde değiştirmek için kullanabileceğiniz, gelişmiş bir yerleşik düzenleyici düzenleyicidir. Bu seçeneğin belirlenmesi, temel bir düzenleyici ile ayrı bir tarayıcı sekmesi başlatır. Bu, git deposu ile tümleştirmenize, kod çalıştırmanıza ve hata ayıklamanıza ve işlev uygulaması ayarlarını değiştirmenize olanak sağlar. Bu düzenleyici, işlevleriniz için yerleşik işlev Düzenleyicisi ile karşılaştırıldığında gelişmiş bir geliştirme ortamı sağlar.  

Yerel bilgisayarınızda işlevlerinizi geliştirmeyi düşünmenizi öneririz. Yerel olarak geliştirip Azure 'da yayımladığınızda, proje dosyalarınız portalda salt okunurdur. Daha fazla bilgi edinmek için bkz. [Azure işlevlerini yerel olarak kod ve test](functions-develop-local.md)etme.

### <a name="console"></a>Konsola

![İşlev uygulaması konsolu](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Portal içi konsol, komut satırından işlev uygulamanız ile etkileşime geçmeyi tercih ettiğiniz durumlarda ideal bir geliştirici aracıdır. Ortak komutlar dizin ve dosya oluşturma ve gezinmeyi ve toplu iş dosyalarını ve betikleri yürütmeyi içerir. 

Yerel olarak geliştirilirken [Azure Functions Core Tools](functions-run-local.md) ve [Azure CLI]kullanmanızı öneririz.

### <a name="kudu"></a>Gelişmiş araçlar (kudu)

![Kudu yapılandırma](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service için gelişmiş araçlar (kudu olarak da bilinir), işlev uygulamanızın gelişmiş yönetim özelliklerine erişim sağlar. Kudu 'den sistem bilgilerini, uygulama ayarlarını, ortam değişkenlerini, site uzantılarını, HTTP üstbilgilerini ve sunucu değişkenlerini yönetirsiniz. Ayrıca, işlev uygulamanız için SCM uç noktasına göz atarak **kudu** 'yi de başlatabilirsiniz.`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>Dağıtım Merkezi

İşlev kodunuzu geliştirmek ve sürdürmek için bir kaynak denetimi çözümü kullandığınızda, dağıtım merkezi kaynak denetiminden derleme ve dağıtım yapmanızı sağlar. Projeniz, güncelleştirmeler yaptığınızda Azure 'a oluşturulup dağıtılır. Daha fazla bilgi için bkz. [Azure Işlevlerinde dağıtım teknolojileri](functions-deployment-technologies.md).

### <a name="cors"></a>Çıkış noktaları arası kaynak paylaşımı

Modern tarayıcılar istemcide kötü amaçlı kod yürütmeyi engellemek için Web uygulamalarından gelen istekleri ayrı bir etki alanında çalışan kaynaklara engeller. [Çıkış noktaları arası kaynak paylaşımı (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) , bir `Access-Control-Allow-Origin` üst bilginin, hangi kaynakların işlev uygulamanızda bitiş noktaları çağırabileceği hakkında bilgi tanımlamasına izin verir.

#### <a name="portal"></a>Portal

İşlev uygulamanız için **izin verilen** kaynaklar listesini yapılandırdığınızda, `Access-Control-Allow-Origin` üst bilgi, işlev uygulamanızdaki HTTP uç noktalarından gelen tüm yanıtlara otomatik olarak eklenir. 

![İşlev uygulamasının CORS listesini yapılandır](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Joker karakteri (`*`) kullanıldığında, diğer tüm etki alanları yok sayılır. 

İzin verilen çıkış noktaları listesine bir etki alanı eklemek için [komutunukullanın.`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) Aşağıdaki örnek contoso.com etki alanını ekler:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Mevcut izin verilen kaynakları listelemek için [komutunukullanın.`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show)

### <a name="auth"></a>Yetkilendirmesi

![İşlev uygulaması için kimlik doğrulamasını yapılandırma](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

İşlevler bir HTTP tetikleyicisi kullanırken, önce kimlik doğrulamasından geçmek için çağrılar yapmanız gerekebilir. App Service, Facebook, Microsoft ve Twitter gibi sosyal sağlayıcılarla Azure Active Directory kimlik doğrulamasını ve oturum açmayı destekler. Belirli kimlik doğrulama sağlayıcılarını yapılandırma hakkında ayrıntılı bilgi için bkz. [Azure App Service kimlik doğrulamasına genel bakış](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Sonraki adımlar

+ [Azure App Service ayarlarını yapılandırma](../app-service/configure-common.md)
+ [Azure İşlevleri için sürekli dağıtım](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure portal]: https://portal.azure.com

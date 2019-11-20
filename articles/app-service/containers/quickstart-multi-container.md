---
title: Docker Compose kullanarak çok Kapsayıcılı uygulama oluşturma Azure App Service
description: Azure Kapsayıcılar için Web App'te ilk çok kapsayıcılı uygulamanızı birkaç dakikada dağıtın
keywords: Azure App Service, Web uygulaması, Linux, Docker, Compose, çok Kapsayıcılı, çok Kapsayıcılı, kapsayıcılar için Web App, birden çok kapsayıcı, kapsayıcı, WordPress, MySQL için Azure DB, kapsayıcılarla üretim veritabanı
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 89cf13fd4405b9ddcbc5b31fad9f0c945aef64aa
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071117"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Docker Compose yapılandırma kullanarak çok Kapsayıcılı (Önizleme) uygulama oluşturma

[Kapsayıcılar için Web App](app-service-linux-intro.md), Docker görüntülerini esnek bir şekilde kullanmanızı sağlar. Bu hızlı başlangıçta Docker Compose yapılandırması kullanarak [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)'de Kapsayıcılar için Web App'e çok kapsayıcılı uygulama dağıtma adımları gösterilmektedir.

Bu hızlı başlangıcı Cloud Shell'de tamamlayacaksınız ama bu komutları [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 veya üzeri) ile yerel olarak da çalıştırabilirsiniz. 

![Kapsayıcılar için Web App üzerinde örnek çok kapsayıcılı uygulama][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Örneği indirme

Bu hızlı başlangıç için [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) Compose dosyasını kullanacaksınız. Yapılandırma dosyasına [Azure Örnekleri](https://github.com/Azure-Samples/multicontainerwordpress) sayfasından ulaşabilirsiniz.

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Cloud Shell'de bir quickstart dizini oluşturun ve o dizine geçin.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ardından, örnek uygulama deposunu quickstart dizininize kopyalamak için aşağıdaki komutu çalıştırın. Ardından `multicontainerwordpress` dizinine geçin.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Cloud Shell içinde [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *Orta Güney ABD* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur. **Standart** katmanda Linux üzerinde App Service için desteklenen tüm konumları görüntülemek için [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) komutunu çalıştırın.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Genellikle kaynak grubunuzu ve kaynakları kendinize yakın bir bölgede oluşturursunuz.

Komut tamamlandığında, bir JSON çıkışı size kaynak grubu özelliklerini gösterir.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service planı oluşturma

Cloud Shell’de, kaynak grubunda [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) komutuyla bir App Service planı oluşturun.

Aşağıdaki örnek, **Standart** fiyatlandırma katmanı (`--sku S1`) ve bir Linux kapsayıcısı (`--is-linux`) içinde `myAppServicePlan` adlı bir App Service planı oluşturur.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service planı oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Docker Compose uygulaması oluşturma

Cloud Shell terminalinde [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) komutunu kullanarak `myAppServicePlan` App Service planında çok kapsayıcılı bir [web uygulaması](app-service-linux-intro.md) oluşturun. _\<App_name >_ `a-z`, benzersiz bir uygulama adıyla değiştirmeyi unutmayın (geçerli karakterler, `0-9`ve `-`).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Web uygulaması oluşturulduğunda Azure CLI aşağıda yer alan çıktıdaki gibi bilgiler gösterir:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Uygulamaya göz atma

Dağıtılan uygulamaya göz atmak için (`http://<app_name>.azurewebsites.net`) adresine gidin. Uygulamanın yüklenmesi birkaç dakika sürebilir. Bir hatayla karşılaşırsanız birkaç dakika daha bekleyip tarayıcıyı yenileyin.

![Kapsayıcılar için Web App üzerinde örnek çok kapsayıcılı uygulama][1]

**Tebrikler**, Kapsayıcılar için Web App üzerinde çok kapsayıcılı bir uygulama oluşturdunuz.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Çok Kapsayıcılı WordPress uygulaması](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Özel kapsayıcı yapılandırma](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
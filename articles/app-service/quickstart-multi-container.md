---
title: 'Hızlı başlangıç: çok kapsayıcılı bir uygulama oluşturma'
description: İlk çok Kapsayıcılı uygulamanızı dağıtarak Azure App Service çok Kapsayıcılı uygulamalarla çalışmaya başlayın.
keywords: Azure App Service, Web uygulaması, Linux, Docker, Compose, çok Kapsayıcılı, çok Kapsayıcılı, kapsayıcılar için Web App, birden çok kapsayıcı, kapsayıcı, WordPress, MySQL için Azure DB, kapsayıcılarla üretim veritabanı
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 0ea55c36c239b5ecdb51ef3dc7a3ff762718bd1e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769020"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Docker Compose yapılandırma kullanarak çok Kapsayıcılı (Önizleme) uygulama oluşturma

> [!NOTE]
> Çok Kapsayıcılı önizleme aşamasındadır.

[Kapsayıcılar için Web App](overview.md#app-service-on-linux), Docker görüntülerini esnek bir şekilde kullanmanızı sağlar. Bu hızlı başlangıçta, bir Docker Compose yapılandırması kullanılarak [Cloud Shell](../cloud-shell/overview.md) kapsayıcılar için Web App için çok kapsayıcılı bir uygulamanın (Önizleme) nasıl dağıtılacağı gösterilmektedir.

![Kapsayıcılar için Web App üzerinde örnek çok kapsayıcılı uygulama][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Bu makale, Azure CLı 'nin sürüm 2.0.32 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="download-the-sample"></a>Örneği indirme

Bu hızlı başlangıç için [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) Compose dosyasını kullanacaksınız. Yapılandırma dosyasına [Azure Örnekleri](https://github.com/Azure-Samples/multicontainerwordpress) sayfasından ulaşabilirsiniz.

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

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

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Cloud Shell, komutuyla bir kaynak grubu oluşturun [`az group create`](/cli/azure/group#az_group_create) . Aşağıdaki örnek *Orta Güney ABD* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur. **Standart** katmanda Linux üzerinde App Service için desteklenen tüm konumları görüntülemek için [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az_appservice_list_locations) komutunu çalıştırın.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Genellikle kaynak grubunuzu ve kaynakları kendinize yakın bir bölgede oluşturursunuz.

Komut tamamlandığında, bir JSON çıkışı size kaynak grubu özelliklerini gösterir.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service planı oluşturma

Cloud Shell, komutuyla kaynak grubunda bir App Service planı oluşturun [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) .

Aşağıdaki örnek, **Standart** fiyatlandırma katmanı (`--sku S1`) ve bir Linux kapsayıcısı (`--is-linux`) içinde `myAppServicePlan` adlı bir App Service planı oluşturur.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service planı oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir:

<pre>
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
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Docker Compose uygulaması oluşturma

> [!NOTE]
> Azure App Services üzerinde Docker Compose Şu anda 4.000 karakter sınırlaması vardır.

Cloud Shell terminalinde [az webapp create](/cli/azure/webapp#az_webapp_create) komutunu kullanarak `myAppServicePlan` App Service planında çok kapsayıcılı bir [web uygulaması](overview.md#app-service-on-linux) oluşturun. _\<app_name>_ Benzersiz bir uygulama adıyla değiştirmeyi unutmayın (geçerli karakterler `a-z` , `0-9` ve `-` ).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Web uygulaması oluşturulduğunda Azure CLI aşağıda yer alan çıktıdaki gibi bilgiler gösterir:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Uygulamaya göz atma

Dağıtılan uygulamaya göz atmak için (`http://<app_name>.azurewebsites.net`) adresine gidin. Uygulamanın yüklenmesi birkaç dakika sürebilir. Bir hatayla karşılaşırsanız birkaç dakika daha bekleyip tarayıcıyı yenileyin.

![Kapsayıcılar için Web App üzerinde örnek çok kapsayıcılı uygulama][1]

**Tebrikler**, kapsayıcılar için Web App içinde çok kapsayıcılı bir uygulama oluşturdunuz.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: çok Kapsayıcılı WordPress uygulaması](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Özel kapsayıcı yapılandırma](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png

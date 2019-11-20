---
title: Hızlı başlangıç-Azure Container Registry olaylarını Event Grid gönderin
description: Bu hızlı başlangıçta, kapsayıcı kayıt defteriniz için Event Grid olaylarını etkinleştirir, ardından kapsayıcı görüntüsü gönderme ve olay silme olaylarını örnek bir uygulamaya gönderirsiniz.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/23/2018
ms.author: danlep
ms.custom: seodec18
ms.openlocfilehash: 49ee9a7f12601b0d93e320ab797be4a1ada41c04
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309793"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Hızlı Başlangıç: Özel kapsayıcı kayıt defterinden olayları Event Grid gönder

Azure Event Grid, yayımlama-abonelik modeli kullanarak Tekdüzen olay tüketimi sağlayan, tam olarak yönetilen bir olay yönlendirme hizmetidir. Bu hızlı başlangıçta, Azure CLı kullanarak bir kapsayıcı kayıt defteri oluşturabilir, kayıt defteri olaylarına abone olur ve olayları almak için örnek bir Web uygulaması dağıtabilirsiniz. Son olarak, kapsayıcı görüntüsünü `push` ve `delete` olayları tetiklersiniz ve olay yükünü örnek uygulamada görüntüleyebilirsiniz.

Bu makaledeki adımları tamamladıktan sonra, kapsayıcı Kayıt defterinizden Event Grid için gönderilen olaylar örnek Web uygulamasında görünür:

![Üç alınan olayla örnek Web uygulamasını işleme Web tarayıcısı][sample-app-01]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap][azure-account] oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bu makaledeki Azure CLı komutları **Bash** kabuğu için biçimlendirilir. PowerShell veya komut Istemi gibi farklı bir kabuk kullanıyorsanız, satır devamlılık karakterlerini veya değişken atama satırlarını uygun şekilde ayarlamanız gerekebilir. Bu makale, gerekli komut düzenlemesini en aza indirmek için değişkenleri kullanır.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Kaynak grubu, Azure kaynaklarınızı dağıttığınız ve yönettiğiniz bir mantıksal kapsayıcıdır. Aşağıdaki [az Group Create][az-group-create] komutu, *Eastus* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur. Kaynak grubunuz için farklı bir ad kullanmak istiyorsanız, farklı bir değere ayarlayın `RESOURCE_GROUP_NAME` .

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Ardından, aşağıdaki komutlarla bir kapsayıcı kayıt defteri 'ni kaynak grubuna dağıtın. [Az ACR Create][az-acr-create] komutunu çalıştırmadan önce, kayıt defteriniz için `ACR_NAME` bir ad ayarlayın. Ad, Azure içinde benzersiz olmalıdır ve 5-50 alfasayısal karakterlerle kısıtlıdır.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Kayıt defteri oluşturulduktan sonra Azure CLı, aşağıdakine benzer bir çıktı döndürür:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Olay uç noktası oluşturma

Bu bölümde, önceden oluşturulmuş örnek bir Web uygulamasını Azure App Service dağıtmak için GitHub deposunda bulunan Kaynak Yöneticisi şablonunu kullanırsınız. Daha sonra, kayıt defterinizin Event Grid olaylarına abone olur ve bu uygulamayı olayların gönderildiği uç nokta olarak belirtirsiniz.

Örnek uygulamayı dağıtmak için, Web uygulamanız `SITE_NAME` için benzersiz bir ad ayarlayın ve aşağıdaki komutları yürütün. Site adı, Web uygulamasının tam etki alanı adının (FQDN) bir kısmını oluşturduğundan Azure içinde benzersiz olmalıdır. Daha sonraki bir bölümde, kayıt defterinizin olaylarını görüntülemek için bir Web tarayıcısında uygulamanın FQDN 'sine gidebilirsiniz.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Dağıtım başarılı olduktan sonra (birkaç dakika sürebilir), bir tarayıcı açın ve çalıştığından emin olmak için Web uygulamanıza gidin:

`http://<your-site-name>.azurewebsites.net`

Örnek uygulamanın, hiçbir olay iletisi görüntülenmediğinde görüntülendiğini görmeniz gerekir:

![Olay görüntülenmeden örnek Web uygulamasını gösteren Web tarayıcısı][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Kayıt defteri olaylarına abone olma

Event Grid, izlemek istediğiniz olayları ve nereden gönderileceğini söylemek için bir *konuya* abone olursunuz. Aşağıdaki [az eventgrid olay-abonelik oluştur][az-eventgrid-event-subscription-create] komutu oluşturduğunuz kapsayıcı kayıt defterine abone olur ve Web uygulamanızın URL 'sini, olayları göndereceği uç nokta olarak belirtir. Önceki bölümlerde doldurmuş olduğunuz ortam değişkenleri burada yeniden kullanılır, bu nedenle hiçbir düzenleme gerekmez.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Abonelik tamamlandığında aşağıdakine benzer bir çıktı görmeniz gerekir:

```JSON
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Kayıt defteri olaylarını tetikleme

Örnek uygulama çalışır duruma gelmiştir ve Event Grid Kayıt defterinize abone olduğunuza göre, bazı olaylar oluşturmaya hazırsınız demektir. Bu bölümde, Kayıt defterinize bir kapsayıcı görüntüsü derlemek ve göndermek için ACR görevlerini kullanırsınız. ACR görevleri, yerel makinenizde Docker altyapısının yüklü olması gerekmeden bulutta kapsayıcı görüntüleri oluşturmanıza olanak sağlayan bir Azure Container Registry özelliğidir.

### <a name="build-and-push-image"></a>Görüntü oluşturma ve gönderme

GitHub deposunun içeriğinden bir kapsayıcı görüntüsü oluşturmak için aşağıdaki Azure CLı komutunu yürütün. Varsayılan olarak, ACR görevleri başarıyla oluşturulmuş bir görüntüyü Kayıt defterinize otomatik olarak gönderir ve `ImagePushed` olayı oluşturur.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

ACR görevleri görüntünüzü oluşturup daha sonra iletirken aşağıdakine benzer bir çıktı görmeniz gerekir. Aşağıdaki örnek çıktı, breçekimi için kesildi.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Oluşturulan görüntünün kayıt defterinizde olduğunu doğrulamak için, "MyImage" deposundaki etiketleri görüntülemek için aşağıdaki komutu yürütün:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Oluşturduğunuz görüntünün "v1" etiketinin çıktıda görünmesi gerekir, aşağıdakine benzer:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Görüntüyü silme

Şimdi, `ImageDeleted` [az ACR Repository Delete][az-acr-repository-delete] komutuyla görüntüyü silerek bir olay oluşturun:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Aşağıdakine benzer bir çıktı görmeniz ve bildirimi ve ilişkili görüntüleri silmeyi onaylamanız istenir:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Kayıt defteri olaylarını görüntüleme

Şimdi Kayıt defterinize bir görüntü gönderdi ve sonra dosyayı silmiş oldunuz. Event Grid Viewer Web uygulamanıza gidin ve hem hem de `ImageDeleted` `ImagePushed` olayları görmeniz gerekir. Ayrıca, [kayıt defteri olaylarına abone ol](#subscribe-to-registry-events) bölümünde komutunu yürüterek oluşturulan bir abonelik doğrulama olayı da görebilirsiniz.

Aşağıdaki ekran görüntüsünde, üç olayla örnek uygulama gösterilmektedir ve `ImageDeleted` bu olay, ayrıntılarını göstermek için genişletilir.

![Imageitilmiş ve ımagedeleted olayları ile örnek uygulamayı gösteren Web tarayıcısı][sample-app-03]

Tebrikler! `ImagePushed` Ve`ImageDeleted` olaylarını görürseniz, kayıt defteriniz Event Grid olaylar gönderiyor ve Event Grid bu olayları Web uygulaması uç noktanıza iletiyor.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturduğunuz kaynaklarla işiniz bittiğinde, bunları aşağıdaki Azure CLı komutuyla silebilirsiniz. Bir kaynak grubunu sildiğinizde, içerdiği tüm kaynaklar kalıcı olarak silinir.

**UYARI**: Bu işlem geri alınamaz. Komutu çalıştırmadan önce gruptaki herhangi bir kaynağa artık ihtiyacınız olmadığından emin olun.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid olay şeması

Azure Container Registry olay iletisi şeması başvurusunu Event Grid belgelerinde bulabilirsiniz:

[Container Registry için Azure Event Grid olay şeması](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir kapsayıcı kayıt defteri dağıttığınızda, ACR görevleri ile bir görüntü oluşturup, silmiş ve kayıt defterinizin olaylarını örnek bir uygulamayla Event Grid kullandınız. Ardından, bulutta kapsayıcı görüntüleri oluşturma hakkında daha fazla bilgi edinmek için ACR görevleri öğreticisine geçin ve temel görüntü güncelleştirmesinde otomatik derlemeler de dahildir:

> [!div class="nextstepaction"]
> [ACR görevlerle bulutta kapsayıcı görüntüleri oluşturun](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create

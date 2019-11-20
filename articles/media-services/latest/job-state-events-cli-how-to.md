---
title: CLı kullanarak Event Grid Azure Media Services olaylarını izleme | Microsoft Docs
description: Bu makalede, Azure Media Services olaylarını izlemek için Event Grid nasıl abone olunacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937281"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Azure CLı kullanarak Event Grid Media Services olaylar oluşturma ve izleme

Azure Event Grid, bulut için bir olay hizmetidir. Bu hizmet, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../../event-grid/concepts.md#event-subscriptions) kullanır. Media Services olaylar, verilerdeki değişikliklere yanıt vermek için gereken tüm bilgileri içerir. EventType özelliği "Microsoft. Media." ile başladığı için bir Media Services olayı tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Media Services olay şemaları](media-services-event-schemas.md).

Bu makalede, Azure CLı kullanarak Azure Media Services hesabınıza yönelik olaylara abone olabilirsiniz. Ardından, sonucu görüntülemek için olayları tetiklersiniz. Genellikle olayları, olay verilerini işleyen ve eylemleri geçen bir uç noktaya gönderirsiniz. Bu makalede, olayları toplayıp görüntüleyen bir Web uygulamasına gönderirsiniz.

## <a name="prerequisites"></a>Prerequisites

- Etkin bir Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
- CLı 'yi yerel olarak yükleyip kullanın, bu makale için Azure CLı 2,0 veya sonraki bir sürümü gerekir. Sahip olduğunuz sürümü bulmak için `az --version` ' yı çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli). 

    Şu anda, tüm [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) komutları Azure Cloud Shell çalışmaz. CLı 'nın yerel olarak kullanılması önerilir.

- [Media Services hesabı oluşturun](create-account-cli-how-to.md).

    Kaynak grubu adı ve Media Services hesap adı için kullandığınız değerleri anımsadığınızdan emin olun.

## <a name="create-a-message-endpoint"></a>İleti uç noktası oluşturma

Media Services hesabı için olaylara abone olmadan önce olay iletisi için uç noktayı oluşturalım. Genellikle, uç nokta olay verilerine göre eylemler gerçekleştirir. Bu makalede, olay iletilerini görüntüleyen [önceden oluşturulmuş bir Web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtırsınız. Dağıtılan çözüm, GitHub 'dan bir App Service planı, bir App Service Web uygulaması ve kaynak kodu içerir.

1. Çözümü aboneliğinize dağıtmak için **Azure 'A dağıt** ' ı seçin. Azure portal parametreler için değerler sağlayın.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Dağıtımın tamamlanması birkaç dakika sürebilir. Dağıtım başarılı olduktan sonra, çalıştığından emin olmak için Web uygulamanızı görüntüleyin. Bir Web tarayıcısında şuraya gidin: `https://<your-site-name>.azurewebsites.net`

"Azure Event Grid Görüntüleyici" sitesine geçerseniz, henüz hiç olayına sahip olduğunu görürsünüz.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Azure aboneliğini ayarlama

Aşağıdaki komutta, Media Services hesabı için kullanmak istediğiniz Azure abonelik KIMLIĞINI sağlayın. [Aboneliklerde](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)gezinerek erişiminiz olan aboneliklerin bir listesini görebilirsiniz.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Media Services olaylarına abone olma

İzlemek istediğiniz olayları Event Grid söylemek için bir makaleye abone olursunuz. Aşağıdaki örnek, oluşturduğunuz Media Services hesabına abone olur ve olay bildirimi için uç nokta olarak oluşturduğunuz Web sitesinden URL 'YI geçirir. 

@No__t-0 değerini olay aboneliğiniz için benzersiz bir adla değiştirin. @No__t-0 ve `<ams_account_name>` için Media Services hesabı oluştururken kullandığınız değerleri kullanın. @No__t-0 için, Web uygulamanızın URL 'sini sağlayın ve giriş sayfası URL 'sine `api/updates` ekleyin. Abone olurken bitiş noktasını belirterek, Event Grid olayların bu uç noktaya yönlendirilmesini işler. 

1. Kaynak kimliğini al

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Örneğin:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Olaylara abone olma

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Örneğin:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Doğrulama el sıkışma uyarısı alabilirsiniz. Birkaç dakika bekleyin ve el sıkışma doğrulaması gerekir.

Şimdi, Event Grid iletiyi uç noktanıza nasıl dağıttığını görmek için olayları tetikleyelim.

## <a name="send-an-event-to-your-endpoint"></a>Uç noktanıza olay gönderme

Bir kodlama işi çalıştırarak Media Services hesabı için olayları tetikleyebilirsiniz. Bir dosya kodlamak ve olay göndermeye başlamak için [Bu hızlı](stream-files-dotnet-quickstart.md) başlangıcı izleyebilirsiniz. 

Web uygulamanızı yeniden görüntüleyin ve kendisine bir abonelik doğrulama olayı gönderildiğini unutmayın. Event Grid, bitiş noktasının olay verilerini almak istediğini doğrulayabilmesi için doğrulama olayını gönderir. Uç noktanın `validationResponse` `validationCode` olarak ayarlanması gerekebilir. Daha fazla bilgi için bkz. [güvenlik ve kimlik doğrulaması Event Grid](../../event-grid/security-authentication.md). Aboneliği nasıl doğrulayacağını görmek için Web uygulaması kodunu görüntüleyebilirsiniz.

> [!TIP]
> Olay verilerini genişletmek için göz simgesini seçin. Tüm olayları görüntülemek istiyorsanız, sayfayı yenilemeyin.

![Abonelik olayını görüntüle](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Sonraki adımlar

[Karşıya yükleme, kodlama ve akış](stream-files-tutorial-with-api.md)


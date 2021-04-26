---
title: Azure Haritalar ile tümleştirme
titleSuffix: Azure Digital Twins
description: Azure Maps ınkapısı haritasını güncelleştirmek için ikizi grafiğini ve Azure dijital TWINS bildirimlerini kullanabileceğiniz bir işlev oluşturmak için bkz. Azure Işlevleri 'ni kullanma.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 990a0ee73bd91ccb748c948b5fcf0e6124d84a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201439"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Azure haritalar ınkapısının haritasını güncelleştirmek için Azure dijital TWINS kullanma

Bu makalede, Azure [Maps](../azure-maps/about-azure-maps.md)kullanarak bir *ınkapılı haritada* görüntülenecek bilgileri güncelleştirmek Için Azure dijital TWINS verilerini kullanmak için gereken adımlar açıklanmaktadır. Azure dijital TWINS, IoT cihaz ilişkilerinizin bir grafiğini depolar ve Telemetriyi farklı uç noktalara yönlendirir ve bu, haritalar üzerinde bilgilendirici Yerpaylaşımları güncelleştirmek için mükemmel bir hizmet sağlar.

Bu şekilde nasıl ele alınacaktır:

1. Azure Digital TWINS örneğinizi, ikizi Update olaylarını [Azure işlevleri](../azure-functions/functions-overview.md)'ndeki bir işleve göndermek için yapılandırma.
2. Azure haritalar ınkapısı haritaları Özellik stateset 'i güncelleştirmek için bir işlev oluşturma.
3. Haritalar KIMLIĞINIZI ve özellik stateset KIMLIĞINIZI Azure dijital TWINS grafiğinde depolama.

### <a name="prerequisites"></a>Önkoşullar

* Azure dijital TWINS [*öğreticisini izleyin: uçtan uca çözümü bağlama*](./tutorial-end-to-end.md).
    * Bu ikizi ek bir uç nokta ve rotayla genişletiyorsunuz. Ayrıca, bu öğreticiden işlev uygulamanıza başka bir işlev da eklersiniz. 
* Azure haritalar öğreticisini izleyin: bir *özellik stateset* Ile Azure Maps ınkapısı haritası oluşturmak üzere [*ınkapılı haritalar oluşturmak Için Azure haritalar Oluşturucu kullanın*](../azure-maps/tutorial-creator-indoor-maps.md) .
    * [Özellik statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) 'ler, odalar veya ekipman gibi veri kümesi özelliklerine atanan dinamik Özellikler (eyaletler) koleksiyonlarıdır. Yukarıdaki Azure haritalar öğreticisinde, stateset özelliği bir haritada görüntülenecek oda durumunu depolar.
    * Özellik *stateset ID* ve Azure Maps *abonelik anahtarınıza* ihtiyacınız olacaktır.

### <a name="topology"></a>Topoloji

Aşağıdaki görüntüde, bu öğreticideki ınkapımaps tümleştirme öğelerinin, daha büyük, uçtan uca bir Azure dijital TWINS senaryosuna hangi noktada uyduğunu gösterilmektedir.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Uçtan uca bir senaryoda Azure hizmetlerinin bir görünümü olan, ınkapıharitaları tümleştirme parçasını vurgulama" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>TWINS güncelleştirme sırasında Haritayı güncelleştirmek için bir işlev oluşturma

İlk olarak, tüm ikizi Update olaylarını bir olay kılavuzu konusuna iletmek için Azure dijital TWINS 'te bir yol oluşturacaksınız. Ardından, bu güncelleştirme iletilerini okumak ve Azure Maps 'ta bir özellik stateset 'i güncelleştirmek için bir işlev kullanacaksınız. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Güncelleştirme bildirimlerinin ikizini oluşturmak için rota ve filtre oluşturma

Azure dijital TWINS örnekleri, bir ikizi durumunun güncelleştirildiği her seferinde ikizi Update olaylarını yayabilir. Azure dijital TWINS [*öğreticisi: Yukarıdaki bir uçtan uca çözümü*](./tutorial-end-to-end.md) , bir odanın ikizi 'e iliştirilmiş bir sıcaklık özniteliğini güncelleştirmek için bir termometre 'nin kullanıldığı bir senaryoya yol gösterir. Bu çözümü, TWINS güncelleştirme bildirimlerine abone olarak ve haritalarınızı güncelleştirmek için bu bilgileri kullanarak genişlettireceksiniz.

Bu kalıp, IoT cihazı yerine doğrudan ikizi 'dan, eşleme mantığınızı güncelleştirmeye gerek kalmadan, sıcaklık için temel alınan veri kaynağını değiştirme esnekliği sağlayan bir yol elde etmenizi sağlar. Örneğin, eşleme mantığınızı güncelleştirmeye gerek kalmadan, birden fazla salon ölçüm ekleyebilir veya bu odayı başka bir oda ile bir termometre paylaşmak üzere ayarlayabilirsiniz.

1. Azure dijital TWINS örneğinden olay alacak bir olay Kılavuzu konusu oluşturun.
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Olay kılavuzunuzun konusunu Azure dijital TWINS 'e bağlamak için bir uç nokta oluşturun.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. İkiz güncelleştirme olaylarını uç noktanıza göndermek için Azure Digital Twins'de bir rota oluşturun.

    >[!NOTE]
    >Şu anda Cloud Shell'de şu komut gruplarını etkileyen **bilinen bir sorun** vardır: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Çözüm için bu komutları çalıştırmadan önce Cloud Shell'de `az login` komutunu çalıştırın veya Cloud Shell yerine [local CLI](/cli/azure/install-azure-cli) ortamını kullanın. Bunun hakkında daha fazla bilgi için bkz. [*sorun giderme: Azure dijital TWINS 'de bilinen sorunlar*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Haritaları güncelleştirmek için bir işlev oluşturma

Uçtan uca öğreticiden işlev uygulamanız içinde **Event Grid tetiklenen bir işlev** oluşturacağız ([*öğretici: uçtan uca bir çözümü bağlama*](./tutorial-end-to-end.md)). Bu işlev, bir odanın sıcaklığını güncelleştirmek için bu bildirimleri paketten çıkarın ve Azure Maps özelliği stateset 'e güncelleştirmeler gönderir.

Başvuru bilgileri için aşağıdaki belgeye bakın: [*Azure işlevleri için Azure Event Grid tetikleyicisi*](../azure-functions/functions-bindings-event-grid-trigger.md).

İşlev kodunu aşağıdaki kodla değiştirin. Yalnızca TWINS 'in bulunduğu güncelleştirmeleri filtreleyerek, güncelleştirilmiş sıcaklığın okunmasını ve bu bilgileri Azure Maps 'a göndermeyecektir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

İşlev uygulamanızda iki ortam değişkeni ayarlamanız gerekir. Bunlardan biri [Azure Maps birincil abonelik anahtarınıza](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account), diğeri Ise [Azure HARITALAR stateset Kimliğinizle](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)biridir.

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Haritadaki canlı güncelleştirmeleri görüntüleme

Canlı güncelleştirme sıcaklığını görmek için aşağıdaki adımları izleyin:

1. Azure dijital TWINS öğreticisindeki **Devicesimülatör** projesini çalıştırarak sanal IoT verilerini göndermeye başlayın [*: uçtan uca bir çözüm bağlayın*](tutorial-end-to-end.md). Bu yönergeler, [*benzetimi Yapılandır ve Çalıştır*](././tutorial-end-to-end.md#configure-and-run-the-simulation) bölümünde bulunur.
2. Azure haritalar Oluşturucu 'da oluşturulan ınkapısı haritalarınızı oluşturmak için [ **Azure Maps ınkapısı** modülünü](../azure-maps/how-to-use-indoor-module.md) kullanın.
    1. Örneğin, HTML 'yi örnek: ınkapıharitaları öğreticisinin [*ınkapıharitaları modülünü kullanın*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) [*: Azure Maps ınkapısı haritaları modülünü*](../azure-maps/how-to-use-indoor-module.md) yerel bir dosya ile kullanın.
    1. Yerel HTML dosyasındaki, *tilesetıd* ve *statesetıd* *abonelik anahtarını* değerlerinizle değiştirin.
    1. Bu dosyayı tarayıcınızda açın.

Her iki örnek de sıcaklığın uyumlu bir aralığa gönderilmesini sağlamak için, her 30 saniyede bir haritada oda 121 güncelleştirme rengini görmeniz gerekir.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Oda 121 renkli turuncu gösteren bir Office Haritası":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Haritalar bilgilerinizi Azure dijital TWINS 'te depolayın

Haritalar bilgilerinizi güncelleştirmek için bir tek kodlanmış çözümünüz olduğuna göre, ınkapımanız eşlemenizi güncelleştirmek için gereken tüm bilgileri depolamak için Azure dijital TWINS grafiğini kullanabilirsiniz. Bu, sırasıyla, her haritanın ve konumun durum kümesi KIMLIĞINI, eşleme abonelik KIMLIĞINI ve özellik KIMLIĞINI içerir. 

Bu belirli örneğe yönelik bir çözüm, her üst düzey alanın bir stateset KIMLIĞI ve Maps abonelik KIMLIĞI özniteliğiyle güncelleştirilmesini ve her odanın bir özellik KIMLIĞINE sahip olacak şekilde güncelleştirilmesini içerir. İkizi grafiğini başlatırken bu değerleri bir kez ayarlamanız gerekir, ardından bu değerleri her bir ikizi Update olayı için sorgulayın.

Topolojinizin yapılandırmasına bağlı olarak, bu üç özniteliği Haritalarınızın ayrıntı düzeyi ile ilişkili farklı düzeylerde depolayabileceksiniz.

## <a name="next-steps"></a>Sonraki adımlar

TWINS grafiğindeki bilgileri yönetme, yükseltme ve alma hakkında daha fazla bilgi edinmek için aşağıdaki başvurulara bakın:

* [*Nasıl yapılır: dijital TWINS 'i yönetme*](./how-to-manage-twin.md)
* [*Nasıl yapılır: ikizi grafiğini sorgulama*](./how-to-query-graph.md)

---
title: Uç noktaları ve yolları yönetme (portal)
titleSuffix: Azure Digital Twins
description: Azure portal kullanarak Azure dijital TWINS verileri için uç noktaları ve olay yollarını ayarlama ve yönetme bölümüne bakın.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8a8cc6563d8e3cae942c4dfb43afa3d05f6f2b75
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257318"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Azure dijital TWINS 'te uç noktaları ve yolları yönetme (portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure dijital TWINS 'de, [olay bildirimlerini](concepts-event-notifications.md) aşağı akış hizmetlerine veya bağlı işlem kaynaklarına yönlendirebilirsiniz. Bunu yapmak için öncelikle olayları alabilecek **uç noktaları** ayarlamanız gerekir. Daha sonra, Azure dijital TWINS tarafından oluşturulan olayların hangi uç noktalara teslim edildiğini belirten [**olay yolları**](concepts-route-events.md) oluşturabilirsiniz.

Bu makalede, [Azure Portal](https://portal.azure.com)kullanarak uç noktalar ve rotalar oluşturma işlemi adım adım açıklanmaktadır.

Alternatif olarak, uç noktaları ve yolları [olay rotaları API 'leri](/rest/api/digital-twins/dataplane/eventroutes), [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)'Lar veya [Azure dijital TWINS CLI](how-to-use-cli.md)ile de yönetebilirsiniz. Bu makalenin Portal yerine bu mekanizmaların kullanıldığı bir sürümü için bkz. [*nasıl yapılır: uç noktaları ve yolları yönetme (API 'ler ve CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Önkoşullar

* Bir **Azure hesabınızın** olması gerekir ( [buradan](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)birini ücretsiz olarak ayarlayabilirsiniz)
* Azure aboneliğinizde bir **Azure dijital TWINS örneği** gerekir. Zaten bir örneğiniz yoksa, [*nasıl yapılır: örnek ve kimlik doğrulaması ayarlama*](how-to-set-up-instance-portal.md)bölümündeki adımları kullanarak bir tane oluşturabilirsiniz. Bu makalede daha sonra kullanmak için kurulum 'un aşağıdaki değerlerini kullanın:
    - Örnek adı
    - Kaynak grubu

Bu ayrıntıları, örneğinizi ayarladıktan sonra [Azure Portal](https://portal.azure.com) bulabilirsiniz. Portalda oturum açın ve Portal arama çubuğunda örneğinizin adını arayın.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure portal arama çubuğunun ekran görüntüsü." lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Bu ayrıntıları örneğiniz için genel bakışta görmek üzere sonuçlardan örneğinizi seçin:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Bir Azure dijital TWINS örneği için genel bakış sayfasının ekran görüntüsü. Ad ve kaynak grubu vurgulanır.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure dijital TWINS için uç nokta oluşturma

Bunlar, örneğiniz için oluşturabileceğiniz desteklenen uç nokta türleridir:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Farklı uç nokta türleri hakkında daha fazla bilgi için bkz. [*Azure mesajlaşma hizmetleri arasında seçim yapın*](../event-grid/compare-messaging-services.md).

Bu bölümde [Azure Portal](https://portal.azure.com)bu uç noktalardan birinin nasıl oluşturulacağı açıklanmaktadır.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Uç noktayı oluşturma 

Uç nokta kaynaklarını oluşturduktan sonra bunları bir Azure dijital TWINS uç noktası için kullanabilirsiniz. Yeni bir uç nokta oluşturmak için, [Azure Portal](https://portal.azure.com) örneğinizin sayfasına gidin (örneğin adını Portal arama çubuğuna girerek örneği bulabilirsiniz).

1. Örnek menüsünde _uç noktalar_' ı seçin. Ardından, izleyen uç *noktalar* sayfasından *+ uç nokta oluştur*' u seçin. Bu işlem, aşağıdaki adımlarda yer alan alanları dolduracakları *bir uç nokta oluştur* sayfasını açar.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Event Grid türünde bir uç nokta oluşturma ekran görüntüsü." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Uç noktanız için bir **ad** girin ve **uç nokta türünü** seçin.

1. Aboneliğiniz ve [yukarıda](#prerequisite-create-endpoint-resources)açıklanan uç nokta kaynakları dahil olmak üzere uç nokta türü için gereken diğer ayrıntıları doldurun.
    1. Yalnızca Olay Hub 'ı ve Service Bus uç noktaları için, bir **kimlik doğrulama türü** seçmeniz gerekir. Azure dijital TWINS örneğiniz için [yönetilen bir kimlikle](concepts-security.md#managed-identity-for-accessing-other-resources-preview) uç noktayı kullanacaksanız, önceden oluşturulmuş yetkilendirme kuralıyla anahtar tabanlı kimlik doğrulaması veya kimlik tabanlı kimlik doğrulaması kullanabilirsiniz. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Olay Hub 'ı türünde bir uç nokta oluşturma ekran görüntüsü." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. _Kaydet_' i seçerek uç noktanızı oluşturmayı tamamlayın.

>[!IMPORTANT]
> Uç noktanız için kimlik tabanlı kimlik doğrulamasını başarılı bir şekilde kullanabilmek için, [*nasıl yapılır: yönlendirme olayları için yönetilen kimliği etkinleştirme (Önizleme)*](./how-to-enable-managed-identities-portal.md)bölümündeki adımları izleyerek örneğiniz için bir yönetilen kimlik oluşturmanız gerekir.

Uç noktanızı oluşturduktan sonra, üstteki Azure portal çubuğundaki bildirim simgesini denetleyerek uç noktanın başarıyla oluşturulduğunu doğrulayabilirsiniz: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Uç nokta oluşturulmasını doğrulama bildiriminin ekran görüntüsü. Portalın üst çubuğundan çan şeklindeki simgenin seçildiği ve ' Endpoint ADT-Eh-Endpoint başarıyla oluşturuldu ' uyarısı var.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Uç nokta oluşturma işlemi başarısız olursa, hata iletisini gözlemleyin ve birkaç dakika sonra yeniden deneyin.

Ayrıca, Azure dijital TWINS örneğiniz için *uç noktalar* sayfasında, geri oluşturulan uç noktayı görüntüleyebilirsiniz.

Artık olay Kılavuzu, Olay Hub 'ı veya Service Bus konusu, uç nokta için seçtiğiniz ad altında Azure dijital TWINS 'in içindeki bir uç nokta olarak sunulmaktadır. Genellikle bu adı, [Bu makalede daha sonra](#create-an-event-route)oluşturacağınız bir **olay yolunun** hedefi olarak kullanacaksınız.

### <a name="create-an-endpoint-with-dead-lettering"></a>Etkin olmayan bir uç nokta oluşturma

Bir uç nokta belirli bir süre içinde bir olayı teslim edimezse veya olayı belirli bir sayıda sunmaya çalıştıktan sonra, teslim edilmemiş olayı bir depolama hesabına gönderebilir. Bu işlem, **atılacak** olarak bilinir.

Etkin olmayan bir uç nokta oluşturmak için, Azure portal yerine uç noktanızı oluşturmak için [CLI komutlarını](how-to-use-cli.md) veya [Denetim düzlemi API 'lerini](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) kullanmanız gerekir.

Bu araçların nasıl yapılacağı hakkında yönergeler için, bu makalenin [*API 'leri ve CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) sürümüne bakın.

## <a name="create-an-event-route"></a>Olay yolu oluşturma

Azure dijital TWINS 'den bir uç noktaya gerçek veri göndermek için bir **olay yolu** tanımlamanız gerekir. Bu yollar, geliştiricilerin sistem genelinde ve aşağı akış hizmetlerinde olay akışını üzerinde bağlantı almasına imkan tanır. Kavramlar bölümünde olay yolları hakkında daha fazla bilgi edinin [*: Azure dijital TWINS olaylarını yönlendirme*](concepts-route-events.md).

**Önkoşul**: bir yol oluşturmak için geçiş yapabilmeniz için önce Bu makalenin önceki kısımlarında açıklandığı gibi uç noktalar oluşturmanız gerekir. Uç noktalarınız kurulum tamamlandıktan sonra bir olay rotası oluşturmaya devam edebilirsiniz.

>[!NOTE]
>Son noktalarınızı dağıttıysanız, yeni bir olay yolu için kullanmayı denemeden **önce** bunların dağıtımını tamamladığınızı doğrulayın. Uç noktalar hazırlanmadığından yolu ayarlayamıyoruz, birkaç dakika bekleyip yeniden deneyin.

### <a name="creation-steps-with-the-azure-portal"></a>Azure portal oluşturma adımları

Bir olay yolu tanımı şu öğeleri içerir:
* Kullanmak istediğiniz yol adı
* Kullanmak istediğiniz uç noktanın adı
* Uç noktaya gönderilen olayları tanımlayan filtre
    - Bir olayın gönderilmemesi için yolu devre dışı bırakmak için bir filtre değeri kullanın `false`
    - Belirli bir filtreleme özelliği olmayan bir yolu etkinleştirmek için, bir filtre değeri kullanın `true`
    - Diğer filtre türleri hakkında daha fazla bilgi için aşağıdaki [*Olayları Filtrele*](#filter-events) bölümüne bakın.

Tek bir yol, birden fazla bildirim ve olay türünün seçili olmasını sağlayabilir.

Bir olay yolu oluşturmak için [Azure Portal](https://portal.azure.com) Azure dijital TWINS örneğinizin ayrıntılar sayfasına gidin (örneğin adını Portal arama çubuğuna girerek örneği bulabilirsiniz).

Örnek menüsünden _olay yolları_' nı seçin. Ardından, aşağıdaki *olay yolları* sayfasından *+ bir olay yolu oluştur*' u seçin. 

Açılan *bir olay yolu oluştur* sayfasında en az ' ı seçin:
* _Ad_ alanına yönlendirmenize ilişkin bir ad
* Yolu oluşturmak için kullanmak istediğiniz _uç nokta_ 

Yolun etkinleştirilmesi için, en azından **bir olay yönlendirme filtresi de eklemeniz** gerekir `true` . (Varsayılan değerinin bırakılması `false` yolu oluşturur, ancak buna hiçbir olay gönderilmez.) Bunu yapmak için, _Gelişmiş düzenleyicinin_ anahtarını etkinleştirmek üzere değiştirin ve `true` *filtre* kutusuna yazın.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Örneğiniz için olay yolu oluşturma ekranının ekran görüntüsü." lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

İşiniz bittiğinde, olay rotasını oluşturmak için _Kaydet_ düğmesine basın.

## <a name="filter-events"></a>Olayları filtreleme

Yukarıda açıklandığı gibi, yolların bir **filtre** alanı vardır. Yolinizdeki filtre değeri ise `false` , uç noktanıza hiçbir olay gönderilmez. 

En az filtresini etkinleştirdikten sonra `true` uç noktalar Azure dijital TWINS 'den çeşitli olaylar alır:
* Azure Digital TWINS hizmet API 'SI kullanılarak [dijital TWINS](concepts-twins-graph.md) tarafından tetiklenen telemetri
* İkizi özellik değişikliği bildirimleri, Azure dijital TWINS örneğindeki herhangi bir ikizi için özellik değişikliklerinde harekete geçirilir
* Yaşam döngüsü olayları, TWINS veya ilişkiler oluşturulduğunda veya silindiğinde tetiklenir

Daha özel bir filtre tanımlayarak gönderilmekte olan olay türlerini kısıtlayabilirsiniz.

Bir olay yolu oluştururken bir olay filtresi eklemek için, *olay yolu oluşturma* sayfasının _olay yol filtresi ekle_ bölümünü kullanın. 

Bazı temel ortak filtre seçeneklerinden birini seçebilir veya kendi özel filtrelerinizi yazmak için gelişmiş filtre seçeneklerini kullanabilirsiniz.

>[!NOTE]
> Filtreler **büyük/küçük harfe duyarlıdır** ve yük durumuyla eşleşmesi gerekir. 
>
> Telemetri filtreleri için bu, büyük küçük harf 'in cihaz tarafından gönderilen telemetrideki büyük/küçük harf ile eşleşmesi gerektiği anlamına gelir.

#### <a name="use-the-basic-filters"></a>Temel filtreleri kullanma

Temel filtreleri kullanmak için, _olay türleri_ seçeneğini genişletin ve uç noktanıza göndermek istediğiniz olaylara karşılık gelen onay kutularını seçin. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Temel filtreye sahip bir olay yolu oluşturma ekran görüntüsü. Olayların onay kutularını seçme.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Bu, filtre metin kutusunu seçtiğiniz filtrenin metniyle otomatik olarak doldurur:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Temel filtreye sahip bir olay yolu oluşturma ekran görüntüsü. Olaylar seçildikten sonra otomatik doldurulmuş filtre metni gösteriliyor.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Gelişmiş filtreleri kullanma

Alternatif olarak, kendi özel filtrelerinizi yazmak için Gelişmiş Filtre seçeneğini de kullanabilirsiniz.

Gelişmiş filtre seçenekleriyle bir olay yolu oluşturmak için, _Gelişmiş düzenleyicinin_ anahtarını etkinleştirmek üzere değiştirin. Daha sonra, *filtre* kutusuna kendi olay filtrelerinizi yazabilirsiniz:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Gelişmiş bir filtreye sahip bir olay yolu oluşturma ekranının ekran görüntüsü.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Desteklenen yol filtreleri aşağıda verilmiştir. *Filtre metin şeması* sütunundaki ayrıntı, filtre kutusuna girilebilecek metindir.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Sonraki adımlar

Alabilmeniz için farklı olay iletisi türleri hakkında bilgi edinin:
* [*Kavramlar: olay bildirimleri*](concepts-event-notifications.md)
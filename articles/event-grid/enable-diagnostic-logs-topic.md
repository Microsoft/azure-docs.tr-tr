---
title: Azure Event Grid-konular veya etki alanları için tanılama günlüklerini etkinleştirme
description: Bu makalede, Azure Event Grid için tanılama günlüklerini etkinleştirme konusunda adım adım yönergeler sağlanmaktadır.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576460"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid konuları veya etki alanları için tanılama günlüklerini etkinleştirme
Bu makalede, Event Grid konular veya etki alanları için tanılama ayarlarını etkinleştirmek üzere adım adım yönergeler sağlanmaktadır.  Bu ayarlar, **Yayımlama ve teslim hatası** günlüklerini yakalamanızı ve görüntülemenizi sağlar. 

## <a name="prerequisites"></a>Önkoşullar

- Sağlanan olay Kılavuzu konusu
- Tanılama günlüklerini yakalamak için sağlanan hedef. Olay Kılavuzu konusuyla aynı konumda bulunan aşağıdaki hedefden biri olabilir:
    - Azure depolama hesabı
    - Olay hub'ı
    - Log Analytics çalışma alanı

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Özel konu için tanılama günlüklerini etkinleştirme

> [!NOTE]
> Aşağıdaki yordam, bir konu için tanılama günlüklerini etkinleştirmeye yönelik adım adım yönergeler sağlar. Bir etki alanı için tanılama günlüklerini etkinleştirme adımları çok benzerdir. 2. adımda, Azure portal olay Kılavuzu **etki alanına** gidin.  

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Tanılama günlük ayarlarını etkinleştirmek istediğiniz olay kılavuzu konusuna gidin. 
    1. Üstteki arama çubuğunda **Event Grid konular**' ı arayın. 
    
        ![Özel konuları ara](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Tanılama ayarlarını yapılandırmak istediğiniz listeden **konuyu** seçin. 
1. Sol menüde Izleme altında **Tanılama ayarları** ' **nı** seçin.
1. **Tanılama ayarları** sayfasında **yeni tanılama ayarı Ekle**' yi seçin. 
    
    ![Tanılama ayarı Ekle düğmesi](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Tanılama ayarı için bir **ad** belirtin. 
6. **Günlük** bölümünde **Deliveryhatalarıyla** ve **publisharızaları** seçeneklerini belirleyin. 
    ![Arızaları seçin](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Günlükler için bir veya daha fazla yakalama hedefini etkinleştirin ve ardından önceden oluşturulmuş bir yakalama kaynağını seçerek bunları yapılandırın. 
    - **Bir depolama hesabına arşiv**' i seçerseniz, **depolama hesabı-Yapılandır**' ı seçin ve ardından Azure aboneliğinizde depolama hesabını seçin. 

        !["Azure Storage hesabı Arşivi" işaretlenmiş ve bir depolama hesabı seçilmiş "Tanılama ayarları" sayfasını gösteren ekran görüntüsü.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **Bir olay hub 'ına akış**' yı seçerseniz, **Olay Hub**'ı ' nı seçin ve Event Hubs ad alanı, Olay Hub 'ı ve erişim ilkesini seçin. 
        !["Olay Hub 'ına akış" denetimli "Tanılama ayarları" sayfasını gösteren ekran görüntüsü.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **Log Analytics gönder**' i seçerseniz Log Analytics çalışma alanını seçin.
        !["Log Analytics gönder" işaretli "Tanılama ayarları" sayfasını gösteren ekran görüntüsü.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. **Kaydet**’i seçin. Sonra sağ köşedeki **X** ' i seçerek sayfayı kapatın. 
9. Şimdi **Tanılama ayarları** sayfasına geri döndüğünüzde, **Tanılama ayarları** tablosunda yeni bir giriş görtığınızdan emin olun. 
    !["Tanılama ayarları" tablosunda vurgulanmış yeni bir girişi olan "Tanılama ayarları" sayfasını gösteren ekran görüntüsü.](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Konunun tüm ölçümlerinin toplanmasını de etkinleştirebilirsiniz. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Bir sistem konusu için tanılama günlüklerini etkinleştirme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Tanılama günlük ayarlarını etkinleştirmek istediğiniz olay kılavuzu konusuna gidin. 
    1. Üstteki arama çubuğunda **Event Grid sistem konuları**' nı arayın. 
    
        ![Sistem konularını ara](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Tanılama ayarlarını yapılandırmak istediğiniz **Sistem konusunu** seçin. 
    
        ![Sistem konusunu seçin](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Sol menüde **izleme** altında **Tanılama ayarları** ' nı seçin ve ardından **Tanılama ayarı Ekle**' yi seçin. 

    ![Tanılama ayarları ekleme-düğme](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Tanılama ayarı için bir **ad** belirtin. 
7. **Günlük** bölümünde **deliverybaşarısızlıklarını** seçin. 
    ![Teslim başarısızlıklarını seçin](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Günlükler için bir veya daha fazla yakalama hedefini etkinleştirin ve ardından önceden oluşturulmuş bir yakalama kaynağını seçerek bunları yapılandırın. 
    - **Log Analytics gönder**' i seçerseniz Log Analytics çalışma alanını seçin.
        ![Log Analytics’e gönderme](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - **Bir depolama hesabına arşiv**' i seçerseniz, **depolama hesabı-Yapılandır**' ı seçin ve ardından Azure aboneliğinizde depolama hesabını seçin. 

        ![Azure depolama hesabına Arşivle](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - **Bir olay hub 'ına akış**' yı seçerseniz, **Olay Hub**'ı ' nı seçin ve Event Hubs ad alanı, Olay Hub 'ı ve erişim ilkesini seçin. 
        ![Olay hub’ına akış yapma](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. **Kaydet**’i seçin. Sonra sağ köşedeki **X** ' i seçerek sayfayı kapatın. 
9. Şimdi **Tanılama ayarları** sayfasına geri döndüğünüzde, **Tanılama ayarları** tablosunda yeni bir giriş görtığınızdan emin olun. 
    ![Listedeki tanılama ayarı](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Ayrıca sistem konusunun tüm **ölçümlerinin** toplanmasını etkinleştirebilirsiniz.

    ![Sistem konusu-tüm ölçümleri etkinleştir](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Azure depolama 'da tanılama günlüklerini görüntüleme 

1. Bir depolama hesabını yakalama hedefi olarak etkinleştirdikten sonra, Event Grid tanılama günlüklerini yaymaya başlar. Depolama hesabında **Öngörüler-logs-deliveryhatalarıyla** ve **Öngörüler-logs-publishhatalarıyla** adlı yeni kapsayıcılar görmeniz gerekir. 

    ![Tanılama günlükleri için depolama kapsayıcıları](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Kapsayıcılardan birine gittiğinizde JSON biçiminde bir blob 'a yönlendirilirsiniz. Dosya, bir teslim hatası ya da bir yayımlama hatası için günlük girişleri içeriyor. Gezinti yolu, olay Kılavuzu konusunun **RESOURCEID** ve günlük girişleri yayıldıkları zaman damgasına (dakika düzeyi) temsil eder. İndirilebilen blob/JSON dosyası, sonraki bölümde açıklanan şemaya uyar. 

    [![Depolama ](./media/enable-diagnostic-logs-topic/select-json.png) alanındaki JSON dosyası](./media/enable-diagnostic-logs-topic/select-json.png)
3. Aşağıdaki örneğe benzer şekilde JSON dosyasında içerik görmeniz gerekir: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Sonraki adımlar
Günlük şeması ve konular veya etki alanları için tanılama günlükleri hakkında diğer kavramsal bilgiler için bkz. [tanılama günlükleri](diagnostic-logs.md).

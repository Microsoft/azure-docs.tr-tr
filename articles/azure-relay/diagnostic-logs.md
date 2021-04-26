---
title: Karma Bağlantılar için tanılama günlükleri
description: Bu makalede, Azure Relay için kullanılabilen tüm etkinlik ve tanılama günlüklerine genel bir bakış sunulmaktadır.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 7ca6dc95567a4867d3c58e0efad0fc74289e586c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079106"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Azure Relay Karma Bağlantılar için tanılama günlüklerini etkinleştir
Azure Relay Karma Bağlantılar kullanmaya başladığınızda, dinleyiclarınızın ve gönderenlerden nasıl ve ne zaman açılacağını ve Karma Bağlantılar nasıl oluşturulduğunu ve iletilerin gönderileceğini izlemek isteyebilirsiniz. Bu makalede, Azure Relay hizmeti tarafından sağlanan etkinlik ve tanılama günlüklerine bir genel bakış sunulmaktadır. 

Azure Relay için iki tür günlük görüntüleyebilirsiniz:

- [Etkinlik günlükleri](../azure-monitor/essentials/platform-logs-overview.md): bu günlüklerde Azure Portal ad alanı veya Azure Resource Manager şablonu aracılığıyla gerçekleştirilen işlemler hakkında bilgiler vardır. Bu Günlükler her zaman etkindir. Örneğin: "ad alanı oluştur veya güncelleştir", "karma bağlantı oluştur veya güncelleştir". 
- [Tanılama günlükleri](../azure-monitor/essentials/platform-logs-overview.md): API kullanarak veya dil SDK 'sı aracılığıyla ad alanı üzerinde yürütülen işlemler ve eylemlerle gerçekleştirilen her şeyin daha zengin bir görünümü için tanılama günlüklerini yapılandırabilirsiniz.

## <a name="view-activity-logs"></a>Etkinlik günlüklerini görüntüleme
Azure Relay ad alanınız için etkinlik günlüklerini görüntülemek için, Azure portal **etkinlik günlüğü** sayfasına geçin.

![Azure Relay-etkinlik günlüğü](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Tanılama günlüklerini etkinleştirme

> [!NOTE]
> Tanılama günlükleri yalnızca Karma Bağlantılar için kullanılabilir, Windows Communication Foundation (WCF) geçişi için değil.

Tanılama günlüklerini etkinleştirmek için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com), Azure Relay ad alanına gidin ve ardından **izleme** altında **Tanılama ayarları**' nı seçin.
1. **Tanılama ayarları** sayfasında, **Tanılama ayarı Ekle**' yi seçin.  

   !["Tanılama ayarı Ekle" bağlantısı](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Aşağıdaki adımları uygulayarak tanılama ayarlarını yapılandırın:
    1. **Ad** kutusuna tanılama ayarları için bir ad girin.  
    2. Günlük türü için **HybridConnectionsEvent** öğesini seçin. 
    3. Tanılama günlüklerinizi için aşağıdaki üç **hedef** arasından birini seçin:  
        1. **Bir depolama hesabına arşiv**' i seçerseniz, tanılama günlüklerinin depolanacağı depolama hesabını yapılandırın.  
        2. **Bir olay hub 'ına akış**' yı seçerseniz, tanılama günlüklerini akışa almak istediğiniz olay hub 'ını yapılandırın.
        3. **Log Analytics gönder**' i seçerseniz, Tanılamanın hangi Log Analytics örneğine gönderileceğini belirtin.  

        ![Örnek Tanılama ayarları](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Ayarları kaydetmek için araç çubuğunda **Kaydet** ' i seçin.

Yeni ayarlar yaklaşık 10 dakika içinde etkili olur. Günlükler, **tanılama günlükleri** bölmesindeki yapılandırılan arşiv hedefinde görüntülenir. Tanılama ayarlarını yapılandırma hakkında daha fazla bilgi için bkz. [Azure tanılama günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Karma Bağlantı olayları için şema
Karma bağlantılar olay günlüğü JSON dizeleri aşağıdaki tabloda listelenen öğeleri içerir:

| Ad | Açıklama |
| ------- | ------- |
| ResourceId | Azure Resource Manager kaynak KIMLIĞI |
| ActivityId | Belirtilen işlemi tanımlamak için kullanılan iç KIMLIK. "Trackingıd" olarak da bilinir |
| Uç Nokta | Geçiş kaynağının adresi |
| OperationName | Günlüğe kaydedilen Karma Bağlantılar işlem türü |
| EventTimeString | Günlük kaydının UTC zaman damgası |
| İleti | Etkinliğin ayrıntılı iletisi |
| Kategori | Etkinliğin kategorisi. Şu anda yalnızca vardır `HybridConnectionsEvents` . 


## <a name="sample-hybrid-connections-event"></a>Örnek karma bağlantılar olayı
JSON biçiminde örnek bir karma bağlantılar olayı aşağıda verilmiştir. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Tanılama günlüklerinde yakalanan olaylar ve işlemler

| İşlem                           | Description                                                     |
|-------------------------------------|-----------------------------------------------------------------|
| AuthorizationFailed                 | Yetkilendirme başarısız oldu.                                           |
| Invalidsastoken                     | SAS belirteci geçersiz.                                              |
| ListenerAcceptingConnection         | Dinleyici bağlantıyı kabul ediyor.                           |
| ListenerAcceptingConnectionTimeout  | Bağlantı kabul eden dinleyici zaman aşımına uğradı.                |
| ListenerAcceptingHttpRequestFailed  | HTTP isteğini kabul eden dinleyici bir özel durum nedeniyle başarısız oldu. |
| ListenerAcceptingRequestTimeout     | İstek kabul eden dinleyici zaman aşımına uğradı.                   |
| ListenerClosingFromExpiredToken     | Güvenlik belirtecinin süresi aşıldığı için dinleyici kapatılıyor. |
| ListenerRejectedConnection          | Dinleyici bağlantıyı reddetti.                       |
| ListenerReturningHttpResponse       | Dinleyici bir HTTP yanıtı döndürüyor.                     |
| Listenerreturninghttpresponsebaşarısız oldu | Dinleyici, hata kodu ile bir HTTP yanıtı döndürüyor. |
| ListenerSentHttpResponse            | Geçiş hizmeti, dinleyicisinden bir HTTP yanıtı aldı.  |
| Listenerkaydettirilmemiş                | Dinleyicinin kaydı silindi.                                   |
| Listenervermemeye yanıt vermiyor                | Bir yanıt döndürürken dinleyici yanıt vermemeye başladı.         |
| Iletiendingtolistener            | İleti, dinleyiciye gönderiliyor.                              |
| Iletienttolistener               | İleti, dinleyiciye gönderilir.                                    |
| NewListenerRegistered               | Yeni dinleyici kaydedildi.                                        |
| Newsenderkayıt                | Yeni gönderici kaydediliyor.                                      |
| ProcessingRequestFailed             | Karma bağlantı işleminin işlenmesi başarısız oldu.     |
| SenderConnectionClosed              | Gönderen bağlantısı kapalı.                                |
| Senderlistenerconnectionkurdu | Gönderen ve dinleyici bağlantı başarıyla oluşturuldu.    |
| SenderSentHttpRequest               | Gönderen bir HTTP isteği gönderdi.                                |


## <a name="next-steps"></a>Sonraki adımlar

Azure Relay hakkında daha fazla bilgi edinmek için bkz.:

* [Azure Relay giriş](relay-what-is-it.md)
* [Geçiş Karma Bağlantıları ile çalışmaya başlama](relay-hybrid-connections-dotnet-get-started.md)

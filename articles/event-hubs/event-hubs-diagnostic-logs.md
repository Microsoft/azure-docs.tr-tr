---
title: Tanılama günlüklerini ayarlama-Azure Olay Hub 'ı | Microsoft Docs
description: Azure 'da Olay Hub 'ları için etkinlik günlüklerini ve tanılama günlüklerini ayarlamayı öğrenin.
ms.topic: article
ms.date: 02/25/2021
ms.openlocfilehash: 5067a2962693ee1c1955aa90e61b43358495585a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602613"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Azure olay hub'ı için tanılama günlüklerini ayarlama

Azure Event Hubs için iki tür günlük görüntüleyebilirsiniz:

* **[Etkinlik günlükleri](../azure-monitor/essentials/platform-logs-overview.md)**: Bu günlüklerde, bir işte yapılan işlemlerle ilgili bilgiler vardır. Günlükler her zaman etkindir. Azure portal olay hub 'ı ad alanınız için sol bölmedeki **etkinlik günlüğü** ' ni seçerek etkinlik günlüğü girdilerini görebilirsiniz. Örneğin: "ad alanı oluştur veya güncelleştir", "Olay Hub 'ı oluştur veya güncelleştir".

    ![Event Hubs ad alanı için etkinlik günlüğü](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Tanılama günlükleri](../azure-monitor/essentials/platform-logs-overview.md)**: Tanılama GÜNLÜKLERI, API kullanarak veya dil SDK 'sindeki yönetim istemcileri aracılığıyla ad alanı üzerinde yürütülen işlemler ve eylemler hakkında daha zengin bilgiler sağlar. 
    
    Aşağıdaki bölümde bir Event Hubs ad alanı için tanılama günlüklerinin nasıl etkinleştirileceği gösterilmektedir.

## <a name="enable-diagnostic-logs"></a>Tanılama günlüklerini etkinleştirme
Tanılama günlükleri varsayılan olarak devre dışıdır. Tanılama günlüklerini etkinleştirmek için şu adımları izleyin:

1.  [Azure Portal](https://portal.azure.com), Event Hubs ad alanına gidin. 
2. Sol bölmede **izleme** altında **Tanılama ayarları** ' nı seçin ve **+ Tanılama ayarı Ekle**' yi seçin. 

    ![Tanılama ayarları sayfası-tanılama ayarı Ekle](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. **Kategori ayrıntıları** bölümünde, etkinleştirmek istediğiniz **tanılama günlüğü türlerini** seçin. Bu kategorilerin ilerleyen kısımlarında bu kategorilerin ayrıntılarını bulabilirsiniz. 
5. **Hedef ayrıntıları** bölümünde istediğiniz arşiv hedefini (hedef) ayarlayın; Örneğin, bir depolama hesabı, Olay Hub 'ı veya Log Analytics çalışma alanı.

    ![Tanılama ayarları ekleme sayfası](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Tanılama ayarlarını kaydetmek için araç çubuğunda **Kaydet** ' i seçin.

    Yeni ayarlar yaklaşık 10 dakika içinde etkili olur. Bundan sonra, **tanılama günlükleri** bölmesinde Günlükler yapılandırılan arşiv hedefinde görüntülenir.

    Tanılamayı yapılandırma hakkında daha fazla bilgi için bkz. [Azure tanılama günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Tanılama günlükleri kategorileri

Event Hubs aşağıdaki kategoriler için tanılama günlüklerini yakalar:

| Kategori | Açıklama | 
| -------- | ----------- | 
| Arşiv günlükleri | Yakalama hatalarıyla ilgili olarak, [Event Hubs yakalama](event-hubs-capture-overview.md) işlemlerine ilişkin bilgileri yakalar. |
| İşletimsel Günlükler | Azure Event Hubs ad alanında gerçekleştirilen tüm yönetim işlemlerini yakalayın. Azure Event Hubs üzerinde gerçekleştirilen yüksek miktarda veri işlemi nedeniyle veri işlemleri yakalanmaz. |
| Günlükleri otomatik ölçeklendir | Event Hubs ad alanı üzerinde gerçekleştirilen otomatik Şişir işlemleri yakalar. |
| Kafka Coordinator günlükleri | Event Hubs ilgili Kafka Coordinator işlemlerini yakalar. |
| Kafka Kullanıcı hatası günlükleri | Event Hubs çağrılan Kafka API 'Leri hakkında bilgi yakalar. |
| Event Hubs sanal ağ (VNet) bağlantı olayı | Event Hubs trafiği gönderen IP adresleri ve sanal ağlarla ilgili bilgileri yakalar. |
| Müşteri tarafından yönetilen anahtar Kullanıcı günlükleri | Müşterinin yönettiği anahtarla ilgili işlemleri yakalar. |


Tüm Günlükler JavaScript Nesne Gösterimi (JSON) biçiminde depolanır. Her girdinin aşağıdaki bölümlerde açıklanan biçimi kullanan dize alanları vardır.

## <a name="archive-logs-schema"></a>Arşiv günlükleri şeması

Arşiv günlüğü JSON dizeleri aşağıdaki tabloda listelenen öğeleri içerir:

Ad | Açıklama
------- | -------
`TaskName` | Başarısız olan görevin açıklaması
`ActivityId` | İzleme için kullanılan iç KIMLIK
`trackingId` | İzleme için kullanılan iç KIMLIK
`resourceId` | Azure Resource Manager kaynak KIMLIĞI
`eventHub` | Olay Hub 'ı tam adı (ad alanı adını içerir)
`partitionId` | Yazılan olay hub 'ı bölümü
`archiveStep` | olası değerler: ArchiveFlushWriter, DestinationInit
`startTime` | Hata başlangıç zamanı
`failures` | Hatanın oluşma sayısı
`durationInSeconds` | Hata süresi
`message` | Hata iletisi
`category` | ArchiveLogs

Aşağıdaki kod arşiv günlüğü JSON dizesinin bir örneğidir:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>İşletimsel Günlükler şeması

İşletimsel günlük JSON dizeleri aşağıdaki tabloda listelenen öğeleri içerir:

Ad | Açıklama
------- | -------
`ActivityId` | İzleme amacıyla kullanılan iç KIMLIK |
`EventName` | İşlem adı. Bu öğenin değerlerinin listesi için bkz. [olay adları](#event-names) |
`resourceId` | Azure Resource Manager kaynak KIMLIĞI |
`SubscriptionId` | Abonelik Kimliği |
`EventTimeString` | İşlem süresi |
`EventProperties` |İşlemin özellikleri. Bu öğe, aşağıdaki örnekte gösterildiği gibi olay hakkında daha fazla bilgi sağlar. |
`Status` | İşlem durumu. Değer **başarılı** ya da **başarısız** olabilir.  |
`Caller` | İşlem (Azure portal veya yönetim istemcisi) çağıranı |
`Category` | OperationalLogs |

Aşağıdaki kod, işletimsel günlük JSON dizesinin bir örneğidir:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

### <a name="event-names"></a>Olay adları
Olay adı, aşağıdaki Numaralandırmalardan işlem türü + kaynak türü olarak doldurulur. Örneğin,, `Create Queue` `Retrieve Event Hu` veya `Delete Rule` . 

| İşlem türü | Kaynak türü | 
| -------------- | ------------- | 
| <ul><li>Oluştur</li><li>Güncelleştir</li><li>Sil</li><li>Almanın</li><li>Bilinmiyor</li></ul> | <ul><li>Ad Alanı</li><li>Kuyruk</li><li>Konu</li><li>Abonelik</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>Usagecredıt</li><li>NamespacePnsCredentials</li>Kural</li>ConsumerGroup</li> |

## <a name="autoscale-logs-schema"></a>Otomatik ölçeklendirme günlüğü şeması
Otomatik ölçeklendirme günlüğü JSON, aşağıdaki tabloda listelenen öğeleri içerir:

| Ad | Açıklama |
| ---- | ----------- | 
| `TrackingId` | İzleme amacıyla kullanılan iç KIMLIK |
| `ResourceId` | Azure Resource Manager kaynak KIMLIĞI. |
| `Message` | Otomatik Şişir eylemiyle ilgili ayrıntıları sağlayan bilgilendirici ileti. İleti, belirli bir ad alanı için önceki ve geçerli üretilen iş birimi değerini ve TU 'nın inmi tetikleneceğini içerir. |

Örnek bir otomatik ölçeklendirme olayı aşağıda verilmiştir: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>Kafka Coordinator günlükleri şeması
Kafka Coordinator log JSON, aşağıdaki tabloda listelenen öğeleri içerir:

| Ad | Açıklama |
| ---- | ----------- | 
| `RequestId` | İzleme amacıyla kullanılan istek KIMLIĞI |
| `ResourceId` | Azure Resource Manager kaynak KIMLIĞI |
| `Operation` | Grup düzenlemesi sırasında gerçekleştirilen işlemin adı |
| `ClientId` | İstemci Kimliği |
| `NamespaceName` | Ad alanı adı | 
| `SubscriptionId` | Azure abonelik KIMLIĞI |
| `Message` | Grup düzenlemesi sırasında yapılan eylemlerle ilgili ayrıntıları sağlayan bilgilendirici veya uyarı iletisi. |

### <a name="example"></a>Örnek

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Kafka Kullanıcı hatası günlüğü şeması
Kafka Kullanıcı hatası günlüğü JSON, aşağıdaki tabloda listelenen öğeleri içerir:

| Ad | Açıklama |
| ---- | ----------- |
| `TrackingId` | İzleme amacıyla kullanılan izleme KIMLIĞI. |
| `NamespaceName` | Ad alanı adı |
| `Eventhub` | Olay hub'ı adı |
| `PartitionId` | Bölüm Kimliği |
| `GroupId` | Grup Kimliği |
| `ClientId` | İstemci Kimliği |
| `ResourceId` | Azure Resource Manager kaynak KIMLIĞI. |
| `Message` | Bir hata hakkındaki ayrıntıları sağlayan bilgilendirici ileti |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs sanal ağ bağlantısı olay şeması
Event Hubs sanal ağ (VNet) bağlantı olayı JSON aşağıdaki tabloda listelenen öğeleri içerir:

| Ad | Açıklama |
| ---  | ----------- | 
| `SubscriptionId` | Azure abonelik KIMLIĞI |
| `NamespaceName` | Ad alanı adı |
| `IPAddress` | Event Hubs hizmetine bağlanan bir istemcinin IP adresi |
| `Action` | Bağlantı istekleri değerlendirilirken Event Hubs hizmeti tarafından gerçekleştirilen eylem. Desteklenen eylemler **bağlantı kabul eder** ve **bağlantıyı reddedebilir**. |
| `Reason` | Eylemin neden yapıldığını bir neden sağlar |
| `Count` | Verilen eylem için oluşum sayısı |
| `ResourceId` | Azure Resource Manager kaynak KIMLIĞI. |

Sanal ağ günlükleri yalnızca ad alanı **Seçili ağlardan** veya **belirli IP adreslerinden** (IP filtre kuralları) erişime izin veriyorsa oluşturulur. Bu özellikleri kullanarak ad alanınızı erişimi kısıtlamak ve yine de Event Hubs ad alanına bağlanan istemcilerin IP adreslerini izlemek için sanal ağ günlükleri almak istiyorsanız, aşağıdaki geçici çözümü kullanabilirsiniz. [IP filtrelemeyi etkinleştirin](event-hubs-ip-filtering.md)ve toplam adreslenebilir IPv4 aralığını (1.0.0.0/1-255.0.0.0/1) ekleyin. Event Hubs IP filtrelemesi IPv6 aralıklarını desteklemez. Günlükteki IPv6 biçiminde özel uç nokta adresleri görebileceğinizi unutmayın. 

### <a name="example"></a>Örnek

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Müşteri tarafından yönetilen anahtar Kullanıcı günlükleri
Müşteri tarafından yönetilen anahtar Kullanıcı günlüğü JSON, aşağıdaki tabloda listelenen öğeleri içerir:

| Ad | Açıklama |
| ---- | ----------- | 
| `Category` | İleti kategorisi türü. Şu değerlerden biri: **hata** ve **bilgi** |
| `ResourceId` | Azure abonelik KIMLIĞI ve ad alanı adı içeren iç kaynak KIMLIĞI |
| `KeyVault` | Key Vault kaynağın adı |
| `Key` | Key Vault anahtarının adı. |
| `Version` | Key Vault anahtarının sürümü |
| `Operation` | İsteklere hizmeti sağlamak için yapılan bir işlemin adı |
| `Code` | Durum kodu |
| `Message` | Bir hata veya bilgilendirici iletiyle ilgili ayrıntıları sağlayan ileti |



## <a name="next-steps"></a>Sonraki adımlar
- [Event Hubs giriş](./event-hubs-about.md)
- [Event Hubs örnekleri](sdks.md)
- Event Hubs kullanmaya başlayın
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)

---
title: Tanılama günlüklerini - Azure olay hub'ı ayarlama | Microsoft Docs
description: Etkinlik Günlükleri ve Azure event hubs için tanılama günlüklerini ayarlama konusunda bilgi edinin.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ff6466ddc6b64dfe85ebfb190205859aeb39ad17
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260830"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Tanılama günlükleri için bir Azure olay hub'ı ayarlama

Azure Event Hubs için iki tür günlüğü görüntüleyebilirsiniz:

* **[Etkinlik günlükleri](../azure-monitor/platform/activity-logs-overview.md)** : Bu günlüklerde bir işte gerçekleştirilen işlemlerle ilgili bilgiler vardır. Günlükler her zaman etkindir.
* **[Tanılama günlükleri](../azure-monitor/platform/resource-logs-overview.md)** : Tanılama günlüklerini, bir işle gerçekleşen her şeyin daha zengin bir görünümü için yapılandırabilirsiniz. Tanılama günlüklerine yönelik güncelleştirmeleri ve iş çalışırken gerçekleşen etkinlikler dahil olmak üzere iş silinene kadar iş oluşturulur zamanından kapak etkinlikler.

## <a name="enable-diagnostic-logs"></a>Tanılama günlüklerini etkinleştirme

Tanılama günlükleri, varsayılan olarak devre dışıdır. Tanılama günlüklerini etkinleştirmek için aşağıdaki adımları izleyin:

1.  İçinde [Azure portalında](https://portal.azure.com)altında **izleme + Yönetim**, tıklayın **tanılama günlükleri**.

    ![Tanılama günlükleri için Gezinti Bölmesi](./media/event-hubs-diagnostic-logs/image1.png)

2.  İzlemek istediğiniz kaynağa tıklayın.

3.  **Tanılamayı aç**’a tıklayın.

    ![Tanılama günlüklerini açın](./media/event-hubs-diagnostic-logs/image2.png)

4.  İçin **durumu**, tıklayın **üzerinde**.

    ![Tanılama günlükleri birinin durumunu değiştirin](./media/event-hubs-diagnostic-logs/image3.png)

5.  İstediğiniz arşiv hedefini ayarlayın; Örneğin, bir depolama hesabı, Olay Hub 'ı veya Azure Izleyici günlükleri.

6.  Yeni tanılama ayarları kaydedin.

Yeni ayarları yaklaşık 10 dakika içinde etkinleşir. Bundan sonra günlüklerini arşivleme yapılandırılmış hedefte görünür **tanılama günlükleri** bölmesi.

Tanılama yapılandırma hakkında daha fazla bilgi için bkz. [Azure tanılama günlükleri'ne genel bakış](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Tanılama günlükleri kategorileri

Olay hub'ları iki kategoriye için tanılama günlüklerini yakalar:

* **Arşiv günlükleri**: Event Hubs arşivi için özellikle ilgili günlükler, hataları arşivlemek için ilgili günlükleri.
* **İşlem günlüklerinde**: Event Hubs işlemleri sırasında özellikle gerçekleşen işlemleri hakkında bilgi işlem türü, olay hub'ı oluşturma, kullanılan kaynaklara ve işlemin durumu dahil olmak üzere.

## <a name="diagnostic-logs-schema"></a>Tanılama günlükleri şeması

Tüm günlükler, JavaScript nesne gösterimi (JSON) biçiminde depolanır. Aşağıdaki bölümlerde açıklanan biçimde kullanan dize alanları her giriş vardır.

### <a name="archive-logs-schema"></a>Arşiv günlükleri şeması

Arşiv günlük JSON dizeleri aşağıdaki tabloda listelenen öğeleri şunlardır:

Ad | Açıklama
------- | -------
TaskName | Başarısız görev açıklaması.
Etkinlik Kimliği | İzleme için kullanılan iç kimliği.
trackingId | İzleme için kullanılan iç kimliği.
resourceId | Azure Resource Manager kaynak kimliği.
eventHub | (Ad alanı adı içerir) olay hub'ı tam adı.
PartitionID | Yazılmakta olan olay hub'ı bölüm.
archiveStep | ArchiveFlushWriter
startTime | Başlangıç zamanı hatası.
hataları | Kaç kez bir hata oluştu.
Durationınseconds | Hata süresi.
message | Hata iletisi.
category | ArchiveLogs

Aşağıdaki kod, bir JSON dizesi arşiv günlüğü örneğidir:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>İşlem günlüklerinde şeması

İşlem günlüğü JSON dizelerini aşağıdaki tabloda listelenen öğeleri içerir:

Ad | Açıklama
------- | -------
Etkinlik Kimliği | Amaç izlemek için kullanılan iç kimliği.
EventName | İşlem adı.  
resourceId | Azure Resource Manager kaynak kimliği.
SubscriptionId | Abonelik kimliği
EventTimeString | İşlem süresi.
EventProperties | İşlem özellikleri.
Durum | İşlem durumu.
Çağıran | Çağıran işlemin (Azure portalı ya da yönetim istemcisi).
category | OperationalLogs

Aşağıdaki kod, bir işlem günlüğü JSON dizesi örneğidir:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Sonraki adımlar
* [Event hubs'a giriş](event-hubs-what-is-event-hubs.md)
* [Event Hubs API’sine genel bakış](event-hubs-api-overview.md)
* [Event Hubs kullanmaya başlayın](event-hubs-dotnet-standard-getstarted-send.md)

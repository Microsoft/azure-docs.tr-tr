---
title: Tanılama günlüklerini Azure Service Bus | Microsoft Docs
description: Azure 'da Service Bus için tanılama günlüklerini ayarlamayı öğrenin.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592450"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Service Bus için tanılama günlüklerini etkinleştirme

Azure Service Bus ad alanınızı kullanmaya başladığınızda, ad alanınızı nasıl ve ne zaman oluşturduğunu, silineceğini veya erişildiğini izlemek isteyebilirsiniz. Bu makale, kullanılabilir olan tüm işletimsel/tanılama günlüklerine genel bir bakış sağlar.

Azure Service Bus Şu anda Azure Service Bus ad alanı üzerinde gerçekleştirilen yakalama **yönetimi işlemlerini** kapsayan etkinlik/işlem günlüklerini destekliyor. Özellikle, bu Günlükler kuyruk oluşturma, kullanılan kaynaklar ve işlemin durumu dahil olmak üzere işlem türünü yakalar.

## <a name="operational-logs-schema"></a>İşletimsel Günlükler şeması

Tüm Günlükler aşağıdaki 2 konumda JavaScript Nesne Gösterimi (JSON) biçiminde depolanır.

- **AzureActivity** -portalda veya Azure Resource Manager şablon dağıtımları aracılığıyla ad alanınız üzerinde gerçekleştirilen işlemlerden veya eylemlerin günlüklerini görüntüler.
- **AzureDiagnostics** -API 'yi kullanarak veya dil SDK 'sindeki yönetim istemcileri aracılığıyla ad alanınız üzerinde gerçekleştirilen işlemlerden/eylemlerden günlükleri görüntüler.

İşletimsel günlük JSON dizeleri aşağıdaki tabloda listelenen öğeleri içerir:

| Adı | Açıklama |
| ------- | ------- |
| Etkinlik kimliği | Belirtilen etkinliği tanımlamak için kullanılan iç KIMLIK |
| eventName | İşlem adı |
| ResourceId | Azure Resource Manager kaynak KIMLIĞI |
| kaynak grubundaki | Abonelik Kimliği |
| EventTimeString | İşlem süresi |
| EventProperties | İşlem özellikleri |
| Durum | İşlem durumu |
| Çağıran | İşlem (Azure portal veya yönetim istemcisi) çağıranı |
| Kategori | OperationalLogs |

İşte işlem günlüğü JSON dizesine bir örnek:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>İşletimsel günlüklerde hangi olaylar/işlemler yakalanır?

İşlem günlükleri Azure Service Bus ad alanında gerçekleştirilen tüm yönetim işlemlerini yakalar. Azure Service Bus üzerinde gerçekleştirilen yüksek miktarda veri işlemi nedeniyle veri işlemleri yakalanmaz.

> [!NOTE]
> Veri işlemlerini daha iyi izlemek için istemci tarafı izlemenin kullanılması önerilir.

Aşağıdaki yönetim işlemleri işlemsel günlüklerde yakalanır- 

| Kapsam | İşlem|
|-------| -------- |
| uzayına | <ul> <li> Ad alanı oluştur</li> <li> Ad alanını güncelleştir </li> <li> Ad alanını sil </li>  </ul> | 
| Kuyruk | <ul> <li> Sıra oluştur</li> <li> Kuyruğu Güncelleştir</li> <li> Kuyruğu Sil </li> </ul> | 
| Konu | <ul> <li> Konu Oluştur </li> <li> Konuyu Güncelleştir </li> <li> Konuyu sil </li> </ul> |
| Abonelik | <ul> <li> Abonelik Oluşturma </li> <li> Aboneliği Güncelleştir </li> <li> Aboneliği Sil </li> </ul> |

> [!NOTE]
> Şu anda, **okuma** işlemleri işlemsel günlüklerde izlenmez.

## <a name="how-to-enable-operational-logs"></a>İşletimsel Günlükler nasıl etkinleştirilir?

İşletimsel Günlükler varsayılan olarak devre dışıdır. Tanılama günlüklerini etkinleştirmek için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com), Azure Service Bus ad alanına gidin ve **izleme**altında **Tanılama ayarları**' na tıklayın.

   ![Tanılama günlüklerine dikey pencere gezintisi](./media/service-bus-diagnostic-logs/image1.png)

2. Tanılama ayarlarını yapılandırmak için **Tanılama ayarı Ekle** ' ye tıklayın.  

   ![tanılama günlüklerini aç](./media/service-bus-diagnostic-logs/image2.png)

3. Tanılama ayarlarını yapılandırma
   1. Tanılama ayarlarını tanımlamak için bir **ad** yazın.
   2. Tanılama için bir hedef seçin.
      - **Depolama hesabı**' nı seçerseniz, tanılamaların depolanacağı depolama hesabını yapılandırmanız gerekir.
      - **Olay Hub 'larını**seçerseniz, tanılama ayarlarının akıtılabileceği uygun olay hub 'ını yapılandırmanız gerekir.
      - **Log Analytics**seçerseniz, Tanılamanın hangi Log Analytics örneğini gönderileceğini belirtmeniz gerekir.
    3. **Operationallogs**' a bakın.

       ![durum tanılama günlüklerini değiştirme](./media/service-bus-diagnostic-logs/image3.png)

4. **Kaydet** düğmesine tıklayın.


Yeni ayarlar yaklaşık 10 dakika içinde etkili olur. Bundan sonra, **tanılama günlükleri** dikey penceresinde yapılandırılan arşiv hedefinde Günlükler görüntülenir.

Tanılamayı yapılandırma hakkında daha fazla bilgi için bkz. [Azure tanılama günlüklerine genel bakış](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

Service Bus hakkında daha fazla bilgi edinmek için aşağıdaki bağlantılara bakın:

* [Service Bus giriş](service-bus-messaging-overview.md)
* [Service Bus kullanmaya başlayın](service-bus-dotnet-get-started-with-queues.md)

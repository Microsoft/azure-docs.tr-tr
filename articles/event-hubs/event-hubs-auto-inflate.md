---
title: Üretilen iş birimlerini otomatik olarak ölçeklendirme-Azure Event Hubs | Microsoft Docs
description: Bir ad alanı üzerinde otomatik şişimine, üretilen iş birimlerini otomatik olarak ölçeklendirmeye olanak tanır.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: dc6edaebebe89b6d4a35ada58d40795f86a935d3
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264481"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Azure Event Hubs üretilen iş birimlerini otomatik olarak ölçeklendirme
Azure Event Hubs, yüksek düzeyde ölçeklenebilir bir veri akışı platformudur. Bu nedenle, Event Hubs kullanım genellikle hizmeti kullanmaya başladıktan sonra artar. Bu tür kullanımlar, önceden tanımlanmış [üretilen iş birimlerinin](event-hubs-scalability.md#throughput-units) Event Hubs ölçeklendirilmesini ve daha büyük aktarım hızlarını işlemesini gerektirir. Event Hubs **Otomatik Şişir** özelliği, kullanım ihtiyaçlarını karşılamak için üretilen iş birimi sayısını artırarak otomatik olarak ölçeklendirilir. Üretilen iş birimlerinin artırılması, azaltma senaryolarına engel olur; bu:

* Veri giriş ücretleri, üretilen iş birimlerini aşıyor.
* Veri çıkış isteği ücretleri, üretilen iş birimlerini aşıyor.

Event Hubs hizmeti, yük en düşük eşiğin ötesinde, sunucu meşgul hatalarıyla başarısız olan istekler olmadan aktarım hızını artırır.

## <a name="how-auto-inflate-works"></a>Otomatik Şişir nasıl yapılır?

Event Hubs trafik, [işleme birimleri](event-hubs-scalability.md#throughput-units)tarafından denetlenir. Tek bir üretilen iş birimi, saniye başına saniyede 1 MB ve bu çıkış miktarı kadar iki kez izin verir. Standart olay hub 'ları 1-20 üretilen iş birimi ile yapılandırılabilir. Otomatik şişir, seçtiğiniz en düşük üretilen iş birimleri ile küçük bir başlangıç yapmanızı sağlar. Bu özellik daha sonra, trafiğinizin artışına bağlı olarak, ihtiyaç duyduğunuz maksimum üretilen iş birimi sınırına göre otomatik olarak ölçeklendirilir. Otomatik Şişir aşağıdaki avantajları sağlar:

- Küçük ve büyüdikçe ölçeği büyütmek için etkili ölçekleme mekanizması.
- Kısıtlama sorunları olmadan belirtilen üst sınıra otomatik olarak ölçeklendirin.
- Ölçeklendirme üzerinde daha fazla denetim, ne zaman ve ne kadar ölçeklenebileceğini kontrol edersiniz.

## <a name="enable-auto-inflate-on-a-namespace"></a>Bir ad alanı üzerinde otomatik olarak Şişir etkinleştir

Aşağıdaki yöntemlerden birini kullanarak standart bir katman Event Hubs ad alanı üzerinde otomatik olarak Şişir etkinleştirebilir veya devre dışı bırakabilirsiniz:

- [Azure Portal](https://portal.azure.com).
- [Azure Resource Manager şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

> [!NOTE]
> Temel katman Event Hubs ad alanları otomatik Şişir desteklemez.

### <a name="enable-auto-inflate-through-the-portal"></a>Portal üzerinden otomatik Şişir özelliğini etkinleştirme


#### <a name="enable-at-the-time-of-creation"></a>Oluşturma sırasında etkinleştirin 
**Event Hubs ad alanı oluştururken**otomatik Şişir özelliğini etkinleştirebilirsiniz:
 
![Olay Hub 'ı oluşturma sırasında otomatik Şişir 'yi etkinleştir](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Bu seçenek etkinken, üretilen iş birimleriniz ile küçük bir başlangıç yapabilir ve kullanım gereksinimleriniz artdıkça ölçeği artırabilirsiniz. Enflasyon üst sınırı, saat başına kullanılan üretilen iş birimi sayısına bağlı olarak fiyatlandırmayı hemen etkilemez.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Mevcut bir olay hub 'ı için otomatik şişimine izin et
Ayrıca, otomatik Şişir özelliğini etkinleştirebilir ve ayarlarını aşağıdaki yönergeleri kullanarak değiştirebilirsiniz: 
 
1. **Event Hubs ad alanı** sayfasında, **Otomatik Şişir üretilen Iş birimleri**altında **devre dışı** öğesini seçin.  

    ![Event Hubs ad alanı sayfasında üretilen iş birimlerini seçin](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. **Ölçek ayarları** sayfasında, **Etkinleştir** onay kutusunu seçin (otomatik ölçeklendirme özelliği etkinleştirilmemişse).

    ![Etkinleştir ' i seçin](./media/event-hubs-auto-inflate/scale-settings.png)
3. **En fazla** üretilen iş birimi sayısını girin veya değeri ayarlamak için kaydırma çubuğunu kullanın. 
4. seçim Bu sayfanın **en üstündeki** üretilen iş birimi sayısını güncelleştirin. 


> [!NOTE]
> Aktarım hızını artırmak için otomatik Şişir yapılandırmasını uyguladığınızda, Event Hubs hizmeti, aktarım hızı ne zaman ve ne zaman arttığı hakkında bilgi veren tanılama günlüklerini yayar. Bir olay hub 'ı için tanılama günlüğünü etkinleştirmek üzere Azure portal, Olay Hub 'ı sayfasındaki sol menüde **Tanılama ayarları** ' nı seçin. Daha fazla bilgi için bkz. [Azure Olay Hub 'ı için tanılama günlüklerini ayarlama](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak otomatik Şişir etkinleştir

Bir Azure Resource Manager şablonu dağıtımı sırasında otomatik olarak Şişir sağlayabilirsiniz. Örneğin, `isAutoInflateEnabled` özelliğini **true** olarak ayarlayın ve `maximumThroughputUnits` ' yi 10 ' a ayarlayın. Örnek:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Tam şablon için, [Create Event Hubs ad alanı](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) ' na bakın ve GitHub 'da ınşişir şablonunu etkinleştirin.


## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs’a genel bakış](event-hubs-what-is-event-hubs.md)


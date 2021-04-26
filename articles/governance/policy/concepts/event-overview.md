---
title: Azure Ilke durum değişikliği olaylarına yeniden davranıma
description: Uygulamaların, karmaşık koda gerek kalmadan durum değişikliklerine tepki vermesini sağlayan uygulama Ilkesi olaylarına abone olmak için Azure Event Grid kullanın.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735117"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Azure Ilke durum değişikliği olaylarına yeniden davranıma

Azure Ilke olayları, uygulamaların durum değişikliklerine tepki vermesini sağlar. Bu tümleştirme, karmaşık kod veya pahalı ve verimsiz yoklama hizmetlerine gerek olmadan yapılır. Bunun yerine, olaylar [Azure işlevleri](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml)gibi abonelere veya kendi özel http dinleyicinize de [Azure Event Grid](../../../event-grid/index.yml) gönderilir.
Kritik ölçüde, yalnızca kullandığınız kadar ödersiniz.

Azure Ilke olayları, zengin yeniden deneme ilkeleri ve atılacak mektup teslimi aracılığıyla uygulamalarınıza güvenilir teslim hizmetleri sağlayan Azure Event Grid gönderilir. Daha fazla bilgi edinmek için bkz. [ileti teslimi Event Grid ve yeniden deneyin](../../../event-grid/delivery-and-retry.md).

Genel Azure Ilkesi olay senaryosu, bir kaynağın uyumluluk durumu, ilke değerlendirmesi sırasında değiştiğinde izleniyor. Olay tabanlı mimari, kaynakların uyumluluk durumunu sabit bir zamanlamaya göre taramak yerine bu değişikliklere tepki vermek için etkili bir yoldur.

> [!NOTE]
> Azure Ilke durum değişikliği olayları, bir [değerlendirme tetikleyicisi](../how-to/get-compliance-data.md#evaluation-triggers) kaynak değerlendirmesini tamamladıktan sonra Event Grid gönderilir.

Eksiksiz bir öğretici için bkz. [Azure CLI ile Event Grid için bkz. Azure CLI ile ilke durumu değişikliği olayları](../tutorials/route-state-change-events.md) .

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Kaynak ve işleyicilerle Event Grid modeli" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Kullanılabilir Azure Ilkesi etkinlikleri

Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../../../event-grid/concepts.md#event-subscriptions) kullanır. Azure Ilke olay abonelikleri üç tür olay içerebilir:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. Poliyeleghts. PolicyStateCreated | Bir ilke uyumluluk durumu oluşturulduğunda tetiklenir. |
| Microsoft. Policınghts. PolicyStateChanged | Bir ilke uyumluluk durumu değiştirildiğinde tetiklenir. |
| Microsoft. Policınghts. PolicyStateDeleted | Bir ilke uyumluluk durumu silindiğinde tetiklenir. |

## <a name="event-schema"></a>Olay şeması

Azure Ilke olayları, verilerinizde yaptığınız değişikliklere yanıt vermek için gereken tüm bilgileri içerir. `eventType`Özellik "Microsoft. Poliyeleghts" ile başladığında bir Azure ilke olayı tanımlayabilirsiniz.
Event Grid olay özelliklerinin kullanımı hakkında ek bilgiler şu şekilde belgelenmiştir  
[Olay şeması Event Grid](../../../event-grid/event-schema.md).

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `id` | dize | Etkinliğin benzersiz tanımlayıcısı. |
| `topic` | string | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Kaynak adı ve kaynak türü dahil olmak üzere, uyumluluk durumu değişikliğinin bulunduğu kaynağın tam nitelikli KIMLIĞI. Biçimini kullanır, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | object | Azure Ilkesi olay verileri. |
| `data.timestamp` | string | Kaynağın Azure Ilkesi tarafından tarandığı saat (UTC). Olayları sıralama için, en üst düzey veya özellikler yerine bu özelliği kullanın `eventTime` `time` . |
| `data.policyAssignmentId` | string | İlke atamasının kaynak KIMLIĞI. |
| `data.policyDefinitionId` | string | İlke tanımının kaynak KIMLIĞI. |
| `data.policyDefinitionReferenceId` | string | İlke ataması bir girişim için ise, girişim tanımının içindeki ilke tanımı için başvuru Kımlığı. Boş olabilir. |
| `data.complianceState` | string | İlke atamasına göre kaynağın uyumluluk durumu. |
| `data.subscriptionId` | string | Kaynağın abonelik KIMLIĞI. |
| `data.complianceReasonCode` | string | Uyumluluk nedeni kodu. Boş olabilir. |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

İlke durum değişikliği olayına bir örnek aşağıda verilmiştir:

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Daha fazla bilgi için bkz. [Azure ilke olayları şeması](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Olayları tüketen uygulamalar

Azure Ilke olaylarını işleyen uygulamalar aşağıdaki önerilen yöntemleri izlemelidir:

> [!div class="checklist"]
> - Birden çok abonelik olayları aynı olay işleyicisine yönlendirmek üzere yapılandırılabilir, bu nedenle olayların belirli bir kaynaktan olduğunu varsaymayın. Bunun yerine, ilke atamasını, ilke tanımını ve durum değişikliği olayının kaynağını sağlamak için iletinin konusunu kontrol edin.
> - `eventType`' İ kontrol edin ve aldığınız tüm olayların istediğiniz tür olduğunu varsayın.
> - `data.timestamp`En üst düzey veya özellikler yerine Azure ilkesindeki olayların sırasını öğrenmek için kullanın `eventTime` `time` .
> - İlke durumu değişikliği olan kaynağa erişmek için konu alanını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Event Grid hakkında daha fazla bilgi edinin ve Azure Ilke durum değişikliği olaylarına bir deneme verin:

- [Azure CLı ile Event Grid ilke durum değişikliği olaylarını yönlendirme](../tutorials/route-state-change-events.md)
- [Event Grid için Azure Ilke şeması ayrıntıları](../../../event-grid/event-schema-policy.md)
- [Event Grid Hakkında](../../../event-grid/overview.md)
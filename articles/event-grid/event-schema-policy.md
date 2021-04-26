---
title: Event Grid kaynağı olarak Azure Ilkesi
description: Bu makalede, Azure Ilkesi 'nin bir Event Grid olay kaynağı olarak nasıl kullanılacağı açıklanır. Bu, şemayı ve öğretici ve nasıl yapılır makalelerini bağlar ve bağlantılar sağlar.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735112"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Event Grid kaynağı olarak Azure Ilkesi

Bu makalede, [Azure ilke](../governance/policy/index.yml) olayları için özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](./event-schema.md). Ayrıca, Azure Ilkesini bir olay kaynağı olarak kullanmak için hızlı başlayan ve öğreticilerin bir listesini de sağlar.

## <a name="available-event-types"></a>Kullanılabilir olay türleri

Azure Ilkesi aşağıdaki olay türlerini yayar:

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. Poliyeleghts. PolicyStateCreated | Bir ilke uyumluluk durumu oluşturulduğunda tetiklenir. |
| Microsoft. Policınghts. PolicyStateChanged | Bir ilke uyumluluk durumu değiştirildiğinde tetiklenir. |
| Microsoft. Policınghts. PolicyStateDeleted | Bir ilke uyumluluk durumu silindiğinde tetiklenir. |

## <a name="example-event"></a>Örnek olay

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)
Aşağıdaki örnekte, bir ilke durumu oluşturma olayının şeması gösterilmektedir: 

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
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

İlke durumu değişti olayının şeması benzerdir: 

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
# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Aşağıdaki örnekte, bir ilke durumu oluşturma olayının şeması gösterilmektedir: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
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
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

İlke durumu değişti olayının şeması benzerdir: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
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
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Olay özellikleri

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `topic` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Kaynak adı ve kaynak türü dahil olmak üzere, uyumluluk durumu değişikliğinin bulunduğu kaynağın tam nitelikli KIMLIĞI. Biçimini kullanır, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Azure Ilkesi olay verileri. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `source` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Kaynak adı ve kaynak türü dahil olmak üzere, uyumluluk durumu değişikliğinin bulunduğu kaynağın tam nitelikli KIMLIĞI. Biçimini kullanır, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `time` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Azure Ilkesi olay verileri. |
| `specversion` | string | CloudEvents şema belirtimi sürümü. |

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `timestamp` | dize | Kaynağın Azure Ilkesi tarafından tarandığı saat (UTC). Olayları sıralama için, en üst düzey veya özellikler yerine bu özelliği kullanın `eventTime` `time` . |
| `policyAssignmentId` | string | İlke atamasının kaynak KIMLIĞI. |
| `policyDefinitionId` | string | İlke tanımının kaynak KIMLIĞI. |
| `policyDefinitionReferenceId` | string | İlke ataması bir girişim için ise, girişim tanımının içindeki ilke tanımı için başvuru Kımlığı. Boş olabilir. |
| `complianceState` | string | İlke atamasına göre kaynağın uyumluluk durumu. |
| `subscriptionId` | string | Kaynağın abonelik KIMLIĞI. |
| `complianceReasonCode` | string | Uyumluluk nedeni kodu. Boş olabilir. |

## <a name="next-steps"></a>Sonraki adımlar

- Azure Ilke durum değişikliği olaylarını yönlendirme hakkında yönergeler için bkz. [ilke durum değişikliği bildirimleri için Event Grid kullanma](../governance/policy/tutorials/route-state-change-events.md).
- Azure Ilkesini Event Grid tümleştirmeyle ilgili genel bir bakış için bkz. [Event Grid kullanarak Azure ilke olaylarına tepki](../governance/policy/concepts/event-overview.md)verme.
- Azure Event Grid giriş için bkz. [Event Grid nedir?](./overview.md)
- Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](./subscription-creation-schema.md).
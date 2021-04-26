---
title: Event Grid kaynak olarak Azure aboneliği
description: Azure Event Grid sahip abonelik olayları için sunulan özellikleri açıklar
ms.topic: reference
ms.date: 02/12/2021
ms.openlocfilehash: b9753ecfb46f5ac5f383f19e3d409e703c144d48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363194"
---
# <a name="azure-subscription-as-an-event-grid-source"></a>Event Grid kaynağı olarak Azure aboneliği

Bu makalede, Azure abonelik olayları için özellikler ve şema sağlanmaktadır. Olay şemalarına giriş için bkz. [Azure Event Grid olay şeması](event-schema.md).

Azure abonelikleri ve kaynak grupları aynı olay türlerini yayar. Olay türleri, kaynak değişiklikleri veya eylemlerle ilgilidir. Birincil fark, kaynak gruplarının kaynak grubundaki kaynakların olaylarını yaymış olması ve Azure aboneliklerinin, abonelik genelindeki kaynakların olaylarını yaymanızdır.

Kaynak olayları, öğesine gönderilen PUT, PATCH, POST ve DELETE işlemleri için oluşturulur `management.azure.com` . GET işlemleri olay oluşturmaz. Veri düzlemine gönderilen işlemler (gibi `myaccount.blob.core.windows.net` ) olay oluşturmaz. Eylem olayları, bir kaynağın anahtarlarını listeleme gibi işlemler için olay verileri sağlar.

Bir Azure aboneliği için olaylara abone olduğunuzda, uç noktanız bu abonelik için tüm olayları alır. Olaylar, bir sanal makineyi güncelleştirme, ancak sizin için önemli olmayan olayları (örneğin, dağıtım geçmişinde yeni bir giriş yazma gibi) de içerebilir. Uç noktanıza tüm olayları alabilir ve işlemek istediğiniz olayları işleyen kodu yazabilirsiniz. Ya da olay aboneliği oluştururken bir filtre ayarlayabilirsiniz.

Olayları programlı bir şekilde işlemek için, değere bakarak olayları sıralayabilirsiniz `operationName` . Örneğin, olay uç noktanız yalnızca veya ' ye eşit olan işlemlere yönelik olayları işleyebilir `Microsoft.Compute/virtualMachines/write` `Microsoft.Storage/storageAccounts/write` .

Olay konusu, işlemin hedefi olan kaynağın kaynak KIMLIĞIDIR. Bir kaynağın olaylarını filtrelemek için, olay aboneliğini oluştururken bu kaynak KIMLIĞINI sağlayın. Bir kaynak türüne göre filtrelemek için aşağıdaki biçimde bir değer kullanın: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`



## <a name="available-event-types"></a>Kullanılabilir olay türleri

Azure abonelikleri, bir VM oluşturulduğunda veya bir depolama hesabı silindiğinde, yönetim olaylarını Azure Resource Manager.

| Olay türü | Açıklama |
| ---------- | ----------- |
| Microsoft. resources. ResourceActionCancel | Kaynak üzerinde eylem iptal edildiğinde tetiklenir. |
| Microsoft. resources. ResourceActionFailure | Kaynak üzerinde eylem başarısız olduğunda tetiklenir. |
| Microsoft. resources. ResourceActionSuccess | Kaynak üzerinde eylem başarılı olduğunda tetiklenir. |
| Microsoft. resources. ResourceDeleteCancel | Silme işlemi iptal edildiğinde tetiklenir. Bu olay, bir şablon dağıtımı iptal edildiğinde oluşur. |
| Microsoft. resources. ResourceDeleteFailure | Silme işlemi başarısız olduğunda tetiklenir. |
| Microsoft. resources. ResourceDeleteSuccess | Silme işlemi başarılı olduğunda tetiklenir. |
| Microsoft. resources. ResourceWriteCancel | Oluşturma veya güncelleştirme işlemi iptal edildiğinde oluşturulur. |
| Microsoft. resources. ResourceWriteFailure | Oluşturma veya güncelleştirme işlemi başarısız olduğunda oluşturulur. |
| Microsoft. resources. ResourceWriteSuccess | Oluşturma veya güncelleştirme işlemi başarılı olduğunda tetiklenir. |

## <a name="example-event"></a>Örnek olay

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Aşağıdaki örnekte bir **Resourcewritesuccess** olayının şeması gösterilmektedir. Aynı şema **Resourcewritefailure** ve için farklı değerlere sahip **resourcewritecancel** olayları için de kullanılır `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

Aşağıdaki örnekte bir **Resourcedeletesuccess** olayının şeması gösterilmektedir. Aynı şema **Resourcedeletefailure** ve için farklı değerlere sahip **resourcedeletecancel** olayları için kullanılır `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

Aşağıdaki örnekte bir **Resourceactionsuccess** olayının şeması gösterilmektedir. Aynı şema **Resourceactionfailure** için, için farklı değerlere sahip **resourceactioncancel** olayları için de kullanılır `eventType` .

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)


Aşağıdaki örnekte bir **Resourcewritesuccess** olayının şeması gösterilmektedir. Aynı şema **Resourcewritefailure** ve için farklı değerlere sahip **resourcewritecancel** olayları için de kullanılır `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "topic": "/subscriptions/{subscription-id}",
  "type": "Microsoft.Resources.ResourceWriteSuccess",
  "time": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "specversion": "`1.0"

}]
```

Aşağıdaki örnekte bir **Resourcedeletesuccess** olayının şeması gösterilmektedir. Aynı şema **Resourcedeletefailure** ve için farklı değerlere sahip **resourcedeletecancel** olayları için kullanılır `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "source": "/subscriptions/{subscription-id}",
  "type": "Microsoft.Resources.ResourceDeleteSuccess",
  "time": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "specversion": "1.0"
}]
```

Aşağıdaki örnekte bir **Resourceactionsuccess** olayının şeması gösterilmektedir. Aynı şema **Resourceactionfailure** için, için farklı değerlere sahip **resourceactioncancel** olayları için de kullanılır `eventType` .

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "source": "/subscriptions/{subscription-id}",
  "type": "Microsoft.Resources.ResourceActionSuccess",
  "time": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "specversion": "1.0"
}]
```

---

### <a name="event-properties"></a>Olay özellikleri

# <a name="event-grid-event-schema"></a>[Event Grid olay şeması](#tab/event-grid-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `topic` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `eventType` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `eventTime` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Abonelik olay verileri. |
| `dataVersion` | string | Veri nesnesinin şema sürümü. Şema sürümünü yayımcı tanımlar. |
| `metadataVersion` | string | Olay meta verilerinin şema sürümü. Event Grid en üst düzey özelliklerin şemasını tanımlar. Event Grid bu değeri sağlar. |

# <a name="cloud-event-schema"></a>[Bulut olay şeması](#tab/cloud-event-schema)

Bir olay aşağıdaki en üst düzey verilere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `source` | dize | Olay kaynağının tam kaynak yolu. Bu alan yazılabilir değil. Event Grid bu değeri sağlar. |
| `subject` | string | Olay konusunun yayımcı tarafından tanımlanan yolu. |
| `type` | string | Bu olay kaynağı için kayıtlı olay türlerinden biri. |
| `time` | string | Etkinliğin UTC saatine göre oluşturulduğu zaman. |
| `id` | string | Etkinliğin benzersiz tanımlayıcısı. |
| `data` | object | Abonelik olay verileri. |
| `specversion` | string | CloudEvents şema belirtimi sürümü. |

---

Veri nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Tür | Description |
| -------- | ---- | ----------- |
| `authorization` | object | İşlem için istenen yetkilendirme. |
| `claims` | object | Taleplerin özellikleri. Daha fazla bilgi için bkz. [JWT belirtimi](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| `correlationId` | string | Sorun giderme için bir işlem KIMLIĞI. |
| `httpRequest` | object | İşlemin ayrıntıları. Bu nesne yalnızca var olan bir kaynağı güncelleştirirken veya bir kaynağı silerken dahil edilir. |
| `resourceProvider` | string | İşlemin kaynak sağlayıcısı. |
| `resourceUri` | string | İşlemdeki kaynağın URI 'SI. |
| `operationName` | string | Gerçekleştirilen işlem. |
| `status` | string | İşlemin durumu. |
| `subscriptionId` | string | Kaynağın abonelik KIMLIĞI. |
| `tenantId` | string | Kaynağın kiracı KIMLIĞI. |

## <a name="tutorials-and-how-tos"></a>Öğreticiler ve nasıl yapılır kılavuzları
|Başlık |Açıklama  |
|---------|---------|
| [Öğretici: Event Grid ve Microsoft ekipleriyle Azure Otomasyonu](ensure-tags-exists-on-new-virtual-machines.md) |Bir olay gönderen bir sanal makine oluşturun. Olay, sanal makineyi etiketleyen bir Otomasyon Runbook 'unu tetikler ve bir Microsoft ekipleri kanalına gönderilen bir ileti tetikler. |
| [Nasıl yapılır: Portal üzerinden olaylara abone olma](subscribe-through-portal.md) | Bir Azure aboneliği için olaylara abone olmak üzere portalı kullanın. |
| [Azure CLı: Azure aboneliği için olaylara abone olma](./scripts/event-grid-cli-azure-subscription.md) |Bir Azure aboneliğine Event Grid aboneliği oluşturan ve bir Web kancasına olay gönderen örnek komut dosyası. |
| [PowerShell: bir Azure aboneliği için olaylara abone olma](./scripts/event-grid-powershell-azure-subscription.md)| Bir Azure aboneliğine Event Grid aboneliği oluşturan ve bir Web kancasına olay gönderen örnek komut dosyası. |

## <a name="next-steps"></a>Sonraki adımlar

* Azure Event Grid giriş için bkz. Event Grid nedir [?](overview.md).
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).

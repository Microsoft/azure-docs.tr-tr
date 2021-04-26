---
title: Azure Event Grid güvenlik ve kimlik doğrulaması
description: Azure Event Grid ve kavramlarını açıklar.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601049"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Event Grid kaynaklarına erişimi yetkilendirme
Azure Event Grid, farklı kullanıcılara verilen erişim düzeyini, olay abonelikleri listeleme, yenilerini oluşturma ve anahtar oluşturma gibi çeşitli **yönetim işlemlerini** yapmak için denetlemenizi sağlar. Event Grid Azure rol tabanlı erişim denetimi (Azure RBAC) kullanır.

> [!NOTE]
> EventGrid, Event Grid konular veya etki alanları için olayları yayımlamak üzere Azure RBAC 'yi desteklemez. Olayları yayınlayan istemcilerin kimliğini doğrulamak için bir paylaşılan erişim Imzası (SAS) anahtarı veya belirteci kullanın. Daha fazla bilgi için bkz. [Yayımlama Istemcilerinin kimlik doğrulaması](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>İşlem türleri
Azure Event Grid tarafından desteklenen bir işlem listesi için aşağıdaki Azure CLı komutunu çalıştırın: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

Aşağıdaki işlemler, normal okuma işlemlerinden filtre uygulanan olası gizli bilgileri döndürür. Bu işlemlere erişimi kısıtlamanız önerilir. 

* Microsoft. EventGrid/Eventabonelikler/getFullUrl/eylem
* Microsoft. EventGrid/konular/listKeys/ACTION
* Microsoft. EventGrid/konular/regenerateKey/Action


## <a name="built-in-roles"></a>Yerleşik roller
Event Grid, aşağıdaki üç yerleşik rolü sağlar. 

Event Grid abonelik okuyucusu ve Event Grid aboneliği katkıda bulunan rolleri, olay aboneliklerinin yönetilmesi içindir. Kullanıcılara olay etki alanındaki konulara abone olmaları için ihtiyaç duydukları izinleri verdiklerinden, bunlar [olay etki alanlarını](event-domains.md) uygularken önemlidir. Bu roller olay aboneliklerine odaklanılmıştır ve konu başlıkları oluşturma gibi eylemler için erişim vermez.

Event Grid katkıda bulunan rolü Event Grid kaynaklarını oluşturmanıza ve yönetmenize olanak sağlar. 


| Rol | Açıklama |
| ---- | ----------- | 
| [Event Grid abonelik okuyucusu](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Event Grid olay aboneliği işlemlerini yönetmenizi sağlar. |
| [Event Grid abonelik Katılımcısı](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Event Grid olay aboneliklerini okumanızı sağlar. |
| [Katkıda bulunan Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Event Grid kaynaklarını oluşturmanıza ve yönetmenize olanak sağlar. |


> [!NOTE]
> Rol hakkında daha fazla ayrıntı sağlayan bir makaleye gitmek için ilk sütundaki bağlantıları seçin. RBAC rollerine Kullanıcı veya Grup atama hakkında yönergeler için, [Bu makaleye](../role-based-access-control/quickstart-assign-role-user-portal.md)bakın.


## <a name="custom-roles"></a>Özel roller

Yerleşik rollerden farklı izinleri belirtmeniz gerekiyorsa, özel roller oluşturabilirsiniz.

Aşağıda, kullanıcıların farklı eylemler yapmasına izin veren örnek Event Grid rol tanımları verilmiştir. Bu özel roller, yalnızca olay aboneliklerinden daha geniş erişim verdiklerinden, yerleşik rollerden farklıdır.

**EventGridReadOnlyRole.js**:: yalnızca salt okuma işlemlerine izin verin.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.js**: kısıtlanmış gönderme eylemlerine izin ver, ancak silme eylemlerine izin verme.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.js**: tüm olay Kılavuzu eylemlerine izin verir.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

[PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md)ve [rest](../role-based-access-control/custom-roles-rest.md)ile özel roller oluşturabilirsiniz.



### <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme

Event Grid hizmeti tarafından diske yazılan tüm olaylar veya veriler, bekleyen bir şekilde şifrelendiğinden emin olmak için Microsoft tarafından yönetilen bir anahtarla şifrelenir. Ayrıca, olayların veya verilerin saklanacağı en uzun süre, [Event Grid yeniden deneme ilkesiyle](delivery-and-retry.md)ilgili olarak 24 saat olur. Event Grid, 24 saat sonra tüm olayları veya verileri otomatik olarak siler veya olayın yaşam süresi (hangisi daha az) olur.

## <a name="permissions-for-event-subscriptions"></a>Olay abonelikleri için izinler
Web kancası olmayan bir olay işleyicisi (örneğin, bir olay hub 'ı veya kuyruk depolaması) kullanıyorsanız, bu kaynağa yazma erişimi gerekir. Bu izin denetimi yetkisiz bir kullanıcının kaynağa olay göndermesini engeller.

Olay kaynağı olan kaynakta **Microsoft. EventGrid/Eventabonelikleriniz/Write** izninizin olması gerekir. Kaynak kapsamına yeni bir abonelik yazıyorsanız, bu izne ihtiyacınız vardır. Gerekli kaynak, bir sistem konusuna veya özel konuya abone olup olmadığınız temel alınarak farklılık gösterir. Her iki tür de bu bölümde açıklanmaktadır.

### <a name="system-topics-azure-service-publishers"></a>Sistem konuları (Azure hizmet yayımcıları)
Sistem konuları için, kaynak kaynağın sahibi veya katılımcısı değilseniz, olayı yayımlayan kaynağın kapsamına yeni bir olay aboneliği yazma izninizin olması gerekir. Kaynağın biçimi: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Örneğin, **myacct** adlı bir depolama hesabındaki bir olaya abone olmak için Microsoft. Eventgrid/Eventaboneliklerin/Write izninizin olması gerekir: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Özel konu başlıkları
Özel konularda, olay Kılavuzu konusunun kapsamına yeni bir olay aboneliği yazma izninizin olması gerekir. Kaynağın biçimi: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Örneğin, **MyTopic** adlı özel bir konuya abone olmak için Microsoft. Eventgrid/Eventaboneliklerin/Write izninizin olması gerekir: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Sonraki adımlar

* Event Grid giriş için bkz. [Event Grid hakkında](overview.md)

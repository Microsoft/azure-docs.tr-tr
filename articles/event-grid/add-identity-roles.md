---
title: Azure Event Grid hedefteki bir role yönetilen kimlik ekleme
description: Bu makalede, yönetilen kimliğin Azure Service Bus ve Azure Event Hubs gibi hedeflere Azure rollerine nasıl ekleneceği açıklanmaktadır.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1578e4c24201614ce89351b3c3cee52a09cadc30
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280488"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>Event Grid hedefe erişimi yönetilen kimliğe verme
Bu bölümde, sistem konu başlığı, özel konu veya etki alanı için bir Azure rolüne nasıl kimlik ekleneceği açıklanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar
Aşağıdaki makalelerdeki yönergeleri kullanarak sistem tarafından atanan bir yönetilen kimlik atayın:

- [Özel konular veya etki alanları](enable-identity-custom-topics-domains.md)
- [Sistem konu başlıkları](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Desteklenen hedefler ve Azure rolleri
Olay kılavuzunuzun özel konusu veya etki alanı için kimliği etkinleştirdikten sonra Azure, Azure Active Directory ' de otomatik olarak bir kimlik oluşturur. Özel konunun veya etki alanının olayları desteklenen hedeflere iletebilmesi için bu kimliği uygun Azure rollerine ekleyin. Örneğin, olay Kılavuzu özel konusunun olayları bu ad alanındaki Olay Hub 'larına iletebilmesi için Azure Event Hubs ad alanı için **azure Event Hubs Data sender** rolüne kimliği ekleyin. 

Şu anda Azure Event Grid, olayları aşağıdaki hedeflere iletmek için sistem tarafından atanan yönetilen kimlikle yapılandırılmış özel konuları veya etki alanlarını destekler. Bu tabloda Ayrıca, özel konunun olayları iletebilmesi için kimliğin içinde olması gereken roller de sunulur.

| Hedef | Azure rolü | 
| ----------- | --------- | 
| Service Bus kuyrukları ve konuları | [Veri Göndericisini Azure Service Bus](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs veri gönderici](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob depolama | [Depolama Blob Verileri Katkıda Bulunanı](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Kuyruk Depolama |[Depolama kuyruğu veri Iletisi gönderici](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Azure portalını kullanma
Özel konunun veya etki alanının olayları hedefe iletebilmesi için, özel konuyu veya etki alanı kimliğini uygun bir role atamak üzere Azure portal kullanabilirsiniz. 

Aşağıdaki örnek, bir kuyruk veya konu kaynağı içeren bir Service Bus ad alanı için **Azure Service Bus veri gönderici** rolüne **msitesttopic** adlı bir Event Grid özel konusu için yönetilen bir kimlik ekler. Ad alanı düzeyinde role eklediğinizde, Event Grid özel konusu, olayları ad alanı içindeki tüm varlıklara iletebilir. 

1. [Azure portal](https://portal.azure.com) **Service Bus ad alanına** gidin. 
1. Sol bölmedeki **Access Control** seçin. 
1. **Rol ataması Ekle** bölümünde **Ekle** ' yi seçin. 
1. **Rol ataması Ekle** sayfasında, aşağıdaki adımları uygulayın:
    1. Rolü seçin. Bu durumda, **veri gönderici Azure Service Bus**. 
    1. Olay kılavuzunuzun özel konusunun veya etki alanının **kimliğini** seçin. 
    1. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin.

Bu adımlar, tabloda bahsedilen diğer rollere bir kimlik eklemeye benzer. 

## <a name="use-the-azure-cli"></a>Azure CLI kullanma
Bu bölümdeki örnekte, bir Azure rolüne kimlik eklemek için Azure CLı 'nın nasıl kullanılacağı gösterilmektedir. Örnek komutlar Event Grid özel konuları içindir. Event Grid etki alanları için komutlar benzerdir. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Özel konunun sistem kimliği için asıl KIMLIĞI al 
İlk olarak, özel konunun sistem tarafından yönetilen kimliğin asıl KIMLIĞINI alın ve kimliği uygun rollere atayın.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Çeşitli kapsamlardaki Olay Hub 'ları için rol ataması oluşturma 
Aşağıdaki CLı örneği, bir özel konunun kimliğini ad alanı düzeyinde veya Olay Hub 'ı düzeyinde **Azure Event Hubs veri gönderici** rolüne nasıl ekleneceğini gösterir. Rol atamasını ad alanı düzeyinde oluşturursanız, özel konu, olayları bu ad alanındaki tüm olay hub 'larına iletebilir. Olay Hub 'ı düzeyinde bir rol ataması oluşturursanız, özel konu olayları yalnızca ilgili olay hub 'ına iletebilir. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Çeşitli kapsamlar üzerinde Service Bus konu başlığı için rol ataması oluşturma 
Aşağıdaki CLı örneği, bir Event Grid özel konusunun kimliğini ad alanı düzeyinde veya Service Bus konu düzeyinde **Azure Service Bus veri gönderici** rolüne nasıl ekleneceğini gösterir. Rol atamasını ad alanı düzeyinde oluşturursanız, olay Kılavuzu konusu ad alanı içinde olayları tüm varlıklara (Service Bus kuyruklar veya konular) iletebilir. Service Bus kuyruğu veya konu düzeyinde bir rol ataması oluşturursanız, olay Kılavuzu özel konusu olayları yalnızca ilgili Service Bus kuyruğuna veya konuya iletebilir. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Sonraki adımlar
Artık sistem konusuna, özel konuya veya etki alanına sistem tarafından atanan bir kimlik atadığınıza ve bu kimliği hedeflere uygun rollere eklemiş olduğunuza göre, bkz. kimliği kullanarak hedeflere olay sunma hakkında [yönetilen kimlik kullanarak olay sunma](managed-service-identity.md) .



---
title: Olay teslimi, yönetilen hizmet kimliği ve özel bağlantı
description: Bu makalede bir Azure olay Kılavuzu konusu için yönetilen hizmet kimliğinin nasıl etkinleştirileceği açıklanır. Olayları desteklenen hedeflere iletmek için kullanın.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625288"
---
# <a name="event-delivery-with-a-managed-identity"></a>Yönetilen bir kimlikle olay teslimi
Bu makalede bir Azure Event Grid sistem konusu, özel konu veya etki alanı için [yönetilen hizmet kimliğinin](../active-directory/managed-identities-azure-resources/overview.md) nasıl kullanılacağı açıklanır. Olayları, Service Bus kuyrukları ve konuları, Olay Hub 'ları ve depolama hesapları gibi desteklenen hedeflere iletmek için kullanın.



## <a name="prerequisites"></a>Önkoşullar
1. Bir sistem konusuna, özel konuya veya etki alanına sistem tarafından atanan bir kimlik atayın. 
    - Özel konular ve etki alanları için bkz. [özel konular ve etki alanları için yönetilen kimliği etkinleştirme](enable-identity-custom-topics-domains.md). 
    - Sistem konuları için bkz. [sistem için yönetilen kimliği etkinleştirme konuları](enable-identity-system-topics.md)
1. Kimliği, hedef üzerinde (örneğin, bir Service Bus kuyruğu) uygun bir role (örneğin, Service Bus veri gönderici) ekleyin. Ayrıntılı adımlar için bkz. [hedeflere Azure rollerine kimlik ekleme](add-identity-roles.md)

    > [!NOTE]
    > Şu anda [Özel uç noktalar](../private-link/private-endpoint-overview.md)kullanarak olay teslim etmek mümkün değildir. Daha fazla bilgi için bu makalenin sonundaki [Özel uç noktalar](#private-endpoints) bölümüne bakın. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Kimlik kullanan olay abonelikleri oluşturma
Bir olay Kılavuzu özel konusuna veya sistem ile yönetilen bir kimliğe sahip etki alanına sahip olduktan sonra ve kimliği hedefteki uygun role ekledikten sonra, kimliği kullanan abonelikler oluşturmaya hazırsınız demektir. 

### <a name="use-the-azure-portal"></a>Azure portalını kullanma
Bir olay aboneliği oluşturduğunuzda, **uç nokta ayrıntıları** bölümünde bir uç nokta için sistem tarafından atanan kimliğin kullanımını etkinleştirme seçeneğini görürsünüz. 

![Service Bus kuyruğu için olay aboneliği oluştururken kimliği etkinleştirin](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Ayrıca, **ek özellikler** sekmesinde atılacak kullanım için kullanılacak sistem tarafından atanan bir kimlik kullanmayı da etkinleştirebilirsiniz. 

![Yok sayılma için sistem tarafından atanan kimliği etkinleştirme](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Azure CLı-Service Bus kuyruğunu kullanma 
Bu bölümde, bir Service Bus kuyruğuna olayları teslim etmek üzere sistem tarafından atanan kimliğin kullanımını etkinleştirmek için Azure CLı 'nın nasıl kullanılacağını öğrenirsiniz. Kimliğin **Azure Service Bus veri gönderici** rolünün bir üyesi olması gerekir. Ayrıca, yük atılacak için kullanılan depolama hesabındaki **Depolama Blobu veri katılımcısı** rolünün bir üyesi olmalıdır. 

#### <a name="define-variables"></a>Değişkenleri tanımlama
İlk olarak, CLı komutunda kullanılacak aşağıdaki değişkenlerin değerlerini belirtin. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Dağıtım için yönetilen bir kimlik kullanarak olay aboneliği oluşturma 
Bu örnek komut, **Service Bus kuyruğuna** ayarlanmış bir uç nokta türü olan Event Grid özel konusu için bir olay aboneliği oluşturur. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Dağıtım ve atılacak kullanım için yönetilen kimlik kullanarak olay aboneliği oluşturma
Bu örnek komut, **Service Bus kuyruğuna** ayarlanmış bir uç nokta türü olan Event Grid özel konusu için bir olay aboneliği oluşturur. Ayrıca, sistem tarafından yönetilen kimliğin, atılacak kullanım için kullanılacağını belirtir. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Azure CLı-Event Hubs kullanma 
Bu bölümde, bir olay hub 'ına olayları teslim etmek üzere sistem tarafından atanan kimliğin kullanımını etkinleştirmek için Azure CLı 'nın nasıl kullanılacağını öğrenirsiniz. Kimliğin **Azure Event Hubs veri gönderici** rolünün bir üyesi olması gerekir. Ayrıca, yük atılacak için kullanılan depolama hesabındaki **Depolama Blobu veri katılımcısı** rolünün bir üyesi olmalıdır. 

#### <a name="define-variables"></a>Değişkenleri tanımlama
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Dağıtım için yönetilen bir kimlik kullanarak olay aboneliği oluşturma 
Bu örnek komut, **Event Hubs** olarak ayarlanmış bir uç nokta türü olan Event Grid özel konusu için bir olay aboneliği oluşturur. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Dağıtım için yönetilen bir kimlik ve sahipsiz mektup kullanarak olay aboneliği oluşturma 
Bu örnek komut, **Event Hubs** olarak ayarlanmış bir uç nokta türü olan Event Grid özel konusu için bir olay aboneliği oluşturur. Ayrıca, sistem tarafından yönetilen kimliğin, atılacak kullanım için kullanılacağını belirtir. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Azure CLı-Azure depolama kuyruğunu kullanma 
Bu bölümde, Azure depolama kuyruğuna olayları teslim etmek üzere sistem tarafından atanan kimliğin kullanımını etkinleştirmek için Azure CLı 'yı nasıl kullanacağınızı öğreneceksiniz. Kimlik, depolama hesabındaki **depolama kuyruğu veri Iletisi gönderici** rolünün bir üyesi olmalıdır. Ayrıca, yük atılacak için kullanılan depolama hesabındaki **Depolama Blobu veri katılımcısı** rolünün bir üyesi olmalıdır.

#### <a name="define-variables"></a>Değişkenleri tanımlama  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Dağıtım için yönetilen bir kimlik kullanarak olay aboneliği oluşturma 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Dağıtım için yönetilen bir kimlik ve sahipsiz mektup kullanarak olay aboneliği oluşturma 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Özel uç noktalar
Şu anda [Özel uç noktalar](../private-link/private-endpoint-overview.md)kullanarak olay teslim etmek mümkün değildir. Diğer bir deyişle, teslim edilen olaylarınızın trafiğinden özel IP alanının olmaması gereken katı ağ yalıtımı gereksinimleriniz varsa destek yoktur. 

Bununla birlikte, gereksinimleriniz şifreli bir kanal ve gönderenin bilinen kimliği (Bu durumda Event Grid) kullanarak olayları göndermek için güvenli bir yol çağrısı yaptıysanız, bir Azure Event Grid özel konusu veya bu makalede gösterildiği gibi, sistem tarafından yönetilen kimliğe sahip bir etki alanı kullanarak Event Hubs, Service Bus veya Azure Storage hizmetine olay teslim edebilirsiniz. Daha sonra, Azure Işlevleri 'nde yapılandırılmış bir özel bağlantı veya sanal ağınızda dağıtılan Web kancası, olayları çekmek için kullanabilirsiniz. Örneğe bakın: [Azure işlevleri ile özel uç noktalara bağlanma](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Bu yapılandırma altında trafik, Event Hubs, Service Bus veya Azure Storage 'a Event Grid karşı genel IP/internet üzerinden gider, ancak kanal şifrelenebilir ve yönetilen bir kimlik Event Grid kullanılır. Sanal ağınıza dağıtılan Azure Işlevlerinizi veya Web kancasını, özel bağlantı aracılığıyla bir Event Hubs, Service Bus veya Azure depolama alanı kullanacak şekilde yapılandırırsanız, trafiğin bu bölümü Azure 'da güvenli bir şekilde kalır.


## <a name="next-steps"></a>Sonraki adımlar
Yönetilen kimlikler hakkında bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir](../active-directory/managed-identities-azure-resources/overview.md).

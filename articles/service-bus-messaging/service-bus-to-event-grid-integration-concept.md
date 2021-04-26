---
title: Azure Service Bus - Event Grid tümleştirmesine genel bakış | Microsoft Docs
description: Bu makalede, Azure Service Bus mesajlaşma 'nın Azure Event Grid ile nasıl tümleştirildiğini gösteren bir açıklama sunulmaktadır.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369671"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Azure Service Bus - Event Grid tümleştirmesine Genel Bakış
Service Bus artık bir alıcı mevcut olmadığında ve bir kuyrukta veya abonelikte iletiler olduğunda olayları Event Grid’e gönderebilir. Service Bus ad alanlarınıza Event Grid abonelikleri oluşturabilir, bu olayları dinleyebilir ve bir alıcı başlatarak olaylara tepki verebilirsiniz. Bu özellik sayesinde Service Bus’ı reaktif programlama modellerinde kullanabilirsiniz. Bu özelliğin temel kullanım modelinde, düşük ileti hacmine sahip Service Bus kuyruklarının veya aboneliklerinin sürekli olarak iletiler için yoklama yapan bir alıcısının olması gerekmez. 

Özelliği etkinleştirmek için gereken öğeler:

* En az bir Service Bus kuyruğu olan bir Service Bus Premium ad alanı veya en az bir aboneliği olan bir Service Bus konusu.
* Service Bus ad alanına katkıda bulunan erişimi. Azure portal Service Bus ad alanına gidin ve ardından **erişim denetimi (IAM)** seçeneğini belirleyin ve **rol atamaları** sekmesini seçin. Ad alanına katkıda bulunan erişimi olduğunu doğrulayın. 
* Ek olarak, Service Bus ad alanı için bir Event Grid aboneliğiniz olması da gerekir. Bu abonelik, alınacak iletiler olduğuna dair Event Grid’den bildirim alır. Normalde aboneler, web uygulaması ile iletişim kuran bir web kancası, Azure İşlevleri veya Azure App Service’in Logic Apps özelliği olabilir. Daha sonra abone iletileri işler. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Service Bus ad alanları için Event Grid abonelikleri
Service Bus ad alanları için Event Grid abonelikleri oluşturmanın üç farklı yolu vardır:

- Azure portal. Azure Logic Apps ve Azure Işlevlerine sahip Service Bus olaylara yönelik işleyiciler için Event Grid abonelikleri oluşturmak üzere Azure portal nasıl kullanacağınızı öğrenmek için aşağıdaki öğreticilere bakın. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure İşlevleri](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Azure CLI. Aşağıdaki CLı örneği, bir Service Bus ad alanı tarafından oluşturulan bir [Sistem konusu](../event-grid/system-topics.md) Için Azure işlevleri aboneliğinin nasıl oluşturulacağını göstermektedir.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Aşağıda bir örnek verilmiştir:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Ne sıklıkla ve kaç tane olay gönderilir?

Ad alanında birden fazla kuyruk ve konu veya abonelik varsa, kuyruk başına en az bir ve abonelik başına bir olay alırsınız. Service Bus varlığında bir ileti yoksa ve yeni bir ileti gelirse olaylar hemen gönderilir. Alternatif olarak, Service Bus etkin bir alıcı algılamazsa iki dakikada bir olaylar gönderilir. İleti taraması, olayları kesintiye uğratmaz.

Varsayılan olarak Service Bus, ad alanındaki tüm varlıklar için olaylar gönderir. Yalnızca belirli varlıklar için olaylar almak istiyorsanız sonraki bölüme bakın.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Olayları aldığınız yeri sınırlamak için filtreleri kullanma

Örneğin, yalnızca ad alanınızdaki bir abonelik veya kuyruktan olaylar almak istiyorsanız, Event Grid tarafından sağlanan *ile başlar* veya *ile biter* filtrelerini kullanabilirsiniz. Bazı arabirimlerde filtrelere, *ön* ve *sonek* filtreleri adı verilir. Tümü için değil, birden fazla kuyruk ve abonelik için olaylar almak istiyorsanız, birden fazla Event Grid aboneliği oluşturabilir ve her birine ilişkin bir filtre sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki öğreticilere bakın: 
- [Event Grid aracılığıyla alınan Service Bus iletilerini işlemek için Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Event Grid aracılığıyla alınan Service Bus iletileri işleyecek Azure Işlevleri](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[renkli]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png

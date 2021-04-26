---
title: Özel bağlantı hizmetini kullanarak olay sunma
description: Bu makalede, özel bağlantı hizmeti kullanılarak olayları teslim edemeyecek sınırlamanın nasıl giderileceği açıklanmaktadır.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 75ae8ff2c341048c39156e43da3615788e9d0f0a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309440"
---
# <a name="deliver-events-using-private-link-service"></a>Özel bağlantı hizmetini kullanarak olay sunma
Şu anda [Özel uç noktalar](../private-link/private-endpoint-overview.md)kullanarak olay teslim etmek mümkün değildir. Diğer bir deyişle, teslim edilen olaylarınızın trafiğinden özel IP alanının olmaması gereken katı ağ yalıtımı gereksinimleriniz varsa destek yoktur. 

## <a name="use-managed-identity"></a>Yönetilen kimlik kullanma
Bununla birlikte, gereksinimleriniz şifreli bir kanal ve gönderenin bilinen kimliği (Bu durumda Event Grid) kullanarak olayları göndermek için güvenli bir yol çağrısı yaptıysanız, bir Azure Event Grid özel konusu veya sistem tarafından yönetilen kimliğe sahip bir etki alanı kullanarak Event Hubs, Service Bus ya da Azure depolama hizmetine olay teslim edebilirsiniz. Yönetilen kimlik kullanarak olay sunma hakkında daha fazla bilgi için bkz. [yönetilen kimlik kullanarak olay teslimi](managed-service-identity.md). 

Daha sonra, Azure Işlevleri 'nde yapılandırılmış bir özel bağlantı veya sanal ağınızda dağıtılan Web kancası, olayları çekmek için kullanabilirsiniz. Örneğe bakın: [Azure işlevleri ile özel uç noktalara bağlanma](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Özel bağlantı hizmeti aracılığıyla teslim edin":::


Bu yapılandırma altında trafik, Event Hubs, Service Bus veya Azure Storage 'a Event Grid karşı genel IP/internet üzerinden gider, ancak kanal şifrelenebilir ve yönetilen bir kimlik Event Grid kullanılır. Sanal ağınıza dağıtılan Azure Işlevlerinizi veya Web kancasını, özel bağlantı aracılığıyla bir Event Hubs, Service Bus veya Azure depolama alanı kullanacak şekilde yapılandırırsanız, trafiğin bu bölümü Azure 'da güvenli bir şekilde kalır.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Yönetilen kimlik kullanarak Event Hubs olaylar sunma
Yönetilen kimlik kullanarak Event Hubs ad alanındaki Olay Hub 'larına olayları teslim etmek için şu adımları izleyin:

1. Sistem tarafından atanan kimliği etkinleştir: [sistem konuları](enable-identity-system-topics.md), [özel konular ve etki alanları](enable-identity-custom-topics-domains.md).  
1. [Kimliği, Event Hubs ad alanındaki **Azure Event Hubs Data sender** rolüne ekleyin](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Event Hubs ad alanındaki **Güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına izin ver** ayarını etkinleştirin](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. Sistem tarafından atanan kimliği kullanmak için bir olay hub 'ı bir uç nokta olarak kullanan [olay aboneliğini yapılandırın](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) .

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Yönetilen kimlik kullanarak Service Bus olaylar sunma
Yönetilen kimlik kullanarak Service Bus ad uzayındaki Service Bus kuyruklara veya konulara olay göndermek için şu adımları izleyin:

1. Sistem tarafından atanan kimliği etkinleştir: [sistem konuları](enable-identity-system-topics.md), [özel konular ve etki alanları](enable-identity-custom-topics-domains.md). 
1. Kimliği Service Bus ad alanındaki [ **Azure Service Bus Data sender** rolüne ekleyin](../service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus)
1. [Service Bus ad alanındaki **Güvenilen Microsoft hizmetlerinin bu güvenlik duvarını atlamasına izin ver** ayarını etkinleştirin](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. Bir Service Bus kuyruğu veya konuyu kullanan [olay aboneliğini,](managed-service-identity.md) sistem tarafından atanan kimliği kullanmak için uç nokta olarak yapılandırın.

## <a name="deliver-events-to-storage"></a>Olayları depolamaya teslim etme 
Yönetilen kimlik kullanarak olayları depolama sıralarına teslim etmek için şu adımları izleyin:

1. Sistem tarafından atanan kimliği etkinleştir: [sistem konuları](enable-identity-system-topics.md), [özel konular ve etki alanları](enable-identity-custom-topics-domains.md). 
1. Azure depolama sırasındaki [ **depolama kuyruğu veri iletisi gönderici** rolüne kimliği ekleyin](../storage/common/storage-auth-aad-rbac-portal.md) .
1. Bir Service Bus kuyruğu veya konuyu kullanan [olay aboneliğini,](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) sistem tarafından atanan kimliği kullanmak için uç nokta olarak yapılandırın.


## <a name="next-steps"></a>Sonraki adımlar
Yönetilen kimlik kullanarak olayları teslim etme hakkında daha fazla bilgi için bkz. [yönetilen kimlik kullanarak olay teslimi](managed-service-identity.md). 

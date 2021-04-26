---
title: Olay işleyicilerine olay teslimini doğrulama (Azure Event Grid)
description: Bu makalede, Azure Event Grid ' de olay işleyicilerine teslimin doğrulanması için farklı yollar açıklanmaktadır.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7db258ee152e4b1c46362e74e0246b80513ca9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777266"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Olay işleyicilerine olay teslimini doğrulama (Azure Event Grid)
Bu makalede olay işleyicilerine olay teslimini doğrulama hakkında bilgi sağlanır. Ayrıca, Azure Active Directory (Azure AD) veya paylaşılan bir gizli dizi kullanarak Event Grid olayları almak için kullanılan Web kancası uç noktalarının güvenliğini nasıl güvence altına alacağını gösterir.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Olay teslimi için sistem tarafından atanan kimlikleri kullanma
Bir konu veya etki alanı için sistem tarafından atanan yönetilen kimliği etkinleştirebilir ve olayları Service Bus kuyrukları ve konuları, Olay Hub 'ları ve depolama hesapları gibi desteklenen hedeflere iletmek için kimliğini kullanabilirsiniz.

Adımlar aşağıdaki gibidir: 

1. Bir sistem tarafından atanan kimliğe sahip bir konu veya etki alanı oluşturun ya da kimliği etkinleştirmek için mevcut bir konuyu veya etki alanını güncelleştirin. 
1. Kimliği, hedef üzerinde (örneğin, bir Service Bus kuyruğu) uygun bir role (örneğin, Service Bus veri gönderici) ekleyin.
1. Olay abonelikleri oluşturduğunuzda, hedefe olayları iletmek için kimliğin kullanımını etkinleştirin. 

Ayrıntılı adım adım yönergeler için bkz. [yönetilen kimlik Ile olay teslimi](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Web kancası uç noktalarına olay teslimi kimlik doğrulaması
Aşağıdaki bölümlerde, Web kancası uç noktalarına olay tesliminin nasıl doğrulanabilmesi anlatılmaktadır. Kullandığınız yöntemden bağımsız olarak bir doğrulama el sıkışma mekanizması kullanmanız gerekir. Ayrıntılar için bkz. [Web kancası olay teslimi](webhook-event-delivery.md) . 


### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory kullanma (Azure AD)
Azure AD 'yi kullanarak Event Grid olayları almak için kullanılan Web kancası uç noktasının güvenliğini sağlayabilirsiniz. Bir Azure AD uygulaması oluşturmanız, uygulamanızda yetkilendirme Event Grid bir rol ve hizmet sorumlusu oluşturmanız ve olay aboneliğini Azure AD uygulamasını kullanacak şekilde yapılandırmanız gerekir. [Event Grid Azure Active Directory yapılandırmayı](secure-webhook-delivery.md)öğrenin.

### <a name="using-client-secret-as-a-query-parameter"></a>Sorgu parametresi olarak istemci gizli anahtarını kullanma
Ayrıca, olay aboneliği oluşturmanın bir parçası olarak belirtilen Web kancası hedef URL 'sine sorgu parametreleri ekleyerek Web kancası uç noktanızı da güvenli hale getirebilirsiniz. Sorgu parametrelerinden birini, [erişim belirteci](https://en.wikipedia.org/wiki/Access_token) veya paylaşılan gizlilik gibi bir istemci gizli anahtarı olacak şekilde ayarlayın. Event Grid hizmet, Web kancasına her olay teslim isteğindeki tüm sorgu parametrelerini içerir. Web kancası hizmeti gizli dizi alabilir ve doğrulayabilir. İstemci parolası güncelleştirilirse olay aboneliğinin da güncelleştirilmesi gerekir. Bu gizli anahtar sırasında teslimat hatalarından kaçınmak için, Web kancasının, olay aboneliğini yeni gizli anahtar ile güncelleştirmeden önce sınırlı bir süre için hem eski hem de yeni gizli dizileri kabul etmesini sağlayın. 

Sorgu parametreleri, istemci gizli dizileri içerebildiği için, bunlar daha fazla dikkatli işlenir. Bunlar şifreli olarak depolanır ve hizmet işleçlerine erişemez. Hizmet günlüklerinin/izlemelerinin bir parçası olarak günlüğe kaydedilmez. Olay aboneliği özellikleri alınırken, hedef sorgu parametreleri varsayılan olarak döndürülmez. Örneğin: [--Include-Full-Endpoint-URL](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) parametresi Azure [CLI](/cli/azure)'da kullanılır.

Web kancalarına olay sunma hakkında daha fazla bilgi için bkz. [Web kancası olay teslimi](webhook-event-delivery.md)

> [!IMPORTANT]
> Azure Event Grid yalnızca **https** Web kancası uç noktalarını destekler. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v 1.0 ile uç nokta doğrulaması
Event Grid zaten biliyorsanız, uygunsuz kullanımı önlemek için uç nokta doğrulama elsıkışmasının farkında olabilirsiniz. CloudEvents v 1.0, **http seçenekleri** metodunu kullanarak kendi [uygunsuz kullanım koruma semantiğini](webhook-event-delivery.md) uygular. Bunun hakkında daha fazla bilgi edinmek için bkz. [olay teslimi-sürüm 1,0 Için HTTP 1,1 Web kancaları](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Çıktı için CloudEvents şemasını kullandığınızda Event Grid, Event Grid doğrulama olay mekanizması yerine CloudEvents v 1.0 kötüye kullanımı korumasını kullanır. Daha fazla bilgi için bkz. [Event Grid Ile CloudEvents v 1.0 şeması kullanma](cloudevents-schema.md). 


## <a name="next-steps"></a>Sonraki adımlar
Konular veya etki alanlarına istemci yayımlama olaylarını doğrulamak hakkında bilgi edinmek için bkz. [Yayımlama Istemcilerinin kimlik doğrulaması](security-authenticate-publishing-clients.md) . 

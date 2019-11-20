---
title: 'Hızlı başlangıç: Service Bus konuları ve abonelikleri oluşturmak için Azure portal kullanma'
description: 'Hızlı başlangıç: Bu hızlı başlangıçta, Azure portal kullanarak bu konuya bir Service Bus konu ve abonelik oluşturmayı öğreneceksiniz.'
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6788bf43ef97daf5ef2f823a502c223d718abaac
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718872"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Hızlı başlangıç: konuya bir Service Bus konu ve abonelik oluşturmak için Azure portal kullanın
Bu hızlı başlangıçta, bir Service Bus konu oluşturmak ve ardından bu konuya abonelikler oluşturmak için Azure portal kullanın. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus konuları ve abonelikleri nelerdir?
Service Bus konuları ve abonelikleri *publish/subscribe* mesajlaşma iletişim modelini destekler. Konular ve abonelikler kullanıldığında, dağıtılmış uygulamanın bileşenleri birbirleriyle doğrudan iletişim kurmazlar; bunun yerine bir aracı gibi davranan bir konu aracılığıyla iletileri değiş tokuş eder.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Her iletinin tek bir tüketici tarafından işlendiği Service Bus kuyruklarının aksine, konular ve abonelikler, Yayımla/abone ol ' u kullanarak bire çok bir iletişim biçimi sağlar. Bir konuya birden fazla abonelik kaydedilebilir. Bir konuya ileti gönderildiğinde, bundan sonra, bağımsız olarak ele almak/işlemek amacıyla her abonelik için kullanılabilir hale getirilir. Bir konuya abone olunması, konuya gönderilmiş olan iletilerin kopyaların alan sanal kuyruğa benzer. İsteğe bağlı olarak, bir konu için filtre kurallarını abonelik başına temelinde kaydedebilirsiniz. Bu, hangi konu aboneliklerinin hangi konuyla ilgili olduğunu filtrelemenizi veya kısıtlamanıza olanak sağlar.

Service Bus konuları ve abonelikleri, çok sayıda kullanıcı ve uygulama genelinde çok sayıda iletiyi işlemek için ölçeklendirmenize olanak tanır.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki adımlar
Bir konuya ileti gönderme ve bu iletileri bir abonelik aracılığıyla alma hakkında bilgi edinmek için aşağıdaki makaleye bakın: TOC 'de programlama dilini seçin. 

> [!div class="nextstepaction"]
> [İletiler için yayımlama ve abone olma](service-bus-dotnet-how-to-use-topics-subscriptions.md)

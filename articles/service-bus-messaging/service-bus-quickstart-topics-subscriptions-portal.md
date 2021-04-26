---
title: Service Bus konuları ve abonelikleri oluşturmak için Azure portal kullanın
description: 'Hızlı başlangıç: Bu hızlı başlangıçta, Azure portal kullanarak bu konuya bir Service Bus konu ve abonelik oluşturmayı öğreneceksiniz.'
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 25b0579f05c1f7669a8dfc1df02e9a1f575ea066
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537140"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Azure portalını kullanarak Service Bus konusunu ve konunun aboneliklerini oluşturma
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
Bu makalede bir Service Bus ad alanı, ad alanında bir konu ve konuya üç abonelik oluşturdunuz. Konuya ileti yayımlamayı ve bir abonelikten iletileri abone olmayı öğrenmek için, **Iletileri Yayımla ve abone ol** bölümünde aşağıdaki hızlı başlangıçlardan birine bakın. 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)

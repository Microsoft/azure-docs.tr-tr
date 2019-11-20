---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "67188864"
---
Varsayılan olarak, bir Mobile Apps arka uçtaki API 'Ler anonim olarak çağrılabilir. Daha sonra, erişimi yalnızca kimliği doğrulanmış istemcilerle kısıtlamanız gerekir.  

* **Node. js arka ucu (Azure Portal aracılığıyla)** :  

    Mobile Apps ayarlarınızda, **kolay tablolar** ' a tıklayın ve tablonuzu seçin. **Izinleri Değiştir**' e tıklayın, yalnızca tüm Izinler için **doğrulanmış erişim** ' i seçin ve **Kaydet**' e tıklayın.
* **.Net arka ucu (C#)** :  

    Sunucu projesinde, **denetleyiciler** > **TodoItemController.cs**' a gidin. Özniteliğini aşağıdaki gibi TodoItemController sınıfına ekleyin. `[Authorize]` Erişimi yalnızca belirli yöntemlerle kısıtlamak için, bu özniteliği sınıfı yerine yalnızca bu yöntemlere de uygulayabilirsiniz. Sunucu projesini yeniden yayımlayın.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node. js arka ucu (node. js kodu aracılığıyla)** :  

    Tablo erişimi için kimlik doğrulaması gerektirmek için Node. js sunucu betiğine aşağıdaki satırı ekleyin:

        table.access = 'authenticated';

    Daha ayrıntılı bilgi için bkz [. nasıl yapılır: Tablolara](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)erişim için kimlik doğrulaması gerektir. Hızlı başlangıç kodu projesini sitenizden nasıl indirileceği hakkında bilgi edinmek için bkz [. nasıl yapılır: Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)kullanarak Node. js arka uç hızlı başlangıç kodu projesini indirin.

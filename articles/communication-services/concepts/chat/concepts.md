---
title: Azure Iletişim hizmetlerinde sohbet kavramları
titleSuffix: An Azure Communication Services concept document
description: Iletişim Hizmetleri sohbet kavramları hakkında bilgi edinin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 292f430a1b08d59efdf05405437b3d1aa49ea2b7
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168611"
---
# <a name="chat-concepts"></a>Sohbet kavramları 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Iletişim Hizmetleri sohbeti SDK 'Ları, uygulamalarınıza gerçek zamanlı metin sohbeti eklemek için kullanılabilir. Bu sayfa, önemli sohbet kavramlarını ve yeteneklerini özetler.    

Belirli SDK dilleri ve özellikleri hakkında daha fazla bilgi için bkz. [Iletişim Hizmetleri sohbet SDK 'Sına genel bakış](./sdk-features.md) .  

## <a name="chat-overview"></a>Sohbete genel bakış    

Sohbet **iş parçacıkları** içinde sohbet görüşmeleri gerçekleşir. Sohbet iş parçacıkları aşağıdaki özelliklere sahiptir:

- Sohbet iş parçacığı tarafından benzersiz şekilde tanımlanır `ChatThreadId` . 
- Sohbet iş parçacıkları bir veya daha fazla kullanıcıya ileti gönderebilen katılımcılar olabilir. 
- Bir Kullanıcı bir veya daha fazla sohbet iş parçacığının parçası olabilir. 
- Yalnızca iş parçacığı katılımcılarının belirli bir sohbet iş parçacığına erişimi vardır ve yalnızca sohbet iş parçacığı işlemleri gerçekleştirebilir. Bu işlemler, ileti gönderme ve alma, katılımcı ekleme ve katılımcıları kaldırma işlemlerini içerir. 
- Kullanıcılar, oluşturdukları tüm sohbet iş parçacıklarıyla otomatik olarak katılımcı olarak eklenir.

### <a name="user-access"></a>Kullanıcı erişimi
Genellikle iş parçacığı Oluşturucu ve katılımcılarının iş parçacığına aynı düzeyde erişimi vardır ve bunu silme de dahil olmak üzere SDK 'da bulunan tüm ilgili işlemleri yürütebilirler. Katılımcılar diğer katılımcılar tarafından gönderilen iletilere yazma erişimine sahip değildir. Bu, yalnızca ileti göndericisinin gönderdikleri iletileri güncelleştirebileceği veya silebileceği anlamına gelir. Başka bir katılımcı bunu yapmayı denediğinde bir hata alırlar. 

Bir kullanıcı kümesi için sohbet özelliklerine erişimi sınırlandırmak istiyorsanız, erişimi güvenilir hizmetinizin bir parçası olarak yapılandırabilirsiniz. Güvenilir hizmetiniz, sohbet katılımcılarının kimlik doğrulamasını ve yetkilendirmesini düzenleyen hizmettir. Bunu aşağıda daha ayrıntılı bir şekilde araştıracağız.  

### <a name="chat-data"></a>Sohbet verileri 
İletişim Hizmetleri, sohbet geçmişini açıkça silinene kadar depolar. Sohbet iş parçacığı katılımcıları, `ListMessages` belirli bir iş parçacığının ileti geçmişini görüntülemek için kullanabilir. Bir sohbet iş parçacığından kaldırılan kullanıcılar önceki ileti geçmişini görüntüleyebilecektir, ancak bu sohbet iş parçacığının bir parçası olarak yeni iletiler gönderemezler veya alamaz. Katılımcıları olmayan tamamen boşta iş parçacığı 30 gün sonra otomatik olarak silinir. Iletişim Hizmetleri tarafından depolanmakta olan veriler hakkında daha fazla bilgi edinmek için [Gizlilik](../privacy.md)bölümüne bakın.  

### <a name="service-limits"></a>Hizmet sınırlamaları  
- Sohbet iş parçacığında izin verilen en fazla katılımcı sayısı 250 ' dir.   
- İzin verilen en büyük ileti boyutu yaklaşık 28 KB 'dir.  
- 20 ' den fazla katılımcı içeren sohbet iş parçacıkları için okundu bilgisi ve yazma göstergesi özellikleri desteklenmez.    

## <a name="chat-architecture"></a>Sohbet mimarisi    

Sohbet mimarisinin iki temel bölümü vardır: 1) Güvenilen hizmet ve 2) Istemci uygulaması.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Iletişim hizmetlerinin sohbet mimarisini gösteren diyagram."::: 

 - **Güvenilen hizmet:** Bir sohbet oturumunu doğru şekilde yönetmek için, kaynak Bağlantı dizenizi kullanarak Iletişim hizmetlerine bağlanmanıza yardımcı olan bir hizmete ihtiyacınız vardır. Bu hizmet, sohbet iş parçacıkları oluşturmaktan, katılımcıları ekleyip kaldırarak ve kullanıcılara erişim belirteçleri yayınmaktan sorumludur. Erişim belirteçleri hakkında daha fazla bilgi [erişim belirteçlerimiz](../../quickstarts/access-tokens.md) hızlı başlangıç bölümünde bulunabilir.  
 - **İstemci uygulaması:**  İstemci uygulaması, güvenilen hizmetinize bağlanır ve kullanıcılar tarafından Iletişim hizmetlerine doğrudan bağlanmak için kullanılan erişim belirteçlerini alır. Güvenilir hizmetiniz sohbet iş parçacığını oluşturup kullanıcıları katılımcı olarak eklediklerinde, bu kişiler istemci uygulamasını kullanarak sohbet iş parçacığına bağlanabilir ve ileti gönderebilir. İstemci uygulamanızda, diğer katılımcılar tarafından yapılan ileti & iş parçacığı güncelleştirmelerine abone olmak için, aşağıda tartışacak gerçek zaman bildirimleri özelliğini kullanın.
    
        
## <a name="message-types"></a>İleti türleri    

İleti geçmişinin bir parçası olarak, sohbet Kullanıcı tarafından oluşturulan iletileri ve sistem tarafından oluşturulan iletileri paylaşır. Sistem iletileri, bir sohbet iş parçacığı güncelleştirildiği zaman oluşturulur ve bir katılımcının ne zaman eklendiğini ya da kaldırıldığını veya sohbet iş parçacığı konusunun ne zaman güncelleştirildiğini belirlemesine yardımcı olabilir. `List Messages`Ya da `Get Messages` bir sohbet iş parçacığını çağırdığınızda, sonuç kronolojik sırada her iki tür iletiyi de içerecektir.

Kullanıcı tarafından oluşturulan iletiler için, `SendMessageOptions` sohbet iş parçacığına ileti gönderilirken ileti türü ayarlanabilir. Değer sağlanmazsa, Iletişim Hizmetleri varsayılan olarak `text` yazacaktır. Bu değeri ayarlamak, HTML gönderilirken önemlidir. `html`Belirtildiğinde, Iletişim Hizmetleri, istemci cihazlarda güvenli bir şekilde işlenmesini sağlamak için içeriği temizler.
 - `text`: Bir sohbet iş parçacığının parçası olarak bir kullanıcı tarafından oluşturulan ve gönderilen düz metin iletisi. 
 - `html`: Sohbet iş parçacığının parçası olarak bir kullanıcı tarafından oluşturulan ve gönderilen HTML kullanan biçimli bir ileti. 

Sistem iletilerinin türleri: 
 - `participantAdded`: Sohbet iş parçacığına bir veya daha fazla katılımcı eklendiğini belirten sistem iletisi.
 - `participantRemoved`: Bir katılımcının sohbet iş parçacığından kaldırıldığını belirten sistem iletisi.
 - `topicUpdated`: İş parçacığı konusunun güncelleştirildiğini belirten sistem iletisi.

## <a name="real-time-notifications"></a>Gerçek zamanlı bildirimler  

Bazı SDK 'lar (JavaScript sohbet SDK 'Sı gibi) gerçek zamanlı bildirimleri destekler. Bu özellik, istemcilerin API 'Leri yoklamaya gerek kalmadan, gerçek zamanlı güncelleştirmeler ve gelen iletiler için bir sohbet iş parçacığına Iletişim Hizmetleri 'ni dinlemesine olanak tanır. İstemci uygulaması, aşağıdaki olaylara abone olabilir:
 - `chatMessageReceived` -bir katılımcı tarafından sohbet iş parçacığına yeni bir ileti gönderildiğinde.
 - `chatMessageEdited` -bir sohbet iş parçacığında bir ileti düzenlendiğinde. 
 - `chatMessageDeleted` -bir sohbet iş parçacığında bir ileti silindiğinde.   
 - `typingIndicatorReceived` -başka bir katılımcı sohbet iş parçacığına bir yazma göstergesi gönderdiğinde.    
 - `readReceiptReceived` -başka bir katılımcı okudukları bir ileti için okundu bilgisi gönderdiğinde.  
 - `chatThreadCreated` -bir Iletişim Hizmetleri kullanıcısı tarafından bir sohbet iş parçacığı oluşturulduğunda.    
 - `chatThreadDeleted` -bir sohbet iş parçacığı bir Iletişim Hizmetleri kullanıcısı tarafından silindiğinde.    
 - `chatThreadPropertiesUpdated` -sohbet iş parçacığı özellikleri güncelleniyorsa; Şu anda yalnızca iş parçacığı için konu güncelleştirmesi destekleniyor. 
 - `participantsAdded` -bir kullanıcı sohbet iş parçacığı katılımcısı olarak eklendiğinde.     
 - `participantsRemoved` -Mevcut bir katılımcı sohbet iş parçacığından kaldırıldığında.

Gerçek zamanlı bildirimler, kullanıcılarınız için gerçek zamanlı bir sohbet deneyimi sağlamak üzere kullanılabilir. Kullanıcılarınızın kaçırıldığı sırada unutulan iletiler için anında iletme bildirimleri göndermek için, Iletişim Hizmetleri, özel uygulama bildirim hizmetinize takılan, sohbet ile ilgili olayları (POST işlemi) yayımlamak için Azure Event Grid ile tümleştirilir. Daha ayrıntılı bilgi için bkz. [sunucu olayları](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Akıllı, AI destekli sohbet deneyimleri oluşturun   

Azure bilişsel [API 'Leri](../../../cognitive-services/index.yml) sohbet SDK ile birlikte kullanarak şu gibi kullanım durumları oluşturabilirsiniz:

- Kullanıcıların farklı dillerde birbirleriyle sohbet kurmasını sağlar.  
- Destek aracısının, bir müşteriden gelen bir iletiyi olumsuz bir şekilde algılayarak bilet önceliklendirmesine yardımcı olun. 
- Anahtar algılama ve varlık tanıma için gelen iletileri çözümleyin ve ileti içeriğine göre uygulamanızdaki kullanıcıya ilgili bilgileri yazın.

Bunu başarmanın bir yolu, güvenilen hizmetinizin sohbet iş parçacığı katılımcısı olarak davranmasından biridir. Dil çevirisini etkinleştirmek istediğinizi varsayalım. Bu hizmet, diğer katılımcılar tarafından düzenlenmekte olan iletilere dinleme yapmaktan sorumlu olacaktır. [1], içeriği istenen dile dönüştürmek için bilişsel API 'Ler çağrılıyor [2, 3] ve çevrilmiş sonucu [4] sohbet iş parçacığında ileti olarak gönderiyor.

Bu şekilde, ileti geçmişi hem özgün hem de çevrilmiş iletileri içerir. İstemci uygulamasında, orijinal veya çevrilmiş iletiyi göstermek için Logic ekleyebilirsiniz. Bilişsel API 'Leri kullanarak metni farklı dillere çevirme hakkında bilgi edinmek için [Bu hızlı](../../../cognitive-services/translator/quickstart-translator.md) başlangıca bakın. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Iletişim hizmetleriyle etkileşime geçen bilişsel hizmetler 'i gösteren diyagram."::: 

## <a name="next-steps"></a>Sonraki adımlar   

> [!div class="nextstepaction"] 
> [Sohbetle çalışmaya başlama](../../quickstarts/chat/get-started.md)    

Aşağıdaki belgeler sizin için ilginç olabilir:  
- [Sohbet SDK 'sını](sdk-features.md) öğrenmeye

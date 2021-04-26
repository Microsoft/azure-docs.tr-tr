---
title: include dosyası
description: include dosyası
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d2c309340155bc626d4da94d74aee9be51bde510
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90606301"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Azure portalında bir ad alanı oluşturma
Azure'da Service Bus mesajlaşma varlıklarını kullanmaya başlamak için öncelikle Azure'da benzersiz olan bir ad alanı oluşturmanız gerekir. Ad alanı, uygulamanızda bulunan Service Bus kaynaklarını adreslemek için içeriğin kapsamını belirleyen bir kapsayıcı sunar.

Ad alanı oluşturmak için:

1. [Azure portalda](https://portal.azure.com) oturum açma
2. Portalın sol gezinti bölmesinde **+ kaynak oluştur**' u seçin, **tümleştirme**' i seçin ve ardından **Service Bus**' yi seçin.

    ![Kaynak > tümleştirme oluşturma-> Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. **Ad alanı oluştur** iletişim kutusunda aşağıdaki adımları uygulayın: 
    1. **Ad alanı için bir ad** girin. Adın kullanılabilirliği sistem tarafından hemen kontrol edilir. Ad alanlarını adlandırma kurallarının bir listesi için bkz. [Create Namespace REST API](/rest/api/servicebus/create-namespace).
    2. Ad alanı için fiyatlandırma katmanını (temel, standart veya Premium) seçin. [Konuları ve abonelikleri](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)kullanmak Istiyorsanız, standart veya Premium ' u seçin. Konular/abonelikler, Temel fiyatlandırma katmanında desteklenmez.
    3. **Premium** fiyatlandırma katmanını seçtiyseniz şu adımları izleyin: 
        1. **Mesajlaşma birimi** sayısını belirtin. Premium katmanı, her iş yükünün yalıtımda çalışması için CPU ve bellek düzeyinde kaynak yalıtımı sağlar. Bu kaynak kapsayıcısı mesajlaşma birimi olarak adlandırılır. Premium ad alanında en az bir mesajlaşma birimi vardır. Her Service Bus Premium ad alanı için 1, 2 veya 4 mesajlaşma birimi seçebilirsiniz. Daha fazla bilgi için bkz. [Service Bus Premium mesajlaşma](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Ad alanı **bölgesini gereksiz** yapmak isteyip istemediğinizi belirtin. Bölge artıklığı, çoğaltmaları hiçbir ek ücret ödemeden bir bölgedeki kullanılabilirlik bölgelerine dağıtarak gelişmiş kullanılabilirlik sağlar. Daha fazla bilgi için bkz. [Azure 'Da kullanılabilirlik alanları](../articles/availability-zones/az-overview.md).
    4. **Abonelik** için, ad alanını oluşturmak Için bir Azure aboneliği seçin.
    5. **Kaynak grubu** için, ad alanının etkin olacağı mevcut bir kaynak grubunu seçin veya yeni bir tane oluşturun.      
    6. **Konum** için, ad alanınızı barındırılacak bölgeyi seçin.
    7. **Oluştur**’u seçin. Artık sistem ad alanınızı oluşturur ve kullanıma açar. Sistem, hesabınıza yönelik kaynakları sağlarken birkaç dakika beklemeniz gerekebilir.
   
        ![Ad alanı oluşturma](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Service Bus ad alanının başarıyla dağıtıldığını doğrulayın. Bildirimleri görmek için araç çubuğundaki **zil simgesini (Uyarılar)** seçin. Görüntüde gösterildiği gibi bildirimde **kaynak grubunun adını** seçin. Service Bus ad alanını içeren kaynak grubunu görürsünüz.

    ![Dağıtım uyarısı](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Kaynak grubunuzun **kaynak grubu** sayfasında, **Service Bus ad** alanınızı seçin. 

    ![Kaynak grubu sayfası-Service Bus ad alanınızı seçin](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Service Bus ad alanınız için ana sayfayı görürsünüz. 

    ![Service Bus ad alanınız için ana sayfa](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Bağlantı dizesini alma 
Yeni bir ad alanı oluşturulduğunda, her biri ad alanının tüm yönleri üzerinde tam denetim veren ilişkili bir çift birincil ve ikincil anahtara sahip bir ilk Paylaşılan Erişim İmzası (SAS) kuralı otomatik olarak oluşturulur. Düzenli Gönderenler ve alıcılar için daha kısıtlanmış haklara sahip kurallar oluşturma hakkında bilgi için bkz. [Service Bus kimlik doğrulaması ve yetkilendirme](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) . Ad alanınız için birincil ve ikincil anahtarları kopyalamak için aşağıdaki adımları izleyin: 

1. **Tüm kaynaklar**’a ve sonra yeni oluşturulan ad alanı adına tıklayın.
2. Ad alanı penceresinde **Paylaşılan erişim ilkeleri**'ne tıklayın.
3. **Paylaşılan erişim ilkeleri** ekranında **RootManageSharedAccessKey** seçeneğine tıklayın.
   
    ![Ekran görüntüsü, ilke vurgulanmış şekilde paylaşılan erişim ilkeleri penceresini gösterir.](./media/service-bus-create-namespace-portal/connection-info.png)
4. **İlke: RootManageSharedAccessKey** penceresinde, **birincil bağlantı dizesi**' nin yanındaki Kopyala düğmesine tıklayın ve bağlantı dizesini daha sonra kullanmak üzere panonuza kopyalayın. Bu değeri Not Defteri veya başka bir geçici konuma yapıştırın.
   
    ![Ekran görüntüsünde, anahtarlar ve bağlantı dizeleri içeren RootManageSharedAccessKey adlı bir s İlkesi gösterilir.](./media/service-bus-create-namespace-portal/connection-string.png)
5. Önceki adımı tekrarlayarak **birincil anahtar** değerini kopyalayarak ve daha sonra kullanmak üzere geçici bir konuma yapıştırmayı.

<!--Image references-->


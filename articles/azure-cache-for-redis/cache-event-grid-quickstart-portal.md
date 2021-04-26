---
title: 'Hızlı başlangıç: Azure portal ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme'
description: Redsıs olayları için Azure önbelleğine abone olmak, olayları bir Web kancasına göndermek ve bir Web uygulamasındaki olayları işlemek için Azure Event Grid kullanın
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: e021f386f255f1cef61e28cbd4fd6116fc2aa727
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529306"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Hızlı başlangıç: Azure portal ile Redsıs olayları için Azure önbelleğini Web uç noktasına yönlendirme

Azure Event Grid, bulut için bir olay oluşturma hizmetidir. Bu hızlı başlangıçta, Redsıs örneği için bir Azure önbelleği oluşturmak, bu örneğe yönelik olaylara abone olmak, bir olayı tetiklemek ve sonuçları görüntülemek için Azure portal kullanacaksınız. Normalde olayları, olay verilerini işleyen ve eylemler gerçekleştiren bir uç noktaya gönderirsiniz. Ancak, bu hızlı başlangıcı basitleştirmek için, iletileri toplayacak ve görüntüleyecek bir Web uygulamasına olayları göndereceğiz. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

İşiniz bittiğinde, olay verilerinin Web uygulamasına gönderildiğini görürsünüz.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="JSON biçiminde Azure Event Grid Görüntüleyici ölçeklendiriliyor.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Redsıs Cache örneği için Azure önbelleği oluşturma 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>İleti uç noktası oluşturma

Önbellek örneği için olaylara abone olmadan önce olay iletisi için uç noktayı oluşturalım. Normalde, olay verileri temelinde uç nokta eylemleri gerçekleştirir. Bu hızlı başlangıcı basitleştirmek için, olay iletilerini görüntüleyen [önceden oluşturulmuş bir Web uygulaması](https://github.com/Azure-Samples/azure-event-grid-viewer) dağıtırsınız. Dağıtılan çözüm bir App Service planı, App Service web uygulaması ve GitHub'dan kaynak kod içerir.

1. Çözümü aboneliğinize dağıtmak için GitHub BENIOKU dosyasında **Azure 'A dağıt** ' ı seçin. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Azure 'a dağıtın düğmesi.":::

2. **Özel dağıtım** sayfasında, aşağıdaki adımları uygulayın: 
    1. **Kaynak grubu** için, önbellek örneğini oluştururken oluşturduğunuz kaynak grubunu seçin. Kaynak grubunu silerek öğreticiye tamamladıktan sonra temizlemeniz daha kolay olacaktır.  
    2. **Site adı** için, Web uygulaması için bir ad girin.
    3. **Barındırma planı adı** için, Web uygulamasını barındırmak üzere kullanılacak App Service planı için bir ad girin.
    4. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum** onay kutusunu seçin. 
    5. **Satın al**'ı seçin. 
    
    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonelik** | Açılır ve aboneliğinizi seçin. | Bu Web uygulamasının oluşturulacağı abonelik. | 
    | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
    | **Site Adı** | Web uygulamanız için bir ad girin. | Bu değer boş olamaz. | 
    | **Barındırma planı adı** | Web uygulamasını barındırmak için kullanılacak App Service planı için bir ad girin. | Bu değer boş olamaz. | 

1. Portalda Uyarılar ' ı (zil simgesi) seçin ve **kaynak grubuna git**' i seçin. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure portal dağıtım bildirimi.":::

4. **Kaynak grubu** sayfasında, kaynak listesinde, oluşturduğunuz Web uygulamasını seçin. Ayrıca, bu listede App Service planı ve önbellek örneğini görürsünüz. 

5. Web uygulamanızın **App Service** sayfasında, Web sitesine gitmek için URL 'yi seçin. URL şu biçimde olmalıdır: `https://<your-site-name>.azurewebsites.net` .

6. Siteyi görtığınızdan, ancak henüz hiç bir olay gönderilmemiştir.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Event Grid Görüntüleyici sitesi boş.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Redsıs örneği için Azure önbelleğine abone olma

Bu adımda, hangi olayları izlemek istediğinizi ve olayların nereye gönderileceğini Event Grid söylemek için bir konuya abone olacaksınız.

1. Portalda, daha önce oluşturduğunuz önbellek örneğine gidin. 
2. **Redsıs Için Azure önbelleği** sayfasında, sol taraftaki menüden **Olaylar** ' ı seçin. 
3. **Web kancası** seçin. Uç nokta için bir Web kancası kullanarak Görüntüleyici uygulamanıza olay gönderiyorsunuz. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure portal Olaylar sayfası.":::

4. **Olay aboneliği oluştur** sayfasında, aşağıdakileri girin: 

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Ad** | Olay aboneliği için bir ad girin. | Değer 3 ila 64 karakter uzunluğunda olmalıdır. Yalnızca harf, rakam ve tire içerebilir. | 
    | **Olay türleri** | ' I seçin ve Hedefinizdeki hangi olay türlerini almak istediğinizi seçin. Bu hızlı başlangıçta, önbellek örneğimizi ölçeklendireceğiz. | Düzeltme eki uygulama, ölçeklendirme, içe aktarma ve dışarı aktarma seçenekleri kullanılabilir seçeneklerdir. | 
    | **Uç nokta türü** | **Web kancası** seçin. | Olaylarınızı alacak olay işleyicisi. | 
    | **Uç Nokta** | **Uç nokta seç**' e tıklayın ve Web uygulamanızın URL 'sini girin ve `api/updates` giriş sayfası URL 'sine ekleyin (örneğin: `https://cache.azurewebsites.net/api/updates` ) ve ardından **Seçimi Onayla**' yı seçin. | Bu, daha önce oluşturduğunuz Web uygulamanızın URL 'sidir. | 

5. Şimdi olay **aboneliği oluştur** sayfasında, olay aboneliğini oluşturmak için **Oluştur** ' u seçin. 

6. Web uygulamanızı yeniden görüntüleyin ve buna bir abonelik doğrulama olayının gönderildiğine dikkat edin. Göz simgesini seçerek olay verilerini genişletin. Uç noktanın olay verilerini almak istediğini doğrulayabilmesi için Event Grid doğrulama olayını gönderir. Web uygulaması aboneliği doğrulamak için kod içerir.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Görüntüleyici.":::

## <a name="send-an-event-to-your-endpoint"></a>Uç noktanıza olay gönderme

Şimdi, Event Grid’in iletiyi uç noktanıza nasıl dağıttığını görmek için bir olay tetikleyelim. Redsıs örneği için Azure önbelleğinizi ölçeklendireceğiz.

1. Azure portal, Redsıs örneği için Azure önbelleğinize gidin ve Sol menüdeki **Ölçek** ' i seçin.

1. **Ölçek** sayfasından istediğiniz fiyatlandırma katmanını seçin ve **Seç**' e tıklayın. 

    Aşağıdaki kısıtlamalara sahip farklı bir fiyatlandırma katmanına ölçeklendirebilirsiniz:
    
    * Daha yüksek bir fiyatlandırma katmanından daha düşük bir fiyatlandırma katmanına ölçeklendiremez.
      * **Premium** önbellekten bir **Standart** veya **temel** önbelleğe ölçeklendiremez.
      * **Standart** bir önbellekten, **temel** bir önbellekten ölçeklendiremez.
    * **Temel** bir önbellekten **Standart** bir önbelleğe ölçeklendirebilirsiniz, ancak aynı anda boyutu değiştiremezsiniz. Farklı bir boyuta ihtiyacınız varsa, sonraki ölçekleme işlemini istenen boyuta getirebilirsiniz.
    * **Temel** önbellekten doğrudan **Premium** önbelleğe ölçeklendiremez. İlk olarak, bir ölçeklendirme işleminde **temel** olarak **Standart** ve sonra da sonraki ölçekleme işleminde **Standart** 'den **Premium** 'a ölçeklendirin.
    * Daha büyük bir boyuttan **C0 (250 MB)** boyutuna kadar ölçeklendiremez.
 
    Önbellek yeni fiyatlandırma katmanına ölçeklendirilirken, **redsıs dikey penceresinde Azure önbelleğinde** **ölçekleme** durumu görüntülenir. Ölçeklendirme tamamlandığında, durum **ölçeklendirmeden** **çalışır** olarak değişir.

1. Olayı tetiklediniz ve Event Grid, iletiyi abone olurken yapılandırdığınız uç noktaya gönderdi. İleti JSON biçimindedir ve bir veya daha fazla olaya sahip bir dizi içerir. Aşağıdaki örnekte, JSON iletisi bir olay içeren bir dizi içerir. Web uygulamanızı görüntüleyin ve bir **Scalingcompleted** olayının alındığını unutmayın. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="JSON biçiminde Azure Event Grid Görüntüleyici ölçeklendiriliyor.":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu olayla çalışmaya devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Aksi takdirde, bu hızlı başlangıçta oluşturduğunuz kaynakları silin.

Kaynak grubunu seçin ve **Kaynak grubunu sil** seçeneğini belirleyin.

## <a name="next-steps"></a>Sonraki adımlar

Özel konu ve olay abonelikleri oluşturma işlemini öğrendiğinize göre artık Event Grid’in size nasıl yardımcı olabileceği konusunda daha fazla bilgi edinebilirsiniz:

- [Redsıs olayları için Azure önbelleğine yeniden davranıma](cache-event-grid.md)
- [Event Grid Hakkında](../event-grid/overview.md)

---
title: Azure Event Grid ölçümleri ve etkinlik günlüğü işlemleri için uyarı ayarlama
description: Bu makalede, Azure Event Grid ölçümleri ve etkinlik günlüğü işlemlerinde uyarıların nasıl oluşturulacağı açıklanır.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48cb402e31435cb3e9390e8aeb461fcc5f90702f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572016"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Azure Event Grid ölçümleri ve etkinlik günlüklerinde uyarı ayarlama
Bu makalede, Azure Event Grid ölçümleri ve etkinlik günlüğü işlemlerinde uyarıların nasıl oluşturulacağı açıklanır. Azure Event Grid kaynaklar (konular ve etki alanları) için hem yayımlama hem de teslim ölçümleri üzerinde uyarı oluşturabilirsiniz. Sistem konuları için [ **ölçümler** sayfasını kullanarak uyarılar oluşturun](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Kullanılmayan olaylarda uyarı oluşturma
Aşağıdaki yordamda, özel bir konu için **kullanılmayan olaylar** ölçümünde uyarı oluşturma işlemi gösterilmektedir. Bu örnekte, bir konu için atılacak olay sayısı 10 ' dan fazla kaldığında Azure Kaynak grubu sahibine bir e-posta gönderilir. 

1. Konunun **Event Grid konu** sayfasında, sol taraftaki menüden **Uyarılar** ' ı seçin ve ardından **+ Yeni uyarı kuralı**' nı seçin. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Uyarılar sayfası-yeni uyarı kuralı düğmesi":::
2. **Uyarı kuralı oluştur** sayfasında, konunun kaynak için seçildiğini doğrulayın. Sonra **Koşul Seç**' e tıklayın. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Uyarılar sayfası-koşul Seç":::    
3. **Sinyal mantığını Yapılandır** sayfasında, aşağıdaki adımları izleyin:
    1. Bir ölçüm veya etkinlik günlüğü girişi seçin. Bu örnekte, **atılacak olaylar** seçilidir. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Kullanılmayan olayları seçin":::        
    2. Boyutları seçin (isteğe bağlı). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Sinyal mantığını yapılandırma":::        

        > [!NOTE]
        > **+** Olayları filtrelemek için bir olay abonelik adı belirtmek üzere **eventsubscriptionname** düğmesini seçebilirsiniz. 
    3. Aşağı kaydırın. **Uyarı mantığı** bölümünde bir **operatör**, **toplama türü** seçin ve bir **eşik değeri** girin ve **bitti**' yi seçin. Bu örnekte, toplam ölü olay sayısı 10 ' dan büyük olduğunda bir uyarı tetiklenir. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Uyarı mantığı":::                
4. **Uyarı kuralı oluştur** sayfasında, **eylem grubu seç**' e tıklayın.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Eylem grubu Seç düğmesi":::
5. Yeni bir eylem grubu oluşturmak için araç çubuğunda **eylem grubu oluştur** ' u seçin. Ayrıca var olan bir eylem grubunu seçebilirsiniz.        
6. **Eylem grubu Ekle** sayfasında, şu adımları izleyin:
    1. **Eylem grubu için bir ad** girin.
    1. Eylem grubu için **kısa bir ad** girin.
    1. Eylem grubunu oluşturmak istediğiniz **Azure aboneliğini** seçin.
    1. Eylem grubunu oluşturmak istediğiniz **Azure kaynak grubunu** seçin.
    1. **Eylem için bir ad** girin. 
    1. **Eylem türünü** seçin. Bu örnekte, **e-posta Azure Resource Manager rolü** , özellikle **sahipler** rolü seçilidir. 
    1. Sayfayı kapatmak için **Tamam ' ı** seçin. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Eylem grubu Ekle sayfası":::                   
7. **Uyarı kuralı oluştur** sayfasına dönün, uyarı kuralı için bir ad girin ve ardından **Uyarı kuralı oluştur**' u seçin.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Uyarı kuralının adı":::  
8. Artık, konunun **Uyarılar** sayfasında uyarı kurallarını yönetmek için henüz uyarı yoksa bir bağlantı görürsünüz. Uyarılar varsa, araç çubuğunda **Yönetici uyarı kuralları** ' nı seçin.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Uyarıları yönetme":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Diğer ölçümler veya etkinlik günlüğü işlemlerinde uyarı oluşturma
Önceki bölümde, kullanılmayan olaylarda uyarı oluşturma konusu gösterilmektedir. Diğer ölçümler veya etkinlik günlüğü işlemlerinde uyarı oluşturma adımları benzerdir. 

Örneğin, bir teslim hatası olayında uyarı oluşturmak için **sinyal mantığını Yapılandır** sayfasında **teslim başarısız olaylar** ' ı seçin. 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Teslim başarısız olayları seçin":::


## <a name="create-alerts-using-the-metrics-page"></a>Ölçümler sayfasını kullanarak uyarı oluşturma
Ayrıca **ölçümler** sayfasını kullanarak da uyarılar oluşturabilirsiniz. Adımlar benzerdir. Sistem konuları için, **Uyarılar** sayfası kullanılabilir olmadığından yalnızca uyarılar oluşturmak üzere **ölçümler** sayfasını kullanabilirsiniz. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Ölçümler sayfası-uyarı oluştur düğmesi":::   
    

> [!NOTE]
> Bu makalede, bir uyarı oluşturmak için kullanabileceğiniz tüm farklı adımlar ve birleşimler ele alınmaktadır. Uyarılara genel bakış için bkz. [uyarılara genel bakış](../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Sonraki adımlar

* Olay teslimi ve yeniden denemeler hakkında daha fazla bilgi için [Event Grid ileti teslimi ve yeniden deneyin](delivery-and-retry.md).
* Event Grid’e giriş için bkz. [Event Grid hakkında](overview.md).
* Event Grid kullanmaya hızlıca başlamak için bkz. [özel olayları oluşturma ve Azure Event Grid ile yönlendirme](custom-event-quickstart.md).

---
title: Azure DevTest Labs 'de Labs için etkinlik günlüğü uyarıları oluşturma
description: Bu makalede, Azure DevTest Labs ' de laboratuvar için etkinlik günlüğü uyarıları oluşturma adımları sağlanmaktadır.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588764"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Azure DevTest Labs 'de Labs için etkinlik günlüğü uyarıları oluşturma
Bu makalede, Azure DevTest Labs (örneğin, bir VM oluşturulduğunda veya bir VM silindiğinde) Labs için etkinlik günlüğü uyarılarının nasıl oluşturulacağı açıklanmaktadır.

## <a name="create-alerts"></a>Uyarı oluşturma
Bu örnekte, abonelik sahiplerine e-posta gönderen bir eylem ile laboratuvardaki tüm yönetim işlemleri için bir uyarı oluşturacaksınız. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure portal arama çubuğunda **izleyici** yazın ve ardından sonuçlar listesinden **izleyici** ' yi seçin. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Izleyici ara":::        
1. Sol menüden **Uyarılar** ' ı seçin ve ardından araç çubuğunda **Yeni uyarı kuralı** ' nı seçin. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Uyarılar sayfası":::    
1. **Uyarı kuralı oluştur** sayfasında **Kaynak Seç**' e tıklayın. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Uyarı için kaynak seçin":::        
1. **Kaynak türüne göre filtre** Için **DevTest Labs** ' i seçin, listeden laboratuvarınızı seçin ve **bitti**' yi seçin.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Kaynak olarak laboratuvarınızı seçin":::
1. **Uyarı kuralı oluştur** sayfasında, **Koşul Seç**' e tıklayın. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Koşul bağlantısını seçin":::    
1. **Sinyal mantığını Yapılandır** sayfasında, DevTest Labs tarafından desteklenen bir sinyal seçin. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Sinyal Seç":::
1. **Olay düzeyine** göre filtrele (ayrıntılı, bilgilendirici, uyarı, hata, kritik, tümü), **durum** (başarısız, başlatılmış, başarılı) ve olayı **başlatan** . 
1. Koşulu yapılandırmayı tamamladıktan sonra **bitti** ' yi seçin. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Sinyal mantığını yapılandırma-bitti":::
1. Kapsam (laboratuvar) ve uyarının koşulu için belirttiniz. Şimdi, koşul karşılandığında çalıştırılacak eylemleri içeren bir eylem grubu belirtmeniz gerekir. **Uyarı kuralı oluştur** sayfasında geri dönün, **eylem grubu seç**' i seçin. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Eylem grubu bağlantısı Seç":::
1. Araç çubuğunda **eylem grubu oluştur** bağlantısını seçin. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Eylem grubu bağlantısı oluştur":::
1. **Eylem grubu Ekle** sayfasında, şu adımları izleyin:
    1. Eylem grubu için bir **ad** girin.
    1. Eylem grubu için **kısa bir ad** girin. 
    1. Uyarının oluşturulmasını istediğiniz **kaynak grubunu** seçin. 
    1. **Eylem için bir ad** girin. 
    1. **Eylem türünü** seçin (Bu örnekte, **e-posta Azure Resource Manager rolü**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Eylem grubu Ekle sayfası":::
    1. **E-posta Azure Resource Manager rolü** sayfasında, rolünü seçin. Bu örnekte, **sahibidir**. Ardından **Tamam**'ı seçin. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Rol seçin":::            
    1. **Eylem grubu Ekle** sayfasında **Tamam ' ı** seçin. 
1. Şimdi **Uyarı kuralı oluştur** sayfasında, uyarı kuralı için bir ad girin ve ardından **Tamam**' ı seçin. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Uyarı kuralı oluştur-bitti":::

## <a name="view-alerts"></a>Uyarıları görüntüleme 
1. Tüm yönetim işlemlerine yönelik **uyarılarda** uyarılar görürsünüz (Bu örnekte). Uyarıların görünmesi biraz zaman alabilir. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Ekran yakalama, panodaki uyarıları görüntüler.":::
1. Sütunda sayı ' yı seçerseniz (örneğin, **Toplam uyarı**), yükseltilen uyarıları görürsünüz. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Tüm uyarılar":::
1. Bir uyarı seçerseniz, onunla ilgili ayrıntıları görürsünüz. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Uyarı ayrıntıları":::
1. Bu örnekte, aşağıdaki örnekte gösterildiği gibi içeriği içeren bir e-posta da alacaksınız: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Uyarı e-postası":::

## <a name="next-steps"></a>Sonraki adımlar
- Farklı eylem türlerini kullanarak eylem grupları oluşturma hakkında daha fazla bilgi için, bkz. [Azure Portal eylem grupları oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md).
- Etkinlik günlükleri hakkında daha fazla bilgi için bkz.  [Azure etkinlik günlüğü](../azure-monitor/essentials/activity-log.md).
- Etkinlik günlüklerinde uyarı ayarlama hakkında bilgi edinmek için bkz. [etkinlik günlüğündeki uyarılar](../azure-monitor/alerts/activity-log-alerts.md).


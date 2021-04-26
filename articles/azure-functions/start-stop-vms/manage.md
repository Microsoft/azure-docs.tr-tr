---
title: Başlat/Durdur VM 'Leri yönetme v2 (Önizleme)
description: Bu makalede, VM 'Leri Başlat/Durdur v2 (Önizleme) özelliği tarafından yönetilen Azure sanal makinelerinizin durumunun nasıl izleneceği ve diğer yönetim görevlerinin nasıl yapılacağı açıklanır.
services: azure-functions
ms.subservice: ''
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: 477e3fe51f05bb4323642f56233f1995e6394eec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112185"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>VM 'Leri Başlat/Durdur v2 'yi yönetme (Önizleme)

## <a name="azure-dashboard"></a>Azure panosu

VM 'leri Başlat/Durdur v2 (Önizleme), VM 'lerinize göre yönetim kapsamını ve en son işlemleri anlamanıza yardımcı olacak bir [Pano](../../azure-monitor/visualizations.md#azure-dashboards) içerir. Azure VM 'leriniz üzerinde gerçekleştirilen her bir işlemin durumunu doğrulamak için hızlı ve kolay bir yoldur. Her kutucukta görselleştirme bir günlük sorgusuna dayalıdır ve sorguyu görmek için döşemenin sağ köşesindeki **günlüklerde aç dikey penceresini** seçin. Bu, Azure portal [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) aracı açar ve buradan sorguyu değerlendirebilir ve özel [günlük uyarıları](../../azure-monitor/alerts/alerts-log.md), özel bir [çalışma kitabı](../../azure-monitor/visualize/workbooks-overview.md)vb. gibi gereksinimlerinizi destekleyecek şekilde değiştirebilirsiniz.

Panodaki her kutucuk, belirli bir görselleştirmede **Yenile** simgesine tıklanarak veya tam Pano yenilenerek, isteğe bağlı el ile yenileme seçeneği ile her saat yenilenir.

Günlük tabanlı bir Panoyla çalışma hakkında bilgi edinmek için aşağıdaki [öğreticiye](../../azure-monitor/visualize/tutorial-logs-dashboards.md)bakın.

## <a name="configure-email-notifications"></a>E-posta bildirimlerini yapılandırma

VM 'Leri Başlat/Durdur v2 (Önizleme) dağıtıldıktan sonra e-posta bildirimlerini değiştirmek için, dağıtım sırasında oluşturulan eylem grubunu değiştirebilirsiniz.

1. Azure portal, **İzle**' ye ve ardından **Uyarılar**' a gidin. **Işlemleri Yönet**' i seçin.

1. **Eylemleri Yönet** sayfasında, **StartStopV2_VM_Notication** adlı eylem grubunu seçin.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Eylem grupları sayfasının ekran görüntüsü.":::

1. **StartStopV2_VM_Notification** sayfasında, e-posta/SMS/Push/Voice Notification seçeneklerini değiştirebilirsiniz. Diğer eylemleri ekleyin veya var olan yapılandırmanızı bu eylem grubuna güncelleştirip **Tamam** ' a tıklayarak değişikliklerinizi kaydedin.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Güncelleştirilmiş örnek bir e-posta adresi gösteren e-posta/SMS/Push/ses sayfasının ekran görüntüsü.":::

    Eylem grupları hakkında daha fazla bilgi edinmek için bkz. [eylem grupları](../../azure-monitor/alerts/action-groups.md)

Aşağıdaki ekran görüntüsü, özellik sanal makineleri kapattığında gönderilen örnek bir e-postadır.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Özellik sanal makineleri kapattığında gönderilen örnek bir e-postanın ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

VM yönetimi sırasında sorunları işlemek için bkz. VM ['Leri Başlat/Durdur v2](troubleshoot.md) (Önizleme) sorunlarını giderme.
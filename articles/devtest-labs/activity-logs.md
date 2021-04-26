---
title: Etkinlik günlükleri Azure DevTest Labs | Microsoft Docs
description: Bu makalede Azure DevTest Labs için etkinlik günlüklerini görüntüleme adımları sunulmaktadır.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582777"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Azure DevTest Labs Labs için etkinlik günlüklerini görüntüleme 
Bir veya daha fazla Laboratuvarı oluşturduktan sonra, laboratuvarlarınızın nasıl ve ne zaman eriştiğini, değiştirildiğini ve yönetildiğini ve kim tarafından yapılacağını izlemek isteyeceksiniz. Azure DevTest Labs, bu işlemleri laboratuvarlara karşı sağlamak için Azure Izleyici 'yi özellikle **etkinlik günlükleri**'ni kullanır. 

Bu makalede, Azure DevTest Labs bir laboratuvardaki etkinlik günlüklerinin nasıl görüntüleneceği açıklanır.

## <a name="view-activity-log-for-a-lab"></a>Laboratuvar için etkinlik günlüğünü görüntüleme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin ve **DevOps** bölümünde **DevTest Labs** ' i seçin. DevOps bölümünde, **DevTest Labs** ' in yanındaki * (Star)  seçeneğini belirleyin. Bu eylem, bir sonraki sefer kolayca erişebilmek için **DevTest Labs** 'i sol gezinti menüsüne ekler. Ardından, sol gezinti menüsünde **DevTest Labs** ' i seçebilirsiniz.

    ![Tüm hizmetler-DevTest Labs seçin](./media/devtest-lab-create-lab/all-services-select.png)
1. Laboratuvarlar listesinden laboratuvarınızı seçin.
1. Laboratuvarın giriş sayfasında, sol taraftaki menüden **Konfigürasyonlar ve ilkeler** ' i seçin. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Sol menüdeki yapılandırma ve ilkeleri seçin":::
1. **Yapılandırma ve ilkeler** sayfasında, **Yönet**' ın altındaki sol menüde **etkinlik günlüğü** ' nü seçin. Laboratuvarda gerçekleştirilen işlemler için girişleri görmeniz gerekir. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Etkinlik günlüğü":::    
1. Ayrıntılarını görmek için bir olay seçin. **Özet** sayfasında, işlem adı, zaman damgası ve işlemi kimin gerçekleştirmediği gibi bilgileri görürsünüz. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="VM olayını durdur-Özet":::        
1. Daha fazla ayrıntı görmek için **JSON** sekmesine geçin. Aşağıdaki örnekte, sanal makinenin adını ve VM üzerinde gerçekleştirilen işlemi (durduruldu) görebilirsiniz.

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="VM olayını durdur-JSON":::           
1. Değişiklik geçmişini görmek için **değişiklik geçmişi (Önizleme)** sekmesine geçin. Aşağıdaki örnekte, VM 'de yapılan değişikliği görürsünüz. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="VM olayını durdur-değişiklik geçmişi":::             
1. Değişiklik hakkında daha fazla ayrıntı görmek için değişiklik geçmişi listesindeki değişikliği seçin. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="VM olayını durdur-değişiklik ayrıntıları":::             

Etkinlik günlükleri hakkında daha fazla bilgi için bkz. [Azure etkinlik günlüğü](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Sonraki adımlar

- Etkinlik günlüklerinde **Uyarı** ayarlama hakkında bilgi edinmek için bkz. [uyarı oluşturma](create-alerts.md).
- Etkinlik günlükleri hakkında daha fazla bilgi için bkz.  [Azure etkinlik günlüğü](../azure-monitor/essentials/activity-log.md).


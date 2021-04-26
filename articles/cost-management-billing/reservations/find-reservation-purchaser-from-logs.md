---
title: Azure Izleyici günlüklerinden bir rezervasyon Satınalmacı bulma
description: Bu makale, Azure Izleyici günlüklerinden bilgi içeren bir rezervasyon satınalmacının bulmasına yardımcı olur.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773381"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Azure günlüklerinden bir rezervasyon Satınalmacı bulma

Bu makale, Dizin günlüklerinizin bilgileriyle bir rezervasyon satınalmacının bulmasına yardımcı olur. Azure Izleyici 'deki dizin günlükleri, rezervasyon satın alımları yapan kullanıcıların e-posta kimliklerini gösterir.

## <a name="find-the-purchaser"></a>Satınalmacının bulun

1. [Azure portalında](https://portal.azure.com) oturum açın.
1.   >  **Etkinlik günlüğünü** izle  >  **etkinliğine** gidin.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Etkinlik günlüğü-etkinliğinin gezintisini gösteren ekran görüntüsü." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. **Dizin etkinliğini** seçin. *Dizin düzeyi günlüklerini görüntüleme izninizin olması gerektiğini* belirten bir ileti görürseniz, izinleri nasıl alabileceğinizi öğrenmek için [bağlantıyı](../../role-based-access-control/elevate-access-global-admin.md) seçin.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Günlüğü görüntüleme izni olmadan dizin etkinliğini gösteren ekran görüntüsü." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. İzne sahip olduktan sonra **kiracı kaynak sağlayıcısını** **Microsoft. Capacity** ile filtreleyin. Seçilen zaman aralığı için rezervasyonla ilgili tüm olayları görmeniz gerekir. Gerekirse, zaman aralığını değiştirin.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Ayırmayı satın alan kullanıcıyı gösteren ekran görüntüsü." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Gerekirse, **tarafından başlatılan olayı** seçmek Için **sütunları düzenlemeniz** gerekebilir.
   Rezervasyon satın almayı yapan Kullanıcı **tarafından başlatılan olay** altında gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

- Gerekirse, faturalandırma yöneticileri [bir ayırmanın sahipliğini alabilir](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations).

---
title: 'Azure eşleme hizmeti: bağlantı telemetrisini ölçme '
description: Bu öğreticide bağlantı telemetrisini ölçme hakkında bilgi edinin.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 6bd00b314c94e1edf094599bdb43c70376a18550
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065387"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Öğretici: ölçüm eşleme hizmeti bağlantı telemetrisi

 Bu öğreticide, eşleme hizmeti bağlantılarınız için telemetri ölçümünü öğreneceksiniz.
 
 Bağlantı telemetrisi, müşterinin konumu ile Microsoft ağı arasındaki bağlantı için toplanan öngörüleri sağlar. Bu makalede, belirli bir Azure eşleme hizmeti bağlantısı için gecikme raporunu görüntülemeyi öğreneceksiniz. 

Eşleme hizmeti bağlantı telemetrisini ölçmek için Azure portal bir eşleme hizmeti bağlantısı kaydetmeniz gerekir. Bir bağlantıyı nasıl kaydedeceğinizi öğrenmek için bkz. [eşleme hizmeti bağlantısını kaydetme-Azure Portal](azure-portal.md).


## <a name="view-a-latency-report"></a>Gecikme raporunu görüntüleme

Belirli bir eşleme hizmeti bağlantısı için bir gecikme raporu görüntülemek için, aşağıdaki adımları izleyin.

1. Sol bölmedeki **tüm kaynaklar** ' ı seçin ve eşleme hizmeti bağlantısı ' nı seçin. Ardından **ön ekler** altında **Aç** ' ı seçin. 

   ![Eşleme hizmeti bağlantısını seçin](./media/peering-service-measure/peering-service-measure-menu.png)

2. Bu eşleme hizmeti bağlantısıyla ilişkili tüm öneklerin gecikme bir rapor sayfası görüntülenir. 

      ![Gecikme rapor sayfası](./media/peering-service-measure/peering-service-latency-report.png)

3. Varsayılan olarak, rapor bu sayfada görüntülenen her saat için güncelleştirilir. Farklı zaman çizelgeleriyle ilgili raporu görüntülemek için, **verileri son olarak göster** seçeneğinden uygun seçeneği belirleyin. 

4. Belirli bir önek için olayları görüntülemek için, önek adını seçin ve sol bölmedeki **önek olayları** ' nı seçin. Yakalanan olaylar görüntülenir.


   ![Önek olayları](./media/peering-service-measure/peering-service-prefix-event.png)

 **Önek olayları** listesinde yakalanan olası olaylardan bazıları burada gösterilmiştir.

| **Önek olayları** | **Olay türü**|**Mantık yürütme**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Bilgi|Ön ek duyurusu alındı|
| PrefixWithdrawalEvent|Uyarı| Ön ek çekme alındı |
| PrefixBackupRouteAnnouncementEvent |Bilgi|Ön ek yedekleme yolu duyurusu alındı |
| PrefixBackupRouteWithdrawalEvent|Uyarı|Ön ek yedekleme yolu çekme alındı |
| PrefixActivePath |Bilgi| Geçerli önek etkin yolu   |
| PrefixBackupPath | Bilgi|Geçerli önek yedekleme yolu   |
| PrefixOriginAsChangeEvent|Kritik| Farklı bir kaynak otonom sistem numarası (etkin yol için) ile tam önek alındı| 
| PrefixBackupRouteOriginAsChangeEvent  | Hata|Farklı bir kaynak otonom sistem numarası (yedekleme yolu için) ile alınan önek  |

## <a name="next-steps"></a>Sonraki adımlar

- Eşleme hizmeti bağlantısı hakkında bilgi edinmek için bkz. [eşleme hizmeti bağlantısı](connection.md).
- Eşleme hizmeti bağlantı telemetrisi hakkında bilgi edinmek için bkz. [eşleme hizmeti bağlantı telemetrisi](connection-telemetry.md).
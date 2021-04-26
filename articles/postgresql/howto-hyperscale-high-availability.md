---
title: Yüksek kullanılabilirlik-hiper ölçek yapılandırma (Citus)-PostgreSQL için Azure veritabanı
description: Yüksek kullanılabilirliği etkinleştirme veya devre dışı bırakma
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90907387"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Hiper ölçek (Citus) yüksek kullanılabilirliği yapılandırma

PostgreSQL için Azure veritabanı-hiper ölçek (Citus), veritabanı kapalı kalma süresini önlemek için yüksek kullanılabilirlik (HA) sağlar. HA etkinken, bir sunucu grubundaki her düğüm bekleme moduna alınır. Özgün düğüm sağlıksız hale gelirse, bekleme konumu yerine geçecek şekilde yükseltilir.

> [!IMPORTANT]
> HA gruptaki sunucu sayısını çift katına katdığı için aynı zamanda maliyeti de aynı olacaktır.

Sunucu grubu oluşturma sırasında HA 'nin etkinleştirilmesi veya daha sonra Azure portal sunucu grubunuz için **işlem + depolama** sekmesinde daha sonra mümkündür. Kullanıcı arabirimi her iki durumda da benzer şekilde görünür. **Yüksek kullanılabilirlik** IÇIN kaydırıcıyı Hayır 'dan Evet 'e sürükleyin:

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="ha kaydırıcısı":::

Seçiminizi uygulamak için **Kaydet** düğmesine tıklayın. Sunucu grubu, her zaman bir veri akışı sağlarken ve bunlara veri akışı sağlarken, HA 'yi etkinleştirme biraz zaman alabilir.

Sunucu grubu için **genel bakış** sekmesi, tüm düğümleri ve bunların kendi kendi bu değerlerini, her düğüm için ha 'nın başarılı bir şekilde etkinleştirilip etkinleştirilmediğini belirten **yüksek kullanılabilirlik** sütunuyla birlikte listeler.

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="sunucu grubundaki ha sütununa genel bakış":::

### <a name="next-steps"></a>Sonraki adımlar

[Yüksek kullanılabilirlik](concepts-hyperscale-high-availability.md)hakkında daha fazla bilgi edinin.

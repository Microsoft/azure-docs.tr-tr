---
title: Belirli bölgelerdeki cihazlar hakkında bilgi edinin
description: Belirli bir bölgeye göre kapsamlı bir görünüm bilgisi almak için şirket içi yönetim konsolunu kullanın
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104776334"
---
# <a name="view-information-per-zone"></a>Bölge başına bilgileri görüntüle


## <a name="view-a-device-map-for-a-zone"></a>Bir bölgenin cihaz haritasını görüntüleme

Bir sensörde Seçili bölge için bir cihaz haritasını görüntüleyin. Bu görünüm, algılayıcılar, bunlara bağlı cihazlar ve diğer bilgiler dahil olmak üzere seçili bölge ile ilgili tüm ağ öğelerini görüntüler.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Bölge eşlemesinin ekran görüntüsü.":::


- **Site yönetimi** penceresinde, bölge adını Içeren çubuktan **bölge haritasını görüntüle** ' yi seçin.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Varsayılan bölge varsayılan iş birimidir.":::

**Cihaz Haritası** penceresi görüntülenir. Haritalardan cihazları ve cihaz bilgilerini görüntülemek için aşağıdaki araçlar kullanılabilir. Bu özelliklerin her biri hakkında ayrıntılı bilgi için bkz. *IoT Platformu Için Defender Kullanıcı Kılavuzu*.

- **Harita yakınlaştırma görünümleri**: Basitleştirilmiş görünüm, bağlantılar görünümü ve ayrıntılı görünüm. Görüntülenmiş harita görünümü haritanın yakınlaştırma düzeyine bağlı olarak değişir. Yakınlaştırma düzeylerini ayarlayarak harita görünümleri arasında geçiş yapabilirsiniz.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Harita arama ve Düzen araçları**: değişen ağ kesimlerini, cihazları, cihaz gruplarını veya katmanları göstermek için kullanılan araçlar.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Arama ve yerleşim araçları görünümünün ekran görüntüsü.":::

- **Cihazlarda Etiketler ve göstergeler:** Örneğin, bir BT ağındaki bir alt ağda gruplandırılan cihazların sayısı. Bu örnekte 8 ' dir.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Etiketlerin ve göstergelerin ekran görüntüsü.":::

- **Cihaz özelliklerini görüntüle**: Örneğin, cihazı izleyen algılayıcı ve temel cihaz özellikleri. Cihaz özelliklerini görüntülemek için cihaza sağ tıklayın.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Cihaz özellikleri görünümünün ekran görüntüsü.":::

- **Bir cihazla Ilişkili uyarı:** İlgili uyarıları görüntülemek için cihaza sağ tıklayın.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Uyarıları göster görünümünün ekran görüntüsü.":::

## <a name="view-alerts-associated-with-a-zone"></a>Bir bölgeyle ilişkili uyarıları görüntüleme

Belirli bir bölgeyle ilişkili uyarıları görüntülemek için:

- **Bölge** penceresini oluşturan uyarı simgesini seçin. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Örneklerle varsayılan Iş birimi görünümü.":::

Daha fazla bilgi için bkz. [genel bakış: uyarılarla çalışma](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Bir bölgenin cihaz envanterini görüntüleme

Belirli bir bölgeyle ilişkili cihaz envanterini görüntülemek için:

- **Bölge** penceresinden **cihaz envanterini görüntüle** ' yi seçin.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Cihaz envanteri ekranı görüntülenir.":::

Daha fazla bilgi için bkz. [cihaz envanterinde tüm kurumsal algılayıcı algılamalarını araştırın](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>Ek bölge bilgilerini görüntüle

Aşağıdaki ek bölge bilgileri kullanılabilir:

- **Bölge ayrıntıları**: Bölgeyle ilişkili cihaz, uyarı ve algılayıcı sayısını görüntüleyin.

- **Algılayıcı ayrıntıları**: bölgeye atanan her algılayıcının adını, IP adresini ve sürümünü görüntüleyin.

- **Bağlantı durumu**: bir algılayıcı bağlantısı kesilirse sensörden bağlanın. Bkz. [Şirket içi yönetim konsoluna algılayıcıların bağlanmasını sağlar](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Güncelleştirme ilerlemesi**: bağlı algılayıcı yükseltiliyorsa, yükseltme durumları görüntülenir. Yükseltme sırasında şirket içi yönetim konsolu, algılayıcıdan cihaz bilgilerini almaz.

## <a name="next-steps"></a>Sonraki adımlar

[Küresel, bölgesel ve yerel tehditlere yönelik içgörü elde etme](how-to-gain-insight-into-global-regional-and-local-threats.md)

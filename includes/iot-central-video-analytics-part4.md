---
title: include dosyası
description: include dosyası
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832082"
---
### <a name="publish-the-device-template"></a>Cihaz şablonunu yayımlama

Uygulamaya bir cihaz ekleyebilmeniz için önce cihaz şablonunu yayımlamanız gerekir:

1. **LVA Edge Gateway v2** cihaz şablonunda **Yayımla**' yı seçin.

1. **Bu cihaz şablonunu uygulama üzerinde Yayımla** sayfasında **Yayımla**' yı seçin.

**LVA Edge Gateway v2** , artık uygulamanın **cihazlar** sayfasında kullanılacak cihaz türü olarak kullanılabilir.

## <a name="migrate-the-gateway-device"></a>Ağ Geçidi cihazını geçirme

Mevcut **ağ geçidi-001** cihazı, **LVA Edge Gateway** cihaz şablonunu kullanır. Yeni Dağıtım bildiriminizi kullanmak için cihazı yeni cihaz şablonuna geçirin:

**Ağ geçidi-001** cihazını geçirmek için:

1. **Cihazlar** sayfasına gidin ve listede vurgulamak için **ağ geçidi-001** cihazını seçin.

1. **Geçir** seçeneğini belirleyin. **Geçiş** simgesi görünmüyorsa, daha fazla seçenek görmek için **...** seçeneğini belirleyin.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Ağ Geçidi cihazını yeni bir sürüme geçirme":::

1. **Geçir** iletişim kutusundaki listede, **LVA Edge Gateway v2**' yi seçin ve ardından **geçir**' i seçin.

Birkaç saniye sonra geçiş tamamlanır. Cihazınız artık, özelleştirilmiş dağıtım bildiriminiz ile **LVA Edge Gateway v2** cihaz şablonunu kullanıyor.

Artık özgün **LVA Edge Gateway** cihaz şablonunu kullanan hiçbir cihaz yok. Bu cihaz şablonunu Sil:

1. **Cihaz şablonları** sayfasına gidin ve **LVA Edge Gateway** cihaz şablonunu seçin.

1. Cihaz şablonunu silmek için **Sil** ' i seçin.

### <a name="get-the-device-credentials"></a>Cihaz kimlik bilgilerini al

Cihazın IoT Central uygulamanıza bağlanmasına izin veren kimlik bilgileri gerekir. Cihaz kimlik bilgilerini al:

1. **Cihazlar** sayfasında, **ağ geçidi-001** cihazını seçin.

1. **Bağlan**’ı seçin.

1. **Cihaz bağlantısı** sayfasında, **kimlik KAPSAMı**, **cihaz kimliği** ve cihaz **birincil anahtarı** *scratchpad.txt* dosyasında bir Note olun. Bu değerleri daha sonra kullanırsınız.

1. Bağlantı yönteminin, **paylaşılan erişim imzası** olarak ayarlandığından emin olun.

1. **Kapat**’ı seçin.


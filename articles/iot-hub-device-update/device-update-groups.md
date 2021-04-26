---
title: Azure IoT Hub cihaz grupları için cihaz güncelleştirmesini anlama | Microsoft Docs
description: Cihaz gruplarının nasıl kullanıldığını anlayın.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680009"
---
# <a name="device-groups"></a>Device groups

Bir cihaz grubu, cihazların bir koleksiyonudur. Cihaz grupları birçok cihaza dağıtımları ölçeklendirmek için bir yol sağlar. Her bir cihaz tek seferde yalnızca bir cihaz grubuna aittir.
Cihazlarınızı düzenlemek için birden çok cihaz grubu oluşturmayı tercih edebilirsiniz. Örneğin contoso, test laboratuvarındaki cihazlar için "Fışıklandırma" cihaz grubunu ve alan ekibinin Operations Center 'da kullandığı cihazların "değerlendirme" cihaz grubunu kullanabilir. Ayrıca, contoso, kendi coğrafi bölgelerine göre üretim cihazlarını gruplamak ve bu sayede cihazları bölgesel Timezones göre hizalanan bir zamanlamaya göre güncelleştirebilmeleri için tercih edebilir. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Cihaz grubu oluşturmak için Device ikizi etiketini kullanma

Cihaz ikizi etiketleri, kullanıcıların cihazları grupkurmasını sağlar. Cihazların gruplandırılmasına izin vermek için cihaz ikizi bir ADUGroup anahtarına ve bir değere sahip olması gerekir.

### <a name="device-twin-tag-format"></a>Cihaz ikizi etiketi biçimi

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Kategorilere ayrılmamış cihaz grubu

Kategorilere ayrılmamış, cihazları gruplamak için kullanılan ayrılmış bir sözcüktür:
- ADUGroup cihaz ikizi etiketine sahip değildir.
- ADUGroup cihaz ikizi etiketine sahip ancak bu grup adıyla bir grup oluşturulmamış.

Örneğin, cihazları cihaz ikizi etiketleriyle birlikte düşünün:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Bunlar için oluşturulabilecek cihazlar ve olası gruplar aşağıda verilmiştir.

|Cihaz |Grup  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Grup2|
|Device4    |Kategorilere Ayrılmamış|



## <a name="next-steps"></a>Sonraki adımlar

[Cihaz grubu oluştur](./create-update-group.md)

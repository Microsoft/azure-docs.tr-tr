---
title: include dosyası
description: include dosyası
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491153"
---
Bu öğreticide daha sonra örnek cihaz uygulamasını çalıştırdığınızda aşağıdaki yapılandırma değerleri gerekir:

* KIMLIK kapsamı: IoT Central uygulamanızda **yönetim > cihaz bağlantısı**' na gidin. **Kimlik kapsamı** değerini bir yere getirin.
* Birincil anahtarı grupla: IoT Central uygulamanızda **yönetim > cihaz bağlantısı > SAS-IoT-cihazlar**' a gidin. Paylaşılan erişim imzası **birincil anahtar** değerini bir yere getirin.

Aldığınız grup birincil anahtarından bir cihaz anahtarı oluşturmak için Cloud Shell kullanın:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Oluşturulan Cihaz anahtarını bir yere getirin, bu öğreticide daha sonra kullanın.

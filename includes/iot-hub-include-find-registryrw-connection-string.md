---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883830"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

**Registryreadwrite** ilkesi için IoT Hub bağlantı dizesi almak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) **kaynak grupları**' nı seçin. Hub 'ınızın bulunduğu kaynak grubunu seçin ve ardından kaynak listesinden hub 'ınızı seçin.

2. Hub 'ınızın sol tarafındaki bölmede **paylaşılan erişim ilkeleri**' ni seçin.

3. İlkeler listesinden **Registryreadwrite** ilkesini seçin.

4. **Paylaşılan erişim anahtarları**altında, **bağlantı dizesi--birincil anahtar** için Kopyala simgesini seçin ve değeri kaydedin.

    ![Bağlantı dizesinin nasıl alınacağını göster](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

IoT Hub paylaşılan erişim ilkeleri ve izinleri hakkında daha fazla bilgi için bkz. [erişim denetimi ve izinleri](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).

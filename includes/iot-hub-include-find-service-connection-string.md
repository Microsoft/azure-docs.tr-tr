---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558464"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

**Hizmet** ilkesi için IoT Hub bağlantı dizesi almak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) **kaynak grupları**' nı seçin. Hub 'ınızın bulunduğu kaynak grubunu seçin ve ardından kaynak listesinden hub 'ınızı seçin.

1. IoT Hub 'ınızın sol tarafındaki bölmede **paylaşılan erişim ilkeleri**' ni seçin.

1. İlke listesinden **hizmet** İlkesi ' ni seçin.

1. **Paylaşılan erişim anahtarları**altında, **bağlantı dizesi--birincil anahtar** için Kopyala simgesini seçin ve değeri kaydedin.

    ![Bağlantı dizesinin nasıl alınacağını göster](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

IoT Hub paylaşılan erişim ilkeleri ve izinleri hakkında daha fazla bilgi için bkz. [erişim denetimi ve izinleri](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).

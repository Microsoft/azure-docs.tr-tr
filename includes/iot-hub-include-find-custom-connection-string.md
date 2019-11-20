---
title: include dosyası
description: include dosyası
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050460"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

**Hizmet Connect** ve **kayıt defteri okuma** izinleri veren ve bu ilke için bir bağlantı dizesi alan bir paylaşılan erişim ilkesi oluşturmak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) **kaynak grupları**' nı seçin. Hub 'ınızın bulunduğu kaynak grubunu seçin ve ardından kaynak listesinden hub 'ınızı seçin.

1. Hub 'ınızın sol tarafındaki bölmede **paylaşılan erişim ilkeleri**' ni seçin.

1. İlke listesinin üstündeki en üstteki menüden **Ekle**' yi seçin.

1. **Paylaşılan erişim Ilkesi Ekle**' nin altında, Ilkeniz Için *Serviceandregistryread*gibi açıklayıcı bir ad girin. **İzinler**altında **kayıt defteri okuma** ve **hizmet Connect**' i seçin ve ardından **Oluştur**' u seçin.

    ![Yeni bir paylaşılan erişim ilkesinin nasıl ekleneceğini göster](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. İlke listesinden yeni ilkenizi seçin.

1. **Paylaşılan erişim anahtarları**altında, **bağlantı dizesi--birincil anahtar** için Kopyala simgesini seçin ve değeri kaydedin.

    ![Bağlantı dizesinin nasıl alınacağını göster](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

IoT Hub paylaşılan erişim ilkeleri ve izinleri hakkında daha fazla bilgi için bkz. [erişim denetimi ve izinleri](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).

---
title: Doğrudan yöntemleri çağırmak için Azure portal kullanma
description: Bu makale, doğrudan yöntemleri çağırmak için Azure portal kullanan bir genel bakıştır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: ddd6a94015829a0b5dd4053f4d3a2fd6d2650d32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492663"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Doğrudan yöntemleri çağırmak için Azure portal kullanma

IoT Hub, uç cihazlarda buluttan [doğrudan Yöntemler](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) çağırma olanağı sağlar. IoT Edge (LVA) modülündeki canlı video analizi, canlı videoyu çözümlemek üzere farklı iş akışlarını tanımlamak, dağıtmak ve örneklemek için kullanılabilecek çeşitli [doğrudan Yöntemler](./direct-methods.md) sunar.

Bu makalede, Azure portal aracılığıyla bir IoT Edge modülü için canlı video analizinden doğrudan Yöntem çağrılarını çağırmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* Uç cihazınızda çalışan IoT Edge modülde canlı video analizinden yararlanarak [hızlı başlangıç: canlı video analizi IoT Edge](./get-started-detect-motion-emit-events-quickstart.md) veya portalı kullanma ' da açıklanan yöntemleri kullanabilirsiniz [.](./deploy-iot-edge-device.md)

* [Canlı video analizlerini](./overview.md) ve [medya grafiği kavramını](./media-graph-concept.md)anlamış olursunuz.

## <a name="invoking-direct-methods-via-azure-portal"></a>Azure portal aracılığıyla doğrudan Yöntemler çağırma

LVA modülü tarafından kullanıma sunulan [doğrudan yöntemlerin](./direct-methods.md) her biri Azure Portal aracılığıyla çağrılabilir. Aşağıdaki adımlarda, bir doğrudan yöntem için ayrıntılar sağlanmaktadır. Benzer adımları kullanarak diğer doğrudan yöntemleri çağırabilirsiniz. Ancak, her bir doğrudan yöntem için belirli bir JSON gövdesi gerekir.

`GraphTopologyList`IoT Edge modülündeki canlı video analizi üzerinde şu anda dağıtılmış olan tüm grafik topolojilerinin bir listesini almak için yöntem çağrısını kullanın. Bu doğrudan yöntemi çağırmak için aşağıdaki adımları kullanın:

1. Azure portal oturum aç
1. IoT Hub bulmak için Portal giriş sayfasından ilgili kaynak grubunu bulun veya IoT Hub biliyorsanız, seçin.
    ![Portal giriş sayfasında kaynak grubu](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. IoT Hub sayfasından bir kez, çeşitli cihaz kimliklerini listelemek için otomatik cihaz yönetimi altında IoT Edge ' i seçin. Cihazda çalışan modülleri listelemek için ilgili cihaz KIMLIĞINI seçin.
    ![IoT Hub sayfası](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Yapılandırma sayfasını açmak için IoT Edge modüldeki canlı video analizi ' ni seçin.<br><br>
    ![Yapılandırma sayfasını açmak için IoT Edge modüldeki canlı video analizlerini seçin](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Doğrudan yöntem menü seçeneğini belirleyin. <br><br>
    ![Doğrudan yöntem menü seçeneğine tıklayın](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Geçerli sayfada görebileceğiniz gibi, bağlantı dizesi bölümlerine bir değer eklemeniz gerekebilir. Ayar adı bölümünde herhangi bir şeyi gizlemenize veya değiştirmenize gerek yoktur. Bu, herkese açık olmasına izin vermek için Tamam.

1. **Yöntem adı** alanına *Graphtopologyılist* yazın.
1. Aşağıdaki JSON 'ı kopyalayıp **Yük** alanına yapıştırın.
    ```json
    {
    "@apiVersion": "2.0"
    }
    ```
1. Sayfanın üst kısmındaki **yöntemi çağır** düğmesini seçin.<br><br>
    ![Invoke yöntemi düğmesi](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. **Sonuç** alanında bir durum 200 iletisi görmeniz gerekir.<br><br>
    ![bağlantı zaman aşımı](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Yanıtlar

| Koşul             | Durum Kodu | Ayrıntılı hata kodu |
|-----------------------|-------------|---------------------|
| Başarılı               | 200         | Yok                 |
| Genel Kullanıcı hataları   | 400 aralığı   |                     |
| Genel sunucu hataları | 500 aralığı   |                     |

## <a name="next-steps"></a>Sonraki adımlar

[Doğrudan Yöntemler](./direct-methods.md) sayfasında daha doğrudan Yöntemler bulunabilir.

> [!NOTE]
> Grafik örneği belirli bir topolojiyi örnekledi, bu nedenle lütfen bir grafik örneği oluşturmadan önce doğru topoloji ayarlamış olduğunuzdan emin olun.

[Hızlı başlangıç: hareket yayma olaylarını algılama](./get-started-detect-motion-emit-events-quickstart.md) , doğrudan yöntem çağrılarının tam sırasını anlamak için iyi bir başvurudur.

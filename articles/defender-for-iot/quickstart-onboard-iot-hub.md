---
title: 'Hızlı başlangıç: bir aracı tabanlı çözüme IoT için Defender ekleme'
description: Bu hızlı başlangıçta, Azure IoT Hub IoT güvenlik hizmeti için Defender 'ın nasıl ekleneceğini ve etkinleştirileceğini öğreneceksiniz.
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: 2704f8989ab39825cef052ca5556a2e6461efe75
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384502"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Hızlı başlangıç: bir aracı tabanlı çözüme IoT için Defender ekleme

Bu makalede, mevcut IoT Hub IoT hizmeti için Defender 'ın nasıl etkinleştirileceği açıklanır. Şu anda bir IoT Hub yoksa, kullanmaya başlamak için [Azure Portal kullanarak IoT Hub oluşturma](../iot-hub/iot-hub-create-through-portal.md) makalesine bakın.

IoT için Defender 'daki IoT Hub IoT güvenlerinizi yönetebilirsiniz. IoT Hub bulunan yönetim portalı aşağıdakileri yapmanıza olanak sağlar: 

- IoT Hub güvenliğini yönetin.

- IoT Hub telemetri temelinde bir aracı yüklemeden IoT cihazının güvenliğinin temel yönetimi. 

- Mikro aracıya dayalı bir IoT cihazının güvenliği için gelişmiş yönetim.

> [!NOTE]
> IoT için Defender Şu anda yalnızca Standart katman IoT Hub 'Larını desteklemektedir.

## <a name="prerequisites"></a>Önkoşullar

Yok

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>IoT Hub IoT için Defender 'ı ekleme

Tüm yeni IoT Hub 'ları için, IoT için Defender varsayılan olarak **Açık** olarak ayarlanmıştır. IoT Hub oluşturma işlemi sırasında IoT için Defender 'ın **Açık** olduğunu doğrulayabilirsiniz.

Değiştirme seçeneğinin **Açık** olarak ayarlandığını doğrulamak için:

1. Azure portalına gidin.

1. Azure hizmetleri listesinden **IoT Hub** seçin.

1. **Oluştur**’u seçin.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Üstteki araç çubuğundan Oluştur düğmesini seçin." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. **Yönetim** sekmesini seçin ve **IoT Için Defender** 'ın **Açık** olarak ayarlandığını doğrulayın.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="IoT geçiş için Defender 'ın açık olarak ayarlandığından emin olun.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>IoT için Defender 'ı mevcut bir IoT Hub ekleme

IoT için Defender 'ı, daha sonra cihaz kimlik yönetimi, cihazdan buluta ve buluttan cihaza iletişim desenlerine göre izleyebildiğiniz mevcut bir IoT Hub için ekleyebilirsiniz.

IoT için Defender 'ı mevcut bir IoT Hub eklemek için:

1. IoT Hub gidin. 

1. Eklendi olacak IoT Hub seçin.

1. **Güvenlik** bölümü altında herhangi bir seçeneği belirleyin.

1.  **IoT çözümünüzü güvenli hale**   getirin ve ekleme formunu doldurun. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Çözümünüzün güvenliğini sağlamak için IoT çözümünüzü güvenli hale getirin düğmesini seçin.":::

**IoT çözümünüzü güvenli hale getirme Düğünüzden** yalnızca IoT Hub henüz eklendi veya bu sırada IoT Için Defender 'ı bir değiştirme işlemi **kapalıyken** görüntülenir.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Eğer ekleme işlemi sırasında geçiş kapalı olarak ayarlandıysa.":::

## <a name="next-steps"></a>Sonraki adımlar

Çözümünüzü yapılandırmak için sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [Bir Defender IoT mikro aracı modülü oluşturma ikizi (Önizleme)](quickstart-create-micro-agent-module-twin.md)

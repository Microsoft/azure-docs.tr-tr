---
title: 'Hızlı başlangıç: güvenlik uyarılarını araştırın'
description: IoT cihazlarınızdaki IoT güvenlik uyarıları için bkz. ve Defender 'ı araştırın.
ms.topic: quickstart
ms.date: 07/30/2020
ms.openlocfilehash: 2eb4a10372680348536231aa0333c43199b8d883
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780999"
---
# <a name="quickstart-investigate-security-alerts"></a>Hızlı başlangıç: güvenlik uyarılarını araştırın

IoT için Defender tarafından verilen uyarıların zamanlanan araştırması ve düzeltilmesi, IoT çözümünüz genelinde uyumluluk ve koruma sağlamanın en iyi yoludur.

Bu hızlı başlangıçta, her bir IoT güvenlik uyarısında bulunan bilgileri keşfedeceğiz ve her uyarının ve ilgili cihazın ayrıntılarını kullanarak yanıt verip düzeltme hakkında bilgi edineceksiniz. 

Haydi başlayalım. 


## <a name="investigate-new-security-alerts"></a>Yeni güvenlik uyarılarını araştır

IoT Hub güvenlik uyarısı listesi, IoT Hub için tüm toplu güvenlik uyarılarını görüntüler. 

1. Azure portal, yeni uyarılar için araştırmak istediğiniz **IoT Hub** açın.
1. **Güvenlik** menüsünden **Uyarılar**' ı seçin. IoT Hub için tüm güvenlik uyarıları görüntülenir ve **Yeni** bir bayrağıyla uyarıları son 24 saatten işaretler.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Yeni uyarı bayrağını kullanarak yeni IoT güvenlik uyarılarını araştırın":::
1. Uyarı ayrıntılarını açmak ve uyarı özelliklerinin detayına gitmek için listeden herhangi bir uyarıyı seçin ve açın. 

## <a name="security-alert-details"></a>Güvenlik uyarısı ayrıntıları

Her toplanmış uyarının açılması, ayrıntılı uyarı açıklaması, düzeltme adımları, bir uyarıyı tetikleyen her bir cihaz için cihaz KIMLIĞI ve Log Analytics kullanarak doğrudan araştırma erişimi gösterir. 

1. **IoT Hub**  >  **güvenlik**  >  **uyarıları** listesinden herhangi bir güvenlik uyarısını seçin ve açın. 
1. Bu uyarıyı toplama döneminde veren tüm cihazların Uyarı **açıklamasını**, **önem derecesini**, **algılama kaynağını** ve **cihaz ayrıntılarını** gözden geçirin.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Toplanan bir uyarıdaki her bir cihazın ayrıntılarını gözden geçirin ve inceleyin "::: 
1. Uyarı özelliklerini inceledikten sonra, uyarıya neden olan sorunu düzeltmeye ve/veya çözmeye yardımcı olması için **el ile düzeltme adım** yönergelerini kullanın. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Cihaz güvenliği uyarılarınızı çözümlemeye veya düzeltmeye yardımcı olması için el ile düzeltme adımlarını izleyin":::

1. Daha fazla araştırma gerekiyorsa, bağlantıyı kullanarak **Log Analytics Uyarıları araştırın** . 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Bir uyarıyı daha fazla araştırmak için, ekranda sağlanan Log Analytics kullanarak araştır bağlantısını kullanın":::

## <a name="next-steps"></a>Sonraki adımlar

Defender uyarı türleri ve olası özelleştirmeler hakkında daha fazla bilgi edinmek için sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [IoT güvenlik uyarılarını anlama](concept-security-alerts.md)

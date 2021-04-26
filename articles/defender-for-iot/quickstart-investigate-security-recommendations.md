---
title: Güvenlik önerilerini araştırın "
description: IoT güvenlik hizmeti için Defender ile ilgili güvenlik önerilerini araştırın.
ms.topic: quickstart
ms.date: 09/09/2020
ms.openlocfilehash: e7a9e8160c809fd53b681900b2283778b0023610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780982"
---
# <a name="quickstart-investigate-security-recommendations"></a>Hızlı başlangıç: güvenlik önerilerini araştırın


IoT için Defender 'ın zamanında analiz ve önerilmesine yönelik öneriler, güvenlik duruşunu artırmanın ve IoT çözümünüz genelinde saldırı yüzeyini azaltmanın en iyi yoludur.

Bu hızlı başlangıçta, her bir IoT güvenlik önerisi içinde sunulan bilgileri keşfedebilir ve risk düzeyini azaltmak için her bir öneri ve ilgili cihazın ayrıntılarının nasıl detayına ve nasıl kullanılacağını açıklayacağız.

Haydi başlayalım.

## <a name="investigate-new-recommendations"></a>Yeni önerileri araştır

IoT Hub öneriler listesi, IoT Hub için tüm toplu güvenlik önerilerini görüntüler.

1.  Azure portal, yeni öneriler için araştırmak istediğiniz **IoT Hub** açın.

1.  **Güvenlik** menüsünden **öneriler**' i seçin. IoT Hub yönelik tüm güvenlik önerileri ve **Yeni** bir bayrağıyla öneriler görüntülenir, son 24 saat içindeki önerilerinizi işaretleyin. 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="IoT için ASC ile güvenlik önerilerini araştırın] (medya/hızlı başlangıç/investigate-security-recommendations-inline.png)":::


1.  Öneri ayrıntılarını açmak ve ayrıntılara gitmek için listeden herhangi bir öneriyi seçip açın.

## <a name="security-recommendation-details"></a>Güvenlik önerisi ayrıntıları

Her toplanmış öneriyi açarak, bir önerisi tetikleyen her bir cihaz için ayrıntılı öneri açıklaması, düzeltme adımları, cihaz KIMLIĞI ' ni görüntüleyin. Ayrıca, Log Analytics kullanarak öneri önem derecesi ve doğrudan araştırma erişimi de görüntülenir.

1.  **IoT Hub**  >  **güvenlik**  >  **önerileri** listesinden herhangi bir güvenlik önerisi seçin ve açın.

1.  Bu öneriyi toplama döneminde veren tüm cihazların öneri **açıklaması**, **önem derecesi**, **cihaz ayrıntılarını** gözden geçirin. 

1.  Öneri özelliklerini inceledikten sonra, öneriye neden olan sorunu düzeltmeye ve çözmeye yardımcı olması için **el ile düzeltme adım** yönergelerini kullanın. 

    :::image type="content" source="media/quickstart/remediate-security-recommendations-inline.png" alt-text="IoT için ASC ile güvenlik önerilerini düzeltin" lightbox="media/quickstart/remediate-security-recommendations-expanded.png":::

1.  Ayrıntıya gitme sayfasında istenen cihazı seçerek belirli bir cihazın öneri ayrıntılarını araştırın.

    :::image type="content" source="media/quickstart/explore-security-recommendation-detail-inline.png" alt-text="IoT için ASC ile bir cihaz için belirli güvenlik önerilerini araştırın" lightbox="media/quickstart/explore-security-recommendation-detail-expanded.png":::

1.  Daha fazla araştırma gerekiyorsa, bağlantıyı kullanarak **Log Analytics öneriyi araştırın** . 

## <a name="next-steps"></a>Sonraki adımlar

Özel uyarılar oluşturmayı öğrenmek için bir sonraki makaleye ilerleyin...

> [!div class="nextstepaction"]
> [Özel uyarılar oluşturma](quickstart-create-custom-alerts.md)

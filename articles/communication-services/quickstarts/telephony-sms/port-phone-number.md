---
title: Hızlı başlangıç-Azure Iletişim hizmetlerinde telefon numarası bağlantı noktası
description: Iletişim Hizmetleri kaynağınız için telefon numarası bağlantı noktası oluşturmayı öğrenin
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729947"
---
# <a name="quickstart-port-a-phone-number"></a>Hızlı başlangıç: bağlantı noktası telefon numarası

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Telefon numaranızı Azure Iletişim Hizmetleri kaynağına taşıyarak Azure Iletişim Hizmetleri 'ni kullanmaya başlayın. Birleşik Devletler dayanan ücretsiz ve coğrafi numaralar, taşıma için uygundur. Telefon numarası türleri hakkında daha fazla bilgi için, [kavramsal belgeler telefon numarasını](../../concepts/telephony-sms/plan-solution.md)ziyaret edin.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Etkin bir Iletişim Hizmetleri kaynağı.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Azure Kaynak ayrıntılarınızı toplayın

Bir bağlantı noktası isteğini başlatmadan önce Azure portal gidin ve Iletişim Hizmetleri kaynağınızı seçin. `Overview`Bölme seçiliyken `JSON View` sağ üst köşedeki bağlantıya tıklayın:

:::image type="content" source="./media/number-port/json-view.png" alt-text="JSON görünümünü seçmeyi gösteren ekran görüntüsü.":::

Kaynağınızın **Azure kimliğini** ve **sabit kaynak kimliğini** kaydedin:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="JSON özelliklerini seçmeyi gösteren ekran görüntüsü.":::

## <a name="initiate-the-port-request"></a>Bağlantı noktası isteğini başlatma

Birleşik Devletler dayanan ücretsiz ve coğrafi numaralar, taşıma için uygundur. Bağlantı noktası isteğinizi göndermek için aşağıdaki formlardan birini kullanın:

- Ücretsiz numaralar için: ücretsiz numara [bağlantı noktası isteği](https://aka.ms/acs-port-form-tollfree)
- ABD 'ye dayalı coğrafi numaralar için: [coğrafi numara bağlantı noktası isteği](https://aka.ms/acs-port-form-geographic)

Tamamlandığında, tamamlanmış bağlantı noktası istek formunuzu öğesine gönderin acsporting@microsoft.com . Lütfen e-posta konu satırınızdan "ACS Port-In Isteği" ile başladığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrenirsiniz:

> [!div class="checklist"]
> * Iletişim Hizmetleri kaynak meta verilerinizi alın
> * Telefon numaranızı bağlantı noktasına göndermek için bir istek gönderme

> [!div class="nextstepaction"]
> [SMS gönder](../telephony-sms/send.md) 
>  [Çağırma ile çalışmaya başlayın](../voice-video-calling/getting-started-with-calling.md)

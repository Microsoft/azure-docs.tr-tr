---
title: Hızlı başlangıç-Azure Iletişim hizmetlerini kullanarak telefon numaralarını yönetme
description: Azure Iletişim hizmetlerini kullanarak telefon numaralarını yönetmeyi öğrenin
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498208"
---
# <a name="quickstart-manage-phone-numbers"></a>Hızlı başlangıç: telefon numaralarını yönetme

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Sorun giderme

Yaygın sorular ve sorunlar:

- Satın alma telefonu yalnızca ABD 'de desteklenmektedir. Telefon numaralarını satın almak için şunları doğrulayın:
  - İlişkili Azure aboneliği faturalandırma adresi Birleşik Devletler bulunmaktadır. Şu anda bir kaynağı başka bir aboneliğe taşıyamazsınız.
  - Iletişim Hizmetleri kaynağınız Birleşik Devletler veri konumunda sağlanır. Kaynağı şu anda başka bir veri konumuna taşıyamazsınız.

- Telefon numarası bırakıldığında, telefon numarası serbest bırakılır veya faturalandırma döngüsünün sonuna kadar yeniden kullanılamaz.

- Bir Iletişim Hizmetleri kaynağı silindiğinde, bu kaynakla ilişkilendirilmiş telefon numaraları otomatik olarak aynı anda serbest bırakılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrenirsiniz:

> [!div class="checklist"]
> * Telefon numarası satın alın
> * Telefon numaranızı yönetin
> * Telefon numarası yayınlama

> [!div class="nextstepaction"]
> [SMS gönder](../telephony-sms/send.md) 
>  [Çağırma ile çalışmaya başlayın](../voice-video-calling/getting-started-with-calling.md)

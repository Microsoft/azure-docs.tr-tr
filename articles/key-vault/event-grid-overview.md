---
title: 'Öğretici: Azure Event Grid ile Key Vault Izleme'
description: 'Öğretici: Key Vault olaylarına abone olmak için Azure Event Grid kullanma'
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5771af365b763d2152eea4ef4f662e08769b378c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133355"
---
# <a name="tutorial-monitoring-key-vault-with-azure-event-grid-preview"></a>Öğretici: Azure Event Grid ile Key Vault Izleme (Önizleme)

Event Grid ile tümleştirme Key Vault Şu anda önizleme aşamasındadır. Anahtar Kasası 'nda depolanan bir gizli dizi durumu değiştiğinde kullanıcılara bildirim gönderilmesini sağlar. Bir durum değişikliği, süresi dolacak (30 gün sonra), süresi dolan bir gizli dizi veya yeni sürümü bulunan gizli anahtar olarak tanımlanır. Üç gizli dizi türü (anahtar, sertifika ve gizli) için bildirimler desteklenir.

Uygulamalar, karmaşık kod veya pahalı ve verimsiz yoklama Hizmetleri gerekmeden modern sunucusuz mimariler kullanarak bu olaylara tepki verebilir. Olaylar, [Azure işlevleri](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), hatta kendi web kancasına dahil olmak üzere olay işleyicisine [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) gönderilir ve yalnızca kullandığınız kadar ödersiniz. Fiyatlandırma hakkında bilgi için bkz. [Event Grid fiyatlandırması](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Key Vault olayları ve şemaları

Olay Kılavuzu, olay iletilerini abonelere yönlendirmek için [olay abonelikleri](../event-grid/concepts.md#event-subscriptions) kullanır. Key Vault olaylar, verilerdeki değişikliklere yanıt vermek için gereken tüm bilgileri içerir. EventType özelliği "Microsoft. Keykasası" ile başladığı için bir Key Vault olayını tanımlayabilirsiniz.

Daha fazla bilgi için bkz. [Key Vault olay şeması](../event-grid/event-schema-key-vault.md).

> [!NOTE]
> Olaylar yalnızca, abonelik ayarlandıktan sonra oluşturulan gizli sürümler (tüm üç tür) için tetiklenir.
>
> Mevcut gizli dizileri için yeni sürümler oluşturmanız gerekir.

## <a name="practices-for-consuming-events"></a>Olayları tüketen uygulamalar

Key Vault olaylarını işleyen uygulamalar, önerilen birkaç uygulamayı izlemelidir:

* Aynı olay işleyicisine olayları yönlendirmek için birden çok abonelik yapılandırılabilir. Olayların belirli bir kaynaktan olduğunu varsaymamak, ancak beklediğiniz anahtar kasasından geldiğinden emin olmak için iletinin konusunu denetlemek önemlidir.
* Benzer şekilde, eventType için hazırlanmakta olan bir olay olduğunu ve aldığınız tüm olayların istediğiniz tür olacağını kabul edin.
* Anladığınızı alanları yoksayın.  Bu uygulama, gelecekte eklenebilecek yeni özelliklere dayanıklı tutmaya yardımcı olur.
* Olayları belirli bir olayla sınırlamak için "konu" önekini ve sonek eşleşmelerini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault genel bakış](key-vault-overview.md)
- [Azure Event Grid genel bakış](../event-grid/overview.md)
- Nasıl yapılır: [Key Vault olaylarını Automation runbook 'A yönlendirme (Önizleme)](event-grid-tutorial.md).
- Nasıl yapılır: [Anahtar Kasası gizli anahtarı değiştiğinde e-posta alma](event-grid-logicapps.md)
- [Azure Key Vault için Azure Event Grid olay şeması (Önizleme)](../event-grid/event-schema-key-vault.md)
- [Azure Otomasyonu’na genel bakış](../automation/index.yml)

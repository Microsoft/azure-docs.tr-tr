---
title: DNS uyarıları için Azure Defender 'a yanıt verme
description: DNS için Azure Defender 'daki uyarılara yanıt vermek için gereken adımlar hakkında bilgi edinin
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754771"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>DNS için Azure Defender uyarılarına yanıt verme

DNS için Azure Defender 'dan bir uyarı aldığınızda, uyarıları aşağıda açıklandığı gibi araştırıp yanıtlamanız önerilir. DNS için Azure Defender tüm bağlı kaynakları koruduğunda, uyarıyı tetikleyen uygulama veya Kullanıcı hakkında bilgi sahibi olsanız bile, her uyarının çevresindeki durumu doğrulamak önemlidir.  


## <a name="step-1-contact"></a>Adım 1. İletişim

1. Davranışın beklenip beklenmediğini veya bilerek mi olduğunu öğrenmek için kaynak sahibine başvurun.
1. Etkinlik bekleniyorsa, uyarıyı kapatın.
1. Etkinlik beklenmiyorsa, kaynağı riskli olarak kabul ediyor ve sonraki adımda açıklandığı gibi azaltsın.

## <a name="step-2-immediate-mitigation"></a>Adım 2. Anında risk azaltma 

1. Yan yana hareketi engellemek için kaynağı ağdan yalıtın.
1. Elde edilen düzeltme önermesinden sonra, kaynak üzerinde tam bir kötü amaçlı yazılımdan koruma taraması çalıştırın.
1. Bilinmeyen veya istenmeyen paketleri kaldırarak kaynak üzerinde yüklü ve çalışan yazılımları gözden geçirin.
1. Makineyi bilinen iyi bir duruma geri yükleyin, gerekirse işletim sistemini yeniden yükleyin ve doğrulanmış bir kötü amaçlı yazılımdan ücretsiz kaynaktan yazılım geri yükleyin.
1. Makinenin tüm Azure Güvenlik Merkezi önerilerini çözümleyin, daha sonraki ihlalleri engellemek için vurgulanan güvenlik sorunlarını giderin.


## <a name="next-steps"></a>Sonraki adımlar

Bu sayfa, DNS için Azure Defender 'dan bir uyarıya yanıt verme işlemini açıklanmıştı. İlgili bilgiler için aşağıdaki sayfalara bakın:

- [DNS için Azure Defender 'a giriş](defender-for-dns-introduction.md)
- [Azure Defender 'dan uyarıları gösterme](alerts-suppression-rules.md)
- [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)
---
title: Kaynak Yöneticisi uyarılar için Azure Defender 'a yanıt verme
description: Kaynak Yöneticisi için Azure Defender 'daki uyarılara yanıt vermek için gereken adımlar hakkında bilgi edinin
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754746"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Resource Manager için Azure Defender uyarılarına yanıt verme

Kaynak Yöneticisi için Azure Defender 'dan bir uyarı aldığınızda, uyarıyı araştırıp aşağıda açıklandığı gibi uyarmanızı öneririz. Kaynak Yöneticisi için Azure Defender tüm bağlı kaynakları korur, bu nedenle uyarıyı tetikleyen uygulama veya Kullanıcı hakkında bilgi sahibi olsanız bile, her uyarının çevresindeki durumu doğrulamak önemlidir.  


## <a name="step-1-contact"></a>Adım 1. İletişim

1. Davranışın beklenip beklenmediğini veya bilerek mi olduğunu öğrenmek için kaynak sahibine başvurun.
1. Etkinlik bekleniyorsa, uyarıyı kapatın.
1. Etkinlik beklenmiyorsa, ilgili Kullanıcı hesaplarını, abonelikleri ve sanal makineleri tehlikeye atıldığı şekilde değerlendirin ve aşağıdaki adımda anlatıldığı gibi etkisini azaltın.

## <a name="step-2-immediate-mitigation"></a>Adım 2. Anında risk azaltma 

1. Güvenliği aşılmış Kullanıcı hesaplarını düzeltin:
    - Alışkın olmaları durumunda bunları bir tehdit aktör tarafından oluşturulmuş olabileceği için silin
    - Tanıdık olduysa, kimlik doğrulama kimlik bilgilerini değiştirin
    - Kullanıcı tarafından gerçekleştirilen tüm etkinlikleri gözden geçirmek ve kuşkulu olduğunu belirlemek için Azure etkinlik günlüklerini kullanın

1. Güvenliği aşılmış abonelikler:
    - Güvenliği aşılmış Otomasyon hesabından bilmediğiniz runbook 'Ları kaldırın
    - Abonelik için ıAM izinlerini gözden geçirin ve tüm tanıdık kullanıcı hesapları için izinleri kaldırın
    - Abonelikteki tüm Azure kaynaklarını gözden geçirin ve bilmediğiniz her türlü silme
    - Azure Güvenlik Merkezi 'nde abonelik için tüm güvenlik uyarılarını gözden geçirin ve inceleyin
    - Abonelikte gerçekleştirilen tüm etkinlikleri gözden geçirmek ve kuşkulu olduğunu belirlemek için Azure etkinlik günlüklerini kullanın

1. Güvenliği aşılmış sanal makineleri düzeltin
    - Tüm kullanıcılar için parolaları değiştirme
    - Makinede tam bir kötü amaçlı yazılımdan koruma taraması çalıştırın
    - Kötü amaçlı yazılımların ücretsiz kaynağından makineleri yeniden görüntü oluşturma


## <a name="next-steps"></a>Sonraki adımlar

Bu sayfa, Kaynak Yöneticisi için Azure Defender 'dan bir uyarıya yanıt verme işlemini açıklanmıştı. İlgili bilgiler için aşağıdaki sayfalara bakın:

- [Kaynak Yöneticisi için Azure Defender 'a giriş](defender-for-resource-manager-introduction.md)
- [Azure Defender 'dan uyarıları gösterme](alerts-suppression-rules.md)
- [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)
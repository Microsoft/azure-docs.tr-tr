---
title: Tehditler-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Sunulan tüm oluşturulan tehditler için kategoriler içeren Microsoft Threat Modeling Tool için tehdit kategorisi sayfası.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727842"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool tehditler

Threat Modeling Tool, Microsoft güvenlik geliştirme yaşam döngüsü 'nin (SDL) temel bir öğesidir. Yazılım mimarlarının olası güvenlik sorunlarını daha erken belirlemesine ve hafifletmesine ve bu sorunların çözülmesi için yüksek maliyetli olmasına olanak sağlar. Sonuç olarak, toplam geliştirme maliyetini büyük ölçüde azaltır. Ayrıca, tehdidi güvenlik dışı uzmanlar ile tasarlıyoruz, tehdit modellerini oluşturma ve çözümleme konusunda açık yönergeler sunarak tüm geliştiriciler için tehdit modellemesini kolaylaştırın.

> Kullanmaya başlamak için **[Threat Modeling Tool](threat-modeling-tool.md)** ziyaret edin!

Threat Modeling Tool, aşağıdaki gibi belirli soruları cevaplamanıza yardımcı olur:

* Bir saldırgan kimlik doğrulama verilerini nasıl değiştirebilir?
* Bir saldırgan Kullanıcı profili verilerini okuyabilolursa etkisi nedir?
* Kullanıcı profili veritabanına erişim reddedilirse ne olur?

## <a name="stride-model"></a>Ilerleme modeli

Microsoft bu tür kapsamlı soruları düzenlemenize yardımcı olmak için, farklı türlerde tehditleri kategorilere ayırır ve genel güvenlik konuşmalarını basitleştiren gelişmiş bir model kullanır.

| Category | Açıklama |
| -------- | ----------- |
| **Sızdır** | Kullanıcı adı ve parola gibi başka bir kullanıcının kimlik doğrulama bilgilerini geçersiz şekilde erişimi ve daha sonra kullanmayı içerir |
| **Oynan** | Kötü amaçlı veri değişikliğini içerir. Örnek olarak, bir veritabanında tutulan gibi kalıcı verilere yapılan yetkisiz değişiklikler ve Internet gibi açık bir ağ üzerinden iki bilgisayar arasında akar. veri değişikliği dahil değildir. |
| **Kar** | Başka taraflar olmadan bir eylemi gerçekleştirmeyi reddeden kullanıcılarla ilişkili, aksi takdirde (örneğin, bir Kullanıcı yasaklanmış işlemleri izleme yeteneğine sahip olmayan bir sistemde geçersiz bir işlem gerçekleştirir). Red olmayan bir sistem, bir sistemin, Red tehditleri sayacı anlamına gelir. Örneğin, bir öğeyi satın alan bir kullanıcının, alındıktan sonra öğe için imzalaması gerekebilir. Satıcı daha sonra, kullanıcının paketi aldığı kanıt olarak imzalı alındı bilgisini kullanabilir |
| **Bilgilerin açığa çıkması** | , Erişimi olmayan kişilere yönelik bilgilerin açıklanmasını içerir — örneğin, kullanıcıların erişim izni verilmeyen bir dosyayı okumaları veya iki bilgisayar arasındaki geçişte verileri okuma yeteneği. |
| **Hizmet reddi** | Hizmet reddi (DoS) saldırıları, örneğin bir Web sunucusunu geçici olarak kullanılamaz veya kullanılamaz hale getirerek geçerli kullanıcılara hizmeti reddeder. Sistem kullanılabilirliğini ve güvenilirliği geliştirmek için bazı DoS tehditleri türlerine karşı korumanız gerekir |
| **Ayrıcalık yükselmesi** | Ayrıcalıksız bir kullanıcı ayrıcalıklı erişim kazanmıştır ve bu nedenle sistemin tamamını aşmak veya yok etmek için yeterli erişime sahip değildir. Ayrıcalıkların yükseltilmesi, bir saldırganın tüm sistem savunmalarına etkili bir şekilde sızma ve güvenilen sistemin bir parçası olduğu durumlarda, gerçekten de tehlikeli bir durumdur |

## <a name="next-steps"></a>Sonraki adımlar

Azure ile bu tehditleri hafifletmenin farklı yollarını öğrenmek için **[Threat Modeling Tool azaltmalarını](threat-modeling-tool-mitigations.md)** izleyin.
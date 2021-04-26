---
title: Olay toplama (Önizleme)
description: IoT güvenlik aracıları için Defender, yerel cihazınızdaki verileri ve sistem olaylarını toplar ve verileri işlenmek üzere Azure bulutuna ve analiz için gönderir.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779333"
---
# <a name="event-aggregation-preview"></a>Olay toplama (Önizleme)

IoT güvenlik aracıları için Defender, yerel cihazınızdaki verileri ve sistem olaylarını toplar ve verileri işlenmek üzere Azure bulutuna ve analiz için gönderir. IoT Micro Agent için Defender, yeni süreçler ve tüm yeni bağlantı olayları gibi birçok cihaz olayı türünü toplar. Hem yeni işlem hem de yeni bağlantı olayları, genellikle ikinci bir cihazda bir aygıtta meydana gelebilir. Bu özellik kapsamlı güvenlik için önemlidir, ancak, güvenlik aracılarının gönderdikleri ileti sayısı IoT Hub kotayı ve maliyet limitlerini hızla karşılayabilir veya aşabilir. Bununla birlikte, bu olaylar, cihazınızı korumak için önemli olan önemli ölçüde önemli güvenlik bilgilerini içerir. 

Cihazların korunmasını sağlarken ek kotayı ve maliyetleri azaltmak için, IoT aracıları için Defender bu olay türlerini toplar: 

- ProcessCreate (yalnızca Linux) 

- ConnectionCreate (yalnızca Azure RTOS) 

## <a name="how-does-event-aggregation-work"></a>Olay toplama nasıl çalışır? 

IoT aracıları için Defender, Aralık dönemi veya zaman penceresi için olayları toplar. Aralık süresi geçtikten sonra, aracı, toplanan olayları daha fazla analiz için Azure bulutuna gönderir. Toplanan olaylar, Azure bulutuna gönderilene kadar bellekte depolanır. 

Aracı, bellekte zaten tutulan bir olay için özdeş bir olay topladığında, aracı, aracının bellek parmak izini azaltmak için bu belirli olayın isabet sayısını artırır. Toplama zamanı penceresi geçtiğinde, aracı oluşan her bir olay türünün isabet sayısını gönderir. Olay toplama, yalnızca toplanan her bir olay türünün isabet sayısı toplamını verir. 

## <a name="process-events"></a>Olay işleme 

İşlem olayları şu anda yalnızca Linux işletim sistemlerinde desteklenir. 

*Komut satırı* ve  *Kullanıcı kimliği* aynı olduğunda işlem olayları özdeş olarak değerlendirilir   . 

İşlem olayları için varsayılan arabellek 32 işlemlerdir ve sonra, arabelleğin dolabileceği ve yeni işlem olayları için yer açmak amacıyla en eski işlem olayları atılır.  

## <a name="network-connection-events"></a>Ağ bağlantısı olayları 

Ağ bağlantısı olayları şu anda yalnızca Azure RTOS üzerinde desteklenmektedir. 

*Yerel bağlantı noktası*, *uzak bağlantı noktası*, *Aktarım Protokolü*, *Yerel adres* ve  *uzak adres* aynı olduğunda ağ bağlantı olayları özdeş olarak değerlendirilir. 

Ağ bağlantı olayları için varsayılan arabellek 64 ' dir. Sonraki koleksiyon döngüsüne kadar hiçbir yeni ağ olayı önbelleğe alınmaz. Önbellek boyutunu artırmanın bir uyarısı günlüğe kaydedilecek.

## <a name="next-steps"></a>Sonraki adımlar

[IoT güvenlik uyarıları Için Defender](concept-security-alerts.md)' a bakın.

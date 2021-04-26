---
title: IoT Hub için güvenlik önerileri
description: Güvenlik önerileri kavramı ve bunların IoT Hub için Defender 'da nasıl kullanıldığı hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a9e33248354aab659694e39df605cc070fdaaf73
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779350"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT Hub için güvenlik önerileri

IoT için Defender, Azure kaynaklarınızı ve IoT cihazlarınızı tarar ve saldırı yüzeyinizi azaltmak için güvenlik önerileri sağlar.
Güvenlik önerileri, kullanıcılara en iyi güvenlik uygulamalarıyla uyumlu olarak yardımcı olmak için uygulanabilir ve hedeflenir.

Bu makalede, IoT Hub tetiklenebilecek önerilerin bir listesini bulacaksınız.

## <a name="built-in-recommendations-in-iot-hub"></a>IoT Hub 'de yerleşik öneriler

Öneri uyarıları, ortamınızın güvenlik duruşunu iyileştirecek eylemler için Öngörüler ve öneriler sağlar.

| Önem derecesi | Name | Veri Kaynağı | Description |
|--|--|--|--|
| Yüksek | Birden çok cihaz tarafından kullanılan özdeş kimlik doğrulama kimlik bilgileri | IoT Hub | IoT Hub kimlik doğrulama kimlik bilgileri birden çok cihaz tarafından kullanılır. Bu işlem, meşru bir cihazın kimliğine bürünerek uygun bir cihaz olduğunu gösterebilir. Yinelenen kimlik bilgileri kullanımı, kötü niyetli bir aktör tarafından cihaz kimliğe bürünme riskini artırır. |
| Orta | Varsayılan IP filtresi ilkesi reddetme olmalıdır | IoT Hub | IP filtresi yapılandırması, izin verilen trafik için tanımlı kurallara sahip olmalıdır ve varsayılan olarak tüm diğer trafiği varsayılan olarak reddeder. |
| Orta | IP filtresi kuralı, büyük IP aralığı içeriyor | IoT Hub | Bir izin verme IP filtresi kuralı kaynak IP aralığı çok büyük. Aşırı izin veren kurallar, IoT Hub 'ınızı kötü amaçlı aktörlerin kullanımına açabilir. |
| Düşük | IoT Hub tanılama günlüklerini etkinleştirme | IoT Hub | Günlükleri etkinleştirin ve bir yıla kadar saklayın. Günlükleri koruma, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. |

## <a name="next-steps"></a>Sonraki adımlar

- IoT hizmetine [genel bakış](overview.md) için Defender
- [Güvenlik verilerinize erişme](how-to-security-data-access.md) hakkında bilgi edinin
- [Cihazı araştırma](how-to-investigate-device.md) hakkında daha fazla bilgi edinin

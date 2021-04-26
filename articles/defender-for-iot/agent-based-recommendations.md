---
title: Aracı tabanlı öneriler
description: Güvenlik önerileri kavramı ve bunların IoT cihazları için Defender için nasıl kullanıldığı hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a29957f459edad8b768daea7bc4567d77c80b165
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784994"
---
# <a name="security-recommendations-for-iot-devices"></a>IoT cihazları için güvenlik önerileri

IoT için Defender, Azure kaynaklarınızı ve IoT cihazlarınızı tarar ve saldırı yüzeyinizi azaltmak için güvenlik önerileri sağlar.
Güvenlik önerileri, kullanıcılara en iyi güvenlik uygulamalarıyla uyumlu olarak yardımcı olmak için uygulanabilir ve hedeflenir.

Bu makalede, IoT cihazlarınızda tetiklenebilecek önerilerin bir listesini bulacaksınız.

## <a name="agent-based-recommendations"></a>Aracı tabanlı öneriler

Cihaz önerileri, cihaz güvenlik duruşunu geliştirmek için Öngörüler ve öneriler sağlar.

| Önem derecesi | Name | Veri Kaynağı | Description |
|--|--|--|--|
| Orta | Cihazda bağlantı noktalarını açma | Klasik Defender-IoT-Micro-Agent| Cihazda bir dinleme uç noktası bulundu. |
| Orta | Zincirden birinde izin veren güvenlik duvarı ilkesi bulundu. | Klasik Defender-IoT-Micro-Agent| İzin verilen güvenlik duvarı ilkesi bulundu (GIRIŞ/çıkış). Güvenlik duvarı ilkesi varsayılan olarak tüm trafiği reddetmelidir ve cihaza/cihazdan gerekli iletişime izin vermek için kurallar tanımlar. |
| Orta | Giriş zincirindeki izin veren güvenlik duvarı kuralı bulundu | Klasik Defender-IoT-Micro-Agent| Çok sayıda IP adresi veya bağlantı noktası aralığı için izin veren bir model içeren güvenlik duvarındaki bir kural bulundu. |
| Orta | Çıkış zincirindeki izin veren güvenlik duvarı kuralı bulundu | Klasik Defender-IoT-Micro-Agent| Çok sayıda IP adresi veya bağlantı noktası aralığı için izin veren bir model içeren güvenlik duvarındaki bir kural bulundu. |
| Orta | İşlem sistemi temel doğrulaması başarısız oldu | Klasik Defender-IoT-Micro-Agent| Cihaz, [CIS Linux kıyaslamalarıyla](https://www.cisecurity.org/cis-benchmarks/)uyumlu değil. |

### <a name="agent-based-operational-recommendations"></a>Aracı tabanlı işlem önerileri

İşletimsel öneriler, güvenlik Aracısı yapılandırmasını geliştirmek için Öngörüler ve öneriler sağlar.

| Önem derecesi | Name | Veri Kaynağı | Description |
|--|--|--|--|
| Düşük | Aracı unutilized iletileri gönderiyor | Klasik Defender-IoT-Micro-Agent | Son 24 saat boyunca 10 KB veya daha fazla güvenlik iletisi 4 KB 'tan daha küçük. |
| Düşük | Güvenlik ikizi yapılandırması en uygun değil | Klasik Defender-IoT-Micro-Agent | Güvenlik ikizi yapılandırması en uygun değildir. |
| Düşük | Güvenlik ikizi yapılandırması çakışması | Klasik Defender-IoT-Micro-Agent | Güvenlik ikizi yapılandırmasında çakışmalar tanımlandı. |

## <a name="next-steps"></a>Sonraki adımlar

- IoT hizmetine [genel bakış](overview.md) için Defender
- [Güvenlik verilerinize erişme](how-to-security-data-access.md) hakkında bilgi edinin
- [Cihazı araştırma](how-to-investigate-device.md) hakkında daha fazla bilgi edinin

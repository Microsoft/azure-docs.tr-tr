---
title: Aracı tabanlı çözüm mimarisi nedir?
description: IoT Aracısı tabanlı mimari ve bilgi akışı için Azure Defender hakkında bilgi edinin.
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: e08c3f151c3d3bc4fe08e61d960874b07f5b63e8
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383533"
---
# <a name="what-is-agent-based-solution-for-device-builders"></a>Cihaz oluşturucular için aracı tabanlı çözüm nedir?

Bu makalede, IoT Aracısı tabanlı çözüm için Defender 'ın işlevsel sistem mimarisi açıklanmaktadır. IoT için Azure Defender, ortamınızın ihtiyaçlarını, kuruluşların aracısız çözümünü ve cihaz oluşturucuları için aracı tabanlı çözümü sunan iki özellik kümesi sunar.

## <a name="iot-hub-built-in-security"></a>IoT Hub yerleşik güvenliği

IoT için Defender, oluşturulan her yeni IoT Hub varsayılan olarak etkindir. IoT için Defender, herhangi bir cihazda aracı yüklemeye gerek kalmadan gerçek zamanlı izleme, öneriler ve uyarılar sağlar ve alan cihazlarınızı ve IoT Hub 'larını çözümlemek ve korumak için günlüğe kaydedilen IoT Hub meta verilerinde gelişmiş analizler kullanır. 

## <a name="defender-for-iot-micro-agent"></a>IoT mikro Aracısı için Defender 

IoT Micro Agent için Defender, ayrıntılı güvenlik koruması ve cihaz davranışına yönelik görünürlük sağlar. cihazlarınızdan ham güvenlik olaylarını toplar, toplar ve analiz eder. Ham güvenlik olayları, IP bağlantıları, işlem oluşturma, Kullanıcı oturum açmaları ve güvenlikle ilgili diğer bilgileri içerebilir. IoT cihaz aracıları için Defender, yüksek ağ aktarım hızını önlemeye yardımcı olmak için olay toplamayı da işler. Aracılar yüksek düzeyde özelleştirilebilir ve en hızlı SLA 'da yalnızca önemli bilgileri göndermek ya da kapsamlı güvenlik bilgilerini ve bağlamı daha büyük kesimlerde toplamak, daha yüksek hizmet maliyetlerine izin vermek gibi belirli görevler için bunları kullanmanıza olanak sağlar.

Cihaz aracıları ve diğer uygulamalar, Azure IoT Hub 'a güvenlik bilgileri göndermek için **Azure güvenlik iletisi gönderme SDK 'sını** kullanır. IoT Hub, bu bilgileri alır ve IoT hizmeti için Defender 'a iletir.

IoT hizmeti için Defender etkinleştirildikten sonra, iletilen verilere ek olarak IoT Hub, IoT için Defender tarafından analiz edilmek üzere tüm iç verileri de gönderir. Bu veriler cihaz bulutu işlem günlükleri, cihaz kimlikleri ve hub yapılandırması içerir. Bu bilgilerin tümü, IoT Analytics işlem hattı için Defender oluşturmaya yardımcı olur.

IoT Analytics işlem hattı için Defender, Microsoft ve Microsoft iş ortakları içindeki çeşitli kaynaklardan diğer tehdit bilgileri akışlarını da alır. Tüm analiz işlem hattı için Defender, hizmette yapılan tüm müşteri yapılandırmaları (özel uyarılar ve güvenlik iletisi gönder SDK 'Sı) ile birlikte kullanılır.

IoT için Defender, analiz işlem hattını kullanarak, eyleme dönüştürülebilir öneriler ve uyarılar oluşturmak için tüm bilgi akışlarını birleştirir. İşlem hattı, güvenlik araştırmacıları ve uzmanlar tarafından oluşturulan özel kuralların yanı sıra standart cihaz davranışından ve risk analizinden sapma arayan makine öğrenimi modellerini içerir.

Her müşterinin Log Analytics çalışma alanına IoT önerileri ve uyarıları için Defender (analiz işlem hattı çıkışı) yazılır. Ham olayları çalışma alanına ekleme ve uyarılar ve öneriler, algılanan şüpheli etkinliklerin tam ayrıntılarını kullanarak derin araştırmalar ve sorgular sunar.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Mikro aracı mimarisi.":::

## <a name="next-steps"></a>Sonraki adımlar

[IoT için Defender SSS](resources-frequently-asked-questions.md)

[Sistem önkoşulları](quickstart-system-prerequisites.md)

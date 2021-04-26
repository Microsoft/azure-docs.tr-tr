---
title: Azure RTOS için Defender-IoT-Micro-Agent hakkında kavramsal açıklama
description: Azure RTOS kavramları ve iş akışı için Defender-IoT-mikro-Agent hakkında temel bilgileri öğrenin.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 04b86d401bcb9fc919c36b28cf4f80ea3bfd7030
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750491"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-mikro-Azure RTOS için aracı (Önizleme)

Özellikler ve avantajlar ve ilgili yapılandırma ve başvuru kaynaklarına bağlantılar da dahil olmak üzere Azure RTOS için Defender-IoT-Micro-Agent ' y i daha iyi anlamak için bu makaleyi kullanın. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-Micro-Agent

Defender-IoT-mikro-aracı Azure RTOS, NetX Duo sunumunun bir parçası olarak Azure RTOS cihazları için kapsamlı bir güvenlik çözümü sağlar. NetX Duo sunumunda Azure RTOS yerleşik Azure IoT Defender-IoT-Micro-Agent ile birlikte gelir ve etkinleştirildikten sonra gerçek zamanlı işletim sistemi cihazlarınızda ortak tehditler için kapsam sağlar.

Azure RTOS için Defender-IoT-Micro-Agent arka planda çalışır ve her bir müşterinin IoT Hub benzersiz bağlantılarını kullanarak güvenlik iletileri gönderirken sorunsuz bir kullanıcı deneyimi sağlar. Azure RTOS için Defender-IoT-Micro-Agent varsayılan olarak etkinleştirilmiştir.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo, özellikle de gömülü gerçek zamanlı ve IoT uygulamaları için özel olarak tasarlanmış gelişmiş, endüstriyel düzeyde bir TCP/IP ağ yığını. Azure RTOS NetX Duo, güvenlik ve bulut dahil olmak üzere zengin bir protokoller kümesi sağlayan çift IPv4 ve IPv6 ağ yığınıdır. [Azure RTOS NetX Duo](/azure/rtos/netx-duo/) çözümleri hakkında daha fazla bilgi edinin.

Modül aşağıdaki özellikleri sunar:

- **Kötü amaçlı ağ etkinliklerini Algıla**
- **Özel uyarılara dayanan cihaz davranışı temelleri**
- **Cihaz güvenliğini iyileştirme durumu**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-mikro-aracı Azure RTOS mimarisi

Azure RTOS için Defender-IoT-Micro-Agent, Azure IoT ara yazılımı platformu tarafından başlatılır ve hub 'a güvenlik telemetri göndermek için IoT Hub istemcileri kullanır.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-IoT-mikro-aracı durum diyagramı ve bilgi akışı":::

Azure RTOS için Defender-IoT-Micro-Agent aşağıdaki cihaz etkinliğini ve üç toplayıcı kullanarak bilgileri izler:
- Cihaz ağı etkinliği **TCP**, **UDP** ve **ICM**
- **Threadx** ve **NETX Duo** sürümleri olarak sistem bilgileri
- Sinyal olayları

Her toplayıcı bir öncelik grubuna bağlanır ve her bir öncelik grubu, olası **düşük**, **Orta** ve **yüksek** değerlerle kendi aralığına sahiptir. Aralıklar, verilerin toplandığı ve gönderildiği zaman aralığını etkiler.

Her zaman aralığı yapılandırılabilir ve çözümünüzü daha fazla [özelleştirmek](how-to-azure-rtos-security-module.md)için IoT bağlayıcıları etkinleştirilebilir ve devre dışı bırakılabilir. 

## <a name="supported-security-alerts-and-recommendations"></a>Desteklenen güvenlik uyarıları ve önerileri

Azure RTOS için Defender-IoT-Micro-Agent, belirli güvenlik uyarılarını ve önerilerini destekler. İlk yapılandırmayı tamamladıktan sonra hizmetinizin [ilgili uyarı ve öneri değerlerini incelediğinizden ve özelleştirdiğinizden](concept-rtos-security-alerts-recommendations.md) emin olun.

## <a name="ready-to-begin"></a>Başlamaya hazırlanıyor musunuz?

Defender-IoT-Micro-for Azure RTOS, IoT cihazlarınız için ücretsiz bir indirme olarak sunulmaktadır. IoT bulut hizmeti için Defender, Azure aboneliği başına 30 günlük deneme sürümü ile kullanılabilir. [Defender-IoT-Micro-Agent ' ı şimdi indirin](https://github.com/azure-rtos/azure-iot-preview/releases) ve başlayalım. 

## <a name="next-steps"></a>Sonraki adımlar

- Azure RTOS [Önkoşulları ve kurulumu](quickstart-azure-rtos-security-module.md)için Defender-IoT-mikro-Agent ile çalışmaya başlayın.
- Azure RTOS [güvenlik uyarıları ve öneri desteği](concept-rtos-security-alerts-recommendations.md)için Defender-IoT-mikro-Agent hakkında daha fazla bilgi edinin. 
- Azure RTOS [başvuru API 'si](azure-rtos-security-module-api.md)için Defender-IoT-mikro-Agent ' ı kullanın.
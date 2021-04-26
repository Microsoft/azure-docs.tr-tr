---
title: IoT Defender için Defender-IoT-mikro-aracı IoT Edge
description: IoT Defender-IoT-mikro aracı için Azure Defender 'ın mimarisini ve yeteneklerini IoT Edge için anlayın.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 81eb8816e1bcf74a9e27e34d14465102599c7d5d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782665"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>IoT Edge Defender için Azure Defender-IoT-Micro-Agent

[Azure IoT Edge](../iot-edge/index.yml) kenarda iş akışlarını yönetmek ve gerçekleştirmek için güçlü özellikler sunar.
IoT ortamlarında IoT Edge oynadığı anahtar bölüm, kötü amaçlı aktörler için özellikle çekici hale gelir.

IoT Defender-IoT-Micro-Agent için Defender, IoT Edge cihazlarınız için kapsamlı bir güvenlik çözümü sağlar.
IoT modülü için Defender, Işletim sisteminizin ve kapsayıcı sisteminizin ham güvenlik verilerini, uygulanabilir güvenlik önerileri ve uyarılarına toplar, toplar ve analiz eder.

IoT cihazlarında IoT güvenlik aracıları için Defender 'a benzer şekilde, IoT Edge modülü için Defender, ikizi modülü aracılığıyla yüksek düzeyde özelleştirilebilir.
Daha fazla bilgi için bkz. [aracınızı yapılandırma](how-to-agent-configuration.md) .

IoT Defender için Defender-IoT-Micro-Agent IoT Edge için aşağıdaki özellikleri sunar:

- Temel Işletim sistemi (Linux) ve IoT Edge kapsayıcı sistemlerinden ham güvenlik olayları toplar.

  Kullanılabilir güvenlik veri toplayıcıları hakkında daha fazla bilgi edinmek için bkz. [IoT Aracısı yapılandırması Için Defender](how-to-agent-configuration.md) .

- IoT Edge dağıtım bildirimlerinin analizi.

- [IoT Edge hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)aracılığıyla gönderilen iletilere ham güvenlik olayları toplar.

- Defender-IoT-Micro-Agent ikizi kullanarak yapılandırmayı kaldırın.

  Daha fazla bilgi edinmek için bkz. [IoT Aracısı için bir Defender yapılandırma](how-to-agent-configuration.md) .

IoT Defender için Defender-IoT-Micro-Agent, IoT Edge IoT Edge altında ayrıcalıklı modda çalışır.
Modülün Işletim sistemini ve diğer IoT Edge modüllerini izlemesine izin vermek için ayrıcalıklı mod gerekir.

## <a name="module-supported-platforms"></a>Modül desteklenen platformlar

IoT Defender-IoT-Micro-Agent for IoT Edge için Defender Şu anda yalnızca Linux için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, IoT Edge için bkz. IoT Defender-IoT-Micro-Agent için Defender 'ın mimari ve özellikleri hakkında bilgi edindiniz.

IoT dağıtımı için Defender 'ı kullanmaya devam etmek için aşağıdaki makaleleri kullanın:

- [IoT Edge Için Defender-IoT-mikro-Aracısı](how-to-deploy-edge.md) dağıtma
- [Defender-IoT-mikro-Agent](how-to-agent-configuration.md) ' ı nasıl yapılandıracağınızı öğrenin
- IoT için IoT [Defender](resources-manage-proprietary-protocols.md) Için Defender 'ı inceleyin
- [IoT Hub Için Defender 'ı nasıl etkinleştirebileceğinizi](quickstart-onboard-iot-hub.md) öğrenin
- [IoT Için Defender](resources-frequently-asked-questions.md) aracılığıyla hizmet hakkında daha fazla bilgi edinin
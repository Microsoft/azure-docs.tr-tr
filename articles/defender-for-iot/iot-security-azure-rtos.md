---
title: Defender-IoT-Micro-Agent for Azure RTOS genel bakış
description: IoT için Azure Defender 'ın bir parçası olarak Azure RTOS desteği ve uygulama için Defender-IoT-Micro-Agent hakkında daha fazla bilgi edinin.
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 8f162cc7e284abc9f1fdc08a10e62369855017c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785912"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>Genel Bakış: IoT Defender için Defender-IoT-Micro-Agent for Azure RTOS (Önizleme)

IoT mikro modülü için Azure Defender, Azure RTOS kullanan cihazlar için kapsamlı bir güvenlik çözümü sağlar. Ortak tehditler ve gerçek zamanlı işletim sistemi (RTOS) cihazlarındaki olası kötü amaçlı etkinlikler için kapsam sağlar. Azure RTOS artık içinde yerleşik olarak bulunan Azure IoT Defender-IoT-Micro-Agent ile birlikte sunulur.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="IoT Azure RTOS için Defender görselleştirme.":::


Azure RTOS için mikro modül aşağıdaki özellikleri sunar:

- Kötü amaçlı ağ etkinliği algılama
- Özel uyarı tabanlı cihaz davranışı taban çizgisi
- İyileştirilmiş cihaz güvenliği durumu

## <a name="detect-malicious-network-activities"></a>Kötü amaçlı ağ etkinliklerini Algıla

Her cihazın gelen ve giden ağ etkinliği izlenir. Desteklenen protokoller, IPv4 ve IPv6 üzerinde TCP, UDP ve ıCMP ' dir. IoT için Defender, bu ağ etkinliklerinin her birini Microsoft Threat Intelligence akışına karşı inceler. Akış, dünya genelinde toplanan milyonlarca benzersiz tehdit göstergesi ile gerçek zamanlı olarak güncelleştirilir.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Özel uyarılara dayalı olarak cihaz davranışı taban çizgisi

Taban çizgisi, güvenlik gruplarına cihaz kümelemesini ve her bir grubun beklenen davranışını tanımlamayı sağlar. IoT cihazları genellikle iyi tanımlanmış ve sınırlı senaryolarda çalışacak şekilde tasarlandığından, bir dizi parametre kullanarak beklenen davranışlarını tanımlayan bir taban çizgisi oluşturmak kolaydır. Taban çizgisinin herhangi bir sapması bir uyarıyı tetikler.

## <a name="improve-your-device-security-hygiene"></a>Cihazınızın güvenlik durumu 'yi geliştirme

IoT için önerilen altyapı Defender 'ı kullanarak, ortamınızdaki sorunlar hakkında bilgi ve Öngörüler sunarak cihazlarınızın güvenlik duruşunu etkiler ve bu bilgileri elde edebilirsiniz. Zayıf bir IoT-cihaz güvenlik duruşi, değişmeden ayrıldıysa olası saldırıların başarılı olmasını sağlayabilir. Güvenlik, her zaman kuruluş içindeki en zayıf bağlantı ile ölçülür.

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS cihazlarını korumaya başlama

Defender-IoT-Micro-for Azure RTOS, cihazlarınız için ücretsiz bir indirme olarak sunulmaktadır. IoT bulut hizmeti için Defender, Azure aboneliği başına 30 günlük deneme sürümü ile kullanılabilir. Başlamak için, [Azure RTOS Için Defender-IoT-mikro-Agent](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md)' ı indirin. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure RTOS için Defender-IoT-mikro-Agent hakkında bilgi edindiniz. Defender-IoT-Micro-Agent ve Başlarken hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure RTOS IoT Defender-IoT-mikro aracı kavramları](concept-rtos-security-module.md)
- [Hızlı başlangıç: Azure RTOS IoT Defender-IoT-Micro-Agent](quickstart-azure-rtos-security-module.md)

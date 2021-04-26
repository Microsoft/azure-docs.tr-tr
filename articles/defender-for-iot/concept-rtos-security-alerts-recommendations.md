---
title: Defender-IoT-mikro-aracı Azure RTOS için yerleşik & özelleştirilebilir uyarılar ve öneriler
description: Azure IoT Defender-IoT-Micro-Agent-RTOS ' ı kullanarak güvenlik uyarıları ve önerilen düzeltme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: cfbd411617a0b80f4857e08f9803b34b80b873d4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784688"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Defender-IoT-Micro-Agent for Azure RTOS güvenlik uyarıları ve önerileri (Önizleme)

Defender-IoT-Micro-Service for Azure RTOS, olası kötü amaçlı etkinlikler ve şüpheli sistem değişiklikleri konusunda sizi uyarmak için gelişmiş analiz ve tehdit bilgilerini kullanarak IoT çözümünüzü sürekli olarak analiz eder. Ayrıca, beklenen cihaz davranışı ve temelleri hakkında bilginiz temelinde özel uyarılar da oluşturabilirsiniz.

Azure RTOS uyarısı için bir Defender-IoT-Micro-Agent olası bir uzlaşmaya yönelik gösterge olarak davranır ve araştırılması ve düzeltilmelidir. Azure RTOS önerisi için bir Defender-IoT-Micro-Agent, düzeltilen ve güncellenen zayıf güvenlik duruşunu belirler. 

Bu makalede, varsayılan aralıklara göre tetiklenen yerleşik uyarıların ve önerilerin bir listesini, beklenen veya temel davranışa göre kendi değerlerinizle özelleştirilebilir bir liste bulacaksınız. 

IoT hizmeti için Defender 'da uyarı özelleştirmenin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [özelleştirilebilir uyarılar](concept-customizable-security-alerts.md). Azure RTOS için Defender-IoT-Micro-Agent kullanılırken özelleştirme için kullanılabilen belirli uyarılar ve öneriler aşağıdaki tablolarda ayrıntılıdır. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Defender-IoT-Micro-Agent for Azure RTOS desteklenen güvenlik uyarıları

### <a name="device-related-security-alerts"></a>Cihazla ilgili güvenlik uyarıları

|Cihazla ilgili güvenlik uyarısı etkinliği  |Uyarı adı  |
|---------|---------|
|IP Adresi| Şüpheli bir IP adresi ile iletişim algılandı|
|X. 509.440 cihaz sertifikası parmak izi|X. 509.440 cihaz sertifikası parmak izi uyuşmazlığı|
|X. 509.440 sertifikası| X. 509.440 sertifikasının geçerliliği geçildi|
|SAS belirteci| Süre dolsa belirteci|
|SAS belirteci| SAS belirteci imzası geçersiz|

### <a name="iot-hub-related-security-alerts"></a>IoT Hub ilişkili güvenlik uyarıları

|IoT Hub güvenlik uyarısı etkinliği  |Uyarı adı  |
|---------|---------|
|Sertifika ekle    |  IoT Hub sertifika ekleme girişimi başarısız oldu       |
|Tanılama ayarını ekleme veya düzenlememe    | Bir IoT Hub tanılama ayarı ekleme veya düzenleme girişimi algılandı      |
|Sertifikayı silme    |  IoT Hub bir sertifikayı silme girişimi başarısız oldu       |
|Tanılama ayarını Sil    |  IoT Hub bir tanılama ayarını silme girişimi algılandı      |
|Sertifika silindi    | IoT Hub bir sertifikayı silme algılandı        |
|Yeni sertifika     |  Yeni sertifikanın bir IoT Hub eklenmesi algılandı       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Defender-IoT-mikro-aracı Azure RTOS tarafından desteklenen özelleştirilebilir uyarılar

### <a name="device-related-customizable-alerts"></a>Cihazla ilgili özelleştirilebilir uyarılar

|Cihazla ilgili etkinlik |Uyarı adı  |
|---------|---------|
|Etkin bağlantılar|Etkin bağlantı sayısı izin verilen aralıkta değil|
|**MQTT** protokolünde buluttan cihaza iletiler|**MQTT** protokolünde cihaz iletilerinin bulut sayısı izin verilen aralıkta değil|
|Giden bağlantı| İzin verilmeyen bir IP 'ye giden bağlantı|

### <a name="hub-related-customizable-alerts"></a>Hub ile ilgili özelleştirilebilir uyarılar 

|Hub ile ilgili etkinlik  |Uyarı adı  |
|---------|---------|
|Komut sırası temizler     |  Komut kuyruğu sayısı izin verilen aralığın dışında temizler       |
|**MQTT** protokolünde buluttan cihaza iletiler    |  İzin verilen aralığın dışında **MQTT** protokolündeki cihaz Iletilerine yönelik bulut sayısı       |
|**MQTT** protokolünde cihazdan buluta iletiler    | İzin verilen aralığın dışında **MQTT** protokolünde bulunan bulut iletilerine ait cihaz sayısı        |
|Doğrudan yöntem çağırır     |  İzin verilen aralığın dışında doğrudan yöntem çağıran sayısı       |
|**MQTT** protokolündeki buluttan cihaza ileti iletileri reddedildi     |   İzin verilen aralığın dışında **MQTT** protokolündeki cihaz iletilerine reddedilen bulut sayısı      |
|İkizi modülleri güncelleştirmeleri     |  İzin verilen Aralık dışında ikizi modüllerine yönelik güncelleştirme sayısı       |
|Yetkisiz işlemler    |  İzin verilen aralığın dışında yetkisiz işlem sayısı       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Defender-IoT-Micro-Agent for Azure RTOS desteklenen öneriler

### <a name="device-related-recommendations"></a>Cihazla ilgili öneriler

|Cihazla ilgili etkinlik  |Öneri adı |
|---------|---------|
|Kimlik doğrulama kimlik bilgileri    |  Birden çok cihaz tarafından kullanılan özdeş kimlik doğrulama kimlik bilgileri       |

### <a name="hub-related-recommendations"></a>Hub ile ilgili öneriler

|IoT Hub ilgili etkinlik  |Öneri adı |
|---------|---------|
|IP filtresi ilkesi   |  Varsayılan IP filtresi ilkesi **Reddet** olarak ayarlanmalıdır  |
|IP filtre kuralı| IP filtresi kuralı, büyük bir IP aralığı içerir|
|Tanılama günlükleri|IoT Hub tanılama günlüklerini etkinleştirme önerisi|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>IoT uyarıları ve önerileri için tüm Defender

IoT hizmeti ile ilgili uyarılar ve öneriler için tüm Defender 'ın tüm listesi için bkz. IoT [güvenlik uyarıları](concept-security-alerts.md), IoT güvenlik [önerileri](concept-recommendations.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Defender-IoT-mikro-aracı Azure RTOS](quickstart-azure-rtos-security-module.md)
- [Azure RTOS için Defender-IoT-Micro-Agent 'ı yapılandırma ve özelleştirme](how-to-azure-rtos-security-module.md)
- [Azure RTOS API 'si Için Defender-IoT-mikro-Agent](azure-rtos-security-module-api.md) bölümüne bakın
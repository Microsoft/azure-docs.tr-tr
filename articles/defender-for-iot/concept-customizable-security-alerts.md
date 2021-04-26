---
title: IoT Hub için özel güvenlik uyarıları
description: IoT Hub özellikleri ve hizmeti için Defender 'ı kullanarak özelleştirilebilir güvenlik uyarıları ve önerilen düzeltme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: d7a58bcdb759c3f31290cc7930eba6ca52fcc17b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784739"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>IoT Hub özel güvenlik uyarıları için Defender

IoT için Defender, gelişmiş analiz ve tehdit bilgilerini kullanarak IoT çözümünüzü sürekli olarak analiz ederek kötü amaçlı etkinliğe karşı sizi uyarır.

Uyarıların, benzersiz kurumsal dağıtımda ve yatay olarak olası güvenliğin en verimli göstergeleri olarak davranmasını sağlamak için beklenen cihaz davranışı hakkındaki bilginiz temelinde özel uyarılar oluşturmanızı öneririz.

IoT uyarıları için aşağıdaki Defender listeleri, beklenen IoT Hub davranışına göre tanımlanabilir. Her uyarının nasıl özelleştirileceği hakkında daha fazla bilgi için bkz. [özel uyarılar oluşturma](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>IoT Hub yerleşik özel uyarılar

| Önem derecesi | Uyarı adı | Veri kaynağı | Description | Önerilen düzeltme |
|--|--|--|--|--|
| Düşük | Özel uyarı-AMQP protokolündeki cihaz iletilerinin bulut sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki bulut-cihaz iletileri (AMQP Protokolü) sayısı, şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-AMQP protokolündeki cihaz iletilerine reddedilen bulut sayısı izin verilen aralığın dışında | IoT Hub | Cihaz tarafından reddedilen bulut ve cihaz iletileri (AMQP Protokolü) sayısı, belirli bir zaman penceresi içinde şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-AMQP protokolündeki bulut iletilerine yönelik cihaz sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki bulut iletilerine (AMQP Protokolü) cihaz miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-çağıran doğrudan Yöntem sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içinde doğrudan yöntem çağıran miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-karşıya dosya yükleme sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki dosya karşıya yükleme miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-HTTP protokolündeki cihaz iletilerinin bulut sayısı izin verilen aralığın dışında | IoT Hub | Bir zaman penceresinde bulut-cihaz iletileri (HTTP protokolü) miktarı yapılandırılmış izin verilen aralıkta değil |
| Düşük | Özel uyarı-HTTP protokolündeki reddedilen bulutun cihaz iletileri sayısı izin verilen aralıkta değil | IoT Hub | Belirli bir zaman penceresi içindeki bulut-cihaz iletileri (HTTP protokolü) miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |
| Düşük | Özel uyarı-HTTP protokolündeki bulut iletilerine yönelik cihaz sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki bulut iletilerine (HTTP protokolü) cihaz miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-MQTT protokolünde cihaz iletilerinin bulut sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki bulut-cihaz iletileri (MQTT Protokolü) miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-MQTT protokolünde reddedilen bulutun cihaz iletileri sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içinde cihaz tarafından reddedilen bulut ve cihaz iletileri (MQTT Protokolü) miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |
| Düşük | Özel uyarı-MQTT protokolündeki bulut iletilerine yönelik cihaz sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki bulut iletilerine (MQTT Protokolü) cihaz miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |
| Düşük | Özel uyarı-izin verilen aralığın dışında kalan komut kuyruğu sayısını temizler | IoT Hub | Komut kuyruğunun miktarı belirli bir zaman penceresi içinde temizler ve şu anda yapılandırılmış ve izin verilen aralığın dışında. |  |
| Düşük | Özel uyarı-modül ikizi Updates sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki modül ikizi güncelleştirmelerinin miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |
| Düşük | Özel uyarı-yetkisiz işlem sayısı izin verilen aralığın dışında | IoT Hub | Belirli bir zaman penceresi içindeki yetkisiz işlem miktarı Şu anda yapılandırılmış ve izin verilen aralığın dışında. |

## <a name="next-steps"></a>Sonraki adımlar

- [Bir uyarıyı özelleştirmeyi](quickstart-create-custom-alerts.md) öğrenin
- IoT hizmetine [genel bakış](overview.md) için Defender
- [Güvenlik verilerinize erişme](how-to-security-data-access.md) hakkında bilgi edinin
- [Cihazı araştırma](how-to-investigate-device.md) hakkında daha fazla bilgi edinin

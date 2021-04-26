---
title: Yerleşik & özel uyarılar listesi
description: IoT Hub özellikleri ve hizmeti için Defender 'ı kullanarak güvenlik uyarıları ve önerilen düzeltme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 73b3a1ca3e52f571ab7b531235650b6bda870691
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784569"
---
# <a name="defender-for-iot-hub-security-alerts"></a>IoT Hub güvenlik uyarıları için Defender

IoT için Defender, gelişmiş analiz ve tehdit bilgilerini kullanarak IoT çözümünüzü sürekli olarak analiz ederek kötü amaçlı etkinliğe karşı sizi uyarır.
Ayrıca, beklenen cihaz davranışı hakkında bilginiz temelinde özel uyarılar oluşturabilirsiniz.
Bir uyarı olası bir uzlaşmaya ilişkin bir gösterge olarak davranır ve araştırılması ve düzeltilmelidir.

Bu makalede, IoT Hub tetiklenebilecek yerleşik uyarıların bir listesini bulacaksınız.
IoT için Defender, yerleşik uyarılara ek olarak beklenen IoT Hub ve/veya cihaz davranışına göre özel uyarılar tanımlamanızı sağlar.
Daha fazla bilgi için bkz. [özelleştirilebilir uyarılar](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>IoT Hub için yerleşik uyarılar

| Önem derecesi | Ad | Açıklama | Önerilen düzeltme |
|--|--|--|--|
| **Orta** önem derecesi |  |  |  |
| IoT Hub yeni sertifika eklendi | Orta | % { \' \' \' Desciothubname} IoT Hub% {desccertificatename} adlı bir sertifika eklendi \' . Bu eylem yetkisiz bir taraf tarafından yapılmışsa, kötü amaçlı etkinlik gösterebilir. | 1. sertifikanın yetkili bir taraf tarafından eklendiğinden emin olun. <br> 2. yetkili bir taraf tarafından eklenmediyse, sertifikayı kaldırın ve uyarıyı kurumsal güvenlik ekibine ilerletin. |
| Sertifika bir IoT Hub silindi | Orta | % { \' Desccertificatename} adlı bir sertifika \' , \' % {desciothubname} IoT Hub silindi \' . Bu eylem yetkisiz bir taraf tarafından yapılmışsa, kötü amaçlı bir etkinlik gösterebilir. | 1. sertifikanın yetkili bir taraf tarafından kaldırıldığından emin olun. <br> 2. sertifika yetkili bir taraf tarafından kaldırılmazsa, sertifikayı geri ekleyin ve uyarıyı kurumsal güvenlik ekibine ilerletin. |
| IoT Hub sertifika eklemek için başarısız girişim algılandı | Orta | % { \' Desccertificatename} sertifikasını \' \' % {desciothubname} IoT Hub ekleme girişimi başarısız oldu \' . Bu eylem yetkisiz bir taraf tarafından yapılmışsa, kötü amaçlı etkinlik gösterebilir. | Sertifikaları değiştirme izinlerinin yalnızca yetkili taraflara verildiğinden emin olun. |
| IoT Hub bir sertifikayı silme girişimi başarısız oldu | Orta | % { \' Desccertificatename} sertifikasını \' \' % {desciothubname} IoT Hub öğesinden silme girişimi başarısız oldu \' . Bu eylem yetkisiz bir taraf tarafından yapılmışsa, kötü amaçlı etkinlik gösterebilir. | Sertifikaları değiştirme izinlerinin yalnızca yetkili bir tarafa verildiğinden emin olun. |
| x. 509.440 cihaz sertifikası parmak izi uyuşmazlığı | Orta | x. 509.440 cihaz sertifikası parmak izi yapılandırmayla eşleşmedi. | Cihazlarda uyarıları gözden geçirin. Başka eylem gerekmiyor. |
| x. 509.440 sertifikasının geçerliliği geçildi | Orta | X. 509.440 cihaz sertifikasının süresi doldu. | Bu, zaman aşımına uğradı bir sertifika veya yasal bir cihazın kimliğine bürünmeye yönelik bir cihaz olabilir. Meşru cihaz şu anda doğru iletişim kursa, bu büyük olasılıkla kimliğe bürünme girişimidir. |
| **Düşük** önem derecesi |  |  |  |
| Algılanan IoT Hub tanılama ayarını ekleme veya düzenleme girişimi | Düşük | IoT Hub tanılama ayarlarını ekleme veya düzenleme girişimi algılandı. Tanılama ayarları, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye atılırsa araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. Bu eylem yetkili bir taraf tarafından yapılmadığından, kötü amaçlı etkinlik gösterebilir. | 1. sertifikanın yetkili bir taraf tarafından kaldırıldığından emin olun.<br> 2. sertifika yetkili bir taraf tarafından kaldırılmazsa, sertifikayı geri ekleyin ve uyarıyı bilgi güvenliği ekibinize ilerletin. |
| Algılanan IoT Hub bir tanılama ayarını silme girişimi | Düşük | % {Deiotemptstatusmessage}% { \' \' \' \' desciothubname} IoT Hub% {descdiagnosticsettingname} tanılama ayarını ekleme veya düzenleme girişimi vardı \' . Tanılama ayarı, bir güvenlik olayı gerçekleştiğinde veya ağınızın güvenliği tehlikeye girerse araştırma amaçlarıyla etkinlik izlerini yeniden oluşturmayı sağlar. Bu eylem yetkili bir taraf tarafından yapılmadığından, kötü amaçlı bir etkinlik gösterebilir. | Tanılama ayarlarını değiştirme izinlerinin yalnızca yetkili bir tarafa verildiğinden emin olun. |
| Süre dolsa belirteci | Düşük | Bir cihaz tarafından kullanılan kullanım vadesi geçen SAS belirteci | , Geçerliliği olmayan bir belirteç olan yasal bir cihaz veya yasal bir cihazın kimliğine bürünme girişimi olabilir. Meşru cihaz şu anda doğru iletişim kurduğunda, bu büyük olasılıkla bir kimliğe bürünme girişimidir. |
| SAS belirteci imzası geçersiz | Düşük | Bir cihaz tarafından kullanılan SAS belirtecinin imzası geçersiz. İmza, birincil veya ikincil anahtarla eşleşmiyor. | Cihazlarda uyarıları gözden geçirin. Başka eylem gerekmiyor. |

## <a name="next-steps"></a>Sonraki adımlar

- IoT hizmetine [genel bakış](overview.md) için Defender
- [Güvenlik verilerinize erişme](how-to-security-data-access.md) hakkında bilgi edinin
- [Cihazı araştırma](how-to-investigate-device.md) hakkında daha fazla bilgi edinin

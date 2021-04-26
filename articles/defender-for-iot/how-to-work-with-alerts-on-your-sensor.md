---
title: Algılayıcı uyarılarını anlama
description: Ağınızın güvenliğini ve çalışmasını geliştirmenize yardımcı olması için uyarılarla çalışın.
ms.date: 3/29/2021
ms.topic: how-to
ms.openlocfilehash: a34871342e9a868ba593ae894fdd91b5e99c5a44
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729431"
---
# <a name="about-sensor-alerts"></a>Algılayıcı uyarıları hakkında

Uyarılar, ağınızın güvenliğini ve çalışmasını geliştirmenize yardımcı olur. Uyarılar hakkında bilgi sağlar:

- Yetkili ağ etkinliğinin sapmaları

- Protokol ve operasyonel bozukluklar

- Şüpheli kötü amaçlı yazılım trafiği

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Adres taramasını algıla.":::

Uyarı yönetimi seçenekleri kullanıcıların şunları yapmasına izin verir:

- Yetkili trafik olarak algılanan etkinlikleri öğrenmek için sensöre bildirin.

- Uyarıyı gözden geçirmeyi kabul edin.

- Aynı cihazlarla ve karşılaştırılabilir trafikle algılanan olayları sessize etmek için algılayıcılar.

Uyarı araştırmasını geliştirmenize ve hızlandırmanıza yardımcı olan ek araçlar vardır. Örnek:

  - Uyarı gözden geçirenler için yönerge Yorumları ekleyin.

  - SOC çözümlerinde görüntülenmek üzere uyarı grupları oluşturun. 

  - Belirli uyarıları arayın; ilgili PCAP dosyalarını gözden geçirin; algılanan cihazları ve cihaz eşlemesindeki diğer bağlı aygıtları görüntüleyin veya iş ortağı sistemlerine Uyarı ayrıntıları gönderin.

  - Uyarıları iş ortağı satıcılarına ilet: SıEM sistemleri, MSSP sistemleri ve daha fazlası.

## <a name="alerts-and-engines"></a>Uyarılar ve altyapılar

Algılayıcı motorları ağ trafiğinden ve ilgilenmeniz gereken davranıştaki değişiklikleri tespit edildiğinde uyarılar tetiklenir. Bu makalede, her altyapının tetiklediği Uyarı türleri açıklanmaktadır.

| Uyarı türü | Description |
|-|-|
| İlke ihlali uyarıları | Ilke Ihlali altyapısı, daha önce öğrenilen trafikten sapma algıladığında tetiklenir. Örnek: <br /> -Yeni bir cihaz algılandı.  <br /> -Bir cihazda yeni bir yapılandırma algılandı. <br /> -Programlama cihazı olarak tanımlanmayan bir cihaz, programlama değişikliğini yürütür. <br /> -Bir bellenim sürümü değişti. |
| Protokol ihlali uyarıları | Protokol Ihlali altyapısı, protokol belirtimine uymayan paket yapılarını veya alan değerlerini algıladığında tetiklenir. | 
| İşletimsel uyarılar | Işletimsel motor ağ işlem olaylarını algıladığında veya bir cihaz arızalı olduğunda tetiklenir. Örneğin, bir ağ aygıtı stop PLC komutuyla durdurulmuş veya algılayıcıdaki bir arabirim izleme trafiğini durdurdu. |
| Kötü amaçlı yazılım uyarıları | Kötü amaçlı yazılım altyapısı kötü amaçlı ağ etkinliği algıladığında tetiklenir. Örneğin, motor Conficker gibi bilinen bir saldırıyı algılar. |
| Anomali uyarıları | Anomali altyapısı bir sapma algıladığında tetiklenir. Örneğin, bir cihaz ağ taraması gerçekleştiriyor ancak tarama cihazı olarak tanımlanmamıştır. |

Araç, algılayıcı altyapılarını etkinleştirmek ve devre dışı bırakmak için kullanılabilir. Devre dışı bırakılan altyapılardan uyarılar tetiklenmez. Bkz. [trafiğin hangi sırada Izleneceğini denetleme](how-to-control-what-traffic-is-monitored.md).

## <a name="alerts-and-sensor-reporting"></a>Uyarılar ve algılayıcı raporlaması

Uyarılara yansıtılan etkinlik, veri araştırma, risk değerlendirmesi ve saldırı vektör raporları oluştururken hesaplanabilir. Bu olayları yönetirken, algılayıcı raporları uygun şekilde güncelleştirir.

Örnek:

  - Tanımlı bir alt ağ ve alt ağ dışında bulunan cihazlarda (genel) bir cihaz arasında yetkisiz bağlantı, veri araştırma *Internet etkinliği* raporunda ve risk değerlendirmesi *İnternet bağlantıları* bölümünde görünür. Bu cihazlar yetkilendirildiğinde (öğrenilir), bu raporlar üretilmeden hesaplanır.

  - Ağ cihazlarında algılanan kötü amaçlı yazılım olayları, risk değerlendirmesi raporlarında raporlanır. Kötü amaçlı yazılım olayları hakkında uyarı *kapatıldığında*, etkilenen cihazlar risk değerlendirmesi raporunda hesaplanmaz.

## <a name="next-steps"></a>Sonraki adımlar

[Öğrenme ve akıllı BT öğrenme modları](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes) 
 Uyarılarda belirtilen bilgileri [görüntüle](how-to-view-information-provided-in-alerts.md) 
 [Uyarı olayını yönetme](how-to-manage-the-alert-event.md) 
 [Uyarı iş akışlarını hızlandırma](how-to-accelerate-alert-incident-response.md) 
 [Uyarı türleri ve açıklamaları](alert-engine-messages.md)

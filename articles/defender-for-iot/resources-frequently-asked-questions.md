---
title: IoT için Defender hakkında sık sorulan sorular
description: IoT özellikleri ve hizmeti için Azure Defender hakkında en sık sorulan soruların yanıtlarını bulun.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778602"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>IoT için Azure Defender hakkında sık sorulan sorular

Bu makalede, IoT için Defender ile ilgili sık sorulan soruların ve yanıtların bir listesi sunulmaktadır.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Azure 'un IoT güvenliği için benzersiz değer teklifi nedir?

IoT için Defender, kuruluşların mevcut siber güvenlik görünümünü tüm IoT çözümüne genişletmelerini sağlar. Azure, iş çözümünüzün uçtan uca bir görünümünü sunarak kurumsal güvenlik duruşunuzu ve toplanan verileri temel alarak işle ilgili eylemler ve kararlar almanıza olanak sağlar. Azure IoT, Azure IoT Edge ve Azure Güvenlik Merkezi kullanılarak birleştirilmiş güvenlik, ihtiyacınız olan güvenlik ile istediğiniz çözümü oluşturmanızı sağlar.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Kuruluşumuz standart olmayan bir endüstriyel protokol kullanıyor. Bunlar destekleniyor mu? 

IoT için Azure Defender kapsamlı protokol desteği sağlar. Gömülü protokol desteğine ek olarak, özel ve özel protokoller çalıştıran IoT ve OT cihazlarının yanı sıra herhangi bir standarda göre farklılık gösteren protokolleri güvenli hale getirebilirsiniz. Geliştiriciler, ufuk açık geliştirme ortamı (ODE) SDK 'sını kullanarak, tanımlı protokollere göre ağ trafiğinin kodunu çözen dissektör eklentileri oluşturabilir. Trafik, tüm izleme, uyarı ve raporlama sağlamak için hizmetler tarafından çözümlenir. Ufku kullanarak:
- Yeni sürümlere yükseltmeye gerek olmadan görünürlük ve denetim ' i genişletin.
- Şirket içinde bir dış eklenti oluşturarak özel bilgileri güvenli hale getirin. 
- Uyarılar, olaylar ve protokol parametreleri için metni yerelleştirin.

Eklentileri eklenti olarak geliştirmeye yönelik bu benzersiz çözüm, yeni bir protokolü desteklemek için adanmış geliştirici takımları veya sürüm sürümleri gerektirmez. Geliştiriciler, iş ortakları ve müşteriler ufku kullanarak protokolleri güvenle geliştirebilir ve Öngörüler ve bilgi paylaşabilir. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Microsoft iş ortaklarından donanım gereçlerini satın almam gerekiyor mu?
IoT algılayıcısı için Azure Defender, [donanım belirtimleri Kılavuzu](./how-to-identify-required-appliances.md)' nda açıklandığı gibi belirli donanım özellikleri üzerinde çalışır, müşteriler Microsoft iş ortaklarından sertifikalı donanımlar satın alabilir veya sağlanan ürün REÇETELERINI (BOM) kullanabilir ve kendi kendinize satın alabilir. 

Sertifikalı donanım, laboratuvarlarımızda sürücü kararlılığı, paket bırakılanlar ve ağ boyutlandırma için test edilmiştir.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>Yönetmeliği, sistemimizi Internet 'e bağlayamamıza izin vermiyor. Hala IoT için Defender 'ı kullanabilir miyim?

Evet! IoT Platformu için Azure Defender çözümü, ağ trafiğini (SPAN, RSPAN veya TAP aracılığıyla), BT ve IoT ağlarını analiz etmek, keşfetmeyi ve sürekli olarak izlemeyi sağlayan bir fiziksel veya sanal algılayıcı gereci olarak dağıtılır. Daha büyük kuruluşlar için birden çok algılayıcı, verilerini şirket içi yönetim konsoluna toplayabilirler.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Ağ nerede izleme bağlantı noktalarına bağlanmalıyım?

IoT için Azure Defender algılayıcısı, bir SPAN bağlantı noktasına veya ağa DOKUNMASıNA bağlanır ve doğrudan pasif (aracısız) izleme aracılığıyla ICS ağ trafiğini toplamaya başlar. Bu, veri yoluna yerleştirilmediğinden ve cihazları etkin bir şekilde taramazsa OT ağları üzerinde sıfır etkiye sahiptir.

Örnek:
- Tek bir gereç (fiziksel sanal), atölye DMZ katmanında bulunabilir ve bu katmana tüm atölye hücre trafiği yönlendirilir.
- Alternatif olarak, atölye DMZ katmanında yer alacak bulut ya da yerel yönetim ile her bir mağaza zemin hücresindeki küçük mini sensörlerini bulun. Başka bir gereç (sanal veya fiziksel), atölye DMZ katmanındaki trafiği izleyebilir (SCADA, Histora veya MES için).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>IoT için Defender, yarışmaya nasıl Karşılaştır?

IoT için Azure Defender, tüm IoT/OT cihazlarınızın genelinde kapsamlı güvenlik sunar. **Son Kullanıcı kurumları** Için, IoT Için Azure Defender, hızlı bir şekilde dağıtılan, farklı mülkiyet ve eski Windows sistemleriyle çalışan ve Azure Sentinel ve diğer SOC araçlarıyla birlikte çalışan aracısız, ağ katmanı güvenliği sağlar. Şirket içinde veya Azure 'a bağlı ortamlarda dağıtılabilir. **IoT cihaz oluşturucuları** Için, IoT Için Azure Defender, cihaz katmanı güvenliğini yeni ıOT/ot girişimlerine eklemek için basit aracılar sunmaktadır.

## <a name="do-i-have-to-be-an-azure-customer"></a>Azure müşterisi olmam gerekiyor mu?

Hayır, IoT için Azure Defender 'ın aracısız sürümü için bir Azure müşterisi olmanız gerekmez. Ancak, Azure Sentinel 'e uyarı göndermek istiyorsanız; Ağ sensörleri sağlayın ve buluttan sistem durumlarını izleyin; ve otomatik yazılım ve tehdit bilgileri güncelleştirmelerinden faydalanabilirsiniz, Azure IoT Hub aracılığıyla algılayıcıyı Azure 'a bağlamanız gerekecektir.

IoT için Azure Defender 'ın aracı tabanlı sürümü için bir Azure müşterisi olmanız gerekir.

## <a name="can-i-create-my-own-alerts"></a>Kendi uyarılarımı oluşturabilir miyim?

Evet, IP/MAC adresi, protokol türü, sınıf, hizmet, işlev, komut, vb. dahil olmak üzere birden çok parametreye göre özel uyarılar oluşturabilir ve yüklerdeki özel etiketlerin değerlerini de kullanabilirsiniz.  Özel uyarılar ve bunların nasıl oluşturulacağı hakkında daha fazla bilgi edinmek için bkz. [özel uyarılar oluşturma](quickstart-create-custom-alerts.md) .

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet bağlantısı çalışmayı kestiğinde ne olur?

Algılayıcılar ve aracılar, cihaz çalıştığı sürece verileri çalıştırmaya ve depolamaya devam eder. Veriler, boyut yapılandırmasına göre güvenlik iletisi önbelleğinde depolanır. Cihazın bağlantısı yapıldığında, güvenlik iletileri göndermeye sürdürülür.

## <a name="next-steps"></a>Sonraki adımlar

IoT için Defender 'ı kullanmaya başlama hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- IoT için Defender 'ı okuyun [genel bakış](overview.md)
- [Sistem önkoşullarını](quickstart-system-prerequisites.md) doğrulama
- [IoT Için Defender 'ı](getting-started.md) kullanmaya başlama hakkında daha fazla bilgi edinin
- [IoT güvenlik uyarıları Için Defender 'ı](concept-security-alerts.md) anlama
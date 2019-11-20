---
title: Azure IoT Hub ölçeklendirme | Microsoft Docs
description: IoT Hub 'ınızı, beklenen ileti aktarım hızını ve istenen özellikleri destekleyecek şekilde ölçeklendirirsiniz. Her katman için desteklenen aktarım hızının özetini ve parçalama seçeneklerini içerir.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: d1de29124825a7f398b9722bb2455d1105e9c9f7
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023651"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Çözümünüz için doğru IoT Hub katmanını seçin

Her IoT çözümü farklıdır, bu nedenle Azure IoT Hub fiyatlandırma ve ölçeğe göre çeşitli seçenekler sunar. Bu makale, IoT Hub gereksinimlerinizi değerlendirmenize yardımcı olmak için tasarlanmıştır. IoT Hub katmanları hakkında fiyatlandırma bilgileri için bkz. [IoT Hub fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-hub).

Çözümünüz için hangi IoT Hub katmanının doğru olduğuna karar vermek için kendinize iki soru sorun:

**Hangi özellikleri kullanacağınızı planlıyorum?**

Azure IoT Hub, destekledikleri özellik sayısına göre farklılık gösteren, temel ve standart olmak üzere iki katman sağlar. IoT çözümünüz cihazlardan veri toplamayı ve merkezi olarak çözümlemeyi temel alıyorsa, temel katman muhtemelen sizin için uygun olabilir. IoT cihazlarını uzaktan denetlemek için daha gelişmiş yapılandırma kullanmak veya iş yüklerinizin bir kısmını cihazlara dağıtmak istiyorsanız, standart katmanı dikkate almanız gerekir. Her katmana eklenen özelliklerin ayrıntılı bir dökümü için [temel ve standart katmanlara](#basic-and-standard-tiers)devam edin.

**Her gün ne kadar veri taşımayı planlıyorum?**

Her bir IoT Hub katmanı, belirli bir günde işleyebilecekleri veri işleme miktarına bağlı olarak üç boyutta sunulur. Bu boyutlar sayısal olarak 1, 2 ve 3 olarak tanımlanır. Örneğin, düzey 1 IoT Hub 'ın her bir birimi günde 400.000 iletiyi işleyebilir, 3. düzey bir birim 300.000.000 ' i işleyebilir. Veri yönergeleri hakkında daha fazla ayrıntı için [ileti işleme](#message-throughput)devam edin.

## <a name="basic-and-standard-tiers"></a>Temel ve Standart katmanlar

IoT Hub standart katmanı tüm özellikleri sağlar ve çift yönlü iletişim yeteneklerini kullanmak isteyen IoT çözümleri için gereklidir. Temel katman, özelliklerin bir alt kümesini sağlar ve cihazlarından buluta yalnızca tek yönlü iletişim gerektiren IoT çözümlerine yöneliktir. Her iki katmanda de aynı güvenlik ve kimlik doğrulama özellikleri sunulur.

Her IoT Hub için bir katmanda yalnızca bir tür [sürümü](https://azure.microsoft.com/pricing/details/iot-hub/) seçilebilir. Örneğin, S1 ve S2 gibi farklı sürümlerden birim karışımı olmadan birden fazla S1 birimiyle bir IoT Hub oluşturabilirsiniz.

| Özellik | Temel katman | Ücretsiz/Standart katman |
| ---------- | ---------- | ------------- |
| [Cihazdan buluta telemetri](iot-hub-devguide-messaging.md) | Yes | Yes |
| [Cihaz başına kimlik](iot-hub-devguide-identity-registry.md) | Yes | Yes |
| [İleti yönlendirme](iot-hub-devguide-messages-read-custom.md), [ileti zenginleştirme](iot-hub-message-enrichments-overview.md)ve [Event Grid tümleştirme](iot-hub-event-grid.md) | Yes | Yes |
| [HTTP, AMQP ve MQTT protokolleri](iot-hub-devguide-protocols.md) | Yes | Yes |
| [Cihaz sağlama hizmeti](../iot-dps/about-iot-dps.md) | Yes | Yes |
| [İzleme ve tanılama](iot-hub-monitor-resource-health.md) | Yes | Yes |
| [Buluttan cihaza mesajlaşma](iot-hub-devguide-c2d-guidance.md) |   | Yes |
| [Cihaz](iot-hub-devguide-device-twins.md)ikne, [Modül TWINS](iot-hub-devguide-module-twins.md)ve [cihaz yönetimi](iot-hub-device-management-overview.md) |   | Yes |
| [Cihaz akışları (Önizleme)](iot-hub-device-streams-overview.md) |   | Yes |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Yes |
| [IoT Tak ve Kullan önizlemesi](../iot-pnp/overview-iot-plug-and-play.md) |   | Yes |

IoT Hub Ayrıca, test ve değerlendirme için tasarlanmış bir ücretsiz katman sağlar. Standart katmanın tüm özelliklerine sahiptir, ancak sınırlı mesajlaşma kesintileri vardır. Ücretsiz katmandan temel ya da standart sürümüne yükseltemezsiniz.

## <a name="partitions"></a>Bölümler

Azure IoT Hub 'Ları, [azure Event Hubs](../event-hubs/event-hubs-features.md) [bölümler](../event-hubs/event-hubs-features.md#partitions)dahil pek çok çekirdek bileşeni içerir. IoT Hub 'Ları için olay akışları, genellikle çeşitli IoT cihazları tarafından raporlanan gelen telemetri verileriyle doldurulur. Olay akışının bölümlenmesi, olay akışlarına eşzamanlı okuma ve yazma sırasında oluşan çekişmeleri azaltmak için kullanılır.

IoT Hub oluşturulduğunda bölüm sınırı seçilir ve değiştirilemez. Temel katman IoT Hub ve Standart katman IoT Hub için maksimum bölüm sınırı 32 ' dir. Birçok IoT Hub 'ı yalnızca 4 bölümden yeterlidir. Bölümleri belirleme hakkında daha fazla bilgi için, [kaç bölüme Ihtiyacım olduğunu](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need) Event Hubs hakkında SSS bölümüne bakın.

## <a name="tier-upgrade"></a>Katman yükseltme

IoT Hub 'ınızı oluşturduktan sonra, mevcut işlemlerinizi kesintiye uğramadan temel katmandan Standart katmana yükseltebilirsiniz. Daha fazla bilgi için bkz. [IoT Hub 'ınızı yükseltme](iot-hub-upgrade.md).

Temel katmandan Standart katmana geçiş yaptığınızda bölüm yapılandırması değişmeden kalır.

> [!NOTE]
> Ücretsiz katman, temel veya standart sürümüne yükseltmeyi desteklemez.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST API’leri

IoT Hub temel ve standart katmanları arasındaki desteklenen özelliklerde fark, bazı API çağrılarının temel katman hub 'ları ile çalışmamasıdır. Aşağıdaki tabloda hangi API 'Lerin kullanılabildiği gösterilmektedir:

| eklentisi | Temel katman | Ücretsiz/Standart katman |
| --- | ---------- | ------------- |
| [Cihazı Sil](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Yes | Yes |
| [Cihazı al](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Yes | Yes |
| [Modülü Sil](https://docs.microsoft.com/rest/api/iothub/service/deletemodule) | Yes | Yes |
| [Modül al](https://docs.microsoft.com/rest/api/iothub/service/getmodule) | Yes | Yes |
| [Kayıt defteri istatistiklerini al](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Yes | Yes |
| [Hizmet istatistiklerini al](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Yes | Yes |
| [Cihaz oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Yes | Yes |
| [Modül oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/iothub/service/createorupdatemodule) | Yes | Yes |
| [Sorgu IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Yes | Yes |
| [Karşıya dosya yükleme SAS URI 'SI oluştur](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Yes | Yes |
| [Cihaz ile sınırlı bildirim al](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Yes | Yes |
| [Cihaz olayı gönder](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Yes | Yes |
| Modül olayı gönder | Yalnızca AMQP ve MQTT | Yalnızca AMQP ve MQTT |
| [Karşıya dosya yükleme durumunu güncelleştir](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Yes | Yes |
| [Toplu cihaz işlemi](https://docs.microsoft.com/rest/api/iothub/service/bulkcreateorupdatedevices) | Evet, IoT Edge özellikleri dışında | Yes |
| [İçeri aktarma dışarı aktarma işini iptal et](https://docs.microsoft.com/rest/api/iothub/service/cancelimportexportjob) | Yes | Yes |
| [İçeri aktarma dışarı aktarma işi oluştur](https://docs.microsoft.com/rest/api/iothub/service/createimportexportjob) | Yes | Yes |
| [İçeri aktarma dışarı aktarma işi al](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjob) | Yes | Yes |
| [İçeri aktarma işlerini al](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjobs) | Yes | Yes |
| [Komut kuyruğunu temizle](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Yes |
| [Cihaz ikizi al](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Yes |
| [Modül ikizi al](https://docs.microsoft.com/rest/api/iothub/service/getmoduletwin) |   | Yes |
| [Cihaz yöntemini çağır](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Yes |
| [Cihaz ikizi Güncelleştir](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Yes |
| [Modül ikizi Güncelleştir](https://docs.microsoft.com/rest/api/iothub/service/updatemoduletwin) |   | Yes |
| [Cihaza bağlanma bildirimini bırakma](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Yes |
| [Cihaz ile sınırlı bildirimi doldurun](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Yes |
| [İşi iptal et](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Yes |
| [İş oluştur](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Yes |
| [İşi al](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Yes |
| [Sorgu işleri](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Yes |

## <a name="message-throughput"></a>İleti işleme

Bir IoT Hub çözümü boyutunun en iyi yolu, trafiği birim başına göre değerlendirmelidir. Özellikle, aşağıdaki işlem kategorileri için gereken en yüksek aktarım hızını göz önünde bulundurun:

* Cihazdan buluta iletiler
* Buluttan cihaza iletiler
* Kimlik kayıt defteri işlemleri

Trafik, IoT Hub 'ınız için birim başına ölçülür. Bir IoT Hub 'ı oluşturduğunuzda, katmanını ve sürümünü seçersiniz ve kullanılabilir birim sayısını ayarlarsınız. B1, B2, S1 veya S2 sürümü için en fazla 200 birim satın alabilir veya B3 veya S3 sürümü için en fazla 10 birim satın alabilirsiniz. IoT Hub 'ınız oluşturulduktan sonra, kendi sürümü içinde kullanılabilir birim sayısını değiştirebilir, katmanı içindeki sürümler arasında yükseltme veya düşürme (B1-B2) veya mevcut işlemlerinizi kesintiye uğramadan temel bilgisayardan Standart katmana (B1-S1) yükseltebilirsiniz. Daha fazla bilgi için bkz. [IoT Hub 'ınızı yükseltme](iot-hub-upgrade.md).  

Her bir katmanın trafik özelliklerine örnek olarak, cihazdan buluta iletiler şu sürekli işleme talimatlarını izler:

| Katman sürümü | Sürekli üretilen iş | Sürekli gönderme oranı |
| --- | --- | --- |
| B1, S1 |Birim başına en fazla 1111 KB/dakika<br/>(1,5 GB/gün/birim) |Birim başına ortalama 278 ileti/dakika<br/>(400.000 ileti/birim başına gün) |
| B2, S2 |Birim başına en fazla 16 MB/dakika<br/>(22,8 GB/gün/birim) |Birim başına ortalama 4.167 ileti/dakika<br/>(6.000.000 ileti/birim başına gün) |
| B3, S3 |Birim başına en fazla 814 MB/dakika<br/>(1144,4 GB/gün/birim) |Birim başına ortalama 208.333 ileti/dakika<br/>(300.000.000 ileti/birim başına gün) |

Cihazdan buluta aktarım hızı, IoT çözümünü tasarlarken göz önünde bulundurmanız gereken ölçülerden yalnızca biridir. Daha kapsamlı bilgi için bkz. [IoT Hub kotaları ve kısıtları](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Kimlik kayıt defteri işlem performansı

IoT Hub kimlik kayıt defteri işlemlerinin, genellikle cihaz sağlama ile ilişkili olduklarından, çalıştırma zamanı işlemleri olması gerekmez.

Belirli veri bloğu performans numaraları için bkz. [IoT Hub kotaları ve kısıtlar](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Otomatik Ölçeklendirme

IoT Hub 'ınızda izin verilen ileti sınırına yaklaşırsanız, aynı IoT Hub katmanındaki IoT Hub birimini artırmak üzere [otomatik olarak ölçeklendirmek](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) için bu adımları kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* IoT Hub özellikleri ve performans ayrıntıları hakkında daha fazla bilgi için bkz. [IoT Hub fiyatlandırma](https://azure.microsoft.com/pricing/details/iot-hub) veya [IoT Hub kotalar ve kısıtlar](iot-hub-devguide-quotas-throttling.md).

* IoT Hub katmanınızı değiştirmek için [IoT Hub 'ınızı yükseltme](iot-hub-upgrade.md)bölümündeki adımları izleyin.
